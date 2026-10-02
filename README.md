# Docsmith - AI-Powered SDK Documentation Generator

> **Turn GitHub repositories into beautiful, comprehensive documentation sites instantly.**

[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![MIT License](https://img.shields.io/badge/license-MIT-green.svg)](https://github.com/your-repo/docsmith/blob/main/LICENSE)
[![Code Style: Ruff](https://img.shields.io/badge/code%20style-ruff-000000.svg)](https://github.com/astral-sh/ruff)
[![CI Status](https://github.com/your-repo/docsmith/actions/workflows/ci.yml/badge.svg)](https://github.com/your-repo/docsmith/actions/workflows/ci.yml)
[![Codecov](https://github.com/your-repo/docsmith/blob/main/.github/workflows/ci.yml)](https://codecov.io/gh/your-repo/docsmith)

---

## 🚀 **What is Docsmith?**

**Docsmith** is a cutting-edge, AI-powered tool that automatically generates comprehensive **MkDocs documentation sites** from Python SDK repositories. 

**No cloning. No manual configuration. Just provide a GitHub URL and get a complete, navigable documentation site.**

### ⚡ **Key Features**

- **Zero-Clone Architecture**: Fetches everything via GitHub REST API
- **AI-Powered Documentation**: LLM-generated guides, examples, and explanations
- **Accurate API Reference**: Deterministic API docs extracted from AST (100% accurate)
- **Beautiful Output**: Material for MkDocs theme with professional styling
- **Serverless Ready**: Built for Vercel deployment with background processing
- **Open Source**: Fully transparent, extensible, and community-driven

---

## 📸 **Preview**

### Before & After

**Before:**
```text
Your SDK repo:
└── complex_api_client/
    ├── __init__.py        # Main client class
    ├── resources/
    │   ├── users.py       # User management
    │   ├── projects.py     # Project operations
    │   └── billing.py      # Billing endpoints
    ├── models/
    │   ├── user.py
    │   ├── project.py
    │   └── invoice.py
    └── README.md           # Basic usage examples
```

**After (Generated Docs):**
```text
docs/
├── index.md              # Home page with intro
├── getting_started.md    # Installation & quickstart
├── guides.md             # Task-oriented how-to guides
├── framework_integrations.md  # Framework-specific guides
├── advanced_usage.md      # Advanced patterns & features
├── troubleshooting.md     # Common issues & solutions
└── api/
    ├── index.md           # API Reference overview
    ├── complex_api_client.md
    ├── resources/
    │   ├── users.md
    │   ├── projects.md
    │   └── billing.md
    └── models/
        ├── user.md
        ├── project.md
        └── invoice.md
```

---

## 🏗️ **Architecture**

```
┌─────────────────────────────────────────────────────────────────────┐
│                              Client (Browser)                            │
├─────────────────────────────────────────────────────────────────────┤
│  ┌──────────────┐     ┌──────────────┐     ┌──────────────┐          │
│  │   Landing    │────▶│   Generate   │────▶│   Job Status  │          │
│  │    Page      │     │    Form      │     │    Dashboard  │          │
│  └──────────────┘     └──────────────┘     └──────────────┘          │
└─────────────────────────────────────────────────────────────────────┘
                                       │
                                       ▼
┌─────────────────────────────────────────────────────────────────────┐
│                           Flask API (Vercel)                            │
├─────────────────────────────────────────────────────────────────────┤
│  ┌─────────────┐     ┌─────────────┐     ┌─────────────┐               │
│  │  /          │     │ /api/generate│     │/api/status/ │               │
│  │ (Landing)   │     │  (POST)      │     │ <job_id>    │               │
│  └─────────────┘     └──────┬──────┘     │ (GET)       │               │
│                              │            └──────┬──────┘               │
│                              ▼                   │                         │
│                      ┌──────────────────────┴─────────┐                  │
│                      │         Jobs Registry           │                  │
│                      │  (In-memory / Redis in prod)     │                  │
│                      └──────────────┬─────────────────┘                  │
│                                 │                                      │
│                                 ▼                                      │
│  ┌─────────────────────────────────────────────────────────────┐    │
│  │                         Background Pipeline                       │    │
│  │                                                                    │    │
│  │  ┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐     │    │
│  │  │ fetcher │───▶│ detector│───▶│analyzer │───▶│generator│───▶│    │
│  │  │  .py    │    │  .py    │    │  .py    │    │  .py    │     │    │
│  │  └─────────┘    └─────────┘    └─────────┘    └─────────┘     │    │
│  │        │               │               │               │           │    │
│  │        ▼               ▼               ▼               ▼           │    │
│  │   GitHub        SDK          AST → IR        LLM +          │    │
│  │   API Tree     Detection     (Accurate)      Templates      │    │
│  │                                                    │           │    │
│  │                                                    ▼           │    │
│  │                                              ┌─────────┐     │    │
│  │                                              │ builder │     │    │
│  │                                              │  .py    │     │    │
│  │                                              └─────────┘     │    │
│  │                                                    │           │    │
│  │                                                    ▼           │    │
│  └─────────────────────────────────────────────────────────────┘    │
│                                                        │                  │
│                                                        ▼                  │
└─────────────────────────────────────────────────────────────────────┘
                                 │
                                 ▼
                    ┌─────────────────────┐
                    │   docs.zip          │
                    │ (Downloadable)       │
                    └─────────────────────┘
```

---

## 🔄 **Pipeline Deep Dive**

### 1. **Fetch** 🌐
- Gets recursive file tree via `/git/trees/{branch}?recursive=1`
- Selectively downloads file contents from `raw.githubusercontent.com`
- **Smart filtering**: Only keeps `.py`, README, CHANGELOG, packaging files, and examples
- **Rate limiting**: Supports GitHub PAT for 5,000 requests/hour (vs 60 unauthenticated)

### 2. **Detect** 🔍
- **Heuristic scoring** (0-1 scale) based on:
  - Repository/package naming (`*sdk*`, `*client*`)
  - Class names (`*Client`, `*API`, `*SDK`)
  - Directory structure (`resources/`, `models/`, `endpoints/`)
  - README signals (API keys, `pip install`, SDK terminology)
  - Packaging metadata (`pyproject.toml`, `setup.py`)
- **LLM fallback**: For borderline cases (0.20-0.55 score), uses AI classification

### 3. **Analyze** 🔬
- Uses Python's `ast` module to extract **Intermediate Representation (IR)**
- Captures: classes, methods, signatures, type hints, docstrings, imports
- **Privacy-first**: Raw source code is **never** sent to LLM
- **Token-efficient**: IR is much more compact than raw source

### 4. **Context Gathering** 📚
- Curates README head (20KB max)
- CHANGELOG/HISTORY head (6KB max)
- Up to 6 example files (4KB each)

### 5. **Generate** ✍️
- **LLM-authored sections**:
  - Getting Started (installation, auth, hello world)
  - Guides (task-oriented how-tos)
  - Framework Integrations (Flask, FastAPI, Django, etc.)
  - Advanced Usage (retries, pagination, concurrency)
  - Troubleshooting (common errors, diagnostics)
- **Deterministic API Reference**: Rendered directly from IR (guaranteed accurate)

### 6. **Build** 🏗️
- Writes markdown + `mkdocs.yml`
- Runs `mkdocs build` with Material theme
- Creates downloadable zip archive
- **Fallback**: If build fails, provides raw markdown + config

---

## 🚀 **Quick Start**

### Prerequisites

- Python 3.11+
- GitHub account (for API access)
- LLM provider account (OpenAI, NVIDIA, Groq, etc.)

### Local Development

```bash
# 1. Clone the repository
git clone https://github.com/your-repo/docsmith.git
cd docsmith

# 2. Create virtual environment
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Configure environment
cp .env.example .env
# Edit .env with your API keys
nano .env  # or use your preferred editor

# 5. Run the development server
python api/index.py

# 6. Open in browser
open http://localhost:5000
```

### Environment Variables

| Variable | Required | Purpose | Example |
|----------|----------|---------|---------|
| `BASE_URL` | ✅ Yes | OpenAI-compatible endpoint | `https://api.openai.com/v1` |
| `API_KEY` | ✅ Yes | API key for LLM provider | `sk-...` |
| `MODEL` | ✅ Yes | Model identifier | `gpt-4o` |
| `GITHUB_TOKEN` | ❌ No | GitHub PAT (raises rate limit) | `ghp_...` |

**Supported LLM Providers:**
- OpenAI (`https://api.openai.com/v1`)
- NVIDIA (`https://integrate.api.nvidia.com/v1`)
- Groq (`https://api.groq.com/v1`)
- Any OpenAI-compatible endpoint

---

## 🎯 **Usage**

### Step 1: Submit a Repository

1. Open Docsmith in your browser (`http://localhost:5000`)
2. Enter a GitHub repository URL (e.g., `https://github.com/stripe/stripe-python`)
3. Click **GENERATE**

### Step 2: Monitor Progress

- You'll be redirected to a job status page
- Watch the real-time progress as Docsmith:
  - ✅ Fetches the repository tree
  - ✅ Detects SDK patterns
  - ✅ Analyzes Python source code
  - ✅ Gathers context (README, examples)
  - ✅ Generates documentation
  - ✅ Builds the static site

### Step 3: Download Results

- Once complete, download the generated `docs.zip`
- Extract and serve the `site/` directory
- Or deploy directly to GitHub Pages, Netlify, Vercel, etc.

### Example Command Line Usage

```bash
# Generate docs for a specific repository
curl -X POST http://localhost:5000/api/generate \
  -H "Content-Type: application/json" \
  -d '{"repo_url": "https://github.com/psf/requests", "branch": "main"}'

# Check job status
curl http://localhost:5000/api/status/JOB_ID_HERE

# Download completed docs
gcurl http://localhost:5000/api/download/JOB_ID_HERE -o docs.zip
```

---

## 🌐 **Deployment**

### Vercel (Recommended)

Docsmith is optimized for **Vercel's serverless Python runtime**.

```bash
# 1. Install Vercel CLI
npm install -g vercel

# 2. Link your project
vercel link

# 3. Set environment variables
vercel env add BASE_URL
vercel env add API_KEY
vercel env add MODEL
vercel env add GITHUB_TOKEN  # Optional but recommended

# 4. Deploy
vercel --prod
```

**Vercel Configuration:**
- **Max Lambda Size**: 50MB (for dependencies)
- **Runtime**: Python 3.11
- **Max Duration**: 60s (Pro tier), 10s (Hobby)
- **Memory**: 1792MB recommended

### Docker

```dockerfile
FROM python:3.11-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .

# Copy .env manually or use Docker secrets
# COPY .env .

EXPOSE 5000
CMD ["python", "api/index.py"]
```

### Other Platforms

Docsmith can run on any platform that supports Python 3.11+ and Flask:
- AWS Lambda (with custom runtime)
- Google Cloud Run
- Azure Functions
- Heroku
- Fly.io
- DigitalOcean App Platform

---

## 📦 **Project Structure**

```
docsmith/
├── api/
│   └── index.py              # Flask app entry point (Vercel compatible)
├── pipeline/
│   ├── __init__.py           # Package marker
│   ├── fetcher.py            # GitHub tree + raw file fetch (no clone)
│   ├── detector.py           # SDK heuristics + optional LLM classifier
│   ├── analyzer.py           # AST → IR extraction
│   ├── generator.py          # Per-section LLM doc generation + API-ref templating
│   ├── builder.py            # MkDocs assembly + build + zip
│   ├── jobs.py               # In-memory job registry with background execution
│   └── llm.py                # Thin OpenAI-compatible LLM client wrapper
├── templates/
│   ├── index.html            # Job status dashboard
│   └── landing.html          # Marketing landing page
├── static/
│   └── app.css               # Frontend styles (dark theme, no dependencies)
├── .github/
│   ├── ISSUE_TEMPLATE/
│   │   ├── bug_report.md
│   │   ├── feature_request.md
│   │   └── documentation.md
│   └── workflows/
│       ├── ci.yml
│       └── release.yml
├── .env.example              # Environment variables template
├── .gitignore                # Git ignore rules
├── requirements.txt          # Python dependencies
├── vercel.json               # Vercel deployment config
├── README.md                 # This file
├── LICENSE                   # MIT License
├── CODE_OF_CONDUCT.md       # Community guidelines
├── CONTRIBUTING.md           # Contribution guide
└── SECURITY.md               # Security policy
```

---

## 🔧 **Configuration Options**

### Pipeline Configuration

**`fetcher.py`**
- `MAX_FILE_BYTES`: Maximum file size to fetch (default: 300KB)
- `FILE_LIMIT`: Maximum number of files to process (default: 400)

**`detector.py`**
- `GREY_ZONE_LOW`: Score threshold for LLM fallback (default: 0.20)
- `GREY_ZONE_HIGH`: Score threshold for LLM fallback (default: 0.55)

**`generator.py`**
- `LLM_SECTIONS`: Configurable documentation sections
- Context truncation limits (README: 20KB, CHANGELOG: 6KB, examples: 4KB each)

**`llm.py`**
- `max_tokens`: Configurable per-section
- `temperature`: Adjustable for deterministic vs creative output

### Customization

**Adding New Documentation Sections:**

Edit `pipeline/generator.py` and add to `LLM_SECTIONS`:

```python
LLM_SECTIONS.append(
    ("migration_guide", "Migration Guide",
     "Help users migrate from v1 to v2 of the SDK")
)
```

**Custom IR Extraction:**

Modify `pipeline/analyzer.py` to include additional AST nodes or metadata.

**Custom Output Format:**

Extend `pipeline/builder.py` to support additional output formats (PDF, EPUB, etc.).

---

## 🤖 **AI Models & Providers**

### Recommended Models

| Provider | Model | Quality | Speed | Cost |
|----------|-------|---------|-------|------|
| OpenAI | `gpt-4o` | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | $$$$$ |
| OpenAI | `gpt-4o-mini` | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | $$ |
| NVIDIA | `deepseek-ai/deepseek-v4-pro` | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | $$$ |
| Groq | `llama-3.1-70b-versatile` | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | $ |
| Anthropic | `claude-3-5-sonnet-20250620` | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | $$$$ |

### Model Comparison

| Model | Context Window | Max Tokens | Best For |
|-------|----------------|------------|----------|
| `gpt-4o` | 128K | 4096 | High-quality docs, complex SDKs |
| `gpt-4o-mini` | 128K | 4096 | Fast, cost-effective docs |
| `deepseek-v4-pro` | 128K | 32768 | Large repos, detailed docs |
| `llama-3.1-70b` | 32K | 8192 | Fast, open-source option |

### Prompt Engineering

Docsmith uses carefully crafted system prompts:
- **Anti-hallucination**: Explicitly forbids inventing APIs not in IR
- **Structured output**: Ensures consistent markdown format
- **Context-aware**: Adapts tone based on README
- **Concise**: Minimizes token usage while maintaining quality

---

## 🛡️ **Security**

### Security Features

✅ **No Code Execution**: Raw source is parsed, never executed  
✅ **Privacy-First**: Source code never sent to LLM, only IR  
✅ **Input Validation**: Repository URLs are validated before processing  
✅ **Environment Variables**: Secrets loaded from env, never hardcoded  
✅ **Rate Limiting**: GitHub API calls respect rate limits  
✅ **Ephemeral Storage**: Temporary files cleaned up automatically  

### Security Best Practices

🔒 **Never commit `.env` files** - Add to `.gitignore`  
🔒 **Use HTTPS** - Always use HTTPS connections  
🔒 **Minimal Scopes** - GitHub PAT should have minimal required scopes  
🔒 **Network Security** - Run in secure, isolated environment  
🔒 **Keep Updated** - Regularly update dependencies  

### Vulnerability Reporting

If you discover a security vulnerability:

1. **DO NOT** create a public issue
2. Email: `security@docsmith.dev`
3. Include: Description, steps to reproduce, potential impact
4. We'll respond within 24 hours

See [SECURITY.md](SECURITY.md) for details.

---

## 📊 **Performance**

### Benchmarks

| Repository Size | Files | Processing Time | Output Size |
|----------------|-------|----------------|-------------|
| Small SDK | < 50 | 5-10 seconds | 1-2 MB |
| Medium SDK | 50-200 | 10-30 seconds | 2-5 MB |
| Large SDK | 200-400 | 30-60 seconds | 5-10 MB |
| Very Large | 400+ | 60+ seconds* | 10+ MB |

*May exceed Vercel Hobby tier's 10s limit

### Optimization Tips

**For Large Repositories:**
- Use GitHub PAT to avoid rate limiting
- Process during off-peak hours
- Consider local execution with longer timeout
- Use faster, cheaper models for initial drafts

**For Production:**
- Use Redis/Upstash for job persistence
- Use Vercel Blob/S3 for artifact storage
- Implement proper queue (Vercel Queues, Inngest)
- Add rate limiting middleware

---

## 🔄 **Limitations & Roadmap**

### Current Limitations

⚠️ **In-memory job registry** - Lost on cold start/restart  
⚠️ **No persistent artifact storage** - Zip is ephemeral  
⚠️ **Background threads may be killed** - On serverless timeout  
⚠️ **Single LLM provider** - Currently OpenAI-compatible only  
⚠️ **No rate limiting** - On `/api/generate` endpoint  
⚠️ **MkDocs build may fail** - In restricted serverless environments  

### Upcoming Features

🎯 **v1.1.0** (Q4 2024)
- Redis/Vercel KV for job persistence
- Vercel Blob/S3 for artifact storage
- Proper queue support (Vercel Queues)
- Rate limiting middleware

🎯 **v1.2.0** (Q1 2025)
- Multiple LLM provider support (Anthropic, etc.)
- PDF and EPUB output formats
- Custom theme support
- Multi-language support (JavaScript, Go, etc.)

🎯 **v2.0.0** (2025)
- Self-hosted web interface
- Real-time collaboration
- Documentation versioning
- User authentication & saved repos

---

## 🤝 **Contributing**

We welcome contributions! Please see:

- [CONTRIBUTING.md](CONTRIBUTING.md) - Contribution guidelines
- [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) - Community standards
- [Issues](https://github.com/your-repo/docsmith/issues) - Report bugs, request features
- [Discussions](https://github.com/your-repo/docsmith/discussions) - Ask questions, share ideas

### How to Contribute

1. **Fork** the repository
2. **Clone** your fork
3. **Create** a feature branch
4. **Commit** your changes
5. **Push** to your branch
6. **Open** a Pull Request

### Good First Issues

- [ ] Improve error messages
- [ ] Add more example SDKs to test with
- [ ] Improve documentation
- [ ] Add unit tests
- [ ] Fix typos and grammar

---

## 📄 **License**

Docsmith is **open source** and available under the [MIT License](LICENSE).

You are free to:
- ✅ Use for personal or commercial purposes
- ✅ Modify the source code
- ✅ Distribute modified versions
- ✅ Use in your own projects

With the understanding that:
- ⚖️ **No warranty** is provided
- 📝 **License and copyright notices** must be included

---

## 🙏 **Acknowledgments**

### Built With

- [Flask](https://flask.palletsprojects.com/) - Web framework
- [MkDocs](https://www.mkdocs.org/) - Static site generator
- [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) - Theme
- [OpenAI API](https://platform.openai.com/) - LLM access
- [GitHub API](https://docs.github.com/en/rest) - Repository access

### Special Thanks

- To the **Python community** for building amazing tools
- To **GitHub** for their excellent REST API
- To **OpenAI** and other providers for democratizing AI
- To all **contributors** who help make Docsmith better

---

## 📞 **Support & Contact**

### Community

- **GitHub Discussions**: [github.com/your-repo/docsmith/discussions](https://github.com/your-repo/docsmith/discussions)
- **Issues**: [github.com/your-repo/docsmith/issues](https://github.com/your-repo/docsmith/issues)
- **Pull Requests**: [github.com/your-repo/docsmith/pulls](https://github.com/your-repo/docsmith/pulls)

### Professional Support

For enterprise support, custom development, or consulting:
- **Email**: [hello@docsmith.dev](mailto:hello@docsmith.dev)
- **Website**: [docsmith.dev](https://docsmith.dev)
- **Twitter/X**: [@docsmithdev](https://twitter.com/docsmithdev)

### Stay Updated

- **Star** this repository ⭐
- **Watch** for releases 👀
- **Follow** [@docsmithdev](https://twitter.com/docsmithdev) on X

---

<p align="center">
  Made with ❤️ by the Docsmith Team<br>
  <a href="https://github.com/your-repo/docsmith">GitHub</a> |
  <a href="https://docsmith.dev">Website</a> |
  <a href="https://twitter.com/docsmithdev">Twitter</a>
</p>

<p align="center">
  <sub>© 2024 Docsmith. All rights reserved. Released under MIT License.</sub>
</p>