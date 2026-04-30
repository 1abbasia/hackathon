# The Wall - NYC Neighborhood Drawing App

## PROJECT OVERVIEW
A hyperlocal art canvas where each NYC neighborhood is a drawable canvas shaped like that neighborhood. MVP for 1-hour hackathon demo.

## CRITICAL CONSTRAINTS
- **Time budget:** 30 minutes of coding total
- **No external map libraries:** Use pure SVG, no Mapbox/Leaflet
- **No backend:** Everything client-side with localStorage
- **No real-time syncing:** Single-user drawing only
- **No authentication:** No login system
- **Mobile-friendly but desktop-first demo**

## TECH STACK (LOCKED)
- **Framework:** React 18 + Vite
- **Styling:** Tailwind CSS (keep it simple)
- **Drawing:** HTML5 Canvas API
- **Storage:** localStorage only
- **Deployment:** Vercel

### Dependencies (ONLY THESE)
```json
{
  "react": "^18.3.1",
  "react-dom": "^18.3.1"
}
```
No other npm packages allowed.

---

## FEATURE SCOPE

### ✅ MUST HAVE (Core MVP)

#### 1. Map View (`/src/components/MapView.jsx`)
- **Visual:** Full-screen SVG showing 8 NYC neighborhoods
- **Neighborhoods (EXACTLY THESE 8):**
  1. Lower East Side (LES)
  2. Williamsburg
  3. Astoria
  4. Greenwich Village
  5. Harlem
  6. Park Slope
  7. Upper West Side
  8. Bushwick

- **Each neighborhood polygon:**
  - Simplified to 6-8 coordinate points max
  - Different pastel fill color (stored in neighborhoods.js constant)
  - 2px white stroke
  - Hover: increase opacity to 90%, show neighborhood name tooltip
  - Click: opens that neighborhood's canvas

- **Layout:**
  - SVG viewBox="0 0 1000 1200" (portrait NYC shape)
  - Dark background: `#0f172a`
  - Header: "The Wall" (text-4xl, white, top-center)
  - Subheader: "Draw your block. Watch the city." (text-sm, gray-400)

#### 2. Canvas View (`/src/components/CanvasView.jsx`)
- **Trigger:** Click any neighborhood on map
- **Layout:**
  - Fullscreen overlay (z-index: 50)
  - Background: `#1e293b`
  - Canvas centered, clipped to neighborhood shape using SVG clip-path
  - Canvas size: 800x600px (scaled to fit viewport)

- **Header bar (fixed top):**
  - ← Back button (left) - returns to map
  - "[Neighborhood Name]" (center, text-2xl, white)
  - "Week 1: Spring Awakening" (below name, text-sm, gray-400)

- **Drawing tools (fixed bottom bar):**
  - 5 color circles (40px diameter each):
    - #fbbf24 (yellow)
    - #fb923c (orange)  
    - #f472b6 (pink)
    - #a78bfa (purple)
    - #60a5fa (blue)
  - Active color: 3px white ring around it
  - No brush size selector (fixed at 3px)
  - No eraser (keep simple)

- **Drawing behavior:**
  - Mouse down: start path
  - Mouse move (while down): draw line to cursor
  - Mouse up: end path
  - Touch events: same behavior for mobile
  - Use Canvas 2D context, `lineCap: 'round'`, `lineJoin: 'round'`

- **Save on exit:**
  - When clicking "← Back", save canvas as data URL to localStorage
  - Key format: `wall-${neighborhoodId}` (e.g., `wall-lower-east-side`)
  - Value: `canvas.toDataURL('image/png')`

#### 3. Data Layer (`/src/data/neighborhoods.js`)
Export a constant array of 8 neighborhood objects:

```javascript
export const neighborhoods = [
  {
    id: 'lower-east-side',
    name: 'Lower East Side',
    color: '#fef3c7',
    // SVG path points (simplified polygon)
    points: [[320, 580], [420, 560], [440, 680], [360, 720], [300, 660]]
  },
  // ... 7 more neighborhoods
]
```

**Point coordinate rules:**
- X range: 100-900 (within 1000px viewBox width)
- Y range: 200-1100 (leaving space for header)
- Points must form closed polygon (first != last, SVG will auto-close)
- Shapes should roughly match NYC geography (Manhattan vertical, Brooklyn right, Queens top-right)

#### 4. Pre-populated Sample Art (`/src/utils/seedArt.js`)
- Function: `initializeSampleArt()`
- Runs once on app load (check localStorage flag: `wall-initialized`)
- Pre-populate 3 neighborhoods with base64 sample images:
  - **Williamsburg:** Abstract colorful squiggles
  - **Astoria:** Text saying "Astoria ❤️ NYC" 
  - **Greenwich Village:** Hearts and flowers doodles

**Implementation:**
```javascript
const sampleArt = {
  'wall-williamsburg': 'data:image/png;base64,...',
  'wall-astoria': 'data:image/png;base64,...',
  'wall-greenwich-village': 'data:image/png;base64,...'
}

export function initializeSampleArt() {
  if (!localStorage.getItem('wall-initialized')) {
    Object.entries(sampleArt).forEach(([key, value]) => {
      localStorage.setItem(key, value)
    })
    localStorage.setItem('wall-initialized', 'true')
  }
}
```

