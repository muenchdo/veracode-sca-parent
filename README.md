# veracode-sca-parent

This repository is intendend to be used to showcase an issue with performing a Veracode SCA scan of a directory which is a Git submodule.

```
git clone https://github.com/muenchdo/veracode-sca-parent.git
git clone https://github.com/muenchdo/veracode-sca-submodule.git

cd veracode-sca-parent
git submodule update --init
```

Running a scan from the repository root expectedly fails, because there is no code to scan there:
```
srcclr scan .
```
**Output:**
```
Veracode SCA agent scanning engine ready
Running the Go get scanner
Processing results...
Processing results complete

An error occurred while attempting to get the project's dependencies. Please ensure that all the project's dependencies have been installed locally.
You can also install the dependencies during scanning by either setting the environment variable 'SRCCLR_FORCE_GO_INSTALL=true', or setting 'force_go_install: true' in srcclr.yml.
```

However, running the scan by passing the submodule directory which contains the Go application unexpectedly fails:
```
srcclr scan ./veracode-sca-submodule
```
**Output:**
```
Unable to collect Git metadata
Veracode SCA only supports git repos and requires an executable git to be on the PATH
```

Running the scan directly from the submodule directory which contains the Go application also unexpectedly fails:
```
cd veracode-sca-submodule
srcclr scan .
```
**Output:**
```
Unable to collect Git metadata
Veracode SCA only supports git repos and requires an executable git to be on the PATH
```

Running the scan directly in the repository which is used a submodule succeeds, indicating an issue with how the scanner handles submodules.
```
cd ../../veracode-sca-submodule
srcclr scan .
```
**Output:**
```
Veracode SCA agent scanning engine ready
Running the Go mod scanner
Scanning completed
Found 11 lines of code
Processing results...
Processing results complete

Summary Report
Scan ID                                        ***
Scan Date & Time                               Nov 22 2024 10:53AM CET
Account type                                   ENTERPRISE
Scan engine                                    3.8.78 (latest 3.8.78)
Analysis time                                  5 seconds
User                                           ***
Project                                        ***
Package Manager(s)                             Go mod

Open-Source Libraries
Total Libraries                                3
Direct Libraries                               1
Transitive Libraries                           2
Vulnerable Libraries                           0
Third Party Code                               99.9%

Security
With Vulnerable Methods                        0
Critical Risk Vulnerabilities                  0
High Risk Vulnerabilities                      0
Medium Risk Vulnerabilities                    0
Low Risk Vulnerabilities                       0

Licenses
Unique Library Licenses                        2
Libraries Using GPL                            0
Libraries With High Risk License               0
Libraries With Medium Risk License             0
Libraries With Low Risk License                4
Libraries With Multiple Licenses               1
Libraries With Unassessable License            0
Libraries With Unrecognizable License          0

Issues
Issue ID     Issue Type          Severity    Description                       Library Name & Version In Use
351125660    Outdated Library    3.0         Latest version at scan: v3.1.0    rsc.io/quote v1.5.2


Full Report Details                            ***
```
