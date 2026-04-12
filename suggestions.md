# Suggestions for Improvement

## Day Maps

Each day section previously had an embedded interactive map (220px tall, dark theme, teal dot markers, lazy-loaded via IntersectionObserver). Maps were removed temporarily due to tile provider reliability and sharing constraints.

**The map data is preserved** — all 13 maps with their center coordinates, zoom levels, and bilingual (EN/ZH) marker labels are documented below and can be restored.

### Why maps were dropped

The itinerary is primarily shared as a raw HTML file via WhatsApp. This means recipients open it from a `content://` URI (Android) or equivalent — not from an HTTP server. This rules out tile providers and JS mapping libraries that require a proper HTTP/HTTPS origin.

| Open method | Origin | Notes |
|---|---|---|
| WhatsApp share → Android | `content://` | Most recipients |
| Double-click in file explorer | `file:///` | Developer/desktop |
| Local HTTP server | `http://localhost:8080` | Dev only |

### Options evaluated

#### Option A — Leaflet + Stadia Maps (was implemented, then removed)
- No API key required
- Dark tile style: `alidade_smooth_dark`
- **Problem:** Stadia Maps CDN was unreliable (tiles not loading). Also blocked on `content://` and `file://` origins in some browsers.
- **Status:** Removed.

#### Option B — Leaflet + OpenFreeMap
- No API key required
- Dark tile style available
- Newer CDN, reportedly more reliable than Stadia
- URL: `https://tiles.openfreemap.org/styles/dark/{z}/{x}/{y}.png`
- **Problem:** Still blocked on `content://` / `file://` origins — same fundamental issue.

#### Option C — Google Maps JavaScript API
- Requires a Google Cloud API key + billing account (free $200/month credit)
- Highly reliable CDN
- Dark theme via inline JSON styles array (no Cloud Console setup needed)
- **Problem:** Only works on `http://` / `https://` origins. Blocked on `content://`, `file://`, and any non-HTTP origin. Wrong tool for a shared HTML file.
- **Bonus if re-added:** Could fix language-switching bug (current Leaflet impl only sets popup labels at init time; Google Maps InfoWindows can be updated after render).

#### Option D — Static map images (e.g. Google Static Maps API or Mapbox Static)
- Rendered as `<img>` tags — works from any origin including `content://`
- No JS library needed
- Requires API key (Google Static Maps API or Mapbox)
- Not interactive (no pan/zoom)
- Images load from CDN so still requires internet connection

#### Option E — Pre-rendered SVG/PNG maps (fully offline)
- Embed map images directly as base64 in the HTML or as separate files
- Works completely offline, any origin
- No API key
- Not interactive
- Manual to create; would need updating if itinerary changes

### Recommendation

If maps are re-added for file-sharing use, **Option D (static map images)** is the most pragmatic:
- Works on `content://` (WhatsApp share)
- Familiar Google Maps visual style
- Requires one API key setup, low cost
- Acceptable trade-off: read-only, but sufficient to show "where is this place"

If the itinerary is eventually hosted on a URL (GitHub Pages, Netlify, etc.), **Option C (Google Maps JS)** becomes the best choice — more interactive, reliable, and the implementation plan is already detailed in `/plans/curried-squishing-frog.md`.

---

### Map data (preserved for restoration)

