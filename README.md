# Irfan's Notebook 📚

A focused documentation site for modern web development, built with [MkDocs](https://www.mkdocs.org/) and [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/).

## 🚀 Quick Start

### Prerequisites

- Python 3.x
- pip (Python package manager)
- Git

### Local Development

1. **Clone the repository**

   ```bash
   git clone https://github.com/irfanhsajid/notebook.git
   cd notebook
   ```

2. **Install dependencies**

   ```bash
   pip install -r requirements.txt
   ```

3. **Run the development server**

   ```bash
   mkdocs serve
   ```

4. **Open your browser**

   Navigate to `http://127.0.0.1:8000` to view the documentation site locally.

## 📝 Adding and Editing Notes

### Project Structure

```
Irfans-Notebook/
├── docs/                    # Documentation source files
│   ├── index.md            # Homepage
│   ├── notes/              # Additional notes
│   └── [other-topics]/     # Topic-specific folders
├── mkdocs.yml              # MkDocs configuration
├── requirements.txt        # Python dependencies
└── README.md               # This file
```

### How to Add New Notes

1. **Create a new Markdown file** in the appropriate directory under `docs/`

   ```bash
   # Example: Adding a new JavaScript note
   touch docs/javascript/new-topic.md
   ```

2. **Write your content** using Markdown syntax

3. **Update the navigation** in `mkdocs.yml`

   ```yaml
   nav:
     - Programming Languages:
         - JavaScript:
             - Guide: javascript/guide.md
             - New Topic: javascript/new-topic.md # Add this line
   ```

4. **Preview your changes**
   ```bash
   mkdocs serve
   ```

### Markdown Tips

MkDocs with Material theme supports enhanced Markdown features:

- **Code blocks with syntax highlighting**

  ````markdown
  ```python
  def hello():
      print("Hello, World!")
  ```
  ````

- **Admonitions (callouts)**

  ```markdown
  !!! note "Important Note"
  This is an important note.
  ```

- **Tables, task lists, and more**

Check the [Material for MkDocs documentation](https://squidfunk.github.io/mkdocs-material/reference/) for all available features.

## 🌐 Deployment

### Automatic Deployment (Recommended)

The repository includes a GitHub Actions workflow that automatically deploys to GitHub Pages on every push to the `main` branch.

**Setup Steps:**

1. **Enable GitHub Pages**

   - Go to your repository settings
   - Navigate to **Pages** section
   - Set source to `gh-pages` branch
   - Save

2. **Push to main branch**

   ```bash
   git add .
   git commit -m "Update documentation"
   git push origin main
   ```

3. **Wait for deployment**
   - GitHub Actions will automatically build and deploy
   - Check the Actions tab to monitor progress
   - Site will be available at `https://irfanhsajid.github.io/notebook/`

### Manual Deployment

If you prefer to deploy manually:

```bash
# Build the static site
mkdocs build

# Deploy to GitHub Pages
mkdocs gh-deploy
```

This command will:

- Build the site in the `site/` directory
- Push the built files to the `gh-pages` branch
- Make the site available on GitHub Pages

## 🛠️ Useful Commands

| Command            | Description                                 |
| ------------------ | ------------------------------------------- |
| `mkdocs serve`     | Start the live-reloading development server |
| `mkdocs build`     | Build the documentation site                |
| `mkdocs gh-deploy` | Deploy to GitHub Pages                      |
| `mkdocs --help`    | Show help information                       |

## 🎨 Customization

### Theme Customization

Edit `mkdocs.yml` to customize:

- Color scheme (light/dark mode)
- Navigation structure
- Features and plugins
- Site metadata

### Adding Plugins

1. Add the plugin to `requirements.txt`
2. Update `mkdocs.yml` configuration
3. Run `pip install -r requirements.txt`

## 🤝 Contributing

This is a personal notebook, but suggestions and corrections are welcome!

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/improvement`)
3. Commit your changes (`git commit -am 'Add some improvement'`)
4. Push to the branch (`git push origin feature/improvement`)
5. Open a Pull Request

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

## 🔗 Links

- **Documentation Site**: https://irfanhsajid.github.io/notebook/
- **MkDocs**: https://www.mkdocs.org/
- **Material for MkDocs**: https://squidfunk.github.io/mkdocs-material/

---

**Author**: Irfan  
**Last Updated**: December 2025
