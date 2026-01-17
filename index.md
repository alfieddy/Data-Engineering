# Git Cheat Sheets - Comprehensive Reference Guides

Welcome to the **Git Cheat Sheets** repository! This collection provides comprehensive, visual, and PDF-exportable reference guides for Git commands and workflows.

[![GitHub Pages](https://img.shields.io/badge/GitHub%20Pages-Live-success)](https://alfieddy.github.io/Data-Engineering/)
[![PDF Ready](https://img.shields.io/badge/PDF-Ready-blue)](https://github.com/alfieddy/Data-Engineering)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)

---

## 📚 Available Cheat Sheets

### [🎯 Git Core Commands](git-core-commands.md)
**Essential reference for Git fundamentals**
- Setup & Configuration
- Repository initialization
- Basic snapshotting (add, commit, status)
- Branching and merging
- Inspection and comparison
- Undoing changes
- Remote repositories
- **~8-10 pages when exported to PDF**

[**View Git Core Commands →**](git-core-commands.md)

---

### [👥 Git Team Workflows](git-team-workflows.md)
**Collaboration best practices for teams**
- Daily workflow checklist
- Feature Branch Workflow
- Git Flow, GitHub Flow, Trunk-Based Development
- 6 common team scenarios with solutions
- Pull request best practices
- Commit message guidelines
- Branch naming conventions
- **~7-9 pages when exported to PDF**

[**View Team Workflows →**](git-team-workflows.md)

---

### [⚡ Git Advanced Commands](git-advanced-commands.md)
**Power user guide for advanced techniques**
- Interactive rebase
- Cherry-pick, stash, submodules
- Reflog recovery
- Bisect for bug hunting
- Worktrees, hooks, patches
- Advanced merging strategies
- Performance optimization
- **~9-11 pages when exported to PDF**

[**View Advanced Commands →**](git-advanced-commands.md)

---

## 🎨 Features

All cheat sheets include:

✅ **Visual Diagrams** - Mermaid flowcharts and ASCII art  
✅ **Color-Coded Safety** - 🟢 Safe / 🟡 Caution / 🔴 Dangerous operations  
✅ **Comprehensive Tables** - Quick reference for all commands  
✅ **Real Examples** - Practical code snippets with explanations  
✅ **PDF Optimized** - Ready for export with proper page breaks  
✅ **Emoji Icons** - 💡 Tips / ⚠️ Warnings / 📝 Notes / ⛔ Danger alerts

---

## 📥 Download Options

### View Online
All cheat sheets are available to read directly on GitHub Pages:
- **Live Site**: [https://alfieddy.github.io/Data-Engineering/](https://alfieddy.github.io/Data-Engineering/)

### Export to PDF

Using **Pandoc** (recommended):
```bash
# Install Pandoc first: https://pandoc.org/installing.html

# Export individual files
pandoc git-core-commands.md -o git-core-commands.pdf --pdf-engine=xelatex --toc
pandoc git-team-workflows.md -o git-team-workflows.pdf --pdf-engine=xelatex --toc
pandoc git-advanced-commands.md -o git-advanced-commands.pdf --pdf-engine=xelatex --toc

# Export all at once
for file in git-*.md; do
  pandoc "$file" -o "${file%.md}.pdf" --pdf-engine=xelatex --toc
done
```

Using **VS Code Extension** (Markdown PDF):
1. Install "Markdown PDF" extension
2. Open any `.md` file
3. Press `Ctrl+Shift+P` → "Markdown PDF: Export (pdf)"

### Clone Repository
```bash
git clone https://github.com/alfieddy/Data-Engineering.git
cd Data-Engineering
```

---

## 🚀 Quick Start

### For Beginners
Start with [Git Core Commands](git-core-commands.md) to learn the fundamentals.

### For Team Members
Check out [Git Team Workflows](git-team-workflows.md) for collaboration best practices.

### For Power Users
Explore [Git Advanced Commands](git-advanced-commands.md) for advanced techniques.

---

## 📖 Usage

### Online Reading
Simply click on any of the cheat sheet links above to view them on GitHub Pages with full markdown rendering, including diagrams and syntax highlighting.

### Offline PDF
Export the markdown files to PDF for offline reading, printing, or sharing with your team.

### Search Functionality
Use your browser's search (Ctrl+F / Cmd+F) to quickly find specific commands or topics.

---

## 🤝 Contributing

Contributions are welcome! If you find errors or want to add content:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

---

## 📄 License

MIT License - feel free to use these cheat sheets for personal or commercial purposes.

---

## 🌟 About

Created by the Data Engineering Team to provide comprehensive, visual, and accessible Git reference materials for developers at all skill levels.

**Total Content**: ~25-30 pages of comprehensive Git documentation  
**Last Updated**: January 17, 2026  
**Version**: 1.0.0

---

## 📞 Support

Found an issue? [Open an issue](https://github.com/alfieddy/Data-Engineering/issues) on GitHub.

---

**Happy Git-ing! 🚀**