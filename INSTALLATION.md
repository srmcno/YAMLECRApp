# Installation and Setup Guide

## Prerequisites

Before installing the ECR Data Collector Power App, ensure you have:

### 1. Required Licenses
- Microsoft Power Apps license (per user or per app)
- SharePoint Online access
- Power Automate license (for PDF generation flow)

### 2. Required Permissions
- Power Apps environment maker role
- SharePoint site collection administrator (for initial setup)
- Ability to create Power Automate flows

### 3. Required Software
- Power Apps CLI (`pac`) - [Download here](https://learn.microsoft.com/en-us/power-platform/developer/cli/introduction)
- Git (for cloning repository)

## Step 1: SharePoint Site Setup

### Create SharePoint Lists and Library

1. **Navigate to SharePoint Site**:
   ```
   https://choctawnationofoklahoma.sharepoint.com/sites/environmentalcompliance
   ```

2. **Create Document Library: "Audit Reports"**
   - Site Contents > New > Document Library
   - Name: "Audit Reports"
   - Add columns:
     - Facility (Lookup to Facility Contact List)
     - Audit Type (Choice: ECR, SPCC, SWPPP, Housing ECR)
     - Auditor Name (Person)
     - Audit Date (Date and Time)
     - Audit Score (Number, Required)
     - Weather Conditions (Single line of text)
     - Latitude (Number)
     - Longitude (Number)
     - Major Findings (Number)
     - Minor Findings (Number)
     - Observations (Number)
     - Department (Single line of text, Required, Default: "Environmental Compliance")
     - Facility Pull (Single line of text)
     - Audit Complete (Single line of text)

3. **Create List: "ECR Corrective Actions"**
   - Enable attachments (List Settings > Advanced Settings)
   - Add columns:
     - Corrective Action (Title field)
     - Facility L (Lookup to Facility Contact List)
     - ECR ID (Lookup to Audit Reports)
     - Date of Finding (Date and Time)
     - CA Status (Choice: Unresolved, Resolved)
     - Severity of Finding (Choice: Major, Minor, Observation)
     - Finding Category (Choice: Permits, Water, Air, Waste, etc.)
     - Specific Issue (Choice)
     - Root Cause (Choice)
     - Quick Fix (Single line of text)
     - Notes (Multiple lines of text)
     - Fixed On Site (Yes/No)
     - AppFindingID (Single line of text) **CRITICAL**
     - Department (Single line of text, Required)

4. **Create List: "Chemical/Waste Inventory"**
   - Add columns as specified in manifest.json

5. **Create List: "Facility Contact List"**
   - This should already exist as the site directory

6. **Create List: "Chemical Products List"**
   - Add columns for SDS database

### Note List IDs

After creating lists, note their GUIDs (found in list settings URL after "List="):
- Verify they match the IDs in `src/DataSources/DataSources.yaml`
- If different, update the EntitySetName in DataSources.yaml

## Step 2: Power Automate Flow Setup

### Create PDF Generation Flow

1. **Create New Flow**:
   - Go to https://make.powerautomate.com
   - Create new Instant cloud flow
   - Name: "PowerAppV2->Createfile"
   - Trigger: "Power Apps (V2)"

2. **Add Input Parameters**:
   - pdfContent (File)
   - fileName (Text)
   - auditScore (Number)
   - auditorName (Text)
   - weather (Text)
   - latitude (Number)
   - longitude (Number)
   - facilityId (Number)

3. **Add SharePoint Action**:
   - Action: "Create file"
   - Site Address: Your SharePoint site
   - Folder Path: /Audit Reports
   - File Name: `@{triggerBody()['file']['name']}`
   - File Content: `@{triggerBody()['file']['contentBytes']}`

4. **Save and Test** the flow

## Step 3: Install Power Apps CLI

### Windows
```powershell
# Using Windows Package Manager
winget install Microsoft.PowerAppsCLI

# Or download installer from Microsoft
```

### macOS
```bash
# Using Homebrew
brew install --cask powerapps-cli
```

### Linux
```bash
# Download and install .NET 6.0 SDK first
# Then install pac CLI
dotnet tool install --global Microsoft.PowerApps.CLI.Tool
```

Verify installation:
```bash
pac --version
```

## Step 4: Clone and Build the App

### Clone Repository
```bash
git clone https://github.com/srmcno/YAMLECRApp.git
cd YAMLECRApp
```

### Update Configuration (if needed)

If your SharePoint list IDs are different:

1. Edit `src/DataSources/DataSources.yaml`
2. Update EntitySetName values with your list GUIDs
3. Save changes

### Build MSAPP File

```bash
# Navigate to repository root
cd YAMLECRApp

# Pack YAML files into MSAPP
pac canvas pack --sources ./src --msapp ECRDataCollector.msapp
```

This creates `ECRDataCollector.msapp` file in the current directory.

## Step 5: Import to Power Apps

### Import the App

1. **Open Power Apps Portal**:
   - Navigate to https://make.powerapps.com
   - Select your environment (top-right)

2. **Import Canvas App**:
   - Click "Apps" in left navigation
   - Click "Import canvas app"
   - Click "Upload" and select `ECRDataCollector.msapp`
   - Click "Import"

3. **Configure Connections**:
   - During import, you'll be prompted to configure connections
   - **SharePoint Connection**:
     - Select existing connection or create new
     - Ensure it points to: `https://choctawnationofoklahoma.sharepoint.com/sites/environmentalcompliance`
   - **Power Automate Connection**:
     - Select the "PowerAppV2->Createfile" flow
     - Grant necessary permissions

4. **Complete Import**:
   - Review all connection mappings
   - Click "Import"
   - Wait for import to complete

## Step 6: Configure the App

### Open App in Editor

1. Click on the imported app
2. Select "Edit"
3. Wait for Power Apps Studio to load

### Verify Data Sources

1. Click "Data" in left panel
2. Verify all SharePoint lists are connected:
   - Audit Reports
   - ECR Corrective Actions
   - Chemical/Waste Inventory
   - Facility Contact List
   - Chemical Products List

3. If any connection shows errors:
   - Remove the data source
   - Re-add it using "Add data" button
   - Select SharePoint
   - Choose your site
   - Select the correct list

### Refresh Data Sources

Important: This ensures all columns are recognized

```powerapp
// In App.OnStart or scrAppStart.OnStart
Refresh('Audit Reports');
Refresh('ECR Corrective Actions');
Refresh('Facility Contact List');
Refresh('Chemical Products List');
```

### Test Basic Functionality

1. Press "Play" button (▶️) in top-right
2. Verify:
   - App initializes without errors
   - GPS coordinates populate
   - Facility dropdown loads
   - Theme colors display correctly

### Save and Publish

1. Click "File" > "Save"
2. Add a version note (e.g., "Initial deployment v1.0")
3. Click "Publish"
4. Select "Publish this version"

## Step 7: Share the App

### Share with Users

1. In Power Apps home, locate the app
2. Click "..." (More Commands)
3. Select "Share"
4. Add users or security groups
5. Choose permission level:
   - **User**: Can run the app
   - **Co-owner**: Can edit the app
6. Send invitation email

### Mobile Deployment

Users can access the app via:

1. **Power Apps Mobile**:
   - Install "Power Apps" from app store
   - Sign in with organizational account
   - App appears in "Apps" list

2. **Web Browser**:
   - Navigate to https://make.powerapps.com
   - Click on app to run

## Step 8: Post-Installation Testing

### Test Complete Workflow

1. **Start New Audit**:
   - Select a facility
   - Verify GPS captures
   - Enter weather conditions
   - Navigate to next step

2. **Add Findings**:
   - Create test findings in each category
   - Add photos to findings
   - Verify photo capture works

3. **Review Score Calculation**:
   - Add 1 Major finding (score should be 90)
   - Add 2 Minor findings (score should be 86)
   - Verify score updates correctly

4. **Submit Audit**:
   - Capture signature
   - Submit audit
   - Verify submission success

5. **Check SharePoint**:
   - Open Audit Reports library
   - Verify audit record created
   - Check attachments present
   - Open ECR Corrective Actions list
   - Verify findings linked to audit
   - Check finding photos attached

6. **Verify PDF Generation**:
   - Check Power Automate flow ran successfully
   - Verify PDF created in SharePoint
   - Download and review PDF content

## Troubleshooting

### Common Issues

**Issue: "Delegation warning" appears**
- This is normal for some operations
- Ensure colFindings stays under 500 items per session
- Consider splitting large audits

**Issue: GPS coordinates show 0,0**
- Grant location permissions in browser/device settings
- On iOS: Settings > Privacy > Location Services
- On Android: Settings > Apps > Permissions > Location
- On browser: Allow location when prompted

**Issue: Camera doesn't work**
- Grant camera permissions
- Test on actual device (may not work in browser emulator)

**Issue: Submission fails with "Invalid argument"**
- Verify @odata.type syntax in Patch statements
- Check all lookup fields reference existing items
- Verify Department field is populated

**Issue: Photos don't attach**
- Ensure ECR Corrective Actions list has attachments enabled
- Verify AppFindingID matches between finding and photo
- Check file size (SharePoint has limits)

**Issue: PDF generation fails**
- Verify scrPDFReport screen exists
- Check Power Automate flow permissions
- Review flow run history for errors

### Getting Help

1. Check Power Apps Monitor for detailed errors
2. Review app connections in Power Apps portal
3. Check SharePoint list permissions
4. Review Power Automate flow run history
5. Consult [DOCUMENTATION.md](./DOCUMENTATION.md)

## Maintenance

### Regular Tasks

1. **Monitor Usage**:
   - Review Power Apps analytics
   - Check SharePoint storage usage
   - Monitor flow runs

2. **Update Data**:
   - Keep Facility Contact List current
   - Update Chemical Products List
   - Archive old audit records

3. **Version Control**:
   - Periodically export app to YAML
   - Commit changes to Git
   - Document modifications

## Security Best Practices

1. **Access Control**:
   - Use Azure AD security groups
   - Follow principle of least privilege
   - Regular access reviews

2. **Data Protection**:
   - Enable SharePoint versioning
   - Configure retention policies
   - Regular backups

3. **Compliance**:
   - Review audit logs regularly
   - Document processes
   - Train users properly

## Upgrade Path

To upgrade to a new version:

1. Export current app as backup
2. Clone updated repository
3. Build new MSAPP file
4. Import as new version
5. Test thoroughly in dev environment
6. Publish to production
7. Notify users of changes

## Support Resources

- [Power Apps Documentation](https://docs.microsoft.com/en-us/powerapps/)
- [SharePoint Online Documentation](https://docs.microsoft.com/en-us/sharepoint/)
- [Power Automate Documentation](https://docs.microsoft.com/en-us/power-automate/)
- Internal IT support: [Contact Information]

---

**Installation Complete!** 🎉

Your ECR Data Collector app is now ready for use. Proceed with user training and pilot testing.

