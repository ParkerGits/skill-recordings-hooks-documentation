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
- `link` *(Object, optional)*
  - `children` *(string)*: child of link element to be displayed.
  - `onClick` *(Function () => void)*: onClick behavior of link element to be displayed.

### Conditions for earning each Achievement
- "Finish 1st Module": `completedArr[0] === true`
- "Finish 3 Modules": `completedArr` contains at least 3 `true` elements. `completedArr.filter((i) => i === true).length >= 3`
- "Finish 5 Modules": `completedArr` contains at least 5 `true` elements. `completedArr.filter((i) => i === true).length >= 5`
- "Finish **all** modules": `completedArr.filter((i) => i === true).length >= 5`
  - Is this intended? Or should we have `completedArr.filter((i) => i === true).length === completedArr.length`

## use-quiz-question.ts

### useQuestion(question: Question): Object

Returns an object containing
- `isCorrectAnswer` *(Function (any) => boolean)*: returns true if a correct answer exists and if the `answer` property of the argument is either one of the many correct answers or is exactly the single correct answer.
- `answeredCorrectly` *(boolean)*: returns true if the user has answered and if the user answer(s) is (are) either one of the multiple correct answers or the single correct answer.
- `onAnswer` *(Function)*: The onSubmit function defined in the useFormik hook. Makes a POST request to /api/answer with the tagId property of the question argument, then sets the answer state to the user's selected choices.
- `hasMultipleCorrectAnswers` *(boolean)*: true if the `correct` property of the question argument is an array.
- `isSubmitting` *(boolean)*: true while POST request from Formik onSubmit function (onAnswer) is being made, false otherwise.
- `isAnswered` *(boolean)*: true if the user answer is not empty, false otherwise.
- `formik` *(Object)*: the useFormik hook. Provides initial values, a submission handler (same as onAnswer above), and object schema validation using Yup.
- `error` *(any)*: presumably the error state of the hook, but this is never set within the code of the hook, so it will always be undefined.

### Question Type
- `question` *(string)*: the actual content of the question.
- `type` *('multiple-choice' | 'essay')*: question can either have multiple-choice or written answer format.
- `tagId` *(number)*: used in the API call to /api/answer, presumably to identify the exact question.
- `correct` *(string[] | string, optional)*: a string or array of strings representing correct the answer(s) to the question.
- `answer` *(string, optional)*: text to display once the question has been answered.
- `choices` *({answer: string; label: string}[], optional)*: an array of all possible answer choices for a multiple choice question.

## use-purchased-bundle.tsx

### usePurchasedBundle(bundles: SellableResource[]): SellableResource | undefined

Grabs the user's sitePurchases and authToken from the `useViewer()` context hook. Searches the bundles argument for the bundle that the user has purchased (if one has been purchased) and fetches data about that bundle. This bundle data is returned if the user has a defined authToken and has purchased a bundle with a defined URL. Returns undefined otherwise.