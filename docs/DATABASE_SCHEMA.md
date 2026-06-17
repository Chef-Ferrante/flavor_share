# Flavor Share Database Schema 💾

Complete PostgreSQL database schema for Flavor Share application.

## Overview

- **Normalization:** Third Normal Form (3NF)
- **Timestamps:** All entities track creation and update times
- **Soft Deletes:** Logical deletion for audit trails
- **Indexing:** Strategic indexes on frequently queried columns

## Core Tables

### Users Table

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    username VARCHAR(100) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    bio TEXT,
    profile_picture_url VARCHAR(500),
    location VARCHAR(255),
    dietary_restrictions TEXT[],
    is_verified BOOLEAN DEFAULT FALSE,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_username ON users(username);
```

### Recipes Table

```sql
CREATE TABLE recipes (
    id SERIAL PRIMARY KEY,
    user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    category VARCHAR(50) NOT NULL,
    cuisine_type VARCHAR(100),
    difficulty_level VARCHAR(20) DEFAULT 'Medium',
    
    ingredients JSONB NOT NULL,
    instructions JSONB NOT NULL,
    
    prep_time_minutes INTEGER,
    cook_time_minutes INTEGER,
    servings INTEGER DEFAULT 4,
    
    primary_image_url VARCHAR(500),
    average_rating DECIMAL(3,2) DEFAULT 0,
    total_ratings INTEGER DEFAULT 0,
    total_views INTEGER DEFAULT 0,
    
    tags TEXT[] DEFAULT '{}',
    allergen_info TEXT[],
    is_published BOOLEAN DEFAULT TRUE,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP
);

CREATE INDEX idx_recipes_user_id ON recipes(user_id);
CREATE INDEX idx_recipes_category ON recipes(category);
CREATE INDEX idx_recipes_created_at ON recipes(created_at DESC);
```

### Recipe Ratings Table

```sql
CREATE TABLE recipe_ratings (
    id SERIAL PRIMARY KEY,
    recipe_id INTEGER NOT NULL REFERENCES recipes(id) ON DELETE CASCADE,
    user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    rating INTEGER NOT NULL CHECK (rating >= 1 AND rating <= 5),
    review_text TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    UNIQUE(recipe_id, user_id)
);

CREATE INDEX idx_recipe_ratings_recipe_id ON recipe_ratings(recipe_id);
CREATE INDEX idx_recipe_ratings_user_id ON recipe_ratings(user_id);
```

### Recipe Likes Table

```sql
CREATE TABLE recipe_likes (
    id SERIAL PRIMARY KEY,
    recipe_id INTEGER NOT NULL REFERENCES recipes(id) ON DELETE CASCADE,
    user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    UNIQUE(recipe_id, user_id)
);

CREATE INDEX idx_recipe_likes_recipe_id ON recipe_likes(recipe_id);
CREATE INDEX idx_recipe_likes_user_id ON recipe_likes(user_id);
```

### Recipe Comments Table

```sql
CREATE TABLE recipe_comments (
    id SERIAL PRIMARY KEY,
    recipe_id INTEGER NOT NULL REFERENCES recipes(id) ON DELETE CASCADE,
    user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    parent_comment_id INTEGER REFERENCES recipe_comments(id) ON DELETE CASCADE,
    comment_text TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP
);

CREATE INDEX idx_recipe_comments_recipe_id ON recipe_comments(recipe_id);
CREATE INDEX idx_recipe_comments_user_id ON recipe_comments(user_id);
```

### Followers Table

```sql
CREATE TABLE followers (
    id SERIAL PRIMARY KEY,
    follower_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    followed_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    UNIQUE(follower_id, followed_id),
    CHECK (follower_id != followed_id)
);

