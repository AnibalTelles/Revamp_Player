# REVAMP.EXE - Kiro Integration

## Overview

This directory contains the complete Kiro configuration for REVAMP.EXE, enabling AI-assisted development, continuous refactoring, and extensibility.

## Directory Structure

```
.kiro/
├── README.md                           # This file
├── specs/
│   └── revamp-exe.md                   # Complete technical specification
├── hooks/
│   ├── visualizer-expander.yaml        # Add new visualizer modes
│   ├── performance-auditor.yaml        # Audit and optimize performance
│   ├── ui-consistency-checker.yaml     # Maintain UI consistency
│   └── theme-painter.yaml              # Create custom themes
└── steering/
    └── revamp-development.md           # Development guidelines
```

## Agent Hooks

### 1. Visualizer Expander
**Trigger**: Save `visualizer_request.txt`  
**Purpose**: Add new visualization modes to REVAMP.EXE

**Usage**:
1. Create/edit `visualizer_request.txt`
2. Describe the visualizer you want (e.g., "particle explosion", "DNA helix")
3. Save the file
4. Kiro agent will implement the visualizer

**Example**:
```
Add a "Starfield" visualizer

Create a 3D starfield effect where:
- Stars move toward viewer based on bass
- Stars are colored by frequency
- Use perspective projection
- Should be a Milkdrop-style mode
```

### 2. Performance Auditor
**Trigger**: Save `performance_audit.txt`  
**Purpose**: Analyze and optimize performance

**Usage**:
1. Create/edit `performance_audit.txt`
2. Describe performance concerns
3. Save the file
4. Kiro agent will audit and optimize

**Example**:
```
Performance audit request

Noticing frame drops in Matrix Rain mode.
Please check for:
- Allocations in render loop
- Expensive canvas operations
- Optimization opportunities
```

### 3. UI Consistency Checker
**Trigger**: Save `ui_check.txt`  
**Purpose**: Ensure UI consistency and retro aesthetic

**Usage**:
1. Create/edit `ui_check.txt`
2. Describe UI concerns
3. Save the file
4. Kiro agent will audit and fix

**Example**:
```
UI consistency check

Please verify:
- All colors use CSS variables
- Bevels are consistent
- Active states are clear
- Text is readable in all themes
```

### 4. Theme Painter
**Trigger**: Save `theme_request.txt`  
**Purpose**: Create custom color themes

**Usage**:
1. Create/edit `theme_request.txt`
2. Describe the theme aesthetic
3. Save the file
4. Kiro agent will create the theme

**Example**:
```
Create a "SYNTHWAVE" theme

Colors:
- Purple/magenta background
- Hot pink accent
- Dark purple panels
- Neon aesthetic
```

## Specifications

### revamp-exe.md
Complete technical documentation including:
- Architecture overview
- Visualizer mode details
- Theme system documentation
- Audio engine specifications
- Performance characteristics
- Extension points
- Development guidelines

## Steering Rules

### revamp-development.md
Always-active development guidelines:
- Single-file architecture rules
- Zero-dependency requirements
- Code style standards
- Performance guidelines
- Testing checklist
- Common patterns

## Quick Start

### Adding a New Visualizer
```bash
# 1. Create request file
echo "Add a circular spectrum visualizer" > visualizer_request.txt

# 2. Save the file (triggers hook)
# Kiro agent will implement it automatically

# 3. Test in browser
# Open index.html and click canvas to cycle to new mode
```

### Creating a Custom Theme
```bash
# 1. Create request file
echo "Create a MIDNIGHT theme with dark blue and cyan" > theme_request.txt

# 2. Save the file (triggers hook)
# Kiro agent will create the theme

# 3. Test in browser
# Click 🎨 SKINS button to cycle to new theme
```

### Auditing Performance
```bash
# 1. Create audit request
echo "Check performance of all visualizer modes" > performance_audit.txt

# 2. Save the file (triggers hook)
# Kiro agent will analyze and optimize

# 3. Review changes
# Check console for FPS improvements
```

