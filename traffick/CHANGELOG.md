# Traffick: Trade & Barter Overhaul changelog

**Unreleased** lists changes that haven't shipped yet.

## Unreleased

### Added

- Added Patreon and permanent Discord invite badges to the README and Foundry listing.

### Changed

- Simplified the README and Foundry listing with shorter feature summaries and collapsible guides for trade controls, payments, appraisals, and recovery.

## [14.2.0] - 2026-10-18

### Added

- **Allow fractional payments**, off by default, enables decimal payment and change while preserving existing fractional wallet balances in either mode.
- Sealed unidentified containers trade as indivisible bundles. Player catalogs, searches, offers, proposal comparisons, and receipts conceal all descendants; GMs retain full detail and may quote the bundle or barter.
- Multiple item rarities display as **Rarity varies**, with a permitted rarity list and neutral styling.

- This changelog, linked from the Foundry package page and the README.
- **Customer layout** setting in Configure Traffick, per client: **Tabs** shows one customer at a time with an **All customers** overview; **Stacked cards** keeps every customer card visible; **Three panes** places customer inventories on the left, the offers in the middle, and the merchant on the right, with two dividers and a **Customer inventories** view.
- Column headings name the characters, for example **Thorin gives** and **Gundren gives**; a player’s own column stays **You give**.
- A reset icon beside each changed field in Configure Traffick restores that one setting’s default.
- Shopkeeper greeting: the GM writes a line on the merchant that customers see above the catalog.
- Coin purses on the customer cards and the merchant header, shown to the GM and to each character’s own players.
- dnd5e item cards on hover and rarity-coloured names for stock you own or control.
- A receipt card whispered to the GM and the customers when a trade completes (world setting), plus an optional per-client coin sound.
- Newly offered rows slide in, the columns that can take a dragged item glow, and the three-pane layout gains larger portraits and subtle pane textures.
- **Shop window** in the merchant’s **More actions** menu: exactly the wares customers see, with their names and prices and a count of hidden stock. Offer, drag, and the eye still work there.
- The merchant menu publishes or hides every item the current search and filters show; the header eyes still act on selected rows.
- Stock on the table is tinted: amber in the merchant pane with the holding customer named on hover, blue in customer inventories, and a green edge on published stock (**Highlight stock on the table**, per client, on by default).
- **Pricing tools**, a world setting that is off by default: a lane footer for the GM with **Due**, **Paid**, and **Outstanding**, plus **Quote at list price**, **Haggle** (sell and buy percentages of list price, with optional automatic quotes for new merchant offers), **Ask for payment**, and **Give change**. Off, deals stay bartered.
- A request inbox at the top of the trade pane gathers every customer's pending requests, with a count in the toolbar.
- **Offer** shows a customer caret on hover when several customers are present, and hovering it highlights the lane that would receive the item.
- **Set up shop** for a merchant with nothing published: a line under the merchant header opens one dialog for greeting, publish all, and, with pricing tools, automatic quotes; also in the merchant menu.

### Changed

- New agreed quotes require **Pricing tools**. Existing quotes remain visible, enforceable, and editable when it is turned off; clearing a quote returns that offer to barter.
- **Add coins** and **Edit coins** sit beneath each editable offer. Item totals are labelled **Item value** and explain that they are informational.
- Settings group everyday trading and appearance separately from **Prices & payments**, with currency and inventory options collapsed. Players get **Preferences** with troubleshooting tucked away.
- Sealed contents require renewed approval after inventory changes. Automatic list-price quotes exclude sealed bundles and their hidden contents.
- Transferred goods retain rarity sets, uses, activities, and conditional enchantments. References within the transferred batch follow the goods; referenced items remain separate when merging would break a reference.
- Settlement reads stored wallet balances rather than rounded native prepared values. Whole-coin payments preserve fractional remainders. Decimal payments, shared merchant reservations, and wallet writes use exact arithmetic and reject unsafe results.

- Requires Foundry V14, build 14.367 or later; verified on 14.369. Requires and verifies dnd5e 6.0.6.
- Updated the package description and author information.
- The module's website link now goes to its Foundry package page.
- The trade panel was rebuilt around resizable panes with draggable dividers that remember their widths. Tabs and stacked cards put merchant stock on the left and customer trades on the right.
- Each customer lane places the two give columns side by side; in tabs and stacked cards the customer's inventory sits directly under the items they give.
- Coins sit under each side's items with their own edit button, replacing the **Payment & change** disclosure. Item-value summaries moved into the column headings.
- Inventory rows are a single line with a ×n stock count. Offer-row remove, menu, and ± controls brighten on hover or focus, and the catalog eye now publishes or hides stock directly.
- The **All panes**, **Customer trades**, and **Merchant** views moved from the **Session** menu into the toolbar.
- Headings use one typeface throughout the panel.
- **Customer layout** now defaults to **Three panes** for new clients; saved choices are kept.
- The GM's pending requests moved from each customer lane into the inbox; players keep theirs in their own lane.

### Fixed

- The trade window close button and three-dot menu remain usable after an exchange or GM disconnection.
- Appraisal and identification validate dnd5e 6 native ability, skill, and tool check messages, including deferred and offline appraisal reconciliation.
- Native cards use typed property descriptors, and native tooltips retain masked, unknown, range, and unidentified price states after native rendering.

### Removed

- Per-pane corner resize grips and saved pane heights; the window edges set the height.
- The **Payment & change** disclosure and the standalone **Show** menu.
