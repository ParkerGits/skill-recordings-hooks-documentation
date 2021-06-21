# Documenting SkillRecordings Next Starter Hooks

## use-bundle-progress.tsx

### useBundleProgress(bundle: any): Object

[Shape of bundle object (mock)](https://github.com/skillrecordings/next-skill-product-starter/blob/main/data/bundles.development.json)

Default export. Fetches bundle progress data using bundle slug.

Returns an object containing that `data` along with `getModuleProgress` 

### getModuleProgress(slug: string): Object

Searches above bundle progress data for module with corresponding `slug` key. 

Returns an object containing
- `totalLessons` *(number)*: number of lessons in module
- `nextLesson` *(Object | null)*: the next lesson in module to be completed
- `completedLessons` *(Object[])*: an array containing the subset of module lessons with property state === "completed"
- `completedLessonsCount` *(number)*: the number of completed lessons in module
- `percentComplete` *(number)*: ratio of completedLessonsCount to totalLessons rounded to the nearest integer. As a percent, not a decimal.
- `isModuleCompleted` *(boolean)*: true if completedLessonsCount === totalLessons, false otherwise.
- `isModuleInProgress` *(boolean)*: true if completedLessonsCount is nonzero, false otherwise.

### resourceIsCompleted(resource: any): boolean

Returns true if lesson (resource) object has property state === "completed"

Exported indepently from `useBundleProgress`.

## use-achievements.ts

### useAchievements(purchasedBundle: any): Achievement[] | any

Uses `useBundleProgress` hook to fetch bundle progress data for `purchasedBundle` object. Filters out modules with no lessons, then maps modules to `true` if property state === 'completed' and to `false` otherwise. Uses this `completedArr` to determine which Achievements have been earned by the user. 

Returns an array of Achievement objects.

### Achievement Object Properties
- `title` *(string)*: title of achievement
- `earned` *(boolean)*: true if achievement has been earned by the user, false otherwise. Conditions for earning each achievement outlined below.
- `image` *(string)*: URL of Achievement image
- `link` *(Object)*
  - `children` *(string)*: child of link element to be displayed.
  - `onClick` *(Function () => void)*: onClick behavior of link element to be displayed.

### Conditions for earning each Achievement
- "Finish 1st Module": `completedArr[0] === true`
- "Finish 3 Modules": `completedArr` contains at least 3 `true` elements. `completedArr.filter((i) => i === true).length >= 3`
- "Finish 5 Modules": `completedArr` contains at least 5 `true` elements. `completedArr.filter((i) => i === true).length >= 5`
- "Finish **all** modules": `completedArr.filter((i) => i === true).length >= 5`
  - Is this intended? Or should we have `completedArr.filter((i) => i === true).length === completedArr.length`