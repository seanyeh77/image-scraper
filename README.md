# Image Scraper

A high-performance Python tool for batch downloading images from Google Images with quality control and intelligent organization. This tool automates large-scale image collection using Selenium WebDriver, parallel processing, and automatic inventory management.

## Overview

The Image Scraper addresses the challenge of collecting large quantities of quality-controlled images for machine learning, design, research, or dataset creation. Whether you're building training datasets, collecting reference images, or conducting visual research, this tool provides fast and reliable batch image downloading capabilities.

Key capabilities:
- Download images in bulk based on keyword searches from Google Images
- Automatically filter images by minimum dimensions to ensure quality
- Organize downloads into structured directories with Excel inventory tracking
- Process multiple keywords simultaneously using parallel execution
- Handle both URL-based and base64-encoded images seamlessly
- Prevent duplicate downloads across multiple runs

## Features

- **High Performance**: Multi-threaded execution optimized for available CPU cores
- **Headless Browsing**: Selenium WebDriver with Chrome in headless mode for efficient background operation
- **Quality Control**: Automatic image size validation with configurable minimum dimensions
- **Duplicate Prevention**: Tracks downloaded images to avoid redundant downloads

## Get Started

### Prerequisites

Before you begin, ensure you have:
- [uv](https://docs.astral.sh/uv/getting-started/installation/) installed on your system. uv is an extremely fast Python package installer and resolver.
- Google Chrome browser installed
- Internet connection for downloading ChromeDriver and images

### Installation

1. **Clone or navigate to the project:**
```bash
git clone git@gitlab.seanyeh77.com:seanyeh77/image-scaper.git
cd "Image Scraper"
```

2. **Install all dependencies:**
```bash
uv sync
```

This will automatically create a virtual environment and install all required dependencies.

3. **Install package in editable mode (optional):**
```bash
uv pip install -e .
```

This allows you to import the module from anywhere in your project.

### Quick Start

Run the image scraper with example keywords:

```bash
uv run python src/main.py
```

This will:
1. Read keywords from `input/key_word_list.txt`
2. Search Google Images for each keyword
3. Download filtered images to `images/<keyword>/` directories
4. Generate Excel inventories tracking all downloads

## Architecture

The tool follows a functional design optimized for batch processing and maintainability:

- **`src/main.py`**: Complete image scraping pipeline with modular functions
  - **`load_keywords()`**: Parse comma-separated keywords from input file
  - **`initialize_chrome_driver()`**: Configure headless Chrome WebDriver
  - **`fetch_image_links()`**: Scroll pages and extract image URLs from Google Images
  - **`verify_image_size()`**: Validate images meet minimum dimension requirements
  - **`save_images()`**: Download and save validated images with sequential naming
  - **`update_excel_inventory()`**: Track downloads in Excel files
  - **`search_and_download()`**: Orchestrate complete search and download workflow

**Design Principles:**
- Functional separation for easy testing and maintenance
- Configurable constants for customization without code changes
- Type hints throughout for better code documentation
- Parallel execution with thread pooling for optimal performance

## Usage

### Basic Keyword Search

Create your keyword list and run a simple search:

```python
# 1. Create input/key_word_list.txt with comma-separated keywords
# Content: apple,banana,orange

# 2. Run the scraper
uv run python src/main.py
```

**Output:**
```
Processing 3 keywords: apple, banana, orange
Searching for images of 'apple'...
Found 47 images for 'apple'
Successfully downloaded 32 images for 'apple' (meeting size requirements)
Searching for images of 'banana'...
Found 51 images for 'banana'
Successfully downloaded 38 images for 'banana' (meeting size requirements)
...
All downloads completed!
```

### Customizing Search Parameters

Adjust constants in `src/main.py` for different requirements:

```python
# Modify image quality requirements
DEFAULT_MIN_WIDTH = 300      # Increase minimum width to 300px
DEFAULT_MIN_HEIGHT = 300     # Increase minimum height to 300px

# Adjust page scrolling for more images
DEFAULT_SCROLLS = 20         # Scroll more times to load more images
SCROLL_DELAY = 3             # Wait longer between scrolls

# Change timeout settings
REQUEST_TIMEOUT = 15         # Increase timeout for slow connections
```

### Using the Image Scraper Programmatically

Import and use the scraper in your own scripts:

```python
from pathlib import Path
from src.main import search_and_download, load_keywords

# Load keywords from file
keywords = load_keywords(Path("input/key_word_list.txt"))

# Download images for a single keyword
search_and_download(
    keyword="machine learning",
    min_width=500,
    min_height=500
)

# Process multiple keywords
for keyword in keywords:
    search_and_download(keyword, min_width=300, min_height=300)
```

## Configuration Options

The tool provides multiple configuration constants in `src/main.py`:

| Constant | Default | Description |
|----------|---------|-------------|
| `DEFAULT_MIN_WIDTH` | 150 | Minimum image width in pixels |
| `DEFAULT_MIN_HEIGHT` | 150 | Minimum image height in pixels |
| `DEFAULT_SCROLLS` | 10 | Number of page scrolls to load more images |
| `SCROLL_DELAY` | 2 | Seconds between each scroll |
| `PAGE_LOAD_DELAY` | 3 | Initial page load wait time |
| `REQUEST_TIMEOUT` | 10 | Timeout for image downloads (seconds) |
| `IMAGES_DIR` | "images" | Output directory for downloaded images |
| `INPUT_DIR` | "input" | Input directory for keyword list |
| `KEYWORD_FILE` | "key_word_list.txt" | Keyword list filename |
| `MASTER_INVENTORY_FILE` | "master_inventory.xlsx" | Master inventory filename |

### Chrome WebDriver Configuration

The tool configures Chrome with these options:
```python
--headless                  # Run without GUI
--no-sandbox               # Required for some Linux environments
--disable-gpu              # Improves stability in headless mode
--disable-dev-shm-usage    # Prevents memory issues in containers
```

## Project Structure

```
Image Scraper/
├── README.md                    # Project documentation
├── pyproject.toml              # Python dependencies and project metadata
├── uv.lock                     # Dependency lock file
├── .python-version             # Python version specification
├── .gitignore                  # Git ignore rules
│
├── input/                      # Input data directory
│   └── key_word_list.txt      # Comma-separated keyword list
│
├── src/                        # Source code directory
│   ├── __init__.py            # Package marker
│   └── main.py                # Core image scraping implementation
│       ├── scroll_page()                 # Page scrolling function
│       ├── verify_image_size()          # Image dimension validation
│       ├── initialize_chrome_driver()    # WebDriver configuration
│       ├── fetch_image_links()          # Image URL extraction
│       ├── download_image()             # Image download handler
│       ├── save_images()                # Batch image saving
│       ├── update_excel_inventory()     # Excel tracking
│       ├── search_and_download()        # Main workflow orchestration
│       ├── load_keywords()              # Keyword file parser
│       └── main()                       # Entry point
│
└── images/                     # Generated output directory (created on first run)
    ├── <keyword1>/            # Images for keyword1
    │   ├── <keyword1>_1.jpg
    │   ├── <keyword1>_2.jpg
    │   ├── ...
    │   └── <keyword1>_inventory.xlsx
    ├── <keyword2>/            # Images for keyword2
    │   ├── <keyword2>_1.jpg
    │   ├── ...
    │   └── <keyword2>_inventory.xlsx
    └── master_inventory.xlsx  # Master inventory file
```

## Dependencies

- **Web Automation**: `selenium` - WebDriver for Chrome automation
- **Driver Management**: `webdriver-manager` - Automatic ChromeDriver installation and updates
- **HTML Parsing**: `beautifulsoup4` - HTML/XML parsing, `lxml` - Fast XML/HTML parser
- **Image Processing**: `pillow` - Image format handling and dimension validation
- **HTTP Requests**: `requests` - HTTP library for image downloading
- **Excel Generation**: `openpyxl` - Excel file creation and manipulation
- **Data Processing**: Built-in Python libraries (`pathlib`, `concurrent.futures`, `typing`)

See [pyproject.toml](pyproject.toml) for the complete dependency list.

## Development Tools

### Running Tests
```bash
uv run pytest
```

### Code Linting
```bash
uv run ruff check .
```

### Type Checking
```bash
uv run mypy .
```

### Code Formatting
```bash
uv run ruff format .
```