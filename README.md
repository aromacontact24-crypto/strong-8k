

This Python script automates **QA-style website visits** with the following features:

- Uses a **fresh Chromium instance + proxy per visit** (ensuring a new IP each time).
- Simulates **human-like interaction**:
  - Scroll up/down.
  - Random dwell times on pages.
- **GA4 QA Injection**:
  - Marks sessions as internal/developer traffic for exclusion in Google Analytics 4.
- Automatically finds and clicks:
  - **Primary CTA** (e.g., "Strong 8K").
  - **Android CTA** ("Strong 8K For Android") on the **same page**.
- Optional **proxy IP verification** per visit.
- Detailed **visit summaries** printed with [Rich](https://github.com/Textualize/rich).

---

## 📦 Requirements

- **Python** 3.8+
- **Playwright** (Chromium)
- Other dependencies: `rich`, `pathlib`, `argparse`

Install requirements:

```bash
pip install playwright rich
playwright install chromium
````

---

## ⚙️ Configuration

You can edit the top of the script to set:

* **Proxy settings**
* **Client TLS cert/key paths** (if required)
* **User-Agent**
* Dwell time ranges for:

  * First page visit
  * Android page

Example in-script config:

```python
PROXY_HOST = "p.webshare.io"
PROXY_PORT = "80"
PROXY_USER = "username"
PROXY_PASS = "password"

CLIENT_CERT_PATH = "cert.pem"
CLIENT_KEY_PATH  = "key.pem"
```

---

## 📄 URLs File

Create a `urls.txt` file with one target URL per line:

```
https://example.com
https://another-example.com/page
```

Lines starting with `#` are ignored.
If protocol is missing, `https://` is automatically added.

---

## 🚀 Usage

Basic run (visit each URL once):

```bash
python script.py --urls urls.txt
```

### Options

| Option        | Description                                 | Default    |
| ------------- | ------------------------------------------- | ---------- |
| `--urls, -u`  | Path to file with URLs                      | `urls.txt` |
| `--count, -n` | Total visits to perform (0 = each URL once) | `0`        |
| `--delay, -d` | Delay between visits (seconds)              | `1.0`      |
| `--dwell-min` | Minimum dwell time (first page)             | `30`       |
| `--dwell-max` | Maximum dwell time (first page)             | `60`       |
| `--check-ip`  | Check & display outbound IP each visit      | *disabled* |

### Example

Run 10 visits total, 2 seconds delay between visits, with proxy IP check:

```bash
python script.py -u urls.txt -n 10 -d 2 --check-ip
```

---

## 🛠 How It Works

1. **Plan Visits**
   Reads URLs, removes duplicates, creates visit sequence based on `--count`.

2. **For Each Visit**

   * Launches new Chromium instance via Playwright with **fresh proxy connection**.
   * Navigates to the URL.
   * Injects GA4 `traffic_type=qa` markers.
   * Searches for "Strong 8K" CTA, clicks it.
   * Simulates user scrolling and dwell time.
   * Searches for "Strong 8K For Android" link **on the current page**, clicks it.
   * Simulates Android page dwell time.

3. **Summary Output**
   Displays success/failure for each step, dwell times, final URLs, elapsed time.

---

## 📊 Example Output

```
Planned visits: 5  (unique per cycle: 3)
Random dwell (first block): 30s–60s
Android dwell window: 20s–40s
─────────────────────────────
Visit: 1/5
URL  : https://example.com
Dwell: ~45s
✓ Page loaded
CTA candidate: Strong 8K
✓ Click success & target loaded
Exercising page & waiting ~45s…
Android CTA on page: Strong 8K For Android
✓ Android click success
─────────────────────────────
```

---

## 🔒 Notes

* **Use responsibly** — this script is intended for QA/testing, not traffic manipulation.
* Ensure your proxy provider allows automated browsing.
* GA4 filtering requires that your analytics setup recognizes `traffic_type=qa`.

---

## 📜 License

MIT License — free to modify and distribute.

```

---

If you want, I can also make a **shorter README.md** with just setup and usage instructions.  
But this one is detailed enough for dev handover and GitHub hosting.
```
