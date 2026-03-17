# peasy-pdf

[![crates.io](https://img.shields.io/crates/v/peasy-pdf)](https://crates.io/crates/peasy-pdf)
[![docs.rs](https://docs.rs/peasy-pdf/badge.svg)](https://docs.rs/peasy-pdf)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

Async Rust client for the [PeasyPDF](https://peasypdf.com) API — merge, split, rotate, and compress PDF files. Built with reqwest, serde, and tokio.

Built from [PeasyPDF](https://peasypdf.com), a comprehensive PDF toolkit offering free online tools for merging, splitting, rotating, compressing, and converting PDF documents. The site includes detailed guides on PDF optimization, accessibility best practices, and format conversion, plus a glossary covering terms from linearization to OCR to PDF/A archival compliance.

> **Try the interactive tools at [peasypdf.com](https://peasypdf.com)** — [Merge PDF](https://peasypdf.com/pdf/merge-pdf/), [Split PDF](https://peasypdf.com/pdf/split-pdf/), [Compress PDF](https://peasypdf.com/pdf/compress-pdf/), [Rotate PDF](https://peasypdf.com/pdf/rotate-pdf/), and more.

<p align="center">
  <img src="demo.gif" alt="peasy-pdf demo — PDF merge, split, and compress tools in Rust terminal" width="800">
</p>

## Table of Contents

- [Install](#install)
- [Quick Start](#quick-start)
- [What You Can Do](#what-you-can-do)
  - [PDF Document Operations](#pdf-document-operations)
  - [Browse Reference Content](#browse-reference-content)
  - [Search and Discovery](#search-and-discovery)
- [API Client](#api-client)
  - [Available Methods](#available-methods)
- [Learn More About PDF Tools](#learn-more-about-pdf-tools)
- [Also Available](#also-available)
- [Peasy Developer Tools](#peasy-developer-tools)
- [License](#license)

## Install

```toml
[dependencies]
peasy-pdf = "0.2.0"
tokio = { version = "1", features = ["full"] }
```

Or via cargo:

```bash
cargo add peasy-pdf
```

## Quick Start

```rust
use peasy_pdf::Client;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = Client::new();

    // List available PDF tools
    let tools = client.list_tools(&Default::default()).await?;
    for tool in &tools.results {
        println!("{}: {}", tool.name, tool.description);
    }

    Ok(())
}
```

## What You Can Do

### PDF Document Operations

The Portable Document Format (PDF) was created by Adobe in 1993 and became an open ISO standard (ISO 32000) in 2008. Today PDF is the most widely used format for document exchange, supporting text, images, forms, digital signatures, and embedded multimedia. PeasyPDF provides tools for every common PDF workflow — from merging invoices into a single file to compressing scanned documents for email delivery.

| Operation | Slug | Description |
|-----------|------|-------------|
| Merge PDF | `pdf-merge` | Combine multiple PDF documents into one file |
| Split PDF | `pdf-split` | Extract specific pages or split into individual files |
| Compress PDF | `pdf-compress` | Reduce file size by optimizing images and removing metadata |
| Rotate PDF | `pdf-rotate` | Rotate pages by 90, 180, or 270 degrees |
| PDF to PNG | `pdf-to-png` | Convert PDF pages to high-resolution PNG images |

```rust
use peasy_pdf::Client;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = Client::new();

    // Retrieve the PDF merge tool and inspect its capabilities
    let tool = client.get_tool("pdf-merge").await?;
    println!("Tool: {}", tool.name);              // PDF merge tool name
    println!("Description: {}", tool.description); // How merging works

    // List all available PDF tools with pagination
    let opts = peasy_pdf::ListOptions {
        page: Some(1),
        limit: Some(20),
        ..Default::default()
    };
    let tools = client.list_tools(&opts).await?;
    println!("Total PDF tools available: {}", tools.count);

    Ok(())
}
```

Learn more: [Merge PDF Tool](https://peasypdf.com/pdf/merge-pdf/) · [How to Merge PDF Files](https://peasypdf.com/guides/how-to-merge-pdf-files/) · [PDF Compression Guide](https://peasypdf.com/guides/pdf-compression-guide/)

### Browse Reference Content

PeasyPDF includes a comprehensive glossary of document format terminology and in-depth guides for common workflows. The glossary covers foundational concepts like PDF linearization (web-optimized PDFs that load page-by-page), OCR (optical character recognition for scanned documents), DPI (dots per inch for print-quality output), and PDF/A (the ISO 19005 archival standard used by governments and libraries worldwide).

| Term | Description |
|------|-------------|
| [PDF](https://peasypdf.com/glossary/pdf/) | Portable Document Format — ISO 32000 open standard |
| [PDF/A](https://peasypdf.com/glossary/pdfa/) | Archival PDF subset (ISO 19005) for long-term preservation |
| [DPI](https://peasypdf.com/glossary/dpi/) | Dots per inch — resolution metric for print and rasterization |
| [OCR](https://peasypdf.com/glossary/ocr/) | Optical character recognition for searchable scanned PDFs |
| [Rasterization](https://peasypdf.com/glossary/rasterization/) | Converting vector PDF content to pixel-based images |

```rust
use peasy_pdf::Client;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = Client::new();

    // Browse the PDF glossary for document format terminology
    let glossary = client.list_glossary(&peasy_pdf::ListOptions {
        search: Some("linearization".into()), // Search for web-optimized PDF concepts
        ..Default::default()
    }).await?;
    for term in &glossary.results {
        println!("{}: {}", term.term, term.definition);
    }

    // Read a specific guide on PDF accessibility best practices
    let guide = client.get_guide("accessible-pdf-best-practices").await?;
    println!("Guide: {} (Level: {})", guide.title, guide.audience_level);

    Ok(())
}
```

Learn more: [PDF Glossary](https://peasypdf.com/glossary/) · [Accessible PDF Best Practices](https://peasypdf.com/guides/accessible-pdf-best-practices/) · [How to Convert PDF to Images](https://peasypdf.com/guides/how-to-convert-pdf-to-images/)

### Search and Discovery

The API supports full-text search across all content types — tools, glossary terms, guides, use cases, and format documentation. Search results are grouped by content type, making it easy to find exactly what you need for a specific PDF workflow.

```rust
use peasy_pdf::Client;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = Client::new();

    // Search across all PDF content — tools, glossary, guides, and formats
    let results = client.search("compress pdf", Some(20)).await?;
    println!("Found {} tools, {} glossary terms, {} guides",
        results.results.tools.len(),
        results.results.glossary.len(),
        results.results.guides.len(),
    );

    // Discover format conversion paths — what can PDF convert to?
    let conversions = client.list_conversions(&peasy_pdf::ListConversionsOptions {
        source: Some("pdf".into()), // Find all formats PDF can be converted to
        ..Default::default()
    }).await?;
    for c in &conversions.results {
        println!("{} -> {}", c.source_format, c.target_format);
    }

    Ok(())
}
```

Learn more: [REST API Docs](https://peasypdf.com/developers/) · [All PDF Tools](https://peasypdf.com/)

## API Client

The client wraps the [PeasyPDF REST API](https://peasypdf.com/developers/) with strongly-typed Rust structs using serde deserialization.

```rust
use peasy_pdf::Client;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = Client::new();
    // Or with a custom base URL:
    // let client = Client::with_base_url("https://custom.example.com");

    // List tools with filters
    let opts = peasy_pdf::ListOptions {
        page: Some(1),
        limit: Some(10),
        search: Some("compress".into()),
        ..Default::default()
    };
    let tools = client.list_tools(&opts).await?;

    // Get a specific tool
    let tool = client.get_tool("pdf-merge").await?;
    println!("{}: {}", tool.name, tool.description);

    // Search across all content
    let results = client.search("compress", Some(20)).await?;
    println!("Found {} tools", results.results.tools.len());

    // Browse the glossary
    let glossary = client.list_glossary(&peasy_pdf::ListOptions {
        search: Some("pdf-a".into()),
        ..Default::default()
    }).await?;
    for term in &glossary.results {
        println!("{}: {}", term.term, term.definition);
    }

    // Discover guides
    let guides = client.list_guides(&peasy_pdf::ListGuidesOptions {
        category: Some("pdf".into()),
        ..Default::default()
    }).await?;
    for guide in &guides.results {
        println!("{} ({})", guide.title, guide.audience_level);
    }

    // List format conversions
    let conversions = client.list_conversions(&peasy_pdf::ListConversionsOptions {
        source: Some("pdf".into()),
        ..Default::default()
    }).await?;

    Ok(())
}
```

### Available Methods

| Method | Description |
|--------|-------------|
| `list_tools(&opts)` | List tools (paginated, filterable) |
| `get_tool(slug)` | Get tool by slug |
| `list_categories(&opts)` | List tool categories |
| `list_formats(&opts)` | List file formats |
| `get_format(slug)` | Get format by slug |
| `list_conversions(&opts)` | List format conversions |
| `list_glossary(&opts)` | List glossary terms |
| `get_glossary_term(slug)` | Get glossary term |
| `list_guides(&opts)` | List guides |
| `get_guide(slug)` | Get guide by slug |
| `list_use_cases(&opts)` | List use cases |
| `search(query, limit)` | Search across all content |
| `list_sites()` | List Peasy sites |
| `openapi_spec()` | Get OpenAPI specification |

Full API documentation at [peasypdf.com/developers/](https://peasypdf.com/developers/).
OpenAPI 3.1.0 spec: [peasypdf.com/api/openapi.json](https://peasypdf.com/api/openapi.json).

## Learn More About PDF Tools

- **Tools**: [Merge PDF](https://peasypdf.com/pdf/merge-pdf/) · [Split PDF](https://peasypdf.com/pdf/split-pdf/) · [Compress PDF](https://peasypdf.com/pdf/compress-pdf/) · [Rotate PDF](https://peasypdf.com/pdf/rotate-pdf/) · [PDF to PNG](https://peasypdf.com/pdf/pdf-to-png/) · [All Tools](https://peasypdf.com/)
- **Guides**: [How to Merge PDF Files](https://peasypdf.com/guides/how-to-merge-pdf-files/) · [PDF Compression Guide](https://peasypdf.com/guides/pdf-compression-guide/) · [Accessible PDF Best Practices](https://peasypdf.com/guides/accessible-pdf-best-practices/) · [How to Convert PDF to Images](https://peasypdf.com/guides/how-to-convert-pdf-to-images/) · [All Guides](https://peasypdf.com/guides/)
- **Glossary**: [PDF](https://peasypdf.com/glossary/pdf/) · [PDF/A](https://peasypdf.com/glossary/pdfa/) · [DPI](https://peasypdf.com/glossary/dpi/) · [OCR](https://peasypdf.com/glossary/ocr/) · [Rasterization](https://peasypdf.com/glossary/rasterization/) · [All Terms](https://peasypdf.com/glossary/)
- **Formats**: [All Formats](https://peasypdf.com/formats/)
- **API**: [REST API Docs](https://peasypdf.com/developers/) · [OpenAPI Spec](https://peasypdf.com/api/openapi.json)

## Also Available

| Language | Package | Install |
|----------|---------|---------|
| **Python** | [peasy-pdf](https://pypi.org/project/peasy-pdf/) | `pip install "peasy-pdf[all]"` |
| **TypeScript** | [peasy-pdf](https://www.npmjs.com/package/peasy-pdf) | `npm install peasy-pdf` |
| **Go** | [peasy-pdf-go](https://pkg.go.dev/github.com/peasytools/peasy-pdf-go) | `go get github.com/peasytools/peasy-pdf-go` |
| **Ruby** | [peasy-pdf](https://rubygems.org/gems/peasy-pdf) | `gem install peasy-pdf` |

## Peasy Developer Tools

Part of the [Peasy Tools](https://peasytools.com) open-source developer ecosystem.

| Package | PyPI | npm | crates.io | Description |
|---------|------|-----|-----------|-------------|
| **peasy-pdf** | [PyPI](https://pypi.org/project/peasy-pdf/) | [npm](https://www.npmjs.com/package/peasy-pdf) | [crate](https://crates.io/crates/peasy-pdf) | PDF merge, split, rotate, compress — [peasypdf.com](https://peasypdf.com) |
| peasy-image | [PyPI](https://pypi.org/project/peasy-image/) | [npm](https://www.npmjs.com/package/peasy-image) | [crate](https://crates.io/crates/peasy-image) | Image resize, crop, convert, compress — [peasyimage.com](https://peasyimage.com) |
| peasy-audio | [PyPI](https://pypi.org/project/peasy-audio/) | [npm](https://www.npmjs.com/package/peasy-audio) | [crate](https://crates.io/crates/peasy-audio) | Audio trim, merge, convert, normalize — [peasyaudio.com](https://peasyaudio.com) |
| peasy-video | [PyPI](https://pypi.org/project/peasy-video/) | [npm](https://www.npmjs.com/package/peasy-video) | [crate](https://crates.io/crates/peasy-video) | Video trim, resize, thumbnails, GIF — [peasyvideo.com](https://peasyvideo.com) |
| peasy-css | [PyPI](https://pypi.org/project/peasy-css/) | [npm](https://www.npmjs.com/package/peasy-css) | [crate](https://crates.io/crates/peasy-css) | CSS minify, format, analyze — [peasycss.com](https://peasycss.com) |
| peasy-compress | [PyPI](https://pypi.org/project/peasy-compress/) | [npm](https://www.npmjs.com/package/peasy-compress) | [crate](https://crates.io/crates/peasy-compress) | ZIP, TAR, gzip compression — [peasytools.com](https://peasytools.com) |
| peasy-document | [PyPI](https://pypi.org/project/peasy-document/) | [npm](https://www.npmjs.com/package/peasy-document) | [crate](https://crates.io/crates/peasy-document) | Markdown, HTML, CSV, JSON conversion — [peasyformats.com](https://peasyformats.com) |
| peasytext | [PyPI](https://pypi.org/project/peasytext/) | [npm](https://www.npmjs.com/package/peasytext) | [crate](https://crates.io/crates/peasytext) | Text case conversion, slugify, word count — [peasytext.com](https://peasytext.com) |

## License

MIT
