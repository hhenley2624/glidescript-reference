# 📚 GlideScript Reference Guide

A beautiful, interactive reference guide for ServiceNow's GlideSystem (gs) API. Built for developers who want fast, searchable access to methods, examples, and use cases.

![GlideScript Reference](https://img.shields.io/badge/ServiceNow-GlideScript-00f5a0)
![Version](https://img.shields.io/badge/version-1.0.0-blue)
![License](https://img.shields.io/badge/license-MIT-green)

## ✨ Features

- 🎨 **Beautiful Dark UI** - Custom-designed interface optimized for developers
- 🔍 **Live Search** - Instantly filter methods and use cases
- 📱 **Fully Responsive** - Works on desktop, tablet, and mobile
- ⚡ **Fast & Lightweight** - Pure HTML/CSS/JS, no frameworks required
- 🎯 **Comprehensive Coverage** - All GlideSystem methods with examples
- 💡 **Real Use Cases** - 12+ practical examples from real ServiceNow work
- 🚀 **Easy Navigation** - Sticky sidebar with smooth scrolling
- 📖 **Ctrl+F Friendly** - Optimized for browser search

## 🚀 Quick Start

### Option 1: GitHub Pages (Recommended)

1. Fork this repository
2. Go to Settings → Pages
3. Select `main` branch as source
4. Your site will be live at `https://yourusername.github.io/glidescript-reference/`

### Option 2: Local Development

```bash
# Clone the repository
git clone https://github.com/yourusername/glidescript-reference.git
cd glidescript-reference

# Open in browser (any of these methods work)
open index.html                    # macOS
start index.html                   # Windows
xdg-open index.html               # Linux

# Or use a local server
python -m http.server 8000        # Python 3
# Then visit http://localhost:8000
```

### Option 3: One-Click Deploy

[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/yourusername/glidescript-reference)

[![Deploy to Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/yourusername/glidescript-reference)

## 📁 Project Structure

```
glidescript-reference/
├── index.html                          # Main webpage
├── glidescript_reference_guide.md      # Markdown source
├── README.md                           # This file
└── LICENSE                             # MIT License
```

## 🎯 What's Included

### Methods Covered

- **User Information**: getUserID(), getUserName(), getUserDisplayName()
- **Session Management**: getSession(), isInteractive(), isMobile()
- **Logging & Debugging**: info(), error(), warn(), debug()
- **Messages & UI**: addInfoMessage(), addErrorMessage(), getMessage()
- **Date & Time**: 30+ methods for date manipulation
- **System Properties**: getProperty(), setProperty()
- **Event Management**: eventQueue(), eventQueueScheduled()
- **Security**: hasRole(), permissions
- **Utilities**: nil(), generateGUID(), tableExists(), base64Encode()

### Use Cases

1. Query records from last 7 days
2. Set due dates based on priority
3. Role-based field access
4. Date validation
5. Conditional logging
6. Monthly reporting
7. Scheduled events
8. Safe table queries
9. User-specific filters
10. Dynamic property configuration
11. Automated workflows
12. Audit trails

## 🎨 Customization

### Change Theme Colors

Edit the CSS variables in `index.html`:

```css
:root {
    --bg-primary: #0a0e14;        /* Main background */
    --accent-primary: #00f5a0;    /* Primary accent color */
    --accent-secondary: #00d4aa;  /* Secondary accent */
    --text-primary: #e6e6e6;      /* Main text color */
}
```

### Add Custom Fonts

Update the Google Fonts import in the `<head>`:

```html
<link href="https://fonts.googleapis.com/css2?family=YourFont:wght@400;700&display=swap" rel="stylesheet">
```

## 🤝 Contributing

Contributions are welcome! Here's how you can help:

1. **Report Issues**: Found a bug or have a suggestion? [Open an issue](https://github.com/yourusername/glidescript-reference/issues)
2. **Add Examples**: Have a great use case? Submit a PR
3. **Improve Documentation**: Help make explanations clearer
4. **Fix Bugs**: See something wrong? Fix it and submit a PR

### Development Workflow

```bash
# Fork the repo
# Clone your fork
git clone https://github.com/yourusername/glidescript-reference.git

# Create a feature branch
git checkout -b feature/your-feature-name

# Make your changes
# Test locally
# Commit and push
git add .
git commit -m "Add: your feature description"
git push origin feature/your-feature-name

# Open a Pull Request
```

## 📝 License

MIT License - feel free to use this for any purpose!

## 🙏 Acknowledgments

- Built with inspiration from ServiceNow's official documentation
- Designed for the ServiceNow developer community
- Dark theme inspired by modern code editors

## 📧 Contact

Have questions or suggestions? 

- Open an issue on GitHub
- Fork and customize for your team
- Share with other ServiceNow developers!

## 🌟 Star This Repo

If you find this helpful, give it a ⭐️ on GitHub!

---

**Happy Coding!** 🚀

Built with ❤️ for ServiceNow developers
