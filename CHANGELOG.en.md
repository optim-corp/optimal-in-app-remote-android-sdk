# Changelog

All notable changes to this project will be documented in this file.

## [2.3.0] - 2026-02-24

### Changed

- Added screen sharing pause feature.

> [!NOTE]
> For details on the screen sharing pause feature, please refer to [CUSTOMIZE.en.md](./docs/CUSTOMIZE.en.md).

- Added support for Android 16.

## [2.2.0] - 2025-11-07

### Changed

- Added masking feature.

> [!NOTE]
> For details on the masking feature, please refer to [CUSTOMIZE.en.md](./docs/CUSTOMIZE.en.md).

## [2.1.0] - 2025-06-23

### Changed

- Added UI design customization feature for the SDK.

> [!NOTE]
> For details on the design customization feature, please refer to [CUSTOMIZE.en.md](./docs/CUSTOMIZE.en.md).

## [2.0.3] - 2024-12-18

### Changed

- Added an SDK without voice call feature ([optimal_remote_no_voip.aar](./optimal_remote/optimal_remote_no_voip.aar)).

> [!NOTE]
> optimal_remote_no_voip.aar excludes modules related to the voice call feature, but there are no differences in other features.

- Added `ORIASession.locale` API to switch the language displayed in the SDK UI.

> [!NOTE]
> For details on switching the display language of the SDK, please refer to [CUSTOMIZE.en.md](./docs/CUSTOMIZE.en.md).

### Fixed

- Fixed an issue where enabling TextureView capture prevented capturing ViewGroups that do not contain a TextureView.

## [2.0.2] - 2024-10-01

### Changed

- Added support for Android 15.

> [!NOTE]
> Updated to support devices with 16 KB page size.

## [2.0.1] - 2024-04-09

### Changed

- Improved voice call feature.

> [!NOTE]
> Updated the version of the library used for the voice call feature.

## [2.0.0] - 2023-11-30

### Changed

- Migrated the development language of Optimal Remote In-App Remote SDK for Android from Java to Kotlin.

> [!WARNING]
> The SDK format has been changed from jar to aar.
