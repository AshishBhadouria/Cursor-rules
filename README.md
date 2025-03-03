# Cursor Security Rules

A comprehensive set of security-focused rules for the Cursor IDE to help developers write more secure code through real-time guidance and automated detection of potential vulnerabilities.

## Overview

This repository contains a collection of security rules designed to be integrated with the Cursor IDE. These rules provide real-time feedback as you write code, highlighting potential security issues and suggesting more secure alternatives.

![cursor](https://github.com/user-attachments/assets/ddac1220-f8f8-4df4-b53d-07c0c458a147)

## Features

- **14 Security Categories**: Comprehensive coverage from input validation to framework-specific security concerns
- **Severity Levels**: Rules categorized by Critical, High, Medium, and Low severity
- **Visual Indicators**: Clear examples of insecure vs. secure code patterns
- **Context-Aware Suggestions**: Tailored recommendations based on the specific security issue detected
- **Multi-Language Support**: Rules for JavaScript/TypeScript, Python, Java, and more

## Installation

### Option 1: Using Cursor Extension Marketplace

1. Open Cursor IDE
2. Navigate to Extensions (Ctrl+Shift+X / Cmd+Shift+X)
3. Search for "Cursor Security Rules"
4. Click Install

### Option 2: Manual Installation

1. Clone this repository:
   ```bash
   git clone https://github.com/your-username/cursor-security-rules.git
   ```

2. Copy the rules file to your Cursor configuration directory:
   ```bash
   cp cursor-security-rules/secure-coding-cursor-rules.md ~/.cursor/rules/
   ```

3. Add the following to your Cursor settings.json:
   ```json
   {
     "security.rules.enabled": true,
     "security.rules.path": "~/.cursor/rules/secure-coding-cursor-rules.md"
   }
   ```

## Usage

Once installed, the security rules will automatically analyze your code as you type. Here's how to make the most of this tool:

### Basic Usage

1. **Write Code Normally**: The rules will run automatically as you type
2. **Watch for Highlights**: Potentially insecure code will be highlighted
3. **Hover for Details**: Hover over highlighted code to see:
   - Description of the security issue
   - Severity level
   - Suggested fix with example

### Quick Fixes

When a security issue is detected:

1. Click the lightbulb icon 💡 that appears
2. Select "Fix this security issue" from the menu
3. Choose from the suggested secure alternatives

### Rule Configuration

You can customize which rules are active in your `settings.json`:

```json
{
  "security.rules": {
    "enabled": true,
    "categories": {
      "input-validation": true,
      "auth": true,
      "crypto": true,
      "memory-management": false
    },
    "severity": {
      "critical": true,
      "high": true,
      "medium": true,
      "low": false
    }
  }
}
```

### AI Integration

When using Cursor's AI features:

1. **AI-Assisted Coding**: The AI will follow these security rules when generating code
2. **AI Prompting**: Add "/secure" to your prompt to prioritize security in generated code
   ```
   /secure Generate a function to handle user authentication
   ```
3. **Security Review**: Ask the AI to review your code for security issues:
   ```
   Review this code for security issues based on our security rules
   ```

## Rule Categories

Here's a quick overview of the security categories covered:

1. **Input Validation**: Prevent injection attacks through proper validation
2. **Output Encoding**: Prevent XSS and other injection attacks
3. **Authentication & Password Management**: Secure credential handling
4. **Session Management**: Secure session handling and prevention of session attacks
5. **Access Control**: Proper implementation of authorization
6. **Cryptographic Practices**: Secure encryption and random number generation
7. **Error Handling & Logging**: Prevent information leakage
8. **Memory Management**: Prevent buffer overflows and memory leaks
9. **General Coding Practices**: General secure coding principles
10. **File Management**: Secure file handling and upload validation
11. **API Security**: Secure API design and implementation
12. **Container & Infrastructure Security**: Secure configuration for infrastructure
13. **Dependency Management**: Managing security in dependencies
14. **Framework-Specific Rules**: Security best practices for popular frameworks

## Contributing

We welcome contributions to improve these security rules:

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-rule`)
3. Add or modify rules in the markdown file
4. Commit your changes (`git commit -m 'Add some amazing rule'`)
5. Push to the branch (`git push origin feature/amazing-rule`)
6. Open a Pull Request

Please ensure new rules follow the existing format and include examples of both secure and insecure code.

## License

MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- OWASP for their security guidance and resources
- The Cursor team for their excellent IDE
- All contributors who have helped improve these rules


