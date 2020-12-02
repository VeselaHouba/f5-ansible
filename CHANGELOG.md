# Changelog

Please document all changes here.
Follow rules from [Keep a Changelog](https://keepachangelog.com/en/0.3.0/)

## [Unreleased]

### Added
- Python implementation of rewrite profile (not provided by official f5 modules)

## [2.0.8]
### Fixed
- tasks/routes.yml - Satisfied linter
- linter findings in meta/main.yml

### Changed
- bigip_profile_persistence_universal - Switched to new ansible full-module path

### Added
- Added ansible-linter to travis

## [2.0.7]
### Added
- f5_profiles - Added bigip_profile_persistence_universal option

## [2.0.6]
### Added
- f5_pools - Option to define priority_group_activation number

## [2.0.5]
### Changed
- Split certificates deploy and undeploy, so it can be run in single play
### Added
- Name of SSL profile being created

## [2.0.4]
### Changed
- Found and fixed bug introduced in last version - variable from previous iteration was used if not explicitly reset.

## [2.0.3] - 2020-09-30
- Added option to define partitions for ASM policies
- Fixed bug with re-creating ASM policy instead of deactivating
- LTM policies are auto-generated and they inherit partition from Virtual Server

## [2.0.2] - 2020-09-24
### Changed
- **Breaking change** - ASM LTM policy is now created for each virtual server and new variable `asm_policy` is introduced. You can still use `policies` for custom definition to keep backward compatibility, but auto-generation of 1 LTM policy for 1 ASM policy is disabled.

## [1.0.0] - 2020-04-08
### Changed
- **Breaking change** - iApp template definition now has to state `name` variable. (Added due to partial deploy). Variable is not used in deploy.
