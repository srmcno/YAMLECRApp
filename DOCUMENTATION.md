# ECR Data Collector Power App - YAML Structure

## Overview

This repository contains the YAML source code for the Environmental Compliance Review (ECR) Data Collector Power App. This is a mobile-responsive Power Apps Canvas application designed for environmental auditors to conduct site visits, capturing facility data, GPS coordinates, weather conditions, chemical/tank inventories, and audit findings with photos.

## Purpose

The ECR Data Collector App enables environmental auditors to:
- Conduct comprehensive site inspections across 5 key audit areas
- Capture and track findings with photos
- Calculate compliance scores based on severity of findings
- Generate professional PDF reports
- Submit audit data to SharePoint with proper parent-child relationships

## Application Architecture

### Data Sources (SharePoint Online)

**Site URL:** `https://choctawnationofoklahoma.sharepoint.com/sites/environmentalcompliance`

#### Primary Lists/Libraries:

1. **Audit Reports** (Document Library) - `e48a774b-a1a4-471b-8e66-339cda4c64f3`
   - Parent record containing high-level audit information
   - Stores audit score, GPS coordinates, weather, finding counts
   - Holds attachments: signatures and general site photos

2. **ECR Corrective Actions** (List) - `27994f91-6307-400c-9bd5-f90a8df83949`
   - Child records for individual findings
   - Links to parent via ECRLink lookup field
   - Uses AppFindingID for photo matching

3. **Chemical/Waste Inventory** (List) - `9a1434a5-4f63-479a-bb6d-69c68c284e94`
   - Tracks chemicals, wastes, and tanks found on-site

4. **Facility Contact List** (List) - `7b8d48f2-5095-40c8-ad51-d8283d3bf40a`
   - Reference list for facility selection

5. **Chemical Products List** (List) - `e4e560b8-c3c7-4b99-98a5-770f03db1cd0`
   - Reference list for SDS database

### Application Screens

1. **scrAppStart** - Initialization and global variable setup
2. **scrSetup** - Step 1: Facility selection, weather, GPS capture
3. **scrPermits** - Step 2: Permit review and findings
4. **scrSystems** - Step 3: Systems inspection (water, septic, backflow, odors)
5. **scrInventories** - Step 4: Chemical and tank inventory tracking
6. **scrSiteConditions** - Step 5: Site conditions checklist with auto-generated findings
7. **scrFindingsReview** - Final review of all findings with score calculation
8. **scrSignature** - Auditor signature capture and final submission
9. **scrComplete** - Success confirmation screen
10. **scrFindingDetail** - Add/edit individual findings
11. **scrInventoryDetail** - Add/edit inventory items
12. **scrPhotoCapture** - Camera interface for photo capture
13. **scrPDFReport** - Hidden screen for PDF generation

### Key Features

#### 1. Scoring Logic
- Starts at 100 points
- Deductions based on severity:
  - **Major Findings:** -10 points each
  - **Minor Findings:** -2 points each
  - **Observations:** 0 points (informational only)
- Minimum score: 0

#### 2. Auto-Generated Findings
Site conditions checklist automatically creates findings when compliance items are marked as non-compliant.

#### 3. Photo Management
- Finding-specific photos linked via AppFindingID (GUID)
- General site photos attached to parent record
- Auditor signature captured via pen input

#### 4. Master Submission Logic

The submission follows a strict sequence to maintain data integrity:

```powerapp
1. Patch Parent Record to 'Audit Reports'
   - Creates parent with all audit metadata
   - Captures returned record ID in varAuditRecord
   
2. Patch Child Records to 'ECR Corrective Actions'
   - ForAll loop through colFindings
   - Links each finding to parent via ECRLink
   - Stores AppFindingID for photo matching
   
3. Attach Finding Photos
   - Matches photos to findings using AppFindingID
   - Creates attachments on child records
   
4. Attach Parent Attachments
   - Auditor signature from penInput
   - General site photos from colGeneralPhotos
   
5. Generate PDF Report
   - Uses PDF() function on scrPDFReport
   - Creates formatted audit report
   
6. Send to Power Automate
   - Triggers 'PowerAppV2->Createfile' flow
   - Uploads PDF to SharePoint
```

#### 5. Schema Enforcement

All Patch statements use explicit `@odata.type` for:
- Lookup fields: `#Microsoft.Azure.Connectors.SharePoint.SPListExpandedReference`
- Choice fields: `#Microsoft.Azure.Connectors.SharePoint.SPListExpandedChoice`
- Person fields: `#Microsoft.Azure.Connectors.SharePoint.SPListExpandedUser`

This prevents "Invalid Argument" errors when submitting to SharePoint.

### Global Variables

#### Theme (gblTheme)
- Primary, Secondary, Success, Warning, Danger colors
- Light, Dark, White, Text, Border colors
- Provides consistent styling across all screens

#### Device (gblDevice)
- Width, Height, IsPhone, IsTablet, IsDesktop
- Responsive padding and font sizes
- Header and footer heights

