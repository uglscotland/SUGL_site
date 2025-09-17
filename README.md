# SUGL site 

Here’s the short version of how you’d get text from a Google Doc and feed it into a GitHub Pages site with near-realtime updates via GitHub Actions:

---

## **1. Make the Google Doc shareable as data**

* Publish the Doc to the web (`File > Share > Publish to web`)
* Grab the published URL. You can often fetch it directly as HTML or plain text without authentication.
* Example:

  ```
  https://docs.google.com/document/d/{DOCUMENT_ID}/export?format=txt
  ```

  This gives raw text without needing API keys (faster to implement, but less structured).

---

## **2. Fetch content in a GitHub Action**

In your workflow YAML:

```yaml
name: Update site from Google Doc
on:
  schedule:
    - cron: "*/5 * * * *" # every 5 minutes
  workflow_dispatch:

jobs:
  update-site:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Fetch Google Doc text
        run: |
          curl -s "https://docs.google.com/document/d/${DOC_ID}/export?format=txt" \
            -o content.txt
      - name: Update HTML
        run: |
          echo "<html><body><pre>$(cat content.txt)</pre></body></html>" > index.html
      - name: Commit changes
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "41898282+github-actions[bot]@users.noreply.github.com"
          git add index.html
          git commit -m "Update site content" || echo "No changes"
          git push
```

---

## **3. Publish via GitHub Pages**

* In repo settings, enable GitHub Pages for the `main` branch root (or `gh-pages` if you prefer).
* Every workflow run updates `index.html`, commits, and pushes → Pages site auto-updates.


# Contents & ideas

- [ ] Event Calendar
- [ ] Competition Rules
- [ ] Competition Results
- [ ] Competition Schedule
- [ ] Training weekend schedule
- [ ] Club portal
- [ ] Participant portal
- [ ] Club links - info for joining clubs