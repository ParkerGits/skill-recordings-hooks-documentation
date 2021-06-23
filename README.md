# Documenting SkillRecordings Next Starter Hooks 

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

## use-cio.tsx

## use-commerce-machine.tsx

### useCommerceMachine({sellable: SellableResource, upgradeFromSellable: SellableResource})

Grabs the slug from the `sellable` argument and the userId from `useViewer`. Initially creates an XState commerce state machine from the `sellable` and `upgradeFromSellable` arguments, and only recreates this machine when `sellable`, `upgradeFromSellable`, userId, or the sellable slug changes. Returns a tuple of `[state, send, service]` for the commerce state machine given by the XState `useMachine()` hook. 

## use-convertkit.tsx

## use-purchased-bundle.tsx

### usePurchasedBundle(bundles: SellableResource[]): SellableResource | undefined

Grabs the user's sitePurchases and authToken from the `useViewer()` context hook. Searches the bundles argument for the bundle that the user has purchased (if one has been purchased) and fetches data about that bundle. This bundle data is returned if the user has a defined authToken and has purchased a bundle with a defined URL. Returns undefined otherwise.

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

## use-redirect-unclaimed-bulk-to-invoice.tsx

### useRedirectUnclaimedBulkToInvoice(): boolean

The `useViewer` context determines if the user is an "unclaimed bulk purchaser": that is, the user has at least one bulk site purchase. If the user is an unclaimed bulk purchaser, and the `useLoginRequired()` hook has verified that the user is logged in, then the user is sent to the /invoice page.

Returns the value of isVerifying, which is true while the `useLoginRequired()` hook is verifying that the user is logged in and while the user is an unclaimed bulk purchaser.

## use-required-login.tsx

### useLoginRequired(): boolean

Attempts to fetch an access token from cookies or localstorage. If this token is null, sends user to login page.

Returns the boolean value of isVerifying, which is true while there is no valid token stored (the user has not logged in). In other words, this value is true when the hook is still verifying that the user has access to the page.

Login is disabled in development.