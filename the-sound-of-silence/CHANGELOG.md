# The Sound of Silence changelog

**Unreleased** lists changes that haven't shipped yet.

## Unreleased

## [14.15.7] - 2026-11-07

This is the last release that supports Foundry V13. From the next release on, The Sound of Silence will require Foundry V14.

### Fixed

- Skipping to another track while a crossfade is still running no longer breaks playback. The track that was fading in keeps playing and becomes the one that fades out, the old track finishes with a short tail, and the playlist shows the right track. This covers Next, Previous, double-clicking Next and picking a track directly, for the GM and for players.
- Stopping a playlist during a crossfade fades both tracks out from where they are instead of jumping the old track back to full volume and cutting the new one.
- Changing a track's volume while it fades no longer disturbs the fade or rewrites the saved volume. The new volume applies when the fade finishes.
- Picking another track while an internal loop was mid-crossfade used to leave the loop playing underneath the new track until Stop All. The loop now stops with its track.
- A loop segment that ends exactly at the end of its track no longer advances to the next track on its first pass.
- Pausing during a loop crossfade no longer loses one pass of a limited loop count.
- Internal loops on playlists set to the Environment or Interface channel now play in that channel instead of Music.
- A track that stops while its audio was still fading can no longer start a silence gap or advance the playlist on its own.
- Segment skips sent to players during a loop crossfade are applied once the crossfade finishes instead of being dropped.
- The Fire Now button in Currently Playing works again after its first use.
- A paused track with skip-intro loops shows its paused position instead of the loop start.
- Turning Silence or Crossfade on or off from the sidebar updates the buttons on every connected client.

### Changed

- Crossfades are capped at half the length of the outgoing track, the same way Foundry caps its own fades, so a long crossfade no longer skips straight through a short track.

## [14.15.6] - 2026-11-02

### Added

- A Questions and chat link to the Discord on the GitHub new-issue page.

### Changed

- Updated documentation links to Bakshi's Bazaar Archives and the permanent Discord invite.
- Shorter, clearer hint on the diagnostics setting.

### Removed

- Removed the developer diagnostics section from the README and listing.
- Removed Ko-fi support links from the Markdown README and generated HTML listing.

## [14.15.5] - 2026-10-14

### Added

- This changelog, linked from the Foundry package page and the README.

### Changed

- Supports Foundry V13 and V14. I haven't retested V13 with this update yet.
- Updated the package description and author information.
- Reorganized the playback guide for loops, crossfades, silence gaps, Soundscapes, and playback controls.
- Clarified the libWrapper requirement for playlist playback, loops, and crossfading.
