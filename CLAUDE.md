# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A React 19 application showcasing the "Stargazers" - an intergalactic alliance of characters. This is a LinkedIn Learning course repository with multiple branches for each video lesson. The main branch holds the final course state.

## Essential Commands

```bash
npm run dev        # Start development server with Vite
npm run build      # Build for production (outputs to docs/)
npm run preview    # Preview production build
npm run lint       # Run ESLint
```

## Architecture

### Data Flow Pattern

The app follows a "lift state up" pattern with unidirectional data flow:

1. **App.jsx** (root component) manages global state:
   - `cast` array - fetched from `cast.json` on mount
   - `memberInfo` object - currently selected cast member for modal display

2. State flows down through props:
   - `App` → `Nav` → passes `cast` and `onChoice` callback
   - `App` → `ListCast` → passes `cast` and `onChoice` callback
   - `App` → `Modals` → passes `memberInfo`, `handleClose`, and `handleChange`

3. Event handlers bubble up:
   - User clicks character → triggers `onChoice` → updates `memberInfo` in App
   - Modal navigation arrows → triggers `handleChange` with new ID → updates `memberInfo`

### Component Architecture

**App.jsx** (src/App.jsx:1)
- Root component managing all state
- Fetches cast data from `cast.json` in `useEffect`
- Conditionally renders `Modals` component when `memberInfo` is set

**Nav.jsx** (src/components/Nav.jsx:1)
- Top navigation with logo, title, theme toggle, and cast dropdown
- Integrates `ToggleTheme` component
- Dropdown menu populated from `cast` array

**ListCast.jsx** (src/components/ListCast.jsx:1)
- Grid display of all cast member thumbnails
- Uses `{slug}_tn.svg` naming pattern for thumbnail images

**Modals.jsx** (src/components/Modals.jsx:1)
- Character detail dialog with navigation arrows
- Arrow buttons use `member.id` for prev/next navigation
- Relies on `cast` array indices being sequential starting from 0

**ToggleTheme.jsx** (src/components/ToggleTheme.jsx:1)
- Three-state theme cycle: auto → light → dark → auto
- Persists theme preference to localStorage
- Updates `data-theme` attribute on document root
- Auto mode respects system preference via `prefers-color-scheme`

### Build Configuration

**Vite config** (vite.config.js:1):
- Uses React Compiler plugin (babel-plugin-react-compiler)
- Sets base path to `./` for relative URLs
- Outputs to `docs/` directory (instead of default `dist/`)
- This allows the project to be served via GitHub Pages from the docs folder

### Data Structure

**cast.json** structure (public/cast.json:1):
```json
{
  "id": 0,
  "name": "Character Name",
  "slug": "character-slug",
  "bio": "Character description",
  "origin": "Planet name",
  ...
}
```

- `id` must be numeric and sequential (used for prev/next navigation in modals)
- `slug` corresponds to image filenames: `images/{slug}.svg` and `images/{slug}_tn.svg`
- Data file exists in both `public/` (source) and `docs/` (built output)

### Styling

- Uses Pico CSS framework for base styling
- Imports both core and color variants from `@picocss/pico`
- Custom styles in App.css
- Theme switching handled via `data-theme` attribute (Pico CSS convention)

## Important Notes

- **No PRs accepted**: This repository does not accept pull requests (see CONTRIBUTING.md)
- **Branch structure**: Branches named `CHAPTER#_MOVIE#` with `b` (beginning) and `e` (end) states
- **React Compiler**: Project uses experimental React Compiler plugin for optimization
- **GitHub Actions**: Includes Claude Code review workflow (`.github/workflows/claude-code-review.yml`)
