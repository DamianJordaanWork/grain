# RecentEntriesScreen (`lib/screens/recent_entries_screen.dart`)

State: `_groupByProject` toggle (local `setState`). Listens to `TimeEntryProvider` via `addListener` in `didChangeDependencies` (not `initState`) to survive tab switches; triggers `AdoService.prefetchForEntries` on each change.

**Layout (top → bottom):**
1. `FilterChip` toolbar — "Group by project" toggle
2. Date nav row — `chevron_left` / tappable date label / `chevron_right` (capped at today)
3. `_WeekSummaryStrip` — Mon–Sun day chips + `WeeklyProgressRing`. Two variants: `_buildCompact` (narrow) and `_buildEmphasized` (≥ `HarvestTokens.kWideBreakpoint`); emphasized shows day-of-month numerals + per-day `LinearProgressIndicator` against `ProjectCategoryProvider.dailyGoalHours`
4. Entry list — flat `SliverList` of `TimeEntryCard`, or `_buildGroupedList` when grouped. Grouped view uses `groupBy` (collection pkg) keyed on `projectId`, sorted by `createdAt` desc; each group rendered by `_buildProjectGroup` → `_ProjectGroupHeader`
5. `_DailyProgressBar` — sticky bottom bar; goal from `ProjectCategoryProvider.dailyGoalHours`, `HarvestTokens.warn` color when over; when viewing today shows a tick marker at the expected position + "Expected: Xh Ym" label (proportional: `elapsedRatio × dailyGoalHours`)

**Private widgets (all in this file):**
- `_WeekSummaryStrip` — reads `weeklyGoalHours` and `dailyGoalHours` from `ProjectCategoryProvider`; `_DayData` holds per-day state
- `_DailyProgressBar` — reads `dailyGoalHours`, `workDayStart`, `workDayEnd` from `ProjectCategoryProvider`; accepts `isToday` to conditionally show expected time tick + label; shows "Xh Ym remaining" or "+Xh Ym over goal"
- `_ProjectGroupHeader` — colored code badge (from `ProjectCategoryProvider.categoryFor`), project name + client, entry count, total hours
- **`lib/widgets/`** — `WorkItemPreview` fetches and renders a live ADO work item card (debounced 600 ms). `TimeEntryCard` renders an entry with an optional embedded ADO card.
