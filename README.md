# YAMLECRApp - Environmental Compliance Review Data Collector

A Microsoft Power Apps Canvas application for environmental auditors to conduct comprehensive site inspections and compliance reviews.

## Overview

This repository contains the YAML source code for the ECR Data Collector Power App - a mobile-responsive tool designed for the Choctaw Nation of Oklahoma Environmental Compliance division.

## Features

- 📍 **GPS & Weather Tracking** - Automatic location capture and weather documentation
- 📋 **5-Step Audit Process** - Structured inspection workflow covering permits, systems, inventories, and site conditions
- 📸 **Photo Documentation** - Integrated camera for finding-specific and general site photos
- 🧮 **Automated Scoring** - Real-time compliance score calculation based on finding severity
- 📄 **PDF Report Generation** - Professional audit reports generated automatically
- 🔗 **SharePoint Integration** - Seamless data submission with parent-child relationship handling
- ✍️ **Digital Signatures** - Auditor signature capture for certification

## Quick Start

### Prerequisites
- Microsoft Power Apps license
- SharePoint Online site configured with required lists
- Power Automate flow for PDF handling
- Power Apps CLI (pac) for YAML to MSAPP conversion

### Installation

1. **Clone this repository**:
   ```bash
   git clone https://github.com/srmcno/YAMLECRApp.git
   cd YAMLECRApp
   ```

2. **Convert YAML to MSAPP**:
   ```bash
   pac canvas pack --sources ./src --msapp ECRDataCollector.msapp
   ```

3. **Import to Power Apps**:
   - Go to https://make.powerapps.com
   - Navigate to Apps > Import canvas app
   - Upload the ECRDataCollector.msapp file
   - Configure SharePoint connections

4. **Configure Connections**:
   - SharePoint site: `https://choctawnationofoklahoma.sharepoint.com/sites/environmentalcompliance`
   - Verify all list IDs match your environment
   - Set up Power Automate flow connection

## Documentation

For detailed documentation, see [DOCUMENTATION.md](./DOCUMENTATION.md) which includes:
- Complete architecture overview
- SharePoint list schemas
- Submission logic details
- Scoring algorithm
- Troubleshooting guide

## Application Structure

```
src/
├── App.yaml                    # Main app configuration
├── Connections/
│   └── Connections.yaml        # Data source connections
├── DataSources/
│   └── DataSources.yaml        # SharePoint list definitions
└── Screens/
    ├── scrAppStart.yaml        # Initialization
    ├── scrSetup.yaml           # Step 1: Setup
    ├── scrPermits.yaml         # Step 2: Permits Review
    ├── scrSystems.yaml         # Step 3: Systems Inspection
    ├── scrInventories.yaml     # Step 4: Inventories
    ├── scrSiteConditions.yaml  # Step 5: Site Conditions
    ├── scrFindingsReview.yaml  # Review & Scoring
    ├── scrSignature.yaml       # Signature & Submission
    └── ...                     # Additional screens
```

## Key Technologies

- **Microsoft Power Apps** - Canvas app platform
- **SharePoint Online** - Backend data storage
- **Power Automate** - PDF generation workflow
- **Power Apps YAML** - Source control format

## Scoring Logic

The app calculates compliance scores starting from 100 points:
- **Major Findings**: -10 points each (health, environmental, sovereignty issues)
- **Minor Findings**: -2 points each
- **Observations**: 0 points (informational only)
- **Minimum Score**: 0

## SharePoint Lists Required

1. **Audit Reports** - Parent audit records
2. **ECR Corrective Actions** - Child finding records
3. **Chemical/Waste Inventory** - Material tracking
4. **Facility Contact List** - Site directory
5. **Chemical Products List** - SDS database

## Contributing

This is a private application for Choctaw Nation of Oklahoma. Internal contributions should follow standard Git workflow:

1. Create a feature branch
2. Make changes
3. Test thoroughly in development environment
4. Submit pull request for review

## Support

For issues or questions:
1. Check [DOCUMENTATION.md](./DOCUMENTATION.md) for troubleshooting
2. Review Power Apps Monitor for error details
3. Verify SharePoint list configurations
4. Check Power Automate flow run history

## License

Copyright © 2024 Choctaw Nation of Oklahoma - Environmental Compliance Division

## Version

**Current Version**: 1.0.0

## Author

Environmental Compliance Division - Choctaw Nation of Oklahoma