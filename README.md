# peasy-pdf

[![crates.io](https://img.shields.io/crates/v/peasy-pdf)](https://crates.io/crates/peasy-pdf)
[![docs.rs](https://docs.rs/peasy-pdf/badge.svg)](https://docs.rs/peasy-pdf)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

Async Rust client for the [PeasyPDF](https://peasypdf.com) API — merge, split, compress, rotate, and watermark PDF files. Built with reqwest, serde, and tokio.

Built from [PeasyPDF](https://peasypdf.com), a free online PDF toolkit with tools for merging, splitting, compressing, rotating, and watermarking PDF documents.

> **Try the interactive tools at [peasypdf.com](https://peasypdf.com)** — [PDF Tools](https://peasypdf.com/), [PDF Glossary](https://peasypdf.com/glossary/), [PDF Guides](https://peasypdf.com/guides/)

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

## Learn More

- **Tools**: [PDF Merge](https://peasypdf.com/pdf/merge-pdf/) · [PDF Split](https://peasypdf.com/pdf/split-pdf/) · [PDF Compress](https://peasypdf.com/pdf/compress-pdf/) · [All Tools](https://peasypdf.com/)
- **Guides**: [PDF Compression Guide](https://peasypdf.com/guides/pdf-compression-guide/) · [How to Merge PDF Files](https://peasypdf.com/guides/how-to-merge-pdf-files/) · [All Guides](https://peasypdf.com/guides/)
- **Glossary**: [PDF](https://peasypdf.com/glossary/pdf/) · [Linearization](https://peasypdf.com/glossary/linearization/) · [OCR](https://peasypdf.com/glossary/ocr/) · [All Terms](https://peasypdf.com/glossary/)
- **Formats**: [PDF](https://peasypdf.com/formats/pdf/) · [PDF/A](https://peasypdf.com/formats/) · [All Formats](https://peasypdf.com/formats/)
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
