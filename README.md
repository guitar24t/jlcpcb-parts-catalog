# jlcpcb-parts-catalog

Every part JLCPCB lists as in stock — Basic, Preferred and Extended, with
their parametrics — as a SQLite database, rebuilt daily. This is the offline
catalog behind the **JLCPCB Parts Filler** KiCad plugin's value-based part
suggestions; the plugin downloads it instead of reading JLCPCB's parts listing
itself (~750 requests and ~15 minutes).

## What is published

The release tagged [`catalog`](../../releases/tag/catalog) always holds:

* `catalog-<UTC stamp>.sqlite3.gz` — the database (~80 MB packed, ~550 MB
  unpacked). Table `parts`: LCSC number, category, sub-category, MPN,
  manufacturer, package, JLCPCB library class, description, datasheet,
  stock, price breaks, and the parametric attributes as JSON.
* `catalog.json` — the manifest: which file is current, when its data was
  read, its SHA-256 and sizes. Clients fetch this first and check the
  download against it.

Each day's manifest is also committed to this repository, as a record of
every build.

## How it is built

`.github/workflows/publish-catalog.yml` runs the plugin's own crawler
(`jlcfiller catalog --build`) against JLCPCB's public parts listing. A build
is published only if it accounts for every part JLCPCB says each category
holds, passes the plugin's integrity and coverage checks, and loses no more
than 20% of any class or major category against the catalog already
published — so a bad night leaves yesterday's catalog in place.

The part data is JLCPCB's; this repository claims no rights over it.
