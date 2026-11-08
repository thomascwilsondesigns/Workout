# Workout Log (personal iPhone workout app)

Single-file vanilla JS/HTML app (`index.html`, ~3,500 lines). Hosted on GitHub Pages; the filename must stay lowercase `index.html`. No build step, no backend, works offline.

## Data
- All data is in localStorage under `workoutLogDB_v1`: `{workouts:[], exercises:[]}`.
- Exercise definitions hold cues, links and notes, shared across workouts.
- Workouts hold `warmup` and `main` arrays of exercise instances. Each instance has `secondaryType` (weight, band or time) and `sets:[{reps, weight, band, time, done, warm}]`.
- `done:false` = unchecked. A missing `done` is old data and counts as checked. `warm:true` marks a W (warmup) set.
- Counted sets (`isCounted`) are checked, non-W sets. They feed progress deltas, PRs, history and stats.
- Never change the storage key or data shape without a migration. This is real training history.
- Exercise icons are looked up by lowercase exercise name (`EXERCISE_ICONS`). Renaming an exercise means renaming its icon key too.
- Nov 2026: one-time migrations via `applyExerciseRenames` (rename, or merge into an existing exercise and repoint workouts). First: built-in exercises renamed to common names, "Child's pose stretch" merged into "Child's pose" (flag `workoutLogRenames_2026_11`, backup `workoutLogDB_v1_backup_pre_renames_2026_11`). Second: band shoulder rotations merged into the cable versions, Inchworm renamed "Inchworm to lizard stretch" (flag `workoutLogCleanup_2026_11b`, backup `workoutLogDB_v1_backup_pre_cleanup_2026_11b`). Each backs up the raw data first and changes nothing if the backup fails. Third (`removeUnusedExercises`): "Foam roller hip hinge drill" and "Nordic hamstring curl" are removed only if no workout references them (flag `workoutLogCleanup_2026_11c`, backup `workoutLogDB_v1_backup_pre_cleanup_2026_11c`). Fourth: Barbell and Bodyweight hip thrust merged into "Hip thrust" (flag `workoutLogCleanup_2026_11d`, backup `workoutLogDB_v1_backup_pre_cleanup_2026_11d`). Fifth: the user's own exercises became built-in with common names (e.g. "kick stand RDL" → "Kickstand RDL", "scap plank" → "Scapular plank"; flag `workoutLogCleanup_2026_11e`), and "Scap Y's" is removed if unused (flag `workoutLogCleanup_2026_11f`). Each has a matching `workoutLogDB_v1_backup_pre_cleanup_…` key. Sixth: Short-lever Copenhagen plank merged into Copenhagen plank (flag `workoutLogCleanup_2026_11g`). Seventh: "Seated cable scapular retraction" renamed "Cable scapular retraction" (flag `workoutLogCleanup_2026_11h`). Eighth: "Banded ski pull" renamed "Banded ski" (flag `workoutLogCleanup_2026_11i`).

## Behavior that must be preserved
- Dates use the local date (`localDateStr()`), never `toISOString()` (a UTC bug bit once).
- Wheel pickers only save on real touch input. This fixes an iOS bug where backgrounding the app zeroed values. Do not remove that guard.
- Copying a workout or adding an exercise gives blank, unchecked sets, with last session's numbers shown beside the exercise. Intentional.
- PRs: weight, set volume, Epley est. 1RM, reps at a weight. A first-ever log is not a PR.
- Circuit timer (4x4 and others) launches from the Cardio 4x4 workout. It uses Web Audio, wall-clock timing and a wake lock.
- iOS Safari: 16px inputs to avoid zoom, no native confirm() (use in-app sheets), sheets sit above the timer overlays (z-index).

## Testing
Syntax-check the script block with node, then test in Playwright with a mobile viewport and touch events.

## Backlog ideas
Auto rest timer, templates, plate calculator, supersets, RPE, export/backup button, kg/lb toggle.

## Working style
Commit in small steps with clear messages and say what changed. Ask before anything that could alter saved data.
