# Contributing to Flavor Share 🍳

Thank you for your interest in contributing to Flavor Share! We're excited to have you join our community. This document outlines how you can contribute to the project.

## Code of Conduct

We're committed to providing a welcoming and inclusive environment for all contributors. Please be respectful, kind, and considerate in all interactions.

## How to Contribute

### 1. Reporting Bugs

If you encounter a bug, please create an issue with:
- **Clear description** of the problem
- **Steps to reproduce** the bug
- **Expected behavior** vs. actual behavior
- **Screenshots or logs** (if applicable)
- **Your environment** (OS, Node version, React Native version, etc.)

### 2. Suggesting Features

Feature suggestions are welcome! Please:
- Check existing issues to avoid duplicates
- Provide a clear description of the feature
- Explain the use case and benefits
- Include mockups or examples if possible

### 3. Submitting Pull Requests

#### Before You Start
1. Fork the repository
2. Create a new branch: `git checkout -b feature/your-feature-name`
3. Set up your local development environment (see README.md)

#### Development Guidelines

**Code Style:**
- Use ESLint and Prettier for code formatting
- Follow the existing code structure and naming conventions
- Write clear, descriptive variable and function names

**Commits:**
- Use meaningful commit messages: `git commit -m "Add feature: description"`
- Keep commits focused on a single feature or fix
- Reference issues in commit messages: `Fixes #123`

**Testing:**
- Write unit tests for new features
- Ensure all tests pass: `npm run test`
- Aim for at least 80% code coverage

**Documentation:**
- Update README.md if adding new features
- Add comments for complex logic
- Update API documentation if modifying endpoints

#### Pull Request Process

1. **Update your branch** with the latest changes:
   ```bash
   git fetch origin
   git rebase origin/main
   ```

2. **Push your changes**:
   ```bash
   git push origin feature/your-feature-name
   ```

3. **Create a Pull Request** on GitHub with:
   - Clear title and description
   - Reference to related issues: `Closes #123`
   - Screenshots or demo links (if applicable)
   - Checklist of what you've done

4. **Address Feedback:**
   - Review comments promptly
   - Make requested changes in new commits
   - Push updates

5. **Merge:**
   - A maintainer will review and merge your PR
   - Your contribution will be part of the next release!

## Development Setup

See [docs/SETUP.md](./docs/SETUP.md) for detailed setup instructions.

## Commit Message Guidelines

Follow conventional commits format:

```
type(scope): subject

body

footer
```

**Types:**
- `feat:` – New feature
- `fix:` – Bug fix
- `docs:` – Documentation changes
- `style:` – Code style changes
- `refactor:` – Code refactoring
- `perf:` – Performance improvements
- `test:` – Adding or updating tests
- `chore:` – Dependency updates, build changes

**Examples:**
- `feat(auth): add JWT refresh token mechanism`
- `fix(recipes): resolve image upload validation error`
- `docs: update API endpoint documentation`

## Areas We Need Help With

- 🎨 UI/UX improvements
- 📚 Documentation and tutorials
- 🧪 Testing coverage
- 🐛 Bug fixes
- 🚀 Performance optimization
- ♿ Accessibility improvements
- 🌍 Internationalization (i18n)

## Questions or Need Help?

- Create a discussion in GitHub Discussions
- Open an issue with the `question` label
- Reach out to the maintainers

---

Thank you for contributing to Flavor Share! Together, we're building something amazing for food lovers everywhere. 🍽️✨
