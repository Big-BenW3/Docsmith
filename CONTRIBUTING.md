# Contributing to Docsmith

First off, thanks for taking the time to contribute! ❤️

Docsmith is an open-source project and we welcome contributions of all kinds: bug reports, feature requests, documentation improvements, and code contributions.

## Table of Contents

- [Getting Started](#getting-started)
- [How Can I Contribute?](#how-can-i-contribute)
  - [Reporting Bugs](#reporting-bugs)
  - [Suggesting Enhancements](#suggesting-enhancements)
  - [Your First Code Contribution](#your-first-code-contribution)
- [Development Setup](#development-setup)
- [Pull Request Guidelines](#pull-request-guidelines)
- [Coding Standards](#coding-standards)
- [Commit Message Guidelines](#commit-message-guidelines)
- [Recognitions](#recognitions)

## Getting Started

- Make sure you have a [GitHub account](https://github.com/signup/free)
- Fork the repository on GitHub
- Read this contributing guide
- Check out the [existing issues](https://github.com/your-repo/docsmith/issues)

## How Can I Contribute?

### Reporting Bugs

When reporting bugs, please include:

- **A clear and descriptive title**
- **Steps to reproduce** the issue
- **Expected behavior** vs **actual behavior**
- **Screenshots or logs** if applicable
- **Environment information**: Python version, OS, browser (for UI issues)
- **Relevant code snippets** (if applicable)

### Suggesting Enhancements

For feature requests, please:

- Explain **why** this feature would be useful
- Describe the **expected behavior**
- Include **mockups or examples** if it's a UI change
- Explain any **alternatives** you've considered

### Your First Code Contribution

Unsure where to begin? Start with:

1. **Good first issues** - Issues labeled "good first issue" are beginner-friendly
2. **Documentation** - Improve docs, add examples, fix typos
3. **Tests** - Add missing test cases
4. **Code** - Fix bugs or implement features

## Development Setup

### Prerequisites

- Python 3.11+
- Git
- Virtual environment manager (venv, poetry, etc.)

### Setup

```bash
# Clone the repository
git clone https://github.com/your-repo/docsmith.git
cd docsmith

# Create virtual environment
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Create .env file
cp .env.example .env
# Edit .env with your API keys

# Run the development server
python api/index.py
```

### Running Tests

```bash
# Run all tests
python -m pytest

# Run with coverage
python -m pytest --cov=pipeline
```

## Pull Request Guidelines

- Follow the [coding standards](#coding-standards)
- Include [good commit messages](#commit-message-guidelines)
- Keep PRs focused on a single feature or bug fix
- Add tests for new functionality
- Update documentation when needed
- Link to any relevant issues

## Coding Standards

### Python

- Follow [PEP 8](https://peps.python.org/pep-0008/) style guide
- Use type hints (Python 3.6+ style)
- Write docstrings for all public functions and classes
- Keep lines under 100 characters when possible
- Use f-strings for string formatting

### JavaScript/CSS

- Use consistent indentation (2-4 spaces)
- Follow modern ES6+ syntax
- Add JSDoc comments for complex functions
- Keep CSS organized and commented

### General

- Write clear, self-documenting code
- Prefer simplicity over cleverness
- Add comments for non-obvious logic
- Remove commented-out code
- Keep code DRY (Don't Repeat Yourself)

## Commit Message Guidelines

We follow [Conventional Commits](https://www.conventionalcommits.org/) style:

```
type(scope): subject

body

footer
```

### Types

- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, missing semicolons, etc.)
- `refactor`: Code refactoring (no functional changes)
- `test`: Adding or fixing tests
- `chore`: Build process or auxiliary tool changes

### Examples

```bash
# Good
feat(ui): add dark theme toggle
fix(api): handle null values in response
docs(readme): update installation instructions
refactor(pipeline): extract common fetch logic

# Bad
fixed bug
update docs
wip: working on stuff
```

## Recognitions

All contributions, big or small, are valuable. Thank you for helping make Docsmith better! 🎉

- Contributors will be listed in the CONTRIBUTORS.md file
- Significant contributions may receive additional recognition
- Every contributor gets our eternal gratitude! 🙏

## Need Help?

If you have questions about contributing:

- Open an issue with your question
- Join our [Discussions](https://github.com/your-repo/docsmith/discussions) forum
- Reach out on [Twitter/X](https://twitter.com/your-handle)

We're here to help you succeed!