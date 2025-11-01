## Console Extraction Script

Paste the following JavaScript code into your browser DevTools Console to batch-extract all records as described above (see detailed usage in "How It Works" below):

```javascript
// Universal Table & Form-Based Data Extractor for Browser Console
(async function() {
    function clean(val) {
        if (!val) return "";
        return val.replace(/^[:\s\u00A0]+/, "").replace(/\s+$/, "").replace(/:$/, "");
    }
    function extractField(doc, labelPart) {
        let row = Array.from(doc.querySelectorAll("td")).find(td =>
            td.textContent.trim().toLowerCase().startsWith(labelPart.toLowerCase())
        );
        if (!row) return "";
        let txt = row.textContent.split(":").slice(1).join(":");
        return clean(txt);
    }
    function extractTextarea(doc, theLabel) {
        const labelEl = Array.from(doc.querySelectorAll('label')).find(
            l => l.innerText.trim().toLowerCase() === theLabel.trim().toLowerCase()
        );
        if (labelEl) {
            const ta = labelEl.parentElement.querySelector('textarea');
            if (ta) return ta.value.trim();
        }
        return "";
    }
    function extractYear(doc) {
        let start = extractField(doc, 'Start Date');
        let end = extractField(doc, 'End Date');
        let sYear = start.match(/\d{4}/), eYear = end.match(/\d{4}/);
        if (sYear && eYear) return sYear[0] === eYear[0] ? sYear[0] : sYear[0] + "-" + eYear[0];
        let pnum = extractField(doc, "Project Number");
        let y = pnum.match(/\d{4}/);
        return y ? y[0] : "";
    }
    // Extract Project Short Title for both Theme and Product/Service logic
    function extractShortTitle(doc) {
        return clean(extractField(doc, "Project Short Title"));
    }
    // Product or service logic—change/add keywords as needed
    function classifyProductService(shortTitle) {
        const st = (shortTitle || "").toLowerCase();
        if (st.match(/product|system|application|integration/)) return "product";
        return "service";
    }

    let projectLinks = Array.from(document.querySelectorAll('table tr td a')).filter(a => a.href.includes('project_display'));
    let headers = [
        "No.",
        "SCPO Serial No.",
        "Year",
        "Theme",
        "Product or Service project",
        "Project title",
        "Brief Description",
        "School/Center",
        "PI",
        "Client",
        "Country"
    ];
    let allRows = [];
    for (let i = 0; i < projectLinks.length; i++) {
        let url = projectLinks[i].href;
        let html = await fetch(url).then(r=>r.text());
        let doc = new DOMParser().parseFromString(html, "text/html");
        let shortTitle = extractShortTitle(doc);
        allRows.push([
            (i+1).toString(),
            clean(extractField(doc, "Serial No")),
            extractYear(doc),
            shortTitle,
            classifyProductService(shortTitle),
            clean(extractField(doc, "Project Title")),
            extractTextarea(doc, "Project Description"),
            clean(extractField(doc, "School")),
            clean(extractField(doc, "Project Initiator")),
            clean(extractField(doc, "Project Sponsor(s)/Client(s)")),
            clean(extractField(doc, "Sponsor Country")) || clean(extractField(doc, "Country of actions"))
        ]);
    }
    function downloadCSV(headers, rows, filename) {
        let csv = headers.join(",") + "\n";
        for(let row of rows){
            csv += row.map(x=>`"${(x??'').replace(/"/g,'""')}"`).join(",") + "\n";
        }
        let blob = new Blob([csv], {type: 'text/csv'});
        let link = document.createElement('a');
        link.href = URL.createObjectURL(blob);
        link.download = filename;
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
    }
    downloadCSV(headers, allRows, "scpo_projects_master01.csv");
})();
```

***

## Technical Explanation

***

### How It Works

- **Query all detail links** on the current listing page.
- For each link:
    - **Fetch** the project detail page with `fetch()`.
    - **Parse** the fetched HTML into a DOM object using `DOMParser`.
    - For each field (title, short title, serial, description, year, client, etc):
        - **Locate fields by their label** using robust `querySelector` and `textContent`.
        - Remove label text, colons, whitespace, and non-breaking spaces.
        - For “Theme”, use “Project Short Title” value.
        - For “Product or Service project”, auto-tag as `product` if keywords like “product”/“system”/“application” are in Short Title, else tag as `service`.
    - **Combine values into a flat array** matching your CSV columns.
- When all rows are processed:
    - **Assemble a CSV string** with headers and rows.
    - **Create a download link** (`Blob` and `<a>`) and trigger download.

### Tech Notes

- Runs entirely client-side in your browser—no external libraries or data privacy concerns.
- Designed for highly variable HTML/web layouts (robust to extra spaces, strange characters, misaligned fields).
- Easy to reconfigure: just tweak `headers`, or the field and keyword arrays.
- Handles Unicode, non-breaking spaces, and common web quirks found in academic or enterprise portals.

***