CREATE INDEX idx_followers_follower_id ON followers(follower_id);
CREATE INDEX idx_followers_followed_id ON followers(followed_id);
```

### Conversations Table

```sql
CREATE TABLE conversations (
    id SERIAL PRIMARY KEY,
    user_1_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    user_2_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    last_message_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    UNIQUE(
        LEAST(user_1_id, user_2_id),
        GREATEST(user_1_id, user_2_id)
    ),
    CHECK (user_1_id != user_2_id)
);

CREATE INDEX idx_conversations_user_1_id ON conversations(user_1_id);
CREATE INDEX idx_conversations_user_2_id ON conversations(user_2_id);
```

### Messages Table

```sql
CREATE TABLE messages (
    id SERIAL PRIMARY KEY,
    conversation_id INTEGER NOT NULL REFERENCES conversations(id) ON DELETE CASCADE,
    sender_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    message_text TEXT NOT NULL,
    is_read BOOLEAN DEFAULT FALSE,
    read_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_messages_conversation_id ON messages(conversation_id);
CREATE INDEX idx_messages_sender_id ON messages(sender_id);
CREATE INDEX idx_messages_created_at ON messages(created_at DESC);
```

### Video Calls Table

```sql
CREATE TABLE video_calls (
    id SERIAL PRIMARY KEY,
    caller_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    callee_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    status VARCHAR(50) NOT NULL DEFAULT 'pending',
    twilio_room_name VARCHAR(255),
    initiated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    answered_at TIMESTAMP,
    ended_at TIMESTAMP,
    duration_seconds INTEGER,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_video_calls_caller_id ON video_calls(caller_id);
CREATE INDEX idx_video_calls_callee_id ON video_calls(callee_id);
CREATE INDEX idx_video_calls_status ON video_calls(status);
```

### Notifications Table

```sql
CREATE TABLE notifications (
    id SERIAL PRIMARY KEY,
    user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    type VARCHAR(50) NOT NULL,
    title VARCHAR(255) NOT NULL,
    message TEXT,
    related_user_id INTEGER REFERENCES users(id) ON DELETE SET NULL,
    related_recipe_id INTEGER REFERENCES recipes(id) ON DELETE SET NULL,
    is_read BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP
);

CREATE INDEX idx_notifications_user_id ON notifications(user_id);
CREATE INDEX idx_notifications_is_read ON notifications(is_read) WHERE NOT is_read;
```

### User Settings Table

```sql
CREATE TABLE user_settings (
    id SERIAL PRIMARY KEY,
    user_id INTEGER UNIQUE NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    profile_visibility VARCHAR(50) DEFAULT 'public',
    allow_direct_messages BOOLEAN DEFAULT TRUE,
    allow_video_calls BOOLEAN DEFAULT TRUE,
    notify_new_followers BOOLEAN DEFAULT TRUE,
    theme VARCHAR(20) DEFAULT 'light',
    language VARCHAR(10) DEFAULT 'en',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_user_settings_user_id ON user_settings(user_id);
```

## Relationships

```
users (1) ──→ (N) recipes
users (1) ──→ (N) recipe_ratings
users (1) ──→ (N) recipe_likes
users (1) ──→ (N) recipe_comments
users (1) ──→ (N) followers
users (1) ──→ (N) video_calls (as caller/callee)
users (1) ──→ (N) notifications

recipes (1) ──→ (N) recipe_ratings
recipes (1) ──→ (N) recipe_likes
recipes (1) ──→ (N) recipe_comments

conversations (N) ──→ (N) users
conversations (1) ──→ (N) messages
```

## Constraints & Business Rules

- Email and username must be unique
- Rating must be 1-5
- One rating/like per user per recipe
- Can't follow yourself
- One conversation per user pair

## Indexes Strategy

**Priority:**
1. Foreign Keys – Referential integrity
2. Primary Keys – Automatic
3. Search Columns – email, username, title
4. Sort Columns – created_at, rating
5. Filter Columns – category, is_published
6. Join Columns – user_id references

---

For detailed API usage, see [API.md](./API.md)
For system overview, see [ARCHITECTURE.md](./ARCHITECTURE.md)
