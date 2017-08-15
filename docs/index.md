OSG Technology Area
===================

Welcome to the home page of the OSG Technology Team documentation area! This is currently a work in progress as we migrate our TWiki documentation to GitHub. If you are looking for our full documentation, please visit the [TWiki](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/WebHome).

The Team
--------

| Software | Release | Technology |
| ----------------- | -- | -- |
| Brian Lin (manager) (90%) | Tim Theisen (manager) (50%) | Brian Bockelman (manager) (50%) |
| Carl Edquist | Brian Lin (10%) | Derek Weitzel (75%) |
| Derek Weitzel (25%) | Suchandra Thapa (50%) | Edgar Fajardo (50%) |
| Edgar Fajardo (50%) | | Jeff Dost (50%) |
| Mat Selmeci | | | Marian Zvada (25%)
| Tim Cartwright (35%) | | |

Internal Documentation
-------------------------

The following documents are a collection of OSG software and release team processes, notes, and internal policies.

### Software

- Developer processes and notes
    - [Software development process](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/SoftwareDevelopmentProcess)
    - [Git workflow](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/GitSoftwareDevWorkflow)
    - [RPM development guide](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/RPMDevelopmentGuide)
    - [OSG build tools](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/OSGBuildTools)
    - [Setting up an OSG build box](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/VDTRPMBuildBox)
    - [Updating empty packages](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/EmptyPackageProcedure)
    - [Resurrecting EPEL RPMs](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/ResurrectingEPELPackages)
    - [How to write tests](https://github.com/opensciencegrid/osg-test)
    - [Creating the VO client package](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/CreateVOClient)
    - [Process for distributing VO data](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/DistributingVOData)
    - [New team member setup](software/new-team-member)
    - [Migrating documents to markdown](software/markdown-migration.md)
    - [Documentation tips](documentation/writing-documentation)
    - [Documentation snippets](https://twiki.opensciencegrid.org/bin/view/Documentation/Release3/DocumentationSnippets)
    - [Effort Tracking](software/effort-tracking)
    - [Promoting packages from upcoming to main](software/upcoming-to-main)
    - [Repository management](software/repository-management)
- Software-specific notes
    - [Component notes](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/ComponentHome)
    - [Team Expertise](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/TeamExpertise)
    - [Running CE scalability tests](software/ce-test-scaling)
    - [BeStMan2 notes](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/BestmanDevelopment)
    - [Building Globus](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/BuildingGlobus)
    - [Globus mass update procedure](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/GlobusMassUpdateProcedure)
    - [Globus patches](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/GlobusPatches)
    - [glideinWMS factory IP ranges](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/FactoryIPRanges)
    - [OSG PKI tools overview](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/PkiOverview)
- Projects and policies
    - [Software projects](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/Projects)
    - [Plans for future releases](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/ReleasePlanning)
    - [GitHub migration](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/GitHubMigrationProposal)
    - [Automated testing](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/TestingHome)
    - [Plans for CA certificate RPMs](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/RPMCAs)
    - [Software design docs](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/DesignDocs)
    - [IPv6 compatibility](https://twiki.opensciencegrid.org/bin/view/Trash/Sandbox/OperationsServicesIPv6CompatibilityTable)

### Release

- [Release policy](release/release-policy)
- [Release schedule](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/SoftwareReleaseSchedule)
- [Acceptance testing procedures](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/AcceptanceTestingHome)
- [How to cut a new release](release/cut-sw-release)
- [How to cut a data release](release/cut-data-release.md)
- [Manual testing recipes](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/TestingRecipes)

### Infrastructure

- Koji
    - [Koji infrastructure overview](infrastructure/koji-inf-overview)
    - [Koji workflow](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/KojiWorkflow)
    - [KojiHub setup](infrastructure/koji-hub-setup)
    - [Rebuilding for new koji build targets](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/KojiMassRebuilds)
    - [Restoring Koji](infrastructure/koji-restore-recipe)
    - [Notes on Koji permissions and policy](infrastructure/koji-policy-writing)
    - [Koji SL6 upgrade notes](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/KojiSL6Upgrade)
    - [Koji initial install](infrastructure/koji-initial-install)
- Other
    - [Madison ITB](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/MadisonITB)
    - [Infrastructure contacts](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/InfSupportContacts)
    - [Yum repository SLA](https://twiki.opensciencegrid.org/bin/view/Operations/SoftwareRepoServiceLevelAgreement)
    - [OSG software repository use cases](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/SoftwareRepoUseCases)

### Support

- [Software support](policy/software-support)
- [Triage duty](https://twiki.opensciencegrid.org/bin/view/SoftwareTeam/TriageDuty)
