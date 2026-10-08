# Contributing to Frontend Slides for OpenCode

Thank you for your interest in contributing to Frontend Slides! This community-driven adaptation of the original Claude Code skill is designed to work seamlessly with OpenCode. We welcome contributions from developers of all skill levels.

---

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [Development Setup](#development-setup)
- [Ways to Contribute](#ways-to-contribute)
- [Pull Request Process](#pull-request-process)
- [Style Guides](#style-guides)
- [Community](#community)

---

## Code of Conduct

By participating in this project, you are expected to uphold our [Code of Conduct](CODE_OF_CONDUCT.md). Please report unacceptable behavior to the maintainers.

---

## Getting Started

### Understanding the Project Structure

```
frontend-slides-OpenCode/
├── SKILL.md                 # Main skill definition (required for OpenCode)
├── README.md                # Documentation
├── STYLE_PRESETS.md         # Visual style references
├── viewport-base.css        # Mandatory responsive CSS
├── html-template.md         # HTML structure reference
├── animation-patterns.md    # Animation reference
├── LICENSE                  # MIT License
├── CONTRIBUTING.md          # This file
├── CODE_OF_CONDUCT.md       # Community code of conduct
└── scripts/
    └── extract-pptx.py     # PPTX extraction script
```

### Key Concepts

1. **SKILL.md** - The core skill definition that OpenCode reads. Contains the workflow, rules, and prompts.
2. **Supporting Files** - Additional reference files loaded on-demand during skill execution.
3. **OpenCode Skills** - Skills are discovered from `~/.config/opencode/skills/` directory.

---

## Development Setup

### Prerequisites

- [OpenCode](https://github.com/opencode-ai/opencode) installed
- Python 3.8+ (for PPTX extraction)
- Git

### Local Development

1. **Fork the repository**
   Click the "Fork" button on the GitHub page.

2. **Clone your fork**

   ```bash
   git clone https://github.com/YOUR_USERNAME/frontend-slides-OpenCode.git
   cd frontend-slides-OpenCode
   ```

3. **Create a symlink for testing**

   ```bash
   # For OpenCode
   ln -s $(pwd) ~/.config/opencode/skills/frontend-slides
   ```

4. **Test your changes**
   - Open OpenCode
   - Invoke the frontend-slides skill
   - Create a test presentation
   - Verify your changes work correctly

### Testing PPTX Extraction

```bash
# Install dependencies
pip install python-pptx Pillow

# Test the extraction script
python scripts/extract-pptx.py test-presentation.pptx output-dir
```

---

## Ways to Contribute

### 🐛 Bug Reports

Use [GitHub Issues](https://github.com/TMFNK/frontend-slides-OpenCode/issues) to report bugs. Include:

- A quick summary and background
- Steps to reproduce
- What you expected vs what happened
- Notes (possibly including why you think this might be happening)

### 💡 Feature Requests

Use [GitHub Issues](https://github.com/TMFNK/frontend-slides-OpenCode/issues) to suggest features. Include:

- What feature you want and why
- How it should work
- Alternative solutions you've considered

### 🎨 Style Presets

Want to add a new visual style? Here's how:

1. Add the style definition to [STYLE_PRESETS.md](STYLE_PRESETS.md)
2. Include color palette, typography, and signature elements
3. Test with the viewport-base.css rules
4. Submit a PR with:
   - Style name and description
   - CSS variables
   - Preview image (optional)

### 📖 Documentation

Improvements to documentation are always welcome:

- Fix typos or unclear explanations
- Add examples
- Translate to other languages

### 🛠️ Code Contributions

Bug fixes and new features are greatly appreciated:

1. Find an issue to work on or create a new one
2. Fork the repo and create a feature branch
3. Make your changes
4. Submit a pull request

---

## Pull Request Process

### Before Submitting

1. **Test locally** - Ensure your changes work with OpenCode
2. **Follow style guides** - Keep code consistent with the project
3. **Update documentation** - If you change functionality, update relevant docs

### Pull Request Checklist

- [ ] I have tested my changes locally
- [ ] My code follows the style guidelines
- [ ] I have updated documentation if needed
- [ ] My changes are consistent with the project's scope

### PR Template

```markdown
## Description

Brief description of changes

## Type of Change

- [ ] Bug fix
- [ ] New feature
- [ ] Documentation update
- [ ] Style preset addition

## Testing

How did you test your changes?

## Screenshots

If applicable, add screenshots
```

---

## Style Guides

### SKILL.md Guidelines

- Use clear, concise language
- Include examples where helpful
- Keep the workflow logical and sequential
- Maintain the YAML frontmatter format

### CSS Guidelines

- Always use `clamp()` for responsive sizing
- Include `viewport-base.css` in every presentation
- Follow the CSS gotchas in STYLE_PRESETS.md
- Test on multiple viewport sizes

### JavaScript Guidelines

- Use vanilla JavaScript (no frameworks)
- Include Intersection Observer for animations
- Support keyboard and touch navigation
- Follow the inline editing implementation pattern

### Markdown Guidelines

- Use ATX-style headers (# ## ###)
- Include code blocks with language hints
- Keep lines at reasonable length (80 chars)
- Use tables for structured data

---

## Community

### Getting Help

- **GitHub Discussions** - For questions and ideas
- **GitHub Issues** - For bug reports and feature requests

### Recognition

Contributors will be recognized in:

- The README.md contributors section
- Release notes

---

## License

By contributing, you agree that your contributions will be licensed under the MIT License.

---

## Questions?

If you have questions, feel free to open a GitHub Discussion or reach out to the maintainers.

Thank you for contributing! 🚀
