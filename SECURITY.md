# Security Policy

## Supported Versions

Security updates are provided for the latest major version of Docsmith. We recommend all users upgrade to the latest version to ensure they receive security patches.

| Version | Supported |
|---------|-----------|
| 1.x.x   | ✅ Yes     |
| < 1.0.0| ❌ No      |

## Reporting a Vulnerability

We take security seriously. If you discover a security vulnerability in Docsmith, please report it to us privately.

**Please do not report security vulnerabilities through public GitHub issues.**

Instead, send an email to [security@docsmith.dev](mailto:security@docsmith.dev) with:

- A clear description of the vulnerability
- Steps to reproduce the issue
- The potential impact
- Your proposed fix (if you have one)

We will:

1. Acknowledge receipt of your report within 24 hours
2. Investigate and verify the vulnerability
3. Work on a fix and test it thoroughly
4. Release a patch as quickly as possible
5. Publicly credit you for the discovery (if you wish)

## Security Best Practices

### For Users

- **Never commit `.env` files** - They may contain API keys and secrets
- **Use HTTPS** - Always use HTTPS connections to GitHub
- **Validate inputs** - Be cautious with repository URLs
- **Rate limiting** - Use GitHub PAT to increase rate limits safely
- **Network security** - Run Docsmith in a secure environment

### For Contributors

- Follow secure coding practices
- Never hardcode secrets or credentials
- Use environment variables for sensitive data
- Sanitize all user inputs
- Keep dependencies updated
- Report security issues responsibly

## Security Features

Docsmith includes several security features:

- **Input validation** - Repository URLs are validated before processing
- **No code execution** - Raw source code is never executed
- **Safe API calls** - GitHub API calls use authenticated requests
- **Environment isolation** - Secrets are loaded from environment variables
- **No data persistence** - Temporary files are cleaned up automatically

## Dependencies

We regularly audit and update our dependencies to address known vulnerabilities. Dependencies are listed in `requirements.txt` and should be kept up-to-date.

To check for vulnerable dependencies:

```bash
pip-audit
# or
safety check
```

## Security Updates

Security updates are released as patches to the latest minor version. We follow semantic versioning:

- **Patch version** (x.x.Z) - Security fixes, bug fixes
- **Minor version** (x.Y.x) - New features (backward-compatible)
- **Major version** (X.x.x) - Breaking changes

## Credits

We would like to thank the following security researchers for responsibly disclosing vulnerabilities:

- *No disclosures yet - be the first!*

## Contact

For general security questions: [security@docsmith.dev](mailto:security@docsmith.dev)

For general inquiries: [hello@docsmith.dev](mailto:hello@docsmith.dev)