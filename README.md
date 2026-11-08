# nukarama
A hyper-fast Manifest V3 media interceptor and content filter

# Nukarama

A high-performance Manifest V3 Chrome extension that filters unwanted Instagram Feed posts and Reels using local blocklists.

*Note: This repository serves as the official issue tracker, documentation hub, and community blocklist exchange for Nukarama. The core extension is closed-source to protect the proprietary microtask interception engine.*

## Privacy & Security

Nukarama requires host permissions for `instagram.com` to evaluate captions and video nodes. 
* **100% Local Processing:** All custom rules, stats, and settings are stored locally via `chrome.storage.local`. 
* **Zero Telemetry:** The extension contains no remote server calls, analytics, or tracking scripts. See the [Privacy Policy](PRIVACY.md) for full details.


## The Problem

Standard ad-blockers and CSS-hiding scripts fail against Instagram's architecture for two primary reasons:
1. **Audio Bleed:** Hiding a `<video>` container via CSS (`display: none` or `opacity: 0`) does not prevent the media from downloading or the audio from playing in the background.
2. **React Virtualization:** Instagram continuously recycles a small pool of DOM nodes during scrolling. Extensions that attach state to physical DOM nodes break as soon as that container is reused for a new post.

## How It Works (Under the Hood)

Nukarama bypasses traditional element hiding by operating strictly at the browser's microtask level, intercepting content before the rendering pipeline completes.

* **Zero-Flash Media Clamping:** Uses a `MutationObserver` on the shared DOM to catch `<video>` elements at `readyState = 0`. It forces `.muted = true` and `.pause()` before the browser's compositor can paint the first frame or flush the audio buffer.
* **Shortcode Identity Tracking:** Instead of tracking recycled React nodes, state is anchored to unique URL shortcodes (e.g., `/reel/C8x.../`). This ensures accurate filtering during rapid, virtualized scrolling.
* **Programmatic Skipping:** When a blocked Reel is detected, the extension dispatches a synthetic scroll event, smoothly advancing to the next unblocked container.
* **Stealth Mode:** Toggle between visual "Nuke Alerts" for feedback or run the engine completely silently in the background.

## Performance Benchmarks

The extension was built with strict performance budgets to prevent main-thread blocking during heavy scrolling:
* **Execution Footprint:** ~1.6 ms per 5-second idle window (0.032% main-thread load).
* **Net Active Overhead:** ~0% CPU penalty during active scrolling.

## Installation

Install the official, verified release directly from the Chrome Web Store:  
**[Add to Chrome](https://chromewebstore.google.com/detail/nukarama/feicnhpkdoabmdcjoldokgagbfokaibi?authuser=0&hl=en)**

## Starter Blocklist (AdVaporizer)

Nukarama supports importing and exporting custom JSON blocklists. 

Included in this repository is the `AdVaporizer.json` starter list, which contains a set of trigger words used in common commercial spam and unwanted topics. 
1. Download `AdVaporizer.json` from this repository.
2. Open the Nukarama popup in your browser.
3. Click **Import JSON** and select the file.

## Bug Reports & Feature Requests

If you encounter an edge case where a post slips through or a layout shifts unexpectedly, please open an issue in this repository. Include:
* The type of post (Feed or Reel).
* The specific browser version.
* A description of the behavior.