You'll generate simple base64 PNGs (100x100px, simple shapes) for the 3 samples.

#### 5. App Structure (`/src/App.jsx`)
```javascript
// State:
- currentView: 'map' | 'canvas'
- selectedNeighborhood: neighborhood object | null

// On mount:
- Call initializeSampleArt()

// Render logic:
- If currentView === 'map': render <MapView />
- If currentView === 'canvas': render <CanvasView />

// Props passed:
- MapView: { neighborhoods, onSelectNeighborhood }
- CanvasView: { neighborhood, onClose }
```

---

### ❌ EXPLICITLY NOT BUILDING

- **NO Gallery/Grid view** (cut for time - map thumbnails are enough)
- **NO voting system**
- **NO archive/history**
- **NO user authentication**
- **NO geolocation verification**
- **NO real-time multi-user drawing**
- **NO database/backend**
- **NO AI-generated prompts** (just hardcode "Week 1: Spring Awakening")
- **NO undo/redo**
- **NO brush size/opacity controls**
- **NO shape tools** (only freehand)
- **NO image upload/stickers**

---

## FILE STRUCTURE (EXACT)
├── src/
│   ├── App.jsx                 # Main app component
│   ├── main.jsx               # Vite entry point
│   ├── index.css              # Tailwind imports only
│   ├── components/
│   │   ├── MapView.jsx        # SVG map with clickable neighborhoods
│   │   └── CanvasView.jsx     # Drawing canvas interface
│   ├── data/
│   │   └── neighborhoods.js   # 8 neighborhood definitions
│   └── utils/
│       └── seedArt.js         # Sample art initialization
├── index.html
├── package.json
├── vite.config.js
├── tailwind.config.js
└── postcss.config.js

**NO other files or folders.** Keep it minimal.

---

## CODING RULES

### General
1. **No comments in code** unless absolutely necessary for clarity
2. **Use functional components only** (no class components)
3. **Use hooks:** useState, useEffect, useRef (no custom hooks)
4. **Tailwind classes only** for styling (no inline styles unless Canvas-specific)
5. **ES6+ syntax:** arrow functions, destructuring, template literals

### Canvas-Specific
1. **Always get 2D context:** `canvas.getContext('2d')`
2. **Set canvas size explicitly:** `canvas.width = 800; canvas.height = 600;`
3. **Clear canvas before drawing saved image:** `ctx.clearRect(0, 0, width, height)`
4. **Use clip-path for neighborhood shape:**
```javascript
// In CanvasView, create SVG defs with clipPath
<svg style={{position: 'absolute', width: 0, height: 0}}>
  <defs>
    <clipPath id="neighborhood-clip">
      <polygon points={pointsString} />
    </clipPath>
  </defs>
</svg>
// Then CSS: canvas { clip-path: url(#neighborhood-clip); }
```

### LocalStorage
1. **Always check if key exists before reading:**
```javascript
const savedArt = localStorage.getItem(`wall-${id}`)
if (savedArt) {
  // load image
}
```
2. **Error handling for quota exceeded:**
```javascript
try {
  localStorage.setItem(key, value)
} catch (e) {
  console.error('Storage full:', e)
}
```

### Performance
1. **Debounce mouse move events** (draw max 60fps)
2. **Use requestAnimationFrame for smooth drawing**
3. **Don't re-render entire map on every state change** (use React.memo if needed)

---

## VALIDATION CHECKLIST

Before considering any component "done", verify:

### MapView
- [ ] All 8 neighborhoods render as polygons
- [ ] Hover shows neighborhood name
- [ ] Click logs neighborhood name to console
- [ ] Click opens CanvasView with correct neighborhood
- [ ] SVG scales to viewport (responsive)

### CanvasView
- [ ] Canvas renders at correct size (800x600)
- [ ] Clipped to neighborhood polygon shape
- [ ] Header shows neighborhood name + "Week 1: Spring Awakening"
- [ ] Back button returns to map
- [ ] 5 color buttons work (active state visible)
- [ ] Mouse drawing works (smooth lines)
- [ ] Touch drawing works on mobile
- [ ] Saving to localStorage works
- [ ] Loading from localStorage works on re-open

### Overall App
- [ ] Sample art visible on 3 neighborhoods on first load
- [ ] Drawing persists after returning to map
- [ ] No console errors
- [ ] Works on mobile viewport (375px width)
- [ ] Deploys to Vercel without errors

---

## COMMON MISTAKES TO AVOID

### ❌ Don't do this:
1. **Using third-party map libraries** (Mapbox, Leaflet, Google Maps)
2. **Complex state management** (Redux, Zustand - just useState)
3. **Over-engineering neighborhoods** (100+ coordinate points)
4. **Fetching GeoJSON from external APIs** (hardcode in neighborhoods.js)
5. **Building a gallery view** (out of scope)
6. **Adding features not in spec** (stay focused on MVP)
7. **Using CSS-in-JS libraries** (Tailwind only)
8. **Creating a backend** (localStorage is the database)

### ✅ Do this:
1. **Start with neighborhoods.js** - hardcode 8 simple polygons
2. **Test MapView standalone** before building CanvasView
3. **Use console.log liberally** while developing
4. **Keep components under 150 lines** each
5. **Mobile-test in Chrome DevTools** (375px viewport)
6. **Deploy early** (Vercel deploy takes 2 min)