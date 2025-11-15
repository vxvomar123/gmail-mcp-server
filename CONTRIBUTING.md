# Contributing to Gmail MCP Server

Thank you for your interest in contributing! This project aims to provide the **best Gmail integration for Claude Desktop** with rock-solid authentication and comprehensive features.

## Why Contribute?

This project fills a critical gap - existing Gmail MCP servers either:
- Have broken authentication flows
- Require unnecessary dependencies (like OpenAI API)
- Lack proper error handling and documentation

Your contributions help thousands of users automate their email workflows with AI.

## How to Contribute

### Reporting Issues

Found a bug or authentication problem? [Open an issue](https://github.com/devdattatalele/gmail-mcp-server/issues/new) with:

1. **Clear description** of the problem
2. **Steps to reproduce** (be specific!)
3. **Expected behavior** vs **actual behavior**
4. **Your environment**:
   - OS (macOS/Windows/Linux)
   - Node.js version (`node --version`)
   - Error messages (full output)

### Suggesting Features

Have an idea? We'd love to hear it! Create an issue with:

- **Use case**: Why is this feature needed?
- **Proposed solution**: How should it work?
- **Alternatives considered**: Other approaches you thought of

### Code Contributions

#### First-Time Contributors

Look for issues tagged with `good-first-issue` or `help-wanted`. These are perfect for getting started!

#### Development Setup

```bash
# Fork and clone the repository
git clone https://github.com/YOUR_USERNAME/gmail-mcp-server.git
cd gmail-mcp-server

# Install dependencies
npm install

# Build the project
npm run build

# Set up OAuth credentials (see README)
# Place gcp-oauth.keys.json in project root

# Test authentication
node dist/index.js auth
```

#### Making Changes

1. **Create a feature branch**
   ```bash
   git checkout -b feature/your-amazing-feature
   ```

2. **Make your changes**
   - Write clean, readable TypeScript
   - Follow existing code style
   - Add comments for complex logic
   - Update README if adding new features

3. **Test thoroughly**
   ```bash
   # Rebuild
   npm run build

   # Test authentication flow
   node dist/index.js auth --force

   # Test your specific changes
   # (manual testing for now - automated tests coming soon!)
   ```

4. **Commit with clear messages**
   ```bash
   git commit -m "Add feature: brief description

   Detailed explanation of what changed and why.
   Fixes #123 (if applicable)"
   ```

5. **Push and create PR**
   ```bash
   git push origin feature/your-amazing-feature
   ```

   Then open a Pull Request on GitHub with:
   - Clear title and description
   - Screenshots/GIFs if UI changes
   - Link to related issues

#### Code Style Guidelines

- **TypeScript**: Use proper types, avoid `any` when possible
- **Error handling**: Always provide actionable error messages
- **Comments**: Explain WHY, not WHAT (code should be self-documenting)
- **Async/await**: Prefer over Promise chains
- **Naming**: Use descriptive names (`validateCredentials` not `vc`)

#### Pull Request Checklist

Before submitting, ensure:

- [ ] Code builds without errors (`npm run build`)
- [ ] Authentication flow works (`node dist/index.js auth`)
- [ ] No credentials committed (check with `git status`)
- [ ] README updated (if adding features)
- [ ] Clear commit messages
- [ ] PR description explains changes

### Documentation Improvements

Documentation is just as important as code! Help us by:

- Fixing typos or unclear explanations
- Adding examples or tutorials
- Improving error message documentation
- Creating troubleshooting guides

### Spread the Word

Not a coder? You can still help!

- ⭐ **Star the repository** - helps with discoverability
- 🐦 **Share on social media** - Twitter, LinkedIn, Reddit
- ✍️ **Write a blog post** - about your experience using it
- 💬 **Help others** - answer questions in issues/discussions
- 📺 **Create a tutorial** - video or written guide

## Areas We Need Help With

### High Priority

1. **Automated testing** - Unit and integration tests
2. **Error handling** - More edge cases and better messages
3. **Performance** - Optimize large batch operations
4. **Documentation** - More examples and tutorials

### Features We'd Love

- [ ] Draft management (list, edit, delete drafts)
- [ ] Email templates system
- [ ] Signature management
- [ ] Calendar integration (meeting invite emails)
- [ ] Email threading improvements
- [ ] Spam filter management
- [ ] Email scheduling
- [ ] Read receipts and tracking
- [ ] Multi-account support

### Don't See Your Idea?

Open a discussion! We're always looking for ways to improve.

## Questions?

- 💬 **GitHub Discussions**: For general questions and ideas
- 🐛 **GitHub Issues**: For bugs and feature requests
- 📧 **Email**: taleledevdatta@gmail.com

## Recognition

Contributors will be:
- Added to README acknowledgments
- Mentioned in release notes
- Forever appreciated by the community!

## Code of Conduct

Be respectful, helpful, and kind. We're all here to build something awesome together.

---

**Thank you for making Gmail MCP Server better!** 🚀

Every contribution, no matter how small, makes a difference.
