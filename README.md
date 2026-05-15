# Maryland SDAT Property Lookup — Wicomico County GIS

A GitHub Pages redirect tool that bridges your **Parcels1 ArcGIS feature layer** pop-ups to the [Maryland SDAT Real Property Search](https://sdat.dat.maryland.gov/RealProperty/Pages/default.aspx).

SDAT uses a JavaScript single-page app that blocks direct deep-links. This page accepts parcel fields as URL parameters, displays the property details, opens SDAT in a new tab, and shows a pre-filled manual search form as a fallback.

---

## 🚀 Deploy to GitHub Pages

1. Create a new GitHub repository (e.g. `sdat-lookup`)
2. Upload `index.html` to the root of the repo
3. Go to **Settings → Pages → Source → Deploy from branch → main → / (root)**
4. Your live URL will be:
   ```
   https://YOUR-USERNAME.github.io/sdat-lookup/
   ```

---

## 🗺️ ArcGIS Pop-up Setup — Arcade Expression

In your ArcGIS Online pop-up, add a new **Attribute Expression** (Arcade) and paste the following. This uses the `acctid` field which is the most reliable SDAT search key.

### Recommended: Account ID link (best match)

```arcade
var base = "https://YOUR-USERNAME.github.io/sdat-lookup/";

// acctid is the Property Account Identifier — most reliable SDAT lookup
var acct    = UrlEncode($feature.acctid);
var owner   = UrlEncode($feature.ownname1);
var address = UrlEncode($feature.address);
var jurscode = UrlEncode($feature.jurscode);

return base + "?acctid=" + acct
           + "&ownname1=" + owner
           + "&address=" + address
           + "&jurscode=" + jurscode;
```

Then in the pop-up configuration, add a **Link** element and set its URL to this expression. Label it something like "View on SDAT" or "🔍 SDAT Property Record".

### Fallback: Street address link

```arcade
var base = "https://YOUR-USERNAME.github.io/sdat-lookup/";

var strtnum = UrlEncode($feature.strtnum);
var strtnam = UrlEncode($feature.strtnam);
var owner   = UrlEncode($feature.ownname1);
var address = UrlEncode($feature.address);

return base + "?strtnum=" + strtnum
           + "&strtnam=" + strtnam
           + "&ownname1=" + owner
           + "&address=" + address;
```

### All fields (most complete)

```arcade
var base = "https://YOUR-USERNAME.github.io/sdat-lookup/";

return base
  + "?acctid="   + UrlEncode($feature.acctid)
  + "&ownname1=" + UrlEncode($feature.ownname1)
  + "&address="  + UrlEncode($feature.address)
  + "&strtnum="  + UrlEncode($feature.strtnum)
  + "&strtnam="  + UrlEncode($feature.strtnam)
  + "&map="      + UrlEncode($feature.map)
  + "&parcel="   + UrlEncode($feature.parcel)
  + "&jurscode=" + UrlEncode($feature.jurscode);
```

---

## 🔗 URL Parameters Accepted

These match the exact field names in the **Parcels1 FeatureServer (Layer 12)** schema:

| URL Param | Layer Field | Alias |
|-----------|------------|-------|
| `acctid`  | `acctid`   | Account ID ✅ best |
| `ownname1`| `ownname1` | Owner Name 1 |
| `address` | `address`  | Address |
| `strtnum` | `strtnum`  | Street Address Number |
| `strtnam` | `strtnam`  | Street Address Name |
| `map`     | `map`      | Map |
| `parcel`  | `parcel`   | Parcel |
| `jurscode`| `jurscode` | Jurisdiction Code |

---

## 🏛️ How It Works

1. User clicks the SDAT link in the ArcGIS pop-up
2. This page opens, reads the URL parameters, and displays parcel details
3. SDAT opens automatically in a new tab
4. The page shows a pre-filled manual search form the user can copy from to complete the SDAT search
5. If popups are blocked, a manual "Open SDAT" button appears

---

## ⚠️ Notes

- SDAT prohibits automated data scraping — this tool opens SDAT for the user to interact with manually; it does not scrape or extract data.
- The **Account ID** (`acctid`) is the most reliable search method. Always include it if available.
- The county is always **WICOMICO COUNTY** for this layer — it's hardcoded in the instructions shown to the user.
- Data source: [Maryland iMap Parcel Boundaries](https://geodata.md.gov/imap/rest/services/PlanningCadastre/MD_ParcelBoundaries/MapServer/0)
