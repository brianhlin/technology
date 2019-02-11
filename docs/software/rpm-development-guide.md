RPM Development Guide
=====================

This page documents technical guidelines and details about RPM development for the OSG Software Stack. The procedures, conventions, and policies defined within are used by the OSG Software Team, and are recommended to all external developers who wish to contribute to the OSG Software Stack.

Principles
----------

The principles below guide the design and implementation of the technical details that follow.

-   Packages should adhere to community standards (e.g., [Fedora Packaging Guidelines](http://fedoraproject.org/wiki/PackagingGuidelines)) when possible, and significant deviations must be documented
-   Every released package must be reproducible from data stored in our system
-   Source code for software should be clearly separable from the packaging of that software
-   Upstream source files (which should not be modified) should be clearly separated from files owned by the OSG Software Team
-   Building source and binary packages from our system should be easy and efficient
-   External developers should have a clear and effective system for developing and contributing packages
-   We should use standard tools from relevant packaging and development communities when appropriate

Contributing Packages
---------------------

We encourage all interested parties to contribute to OSG Software, and all the infrastructure described on this page should be friendly to external contributors.

-   To participate in the packaging community: You must subscribe to the <osg-software@opensciencegrid.org> email list. Subscribing to an OSG email list is [described here](https://listserv.fnal.gov/users.asp#subscribe%20to%20list).
-   To create and edit packages: [Obtain access to VDT SVN](http://vdt.cs.wisc.edu/internal/svn.html).
-   To upload new source tarballs: You must have a cs.wisc.edu account with write access to the VDT source tarball directory. Email the osg-software list and request permission.
-   To build using the OSG's Koji build system: You must have a valid grid certificate and a Koji account. Email the osg-software list with your cert's DN and request permission.

Development Infrastructure
--------------------------

This section documents most of what a developer needs to know about our RPM infrastructure:

-   Upstream Source Cache — a filesystem scheme for caching upstream source files
-   Revision Control System — where to get and store development files, and how they are organized
-   Build System — how to build packages from the revision control system
-   Yum Repository — the location and organization of our Yum repository, and how to promote packages through it

### Upstream Source Cache

One of our principles (every released package must be reproducible from data stored in our system) creates a potential issue: If we keep all historical source data, especially upstream files like source tarballs and source RPMs, in our revision control system, we may face large checkouts and consequently long checkout and update times.

Our solution is to cache all upstream source files in a separate filesystem area, retaining historical files indefinitely. To avoid tainting upstream files, our policy is to leave them unmodified after download.

#### Locating Files in the Cache

Upstream source files are stored in the filesystem as follows:

> `/p/vdt/public/html/upstream/<PACKAGE>/<VERSION>/<FILE>`

where:

| Symbol      | Definition                                                                | Example            |
|:------------|:--------------------------------------------------------------------------|:-------------------|
| `<PACKAGE>` | Upstream name of the source package, or some widely accepted form thereof | `ndt`              |
| `<VERSION>` | Upstream version string used to identify the release                      | `3.6.4`            |
| `<FILE>`    | Upstream filename itself                                                  | `ndt-3.6.4.tar.gz` |

The authoritative cache is the VDT webserver, which is fully backed up. The Koji build system uses this cache.

Upstream source files are referenced from within the revision control system; see below for details.

#### Contributing Upstream Files

You must make sure that any new upstream source files are cached on the VDT webserver before building the package via Koji. You have two options:

-   If you have access to a UW–Madison CSL machine, you can scp the source files directly into the AFS locations using that machine
-   If you do not have such access, write to the osg-software list to find someone who will post the files for you

#### Git/GitHub Hosted Upstream Files

It is also possible to pull sources and spec files from remote Git or GitHub repos instead of our source cache.
See the [upstream dir info](#upstream) for more information.

### Revision Control System

All packages that the OSG Software Team releases are checked into our Subversion repository.

#### Subversion Access

Our Subversion repository is located at:

>     https://vdt.cs.wisc.edu/svn

[Procedure for offsite users obtaining access to Subversion](http://vdt.cs.wisc.edu/internal/svn.html)

Or, from a UW–Madison Computer Sciences machine:

>     file:///p/condor/workspaces/vdt/svn

The current SVN directory housing our native package work is `$SVN/native/redhat` (where `$SVN` is one of the ways of accessing our SVN repository above). For example, to check out the current package repository via HTTPS, do:

```console
[you@host]$ svn co https://vdt.cs.wisc.edu/svn/native/redhat
```

#### OSG-Owned Software

OSG-owned software goes into GitHub under the `opensciencegrid` organization. Files are organized as the developer sees fit.

It is strongly recommended that each software package include a top-level Makefile with at least the following targets:

| Symbol     | Purpose                                                                               |
|:-----------|:--------------------------------------------------------------------------------------|
| `install`  | Install the software into final FHS locations rooted at `DESTDIR`                     |
| `dist`     | Create a distribution source tarball (in the current section directory) for a release |
| `upstream` | Install the distribution source tarball into the upstream source cache                |

#### Packaging Top-Level Directory Organization

The top levels of our Subversion directory hierarchy for packaging are as follows:

> `native/redhat/<SECTION>/<PACKAGE>`

where:

| Symbol      | Definition                                 | Example                                                    |
|:------------|:-------------------------------------------|:-----------------------------------------------------------|
| `<SECTION>` | Development section                        | Standard Subversion sections like `trunk` and `branches/*` |
| `<PACKAGE>` | Our standardized name for a source package | `ndt`                                                      |

#### Package Directory Organization

Within a source package directory, the following files (detailed in separate sections below) may exist:

|             |           |                                                                                                           |
|-------------|-----------|-----------------------------------------------------------------------------------------------------------|
| `README`    | text file | package notes, by and for developers                                                                      |
| `upstream/` | directory | references to the upstream source cache and other kinds of upstream files                                 |
| `osg/`      | directory | overrides and patches of upstream files, plus new files, which contribute to the final OSG source package |

##### README

This is a free-form text file for developers to leave notes about the package. Please document anything interesting about how you procured the upstream source, the reasons for the modifications you made, or anything else people might need to know in order to maintain the package in the future. Please document the *why*, not just the *what*.

##### upstream

Within the per-package directories of the revision control system, there must be a way to refer to cached files. This is done with small text files that (a) are named consistently, and (b) contain the location of the referenced file as its contents.

A reference file is named:

> `<DESCRIPTION>.<TYPE>.source`

where:

| Symbol          | Definition                                             | Example                    |
|:----------------|:-------------------------------------------------------|:---------------------------|
| `<DESCRIPTION>` | Descriptive label of the source of the referenced file | `developer`, `epel`, `emi` |
| `<TYPE>`        | Type of referenced file                                | `tarball`, `srpm`          |

and contain references to cached files, Git repos, and comments.
which start with `#` and continue until the end of the line.
It is useful to add the source of the upstream file into a comment.

###### Cached files

To reference files in the upstream source cache, use the upstream source cache path defined above, without the prefix component:

> `<PACKAGE>/<VERSION>/<FILE>`

!!! example
    The reference file for `globus-common`'s source tarball is named `epel.srpm.source` and contains:

        globus-common/16.4/globus-common-16.4-1.el6.src.rpm
        # Downloaded from 'http://dl.fedoraproject.org/pub/epel/6/SRPMS/globus-common-16.4-1.el6.src.rpm'

###### Git repos

!!! warning
    OSG software policy requires that all Git and GitHub repos used for building software have mirrors at the UW.
    Many software repos under the [opensciencegrid GitHub organization](https://github.com/opensciencegrid) are already mirrored.
    If you are uncertain, or have a new project that you want mirrored, send email to <osg-software@opensciencegrid.org>.

!!! note
    This feature requires OSG-Build 1.11.2 or later.

!!! note
    You can use a shorter syntax for GitHub repos -- see below.

    See also [advanced features for Git and GitHub repos](#advanced-features-for-git-and-github-repos).

To reference tags in Git repos, use the following syntax (all on one line):

> `type=git url=<URL> name=<NAME> tag=<TAG> hash=<HASH>`

where:

| Symbol   | Definition                       | Example                                            |
|:---------|:---------------------------------|:---------------------------------------------------|
| `<URL>`  | Location of the Git repo         | `https://github.com/opensciencegrid/osg-build.git` |
| `<NAME>` | Name of the software (optional)  | `osg-build`                                        |
| `<TAG>`  | Git tag to use                   | `v1.11.2`                                          |
| `<HASH>` | Full 40-char Git hash of the tag | `5bcf48c442d21b1e8c93a468d884f84122f7cc9e`         |

!!! note
    `<NAME>` is optional; if not present, OSG-Build will use the last component of the URL, without the `.git` suffix.

    The tarball will be called `<NAME>-<VERSION>.tar.gz` where `<VERSION>` is `<TAG>` without the `v` prefix (if there is one).

!!! example
    The reference file for `osg-build`'s repo is named `osg.github.source` and contains:

        type=git url=https://github.com/opensciencegrid/osg-build.git name=osg-build tag=v1.11.2 hash=5bcf48c442d21b1e8c93a468d884f84122f7cc9e

    This results in a tarball named `osg-build-1.11.2.tar.gz`.

In addition, if the repository contains a file called `rpm/<NAME>.spec`, it will be used as the spec file for the build
(unless overridden in the `osg` directory).


###### GitHub repos

!!! warning
    OSG software policy requires that all Git and GitHub repos used for building software have mirrors at the UW.
    Many software repos under the [opensciencegrid GitHub organization](https://github.com/opensciencegrid) are already mirrored.
    If you are uncertain, or have a new project that you want mirrored, send email to <osg-software@opensciencegrid.org>.

!!! note
    This feature requires OSG-Build 1.12.2 or later.

!!! note
    See also [advanced features for Git and GitHub repos](#advanced-features-for-git-and-github-repos).

To reference tags in GitHub repos, use the following syntax (all on one line):

> `type=github repo=<OWNER>/<PROJECT> tag=<TAG> hash=<HASH>`

where:

| Symbol      | Definition                       | Example                                    |
|:------------|:---------------------------------|:-------------------------------------------|
| `<OWNER>`   | Owner of the GitHub repo         | `opensciencegrid`                          |
| `<PROJECT>` | Name of the project              | `osg-build`                                |
| `<TAG>`     | Git tag to use                   | `v1.12.2`                                  |
| `<HASH>`    | Full 40-char Git hash of the tag | `cff50ffe812282552cedae81f3809d3cf7087a3e` |

!!! note
    The tarball will be called `<PROJECT>-<VERSION>.tar.gz` where `<VERSION>` is `<TAG>` without the `v` prefix (if there is one).

!!! example
    You can refer to the 1.12.2 release of osg-build with this line:

        type=github repo=opensciencegrid/osg-build tag=v1.12.2 hash=cff50ffe812282552cedae81f3809d3cf7087a3e

    This results in a tarball named `osg-build-1.12.2.tar.gz`.

In addition, if the repository contains a file called `rpm/<PROJECT>.spec`, it will be used as the spec file for the build
(unless overridden in the `osg` directory).

##### Typical workflow when building out of GitHub repos

This workflow requires write access for the repository of the package that you would like to build in the
`opensciencegrid` GitHub organization:

1. Clone the repository for the package that you would like to build from the `opensciencegrid` organization
1. Create a new branch in your local clone named after the version tag you would like to build, e.g. `v1.12.2`.
   Update the `tag` in `upstream/osg.github.source` to point at your new branch.
1. Make packaging changes:
    1. Make and commit your changes to the new branch
    1. Push your branch to the `opensciencegrid`'s repository
    1. Attempt a scratch build
    1. If the build fails, repeat steps a-c
    1. Submit a PR to merge changes upstream
1. After changes have been merged, finalize the build
    1. Delete the versioned branch that you created
    1. Tag the final version on the upstream fork (this should be the same as the versioned branch name)
    1. Update the `hash` in `upstream/osg.github.source`  to point at the git SHA corresponding to the versioned branch
    1. Commit the changes to `upstream/osg.github.source` in SVN
    1. Build the version that will go through the normal software cycle

!!! note
    Packaging-only changes should be tagged with a release number of the format `v<version>-<release>`, e.g. `v3.4.23-2`

##### Advanced features for Git and GitHub repos

!!! note
    These features require OSG-Build 1.12.2 or later.

The following features make software development in Git and GitHub more convenient:


-   Support for RPM release numbers in Git tags:

    If the tag for the software contains a dash, as in `v1.12.2-1`,
    it is assumed that the text after the dash is the RPM release instead of the software version.
    The RPM release is not included in the tarball.
    That is, the project `osg-build` with the tag `v1.12.2-1` will result in a tarball named `osg-build-1.12.1.tar.gz`,
    not `osg-build-1.12.1-1.tar.gz`.

-   Can specify tarball name in the .source file:

    The new `tarball` attribute allows you to specify the name of the tarball and directory that the repo contents will be put into.
    The syntax is `tarball=<NAME>.tar.gz`.
    The extension must be `.tar.gz`, no other archive formats are supported.
    The directory inside the tarball will then be `<NAME>/`.

-   Can specify hash and tarball instead of tag (scratch and local builds only):

    For local builds (rpmbuild and mock tasks) and Koji scratch builds,
    you can omit the `tag` attribute and use only the hash to determine what to check out.
    You *must* specify the `tarball` attribute in this case.
    Non-scratch Koji builds will still require a tag.

-   Can ignore hash mismatch (scratch and local builds only):

    For local builds (rpmbuild and mock tasks) and Koji scratch builds, a hash mismatch will result in a warning.
    Non-scratch Koji builds will still consider it an error.

-   Can use a branch as the tag:

    The `tag` attribute can refer to a branch instead of a tag, e.g. `tag=master`.

Combining the last two features can really speed up package development.
For example, you can use this to make scratch builds of the current master:

    type=github repo=<OWNER>/<PROJECT> tarball=<PROJECT>-<VERSION>.tar.gz tag=master hash=0

This might also be useful as part of a continuous integration scheme (e.g. Travis-CI).


##### osg

The `osg` directory contains files that are owned by the OSG Software Team and that are used to create the final, released source package. It may contain a variety of development files:

-   An RPM `.spec` file, which overrides any spec file from a referenced source
-   Patch (`.patch`) or replacement files, which override any same-named file from the top-level directory of a referenced source
-   Other files, which must be explicitly placed into the package by the spec file

#### Generated directories

The following directories may be generated by our build tool, [OSG-Build](/software/osg-build-tools). They are not under revision control.

|                               |                                                                                      |
|-------------------------------|--------------------------------------------------------------------------------------|
| `_upstream_srpm_contents/`    | expanded contents of a cached upstream source package                                |
| `_upstream_tarball_contents/` | expanded contents of all cached upstream source tarballs                             |
| `_final_srpm_contents/`       | the final contents of the OSG source package                                         |
| `_build_results/`             | OSG source and binary packages resulting from a build                                |
| `_quilt/`                     | expanded, patched contents of the upstream sources, as generated by the `quilt` tool |

##### \_upstream\_srpm\_contents

The `_upstream_srpm_contents` directory contains the files that are part of the upstream source package. It is a volatile record of the upstream source for developer use.

##### \_upstream\_tarball\_contents

The `_upstream_tarball_contents` directory contains the files that are part of the upstream source tarballs. It is generated by the package build tool if the `--full-extract` option is passed. It is not used for anything by the build tool, but meant as a convenience to allow the developer to look inside the upstream sources (for making patches, etc.).

##### \_final\_srpm\_contents

The `_final_srpm_contents` directory contains the final files that are part of the released source package. It is a volatile record of a build for developer use.

##### \_build\_results

The `_build_results` directory contains the source and binary RPMs that are produced by a local build. It is a volatile record of a build for developer use.

##### \_quilt

The `_quilt` directory contains the unpacked sources after they have been patched using the [quilt](http://savannah.nongnu.org/projects/quilt) utility. This allows easier patch development.

#### Packaging Organization Examples

##### Use Case 1: Packaging an Upstream Source Tarball

When the OSG Software Team packages an upstream source tarball, for which there is no existing package, the source tarball is referenced with a .source file and we provide a spec file and, if necessary, patches. For example, RSV is provided as a source tarball only. Its package directory contains:

>     rsv/
>         osg/
>             rsv.spec
>         upstream/
>             developer.tarball.source

##### Use Case 2: Passing Through a Source RPM

When the OSG Software Team simply provides a copy of an existing source RPM, it is referenced with a .source file and that is it. For example, we do not modify the `globus-common` source RPM from EPEL. Its package directory contains:

>     globus-common/
>         upstream/
>             epel.srpm.source

##### Use Case 3: Modifying a Source RPM

When the OSG Software Team modifies an existing source RPM, it is referenced with a .source file and then all changes to the upstream source are contained in the `osg` directory. For example, we use this mechanism for the `globus-ftp-client` package, originally obtained from EPEL. Its package directory contains:

>     globus-ftp-client/
>         osg/
>             globus-ftp-client.spec
>             1853-ssh-bin.patch
>         upstream/
>             epel.srpm.source

### Build Process

1.  All necessary information to create the package will be committed to the VDT source code repository (see below)
2.  The [OSG build tools](/software/osg-build-tools) will take those files, create a source RPM, and submit it to our Koji build system

Developers may use `rpmbuild` and `mock` for faster iterative development before submitting the package to Koji. `osg-build` may be used as a wrapper script around `rpmbuild` and `mock`.

### OSG Software Repository

OSG Operations maintains the Yum repositories that contain our source and binary RPMs at `https://repo.opensciencegrid.org/osg/` and are mirrored at other institutions as well.

#### Release Levels

Every package is classified into a release level based on the amount of testing it has undergone and our confidence in its stability. When a package is first built, it goes into the lowest level (`osg-development`). The members of the OSG Software and Release teams may promote packages through the release levels, as per our [Release Policy page](/release/release-policy).

Packaging Conventions
---------------------

In addition to adhering to the [Fedora Packaging Guidelines](http://fedoraproject.org/wiki/PackagingGuidelines) (FPG), we have a few rules and guidelines of our own:

-   When we pass-through an RPM and make any changes to it (so it has an updated package number), we construct the version-release as follows:
    -   The version of the original RPM remains unchanged
    -   The release is composed of three parts: ORIGINALRELEASE.OSGRELEASE
    -   We add a distro tag based on the OSG major version and OS major version, e.g. "osg33.el6". (Use `%{?dist}` in the Release field)

Example: We copy package foobar-3.0.5-1 from somewhere. We need to patch it, so the full name-version-release (NVR) for OSG 3.3 on EL 6 becomes `foobar-3.0.5-1.1.osg33.el6` Note that we added ".1.osg33.el6" to the release number. If we update our packaging (but still base on foobar-3.0.5-1), we change to ".2.osg33.el6". In the spec file, this would look like:

```spec
Release: 1.2%{?dist}
```

Packaging for Multiple Distro Versions
--------------------------------------

### Conditionalizing spec files

Some packages may need different build behavior between major versions of the OS; RPM conditional statements will be used to handle this.

The following macros are defined:

| Name    | Value (EL6)        | Value (EL7)        |
|:--------|:-------------------|:-------------------|
| `%rhel` | `6`                | `7`                |
| `%el6`  | `1`                | *undefined* or `0` |
| `%el7`  | *undefined* or `0` | `1`                |

Here's how to use them:

```spec
%if 0%{?el6}
# this code will be executed on EL 6 only
%endif

%if 0%{?el7}
# this code will be executed on EL 7 only
%endif

%if 0%{?rhel} >= 7
# this code will be executed on EL 7 and newer
%endif
```

(There does not seem to be an `%elseif`).

The syntax `%{?el6}` expands to the value of the `%el6` macro if it is defined, and to the empty string if not; the `0` is there to keep the condition from being empty in the `%if` statement if the macro is not defined.


Renaming or Removing Packages
-----------------------------

Occasionally we want to cause a package to be removed on update, or replaced by a package with a different name.

For the most part, the [Fedora Packaging Guidelines page on renames](https://fedoraproject.org/wiki/Packaging:Guidelines#Renaming.2FReplacing_Existing_Packages) shows how to do that.
The exception is that we do not have the equivalent of a `fedora-obsolete-packages` package, so in order to force the removal of an entire package (not a subpackage), we have to dummy out the package instead -- see below.
(This should be a rare situation.)

!!! note
    After doing a rename or a removal, you must update all the packages and subpackages that require the package being removed or renamed, and change or remove the requirements as appropriate.

To find packages that require the old package at run time, set up a host with the OSG repos and install the `yum-utils` RPM.
Then, run:
```console
$ repoquery --plugins --whatrequires $OLDPACKAGE
```

To find packages that require the old package at build time, install `osg-build`, and do this from a checkout of the OSG repos:
```console
$ osg-build prebuild *
$ for srpm in */_final_srpm_contents/*.src.rpm; do \
    echo "***** $srpm *****"; \
    rpm -q --requires -p $srpm | grep -w $OLDPACKAGE; \
  done
```
(examine the output to avoid false matches)

!!! note
    Carefully test these changes, including places where the old package may be brought in indirectly.


### Dummying out a package

In order to forcibly remove an entire package with no replacement, you have to replace the package with one that does nothing.
This is because there is no package that will "obsolete" the old package.

Do the following for the main package and any subpackages it may have:

-   Change the Summary to "Dummy package"
-   Change the %description to:

    > This is an empty package created for %RED%$REASONS%ENDCOLOR%.
    > It may safely be removed.

-   Remove all Requires and Obsoletes lines
-   Do not remove Provides lines
-   Remove %pre and %post scriptlets
-   Unless there is a good reason not to, remove %preun and %postun scriptlets
-   Empty the %files section

