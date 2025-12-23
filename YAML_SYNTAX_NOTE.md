# Power Apps YAML Syntax Note

## Important: Power Apps YAML Format

The YAML files in this repository use **Power Apps YAML format**, which is the official format used by Microsoft Power Apps CLI for source control.

### Key Characteristics

1. **List-Based Control Declaration with Types and Versions**:
   ```yaml
   - ScreenName:
       Control: Screen@1.0.0
       Properties:
         Fill: =RGBA(255, 255, 255, 1)
       Children:
         - ButtonName:
             Control: Classic/Button@2.2.0
             Properties:
               Text: ="Click Me"
               OnSelect: =Navigate(NextScreen)
               ZIndex: =1
   ```

2. **Control Format** - Each control uses the structure:
   ```yaml
   - ControlName:
       Control: ControlType@Version
       Variant: VariantName (optional)
       Properties:
         PropertyName: =value
       Children:
         - ChildControl:
             ...
   ```

3. **Formula Syntax** - Properties use `=` prefix for Power Apps formulas:
   ```yaml
   Text: ="Hello World"
   Color: =gblTheme.Primary
   Visible: =varShowControl
   ```

4. **Control Types with Versions**:
   - `Screen@1.0.0` - Screen control
   - `Label@2.5.1` - Label control
   - `Classic/Button@2.2.0` - Button control  
   - `Classic/TextInput@2.3.2` - Text input control
   - `Classic/DropDown@2.3.2` - Dropdown control
   - `Classic/ListBox@2.3.0` - ListBox control
   - `Gallery@2.15.0` - Gallery control
   - `GroupContainer@1.3.0` - Group container (with Variant for layout type)
   - `PenInput@2.3.0` - Pen/signature input
   - `Camera@2.3.0` - Camera control

5. **Variants for Containers**:
   - `ManualLayout` - Manual positioning
   - `VerticalAutoLayoutContainer` - Vertical auto-layout
   - `HorizontalAutoLayoutContainer` - Horizontal auto-layout
   - `BrowseLayout_Vertical_OneTextVariant_ver5.0` - Gallery variant

6. **Required Properties Section**:
   - `ZIndex` - Z-ordering of controls (layering)
   - `X`, `Y` - Position coordinates
   - `Width`, `Height` - Dimensions

7. **Multi-line Formulas** use `|-` or `=` syntax:
   ```yaml
   OnSelect: |-
     =Set(varTest, true);
     Navigate(NextScreen)
   ```

8. **Children Section** for nested controls:
   ```yaml
   Children:
     - childControl1:
         Control: Label@2.5.1
         Properties:
           Text: ="Child 1"
     - childControl2:
         Control: Label@2.5.1
         Properties:
           Text: ="Child 2"
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