#### Collections
- **colFindings** - Active audit findings before submission
- **colFindingPhotos** - Camera images mapped to FindingID
- **colGeneralPhotos** - Site overview images
- **colInventories** - Chemicals and tanks identified

#### Audit Variables
- varCurrentStep, varAuditScore, varAuditDate
- varWeather, varLatitude, varLongitude
- varSelectedFacility, varSelectedAuditType
- varMajorCount, varMinorCount, varObservationCount

## Directory Structure

```
YAMLECRApp/
├── README.md (this file)
├── src/
│   ├── App.yaml                    # Main app configuration
│   ├── Connections/
│   │   └── Connections.yaml        # SharePoint and Power Automate connections
│   ├── DataSources/
│   │   └── DataSources.yaml        # SharePoint list definitions
│   └── Screens/
│       ├── scrAppStart.yaml        # App initialization
│       ├── scrSetup.yaml           # Step 1: Setup
│       ├── scrPermits.yaml         # Step 2: Permits
│       ├── scrSystems.yaml         # Step 3: Systems
│       ├── scrInventories.yaml     # Step 4: Inventories
│       ├── scrSiteConditions.yaml  # Step 5: Site Conditions
│       ├── scrFindingsReview.yaml  # Findings review & scoring
│       ├── scrSignature.yaml       # Signature & submission
│       ├── scrComplete.yaml        # Completion screen
│       ├── scrFindingDetail.yaml   # Add/edit findings
│       ├── scrInventoryDetail.yaml # Add/edit inventory
│       ├── scrPhotoCapture.yaml    # Camera interface
│       └── scrPDFReport.yaml       # PDF generation template
```

## Power Automate Integration

The app integrates with a Power Automate flow named `PowerAppV2->Createfile` which:
- Receives PDF content, filename, and audit metadata
- Uploads the PDF to the Audit Reports library
- Links the PDF to the parent audit record

### Flow Parameters:
- `pdfContent` (File) - Binary PDF data
- `fileName` (Text) - Generated filename
- `auditScore` (Number) - Compliance score
- `auditorName` (Text) - Auditor's name
- `weather` (Text) - Weather conditions
- `latitude` (Number) - GPS latitude
- `longitude` (Number) - GPS longitude
- `facilityId` (Number) - Facility reference ID

## Importing into Power Apps

To import this YAML structure into Power Apps:

1. **Using Power Apps CLI (pac)**:
   ```bash
   pac canvas pack --sources ./src --msapp ECRDataCollector.msapp
   ```

2. **Import the .msapp file**:
   - Open https://make.powerapps.com
   - Go to Apps > Import canvas app
   - Upload the .msapp file
   - Configure connections to your SharePoint site

3. **Configure Connections**:
   - SharePoint Online connection to: `https://choctawnationofoklahoma.sharepoint.com/sites/environmentalcompliance`
   - Power Automate connection for the flow
   - Ensure all list IDs match your environment

## Development Notes

### Required SharePoint Columns

**Audit Reports:**
- All columns must include "Department" (Text, Required)
- "AuditScore" must be marked as Required

**ECR Corrective Actions:**
- Must enable attachments on the list
- "AppFindingID" field is critical for photo linking
- "Department" (Text, Required)

### Best Practices

1. **Always refresh data sources** after major changes to SharePoint schema
2. **Use GUID() function** to generate unique AppFindingID values
3. **Test submission logic** thoroughly in a dev environment first
4. **Validate GPS permissions** are granted on mobile devices
5. **Test camera functionality** on target devices
6. **Ensure network connectivity** for SharePoint operations

### Common Issues & Solutions

**Issue: "Invalid Argument" errors on submission**
- Solution: Ensure @odata.type is specified for all Lookup, Choice, and Person fields

**Issue: Photos not attaching to findings**
- Solution: Verify AppFindingID is populated and matches between colFindings and colFindingPhotos

**Issue: GPS coordinates showing as 0**
- Solution: Ensure location permissions are granted in browser/device settings

**Issue: PDF generation fails**
- Solution: Verify scrPDFReport screen is present and properly formatted

## Security Considerations

- User authentication via Azure AD/Microsoft 365
- SharePoint permissions control data access
- Department field ensures proper data segregation
- Audit trail maintained via SharePoint versioning
- PDF reports provide immutable record of audit

## Mobile Responsiveness

The app uses gblDevice variable to adapt to screen sizes:
- **Phone (< 768px)**: Compact layout, smaller fonts
- **Tablet (768-1024px)**: Medium spacing and fonts
- **Desktop (> 1024px)**: Full layout with maximum spacing

## Support

For issues or questions about this Power App:
1. Check SharePoint list configurations match expected schema
2. Verify all connections are properly configured
3. Review Power Automate flow status
4. Check app monitor for detailed error logs

## Version History

- **v1.0** - Initial release with full 5-step audit process, scoring, and PDF generation

## License

This application is designed for use by the Choctaw Nation of Oklahoma Environmental Compliance division.

