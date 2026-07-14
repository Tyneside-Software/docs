# Tyneside Store — white-label over RST Wholesale

## Model

| Piece | Detail |
|--------|--------|
| **Supplier** | [RST Wholesale Ltd](https://rst-wholesale.com/) (Shopify: `rst-wholesale.myshopify.com`) |
| **Storefront** | [tyneside.store](https://tyneside.store) — Tyneside-branded experience |
| **Stock / fulfilment** | RST (we do not hold inventory) |
| **Pricing** | Customer pays **RST price + 2%** |
| **The 2%** | **100% donated** to [Tyneside Charity](https://tyneside.charity) |

Example: RST £10.00 → customer **£10.20** (£10.00 supply · £0.20 charity).

## Why

- Better UX than a raw wholesale shop for our customers and story
- Transparent supplier relationship (not a fake warehouse)
- Retail margin is impact funding for Forest Hall free cleans

## Technical

| Layer | Approach |
|--------|----------|
| **Now** | Static storefront in `site-generator` (`sites/store`); catalogue sync via `scripts/sync_rst_catalog.py` from public Shopify JSON |
| **Next** | Wholesale account + Shopify Storefront / Admin API for cart, checkout automation, and order push to RST |
| **Pages repo** | `Tyneside-Software/tyneside.store` |

### Sync

```powershell
python scripts/sync_rst_catalog.py
python -m site_generator store
```

Writes `sites/store/catalog/catalog.json` and `index.json` (featured products + collections with customer pricing).

## Legal / ops notes

- Confirm RST wholesale / dropship terms before taking live paid orders
- VAT / consumer rights / returns policy must match how RST ships
- Charity donation flow (which entity receives the 2%, gift aid if applicable) — formalise with proper accounts

## Related

- [Forest Hall free cleans](../charity/forest-hall-free-cleans.md)
- [Jobs engine](../software/jobs-engine.md)