# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **single-file web-based drawing application** written in vanilla JavaScript. The application provides a touch and mouse-enabled drawing canvas with color selection and canvas clearing functionality. It's designed for high-performance rendering using WebGL.

## Architecture

### Structure
- **Monolithic single-file architecture**: All code (HTML, CSS, JavaScript) is contained in `index.html:1`
- **No build system or dependencies**: Runs directly in browsers without transpilation or bundling
- **Touch-first design**: Optimized for mobile/touch devices with gesture handling

### Core Components (index.html)

**WebGL Rendering System** (index.html:197-434):
- Custom vertex and fragment shaders for point-based rendering
- Three dynamic buffers: positions, colors, and point sizes
- Smooth line rendering using interpolated points (`drawLine` function index.html:397-409)
- Antialiasing and alpha blending for smooth stroke edges

**Drawing Engine** (index.html:436-486):
- Speed-sensitive line width (4-20px range based on drawing speed)
- Mouse and touch event handling
- Line interpolation for smooth curves
- `startDrawing`, `draw`, and `stopDrawing` functions coordinate the drawing state

**Color Management** (index.html:308-322):
- 16 preset colors in a grid layout
- Hex to RGB conversion utility (`hexToRgb` function index.html:291-298)
- Current color stored as normalized RGB values

**Clear Functionality** (index.html:324-389):
- 3-second countdown before clearing (in German: "Löschen in 3...")
- Prevents accidental clears with touch/mouse hold interaction
- Visual countdown overlay

**Touch/Gesture Handling** (index.html:498-517):
- Disables pinch-to-zoom, double-tap zoom, and context menus
- Prevents multi-touch gestures from interfering with drawing

### Data Flow
```
User Input (mouse/touch) → Event Handlers → addPoint/drawLine → Buffer Update → WebGL Render
```

State is maintained in:
- `positions[]`, `colors[]`, `pointSizes[]` arrays
- Global variables: `currentColor`, `isDrawing`, `currentLineWidth`

## Development Workflow

### Running the Application
Simply open `index.html` directly in any modern web browser:

```bash
# macOS
open index.html

# Linux
xdg-open index.html

# Windows
start index.html
```

### Making Changes
1. Edit `index.html` directly
2. Refresh the browser to see changes
3. No build, bundling, or compilation steps required

### Testing
- **No automated testing framework exists**
- Manual testing only: draw with mouse/touch, change colors, clear canvas
- Test on multiple devices/browsers for touch compatibility

### Mobile Testing Workflow

For testing on mobile devices (recommended for touch functionality and PWA testing):

```bash
# Start local web server (in /home/daniel/code/malen directory)
python3 -m http.server 8080
```

Then access at **http://ailab-ubuntu.lan:8080** on your mobile device

**Claude can run this in background:**
- Use `run_in_background: true` parameter to keep server running
- Perfect for testing: touch drawing, PWA install prompts, offline mode

**Workflow:**
1. Start the Python server
2. Open http://ailab-ubuntu.lan:8080 on mobile browser
3. Test all features (drawing, colors, clear, PWA install)
4. Make changes to index.html
5. Refresh mobile browser to see changes
6. Press `Ctrl+C` in terminal when done

## Key Technical Details

**Canvas Resizing** (index.html:274-289):
- Auto-resizes to window dimensions
- Updates WebGL viewport on resize
- Clears to white background

**Line Width Calculation** (index.html:463-473):
- Speed = distance / timeDelta
- Line width: 4-20px range
- Smoothed using 80% previous + 20% target for natural feel

**WebGL Configuration** (index.html:177-181):
```javascript
{
  antialias: true,          // Smooth edges
  preserveDrawingBuffer: true,  // Maintains drawings on context loss
  alpha: false              // Opaque background for performance
}
```

## Important Code Sections

- **Shader setup**: index.html:197-259
- **Canvas initialization**: index.html:274-289
- **Color picker UI**: index.html:150-169
- **Drawing logic**: index.html:391-434
- **Event handlers**: index.html:488-517

## Limitations

- All code in a single file makes large changes difficult to manage
- No code organization or modularity
- No automated testing
- German UI text mixed with code (not internationalized)
- No linting or code quality tools

## Adding Features

When modifying the application:
1. WebGL rendering: Update shaders, buffers, or `render()` function
2. Drawing behavior: Modify `addPoint`, `drawLine`, or drawing event handlers
3. UI elements: Update CSS styles and corresponding event handlers
4. Touch handling: Add event listeners with `{ passive: false }` to prevent default
5. Performance: Use WebGL buffers efficiently; avoid recreating arrays unnecessarily

## Existing Documentation

- `AGENTS.md`: Provides basic project overview and development workflow
- No `README.md` exists for end users
- No inline code comments in JavaScript sections