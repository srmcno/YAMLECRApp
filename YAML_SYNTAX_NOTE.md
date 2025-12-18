# Power Apps YAML Syntax Note

## Important: Power Apps YAML Format

The YAML files in this repository use **Power Apps YAML format**, which is the official format used by Microsoft Power Apps CLI for source control.

### Key Characteristics

1. **Control Declaration with Types**:
   ```yaml
   ScreenName As screen:
       Fill: =RGBA(255, 255, 255, 1)
       
       ButtonName As button:
           Text: ="Click Me"
           OnSelect: =Navigate(NextScreen)
           ZIndex: =1
   ```

2. **Formula Syntax** - Properties use `=` prefix for Power Apps formulas:
   ```yaml
   Text: ="Hello World"
   Color: =gblTheme.Primary
   Visible: =varShowControl
   ```

3. **Control Versions** - Each control specifies its type:
   - `screen` - Screen control
   - `label` - Label control
   - `button` - Button control  
   - `text` - Text input control
   - `dropdown` - Dropdown control
   - `gallery.galleryTemplate` - Gallery control
   - `groupContainer.verticalAutoLayoutContainer` - Vertical container
   - `groupContainer.manualLayoutContainer` - Manual layout container
   - `penInput` - Pen/signature input
   - `camera` - Camera control

4. **Required Properties**:
   - `ZIndex` - Z-ordering of controls (layering)
   - `X`, `Y` - Position coordinates
   - `Width`, `Height` - Dimensions

5. **Multi-line Formulas** use `|-` or `=` syntax:
   ```yaml
   OnSelect: |-
       =Set(varTest, true);
       Navigate(NextScreen)
   ```

### Processing These Files

**Use Power Apps CLI**:
```bash
# Pack YAML to MSAPP (for importing to Power Apps)
pac canvas pack --sources ./src --msapp ECRDataCollector.msapp

# Unpack MSAPP to YAML (for version control)
pac canvas unpack --msapp ECRDataCollector.msapp --sources ./src
```

### Why Not Standard YAML?

Microsoft Power Apps uses this custom format to represent:
- Power Apps formulas (Excel-like syntax with `=`)
- Control hierarchies and properties
- Data bindings and expressions
- UI layout and styling

**Standard YAML validators will report errors** - this is expected and normal for Power Apps YAML files.

### Validation

To validate these files:
1. Install Power Apps CLI (`pac`)
2. Use `pac canvas pack` to convert to .msapp
3. Import into Power Apps Studio
4. Test app functionality

### Reference

- [Power Apps CLI Documentation](https://learn.microsoft.com/en-us/power-platform/developer/cli/introduction)
- [Canvas App YAML Reference](https://learn.microsoft.com/en-us/power-platform/developer/cli/reference/canvas)
- [Power Apps Formula Reference](https://learn.microsoft.com/en-us/power-platform/power-fx/formula-reference)