### Checking UI Consistency
```bash
# 1. Create check request
echo "Verify all themes have proper contrast" > ui_check.txt

# 2. Save the file (triggers hook)
# Kiro agent will audit and fix issues

# 3. Test all themes
# Click 🎨 SKINS button and verify each theme
```

## Development Workflow

### 1. Understand the Codebase
Read `.kiro/specs/revamp-exe.md` for complete technical documentation.

### 2. Use Agent Hooks
Instead of manually editing code, use agent hooks for common tasks:
- New visualizers → `visualizer_request.txt`
- New themes → `theme_request.txt`
- Performance → `performance_audit.txt`
- UI issues → `ui_check.txt`

### 3. Follow Guidelines
Steering rules in `.kiro/steering/revamp-development.md` are always active.

### 4. Test Thoroughly
- Test in multiple browsers
- Test all visualizer modes
- Test all themes
- Check performance (60 FPS target)

## Extension Points

### Visualizer Modes
**Location**: `animate()` function in index.html  
**Pattern**: `else if(visMode === N) { ... }`  
**Variables**: dataArray, timeArray, w, h, cx, cy, bass, mids, hue, accent, ctx

### Themes
**Location**: `themes` array in index.html  
**Pattern**: `{ name: "NAME", vars: { ... } }`  
**Variables**: 9 CSS variables for complete theme

### Radio Stations
**Location**: `stations` array in index.html  
**Pattern**: `{ name: "NAME", url: "URL" }`

## Best Practices

### DO:
✅ Use agent hooks for common tasks  
✅ Follow steering guidelines  
✅ Test in multiple browsers  
✅ Maintain 60 FPS performance  
✅ Keep single-file architecture  
✅ Use CSS variables for colors  

### DON'T:
❌ Split into multiple files  
❌ Add dependencies  
❌ Add build steps  
❌ Hardcode colors  
❌ Allocate in render loop  
❌ Break theme system  

## Troubleshooting

### Hook Not Triggering
1. Verify file name matches pattern exactly
2. Check that hook is enabled in YAML
3. Save file (don't just create it)
4. Check Kiro agent logs

### Performance Issues
1. Use `performance_audit.txt` hook
2. Check browser DevTools Performance tab
3. Verify 60 FPS target
4. Profile with FPS counter

### UI Inconsistencies
1. Use `ui_check.txt` hook
2. Verify CSS variables usage
3. Test all themes
4. Check contrast ratios

### Visualizer Not Working
1. Check console for errors
2. Verify mode added to `visList`
3. Test with both local and radio modes
4. Check performance (may be too slow)

## Contributing

### Adding New Hooks
1. Create YAML file in `.kiro/hooks/`
2. Define trigger (file pattern)
3. Write detailed instructions
4. Test with example file
5. Document in this README

### Improving Specs
1. Edit `.kiro/specs/revamp-exe.md`
2. Add technical details
3. Include code examples
4. Update extension points

### Updating Guidelines
1. Edit `.kiro/steering/revamp-development.md`
2. Add new patterns
3. Update best practices
4. Include examples

## Resources

### Documentation
- **Specs**: `.kiro/specs/revamp-exe.md`
- **Guidelines**: `.kiro/steering/revamp-development.md`
- **This README**: `.kiro/README.md`

### Example Files
- `visualizer_request.txt` - Example visualizer request
- `theme_request.txt` - Example theme request
- `performance_audit.txt` - Example audit request
- `ui_check.txt` - Example UI check request

### External Resources
- [Web Audio API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API)
- [Canvas 2D API](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D)
- [CSS Variables](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)

## Version History

- **v2.0** (Current): Complete Kiro integration
  - 4 agent hooks
  - Complete specifications
  - Development guidelines
  - Example trigger files

## Support

For questions or issues:
1. Check `.kiro/specs/revamp-exe.md` for technical details
2. Review `.kiro/steering/revamp-development.md` for guidelines
3. Use agent hooks for common tasks
4. Check example trigger files

---

**REVAMP.EXE** - Resurrecting the Soul of Audio with AI-Assisted Development