```js
const dayMaps = {
  'map-1':  { center: [50.050, 8.571], zoom: 14, markers: [
    { lat: 50.050, lng: 8.571, en: 'Sheraton Frankfurt Airport', zh: '法兰克福机场喜来登酒店' }
  ]},
  'map-2':  { center: [64.136, -21.895], zoom: 12, markers: [
    { lat: 63.985, lng: -22.606, en: 'Keflavík Airport', zh: '凯夫拉维克机场' },
    { lat: 64.136, lng: -21.895, en: 'Reykjavík', zh: '雷克雅未克' }
  ]},
  'map-3':  { center: [64.148, -21.927], zoom: 14, markers: [
    { lat: 64.142, lng: -21.927, en: 'Hallgrímskirkja', zh: '哈尔格林姆斯教堂' },
    { lat: 64.150, lng: -21.932, en: 'Harpa Concert Hall', zh: 'Harpa音乐厅' },
    { lat: 64.153, lng: -21.942, en: 'Old Harbour', zh: '旧港' }
  ]},
  'map-4':  { center: [64.148, -21.940], zoom: 14, markers: [
    { lat: 64.148, lng: -21.940, en: 'Aurora Reykjavík Museum', zh: '极光博物馆' }
  ]},
  'map-5':  { center: [64.138, -21.940], zoom: 14, markers: [
    { lat: 64.133, lng: -21.948, en: 'National Museum of Iceland', zh: '冰岛国家博物馆' },
    { lat: 64.148, lng: -21.936, en: 'Settlement Exhibition', zh: '定居展览馆' }
  ]},
  'map-6':  { center: [63.880, -22.449], zoom: 13, markers: [
    { lat: 63.880, lng: -22.449, en: 'Blue Lagoon', zh: '蓝湖温泉' }
  ]},
  'map-7':  { center: [64.143, -21.912], zoom: 13, markers: [
    { lat: 64.140, lng: -21.923, en: 'Laugavegur', zh: '劳格韦格尔购物街' },
    { lat: 64.148, lng: -21.943, en: 'Reykjavík Art Museum', zh: '雷克雅未克艺术博物馆' },
    { lat: 64.147, lng: -21.943, en: 'Tjörnin Pond', zh: '特约尔宁湖' },
    { lat: 64.141, lng: -21.876, en: 'Laugardalslaug Pool', zh: '拉加达拉泳池' }
  ]},
  'map-8':  { center: [63.510, -19.510], zoom: 9, markers: [
    { lat: 63.616, lng: -19.989, en: 'Seljalandsfoss', zh: '塞里雅兰瀑布' },
    { lat: 63.532, lng: -19.512, en: 'Skógafoss', zh: '斯科加瀑布' },
    { lat: 63.404, lng: -19.057, en: 'Reynisfjara Black Sand Beach', zh: '雷尼斯黑沙滩' },
    { lat: 63.402, lng: -19.128, en: 'Dyrhólaey', zh: '迪霍拉里海角' },
    { lat: 63.419, lng: -19.006, en: 'Vík', zh: '维克小镇' }
  ]},
  'map-9':  { center: [64.078, -16.230], zoom: 9, markers: [
    { lat: 64.078, lng: -16.231, en: 'Jökulsárlón Glacier Lagoon', zh: '冰川湖' },
    { lat: 64.066, lng: -16.179, en: 'Diamond Beach', zh: '钻石海滩' },
    { lat: 64.254, lng: -15.204, en: 'Höfn', zh: '霍芬' }
  ]},
  'map-10': { center: [64.141, -21.876], zoom: 14, markers: [
    { lat: 64.141, lng: -21.876, en: 'Laugardalslaug Pool', zh: '拉加达拉泳池' }
  ]},
  'map-11': { center: [64.180, -20.620], zoom: 9, markers: [
    { lat: 64.256, lng: -21.130, en: 'Þingvellir National Park', zh: '辛格韦德利国家公园' },
    { lat: 64.313, lng: -20.301, en: 'Geysir / Strokkur', zh: '间歇泉' },
    { lat: 64.327, lng: -20.122, en: 'Gullfoss', zh: '黄金瀑布' },
    { lat: 64.041, lng: -20.886, en: 'Kerið Crater', zh: '凯里斯火山口' },
    { lat: 64.135, lng: -20.313, en: 'Secret Lagoon, Flúðir', zh: '秘密温泉' }
  ]},
  'map-12': { center: [50.107, 8.684], zoom: 13, markers: [
    { lat: 50.112, lng: 8.684, en: 'Ruby Louise Hotel, Frankfurt', zh: '法兰克福鲁比路易斯酒店' },
    { lat: 50.101, lng: 8.684, en: 'Sachsenhausen', zh: '萨克森豪森' }
  ]},
  'map-13': { center: [50.111, 8.681], zoom: 15, markers: [
    { lat: 50.111, lng: 8.682, en: 'Römerberg Christmas Market', zh: '罗马广场圣诞市集' },
    { lat: 50.114, lng: 8.679, en: 'Hauptwache S-Bahn', zh: '豪普特瓦赫地铁站' }
  ]}
};
```
