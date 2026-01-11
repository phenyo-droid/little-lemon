Little Lemon (Android)

A Compose-first Android app for the Little Lemon restaurant experience. It includes onboarding (registration), a home screen to browse the menu (with search + category filters), and a profile screen with logout.
Design (wireframe)

The Home screen UI is based on this wireframe:

Home screen wireframe
Features

    Onboarding: Register with first name, last name, and email (with validation).
    Menu browsing: View menu items, search by title/description, and filter by category.
    Offline-first menu: Menu is persisted locally and refreshed from the network.
    Profile: View saved user info and logout (clears session).

User flow

    First launch: complete registration → you’ll be taken to Home
    Home: browse/search/filter menu items; tap the profile icon to open Profile
    Profile: logout → returns you to Onboarding

Tech stack

    Language: Kotlin
    UI: Jetpack Compose + Material 3
    Navigation: Navigation Compose
    Networking: Ktor Client + Kotlinx Serialization
    Local storage:
        Room (menu cache)
        SharedPreferences (user session/profile)
    Images: Glide (Compose integration)
    Quality: Spotless (ktlint) + Detekt
    Build tooling: Gradle Version Catalog (gradle/libs.versions.toml)

Project structure

This repo uses a layered, Compose-friendly structure:

app/src/main/java/com/example/littlelemon/
  app/                    // top-level app composition
  ui/
    navigation/           // NavHost + routes
    theme/                // Compose theme
  feature/
    onboarding/           // registration flow
    home/                 // home/menu flow
    profile/              // profile/logout flow
  domain/                 // pure Kotlin models + contracts
  data/                   // Room + networking + mappers + repository impl
  di/                     // manual DI container (AppContainer)

Architecture overview

    UI (Compose) follows unidirectional data flow:
        *Route collects state from a ViewModel and wires callbacks.
        *Screen is stateless and renders UiState.
        One-off events (snackbars/navigation triggers) use an effect flow.

    Navigation uses a real back stack (Navigation Compose):
        Onboarding → Home → Profile

    Session & start destination:
        UserPreferences stores a boolean login flag.
        LittleLemonApp chooses the start route based on that flag.

    Menu data flow (offline-first):
        UI reads menu items from Room (MenuItemDao.observeMenuItems()).
        syncMenu() fetches JSON from the network and replaces the local table.

Setup
Prerequisites

    Android Studio (latest stable recommended)
    JDK 11
    Android SDK installed for the project’s configured SDK level
        minSdk: 24
        compileSdk/targetSdk: 36

Run in Android Studio

    Open this project in Android Studio
    Let Gradle sync finish
    Run the app configuration on an emulator or device

Build from the command line

./gradlew :app:assembleDebug

To install on a connected device/emulator:

./gradlew :app:installDebug

Testing

Unit tests:

./gradlew :app:testDebugUnitTest

Instrumentation tests (requires a connected device/emulator):

./gradlew :app:connectedDebugAndroidTest

Quality checks

Run all checks (includes Detekt + Spotless checks):

./gradlew check

Auto-format Kotlin sources:

./gradlew spotlessApply

Data source

Menu data is fetched from:

    https://raw.githubusercontent.com/Meta-Mobile-Developer-PC/Working-With-Data-API/main/menu.json

Troubleshooting

    Build fails due to missing SDK 36: install Android SDK Platform 36 from Android Studio’s SDK Manager.
    Menu is empty:
        ensure you have an internet connection (first sync needs it)
        retry by restarting the app (sync runs on Home start)

Git hooks (optional, but recommended)

This repo includes git hooks to keep quality high:
<img width="457" height="431" alt="Screenshot 2026-01-11 065629" src="https://github.com/user-attachments/assets/95e1ea0e-b57f-4817-afeb-13c86164ea45" />

./scripts/setup-githooks.sh

    pre-commit: runs ./gradlew spotlessCheck
    pre-push: runs ./gradlew check
    commit-msg: enforces a Conventional Commits-ish format

Commit header format:

type(scope)!: subject

Allowed type values: feat, fix, docs, style, refactor, perf, test, build, ci, chore, revert
License
No license file is included in this repository.
