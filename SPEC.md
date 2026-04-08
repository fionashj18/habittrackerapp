# App Specification
<!-- Template created with CLAUDE -->

## Overview

**HabitTracker** is a mobile app that helps users build and maintain daily habits. Users create habits, check them off each day, and track their progress through streaks and visual stats. All data is stored locally on-device — no account or internet connection required. The target audience is anyone looking for a simple, private, and distraction-free way to build better routines.

## Core Features

- **Daily check-off habits:** Create habits and mark them complete each day with a single tap.
- **Streak tracking:** Automatically track consecutive days of completion for each habit; display current and best streaks.
- **Progress stats & charts:** Visualize completion rates over time with weekly/monthly charts.
- **Reminders & notifications:** Schedule local push notifications to remind users to complete specific habits.
- **Habit management:** Create, edit, reorder, archive, and delete habits.
- **Categories & colors:** Organize habits into categories (e.g., Health, Productivity) with color coding.
- **Settings:** Configure default reminder times, theme (light/dark), and first day of week.

## User Roles

| Role       | Description                        | Permissions              |
|------------|------------------------------------|--------------------------|
| Solo User  | The single local user of the app   | Full access to all features |

Since data is local-only with no auth, there is a single implicit user role.

## Screens & Navigation

| Screen             | Purpose                                              | Navigation                              |
|--------------------|------------------------------------------------------|-----------------------------------------|
| Home (Today)       | Show today's habits as a checklist                   | Default tab; tap a habit to check it off |
| Habit Detail       | View streak, stats, and history for a single habit   | Tap habit name from Home                |
| Add / Edit Habit   | Create a new habit or edit an existing one            | FAB on Home or edit button on Detail    |
| Stats              | Overview charts for all habits (weekly/monthly)      | Bottom tab                              |
| Settings           | Theme, reminders defaults, first day of week         | Bottom tab                              |

**Navigation structure:**
- Bottom tab bar: **Today** · **Stats** · **Settings**
- Stack navigation within each tab for detail/edit screens
- Add/Edit Habit presented as a modal

## Data Model

### Entities

### Habit
- id: string (UUID)
- name: string
- description: string (optional)
- category: string (optional)
- color: string (hex color)
- reminderTime: string | null (ISO time, e.g., "08:00")
- createdAt: string (ISO datetime)
- archivedAt: string | null (ISO datetime)
- sortOrder: number

### Completion
- id: string (UUID)
- habitId: string (foreign key → Habit.id)
- date: string (ISO date, e.g., "2026-04-08")
- completedAt: string (ISO datetime)

### Category
- id: string (UUID)
- name: string
- color: string (hex color)
- sortOrder: number

### Relationships
- A **Habit** has many **Completions** (one per day max).
- A **Habit** optionally belongs to a **Category**.
- **Streaks** are computed from Completions, not stored separately.

## API & Backend

- **Authentication:** None — local-only app with no user accounts.
- **Database:** SQLite via `expo-sqlite` for structured local storage.
- **Third-party APIs:** None.

## Design & Branding

- **Color palette:** Light neutral base (#FFFFFF / #F5F5F5) with accent color for primary actions (e.g., teal #0D9488). Dark mode uses dark grays (#1A1A1A / #2D2D2D). Each habit can have its own color.
- **Typography:** System default fonts (San Francisco on iOS, Roboto on Android) for consistency and performance.
- **Style direction:** Clean, minimal, and friendly. Emphasis on whitespace and clear visual hierarchy. Checkmarks and streaks should feel satisfying (subtle haptic feedback on completion).

## Platform Targets

- [x] iOS
- [x] Android
- [ ] Web

## Notifications & Background Tasks

- **Local push notifications** via `expo-notifications` to remind users to complete specific habits.
- Each habit can have an optional daily reminder time.
- A global default reminder time can be set in Settings.
- No background sync or server-side push needed (local-only).

## Offline Behavior

The app is fully offline by design. All data is stored on-device in SQLite. No network connectivity is required for any feature.

## Analytics & Monitoring

- No analytics or crash reporting in v1 — privacy-first approach.
- Future consideration: opt-in anonymous crash reporting via `expo-updates` or Sentry.

## Constraints & Non-Goals

- **No cloud sync or multi-device support** — data lives on one device only.
- **No social features** — no sharing, leaderboards, or accountability partners.
- **No gamification beyond streaks** — no points, badges, or levels in v1.
- **No web support** in v1 — focus on native mobile experience.
- **No data export** in v1 (potential future feature).

## Open Questions

- Should archived habits be restorable, or permanently deletable only?
- Should the app support weekly or custom-frequency habits (e.g., 3x/week), or only daily?
- What should happen to a streak if the user misses one day — reset to zero, or allow a configurable "grace day"?
- Should there be an onboarding flow with suggested starter habits?
