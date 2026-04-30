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