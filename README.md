# HTML-Table-Structured-Data-Scraper(Browser Console)
This project is a reusable, JavaScript-based web-scraping utility for systematically extracting structured data from complex HTML tables and forms—directly from your browser, without any external dependencies.
***
## Key Features

- **Zero install, zero dependencies:** Run as a single paste-and-go script in your browser console.
- **Robust label-value association:** Designed for pages with inconsistent HTML, label-based fields, or nested tables.
- **Advanced cleaning:** Removes special whitespace, stray punctuation, label artifacts, and supports multi-byte/Unicode spaces.
- **Custom logic per column:** Map “theme”, “category”, or other columns from any source field (e.g., “Project Short Title”, “Meta Tags”, etc).
- **Flexible classification:** Uses easy-to-modify rules for auto-categorization (e.g., "product" vs "service", or other buckets).
- **Outputs CSV** instantly ready for Excel, Google Sheets, or further transformation.
- **Easily adaptable:** Works for any tabular or field-based web application (academic, business, inventory, etc).

## Usage

1. **Open the target listing/search page in your browser (must be logged in and able to see links to details).**
2. **Open the browser DevTools Console (F12, Ctrl+Shift+I, or Cmd+Opt+I).**
3. **Paste and run the script** from [`extractor.js`](./extractor.js).[browser console]
4. **A CSV file will be downloaded** when processing finishes.
5. **Open in Excel, Google Sheets, or your analytics platform.**

## Example Extraction Targets

- Research, funding, policy, or competition results
- Company directories, product specs, meeting/deliverable indexes
- Any web system with semi-structured project/task/record details

## Script Highlights

- Fully DOM-driven: no reliance on fragile class/id selectors.
- Automatically follows detail links in listings, and processes each details view in sequence.
- Modular field extraction and cleaning ensures highly reusable logic.
- Handles label, textarea, and one-to-many table structures.

## Customization

- **Change which fields to extract:** Edit the core script’s `headers` and extraction function array.
- **Logic for cell cleaning/classification:** Modify the `clean()`, `classifyProductService()` or add similar functions.
- **Apply to any system:** Just update the label-matching terms as required for your site.

## AI Assistance & Acknowledgments

Much of the design, iterative enhancement, and field mapping logic was developed with the guidance of AI Assistant(s) such as ChatGPT and Bing Copilot—applied to real user feedback and HTML samples.  
All logic and code is presented as an open, modifiable foundation for your own workflows.

Feel free to **share, fork, and adapt** to your own technical, research, or operations projects.

***
