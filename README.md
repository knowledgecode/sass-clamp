# sass-clamp

A responsive CSS clamp() utility for Sass that generates fluid typography and spacing with linear interpolation.

## Installation

```bash
npm install sass-clamp
```

## Usage

**Requirements**: This library requires Dart Sass. Node Sass is not supported.

### Modern Dart Sass (≥1.71.0)

```scss
@use 'pkg:sass-clamp' as clamp;

.heading {
  font-size: clamp.fluid(16px, 24px, 320px, 1200px);
  // Output: clamp(16px, 13.09px + 0.91vw, 24px)
}
```

**CLI compilation:**
```bash
sass --pkg-importer=node style.scss output.css
```

**Vite configuration:**
```javascript
// vite.config.js
import { defineConfig } from 'vite';
import { NodePackageImporter } from 'sass';

export default defineConfig({
  css: {
    preprocessorOptions: {
      scss: {
        pkgImporter: new NodePackageImporter(),
      },
    },
  },
});
```

### Legacy Dart Sass (<1.71.0)

```scss
// Option 1: Use --load-path flag (recommended)
@use 'sass-clamp' as clamp;

// Option 2: Relative path from node_modules
@use './node_modules/sass-clamp' as clamp;
```

**CLI compilation:**
```bash
# Option 1: Using load-path
sass --load-path=./node_modules style.scss output.css

# Option 2: Direct compilation (relative paths)
sass style.scss output.css
```

**Vite configuration:**
```javascript
// vite.config.js
import { defineConfig } from 'vite';

export default defineConfig({
  css: {
    preprocessorOptions: {
      scss: {
        // Add node_modules to include paths
        includePaths: ['node_modules'],
      },
    },
  },
});
```

**Note**: The `pkg:` syntax requires Dart Sass 1.71.0 or later and the `--pkg-importer=node` flag when using CLI. For older versions, use `--load-path=./node_modules` flag or relative paths.

## API

### `fluid($min, $max, $min-vw, $max-vw, $base-font-size)`

Generates a responsive clamp() function with linear interpolation.

**Parameters:**

- `$min` - Minimum value (px or rem)
- `$max` - Maximum value (px or rem)
- `$min-vw` - Minimum viewport width (px or rem)
- `$max-vw` - Maximum viewport width (px or rem)
- `$base-font-size` - Base font size for unit conversion (default: 16)

**Returns:**

- CSS clamp() function as a string

**Example:**

```scss
font-size: clamp.fluid(1rem, 1.5rem, 320px, 1200px);
// Returns: clamp(1rem, 0.82rem + 0.91vw, 1.5rem)
```

#### Min/Max Values

- Supports `px` or `rem` units
- One unit can be omitted (uses the other value's unit)
- Both values must use the same unit (when both are specified)

```scss
@use 'pkg:sass-clamp' as clamp; // or 'sass-clamp' for legacy

// Both units specified (must match)
.text-1 { font-size: clamp.fluid(16px, 24px, 320px, 1200px); }
.text-2 { font-size: clamp.fluid(1rem, 1.5rem, 20rem, 75rem); }

// One unit omitted (24 => 24px, 1 => 1rem)
.text-3 { font-size: clamp.fluid(16px, 24, 320px, 1200px); }
.text-4 { font-size: clamp.fluid(1, 1.5rem, 20rem, 75rem); }
```

#### Viewport Values

- Supports `px` or `rem` units
- One unit can be omitted (uses the other value's unit)
- Can mix px and rem units

```scss
// Mixed viewport units
.element {
  padding: clamp.fluid(16px, 32px, 20rem, 75rem);
}
```

#### Base Font Size ($base-font-size)

- Specify the pixel equivalent of 1rem (without unit)
- Used for px/rem conversions
- Can be set globally or per function call

```scss
// Global configuration
@use 'pkg:sass-clamp' as clamp with ( // or 'sass-clamp' for legacy
  $default-base-font-size: 18
);

// Per function call
.text {
  font-size: clamp.fluid(1rem, 1.5rem, 20rem, 75rem, 18);
}
```

### Responsive Behavior

#### Growing with Viewport

```scss
// Font size grows as viewport expands
.heading {
  font-size: clamp.fluid(16px, 24px, 320px, 1200px);
}
```

#### Shrinking with Viewport

```scss
// Sidebar width shrinks as viewport grows (more space for content)
.sidebar {
  width: clamp.fluid(300px, 200px, 768px, 1920px);
}

// Font size decreases on larger screens
.dense-text {
  font-size: clamp.fluid(18px, 14px, 320px, 1200px);
}
```

## Limitations

- **Unitless values not supported**: Properties like `line-height`, `opacity`, `z-index` that accept unitless values cannot be used with this function
- **px/rem only**: Only `px` and `rem` units are supported for each values

## License

MIT
