# standards

### Note:
Suggestions for improving our design and coding standards are more than welcome. But please bring it up in a forum before it's implemented.
This can save a lot of time for developer and reviewer. Also this will ensure consistency in the codebase.

## Table of Content
- [Development Process](#development-process)
- [Coding Standards](#coding-standards)
- [Unit Testing](#unit-testing)
- [Folder Structure](#folder-structure)
- [Components](#components)
  - [Composing a component](#composing-a-component)
  - [Container components](#container-components)
  - [Component Guardians](#component-guardians)
- [StateManagement](#state-management)
  - [Forms](#forms)
- [Styling](#styling)
- [Mutation](#mutation)
- [Memoization](#memoization)
- [Hooks](#hooks)
- [Resources](#resources)
## Development process

 1. Come up with a design for the feature by breaking them into small reusable components. Define the  props and state
 2. See if existing components suite all or most of your needs and reuse those components
 3. Get approval from Component Guardians(We can figure out who all can be this, for each project) if a base component is being developed/updated
 4. If needed discuss the design with squad lead, especially when your feature has a dependency
 5. Add stories and provide knobs for each prop
 6. Write unit tests for all possible props combination and DOM updates using react-testing-library.
 9. Add README
 10. Run the application on localhost and test. Test all the scenarios. Make sure the styling/UX is as per the design provided in US.
 11. If a component being used by other pages is updated (look at references), make sure test those areas. Also add 'Impacted Areas' task under the story with the details of these impacted areas and assign tester as an owner.
 12. Run storybook using `yarn storybook`, verify all the knobs are provided. Do some basic testing.
 13. Run linting

 ## Coding Standards
- Use PascalCase for
    - Interfaces
    - Types
    - React Components
- Use camelCase for
    - function names
    - local variables
- Use arrow functions instead of anonymous functions.
- Allow implicit typing when possible to avoid redundant typing and lesser code.
    - `const widthFactor = 12;` We always know the type is number so dont need to do `const widthFactor: number = 12;`
    - `let a = getName();`  Let's assume `getName` returns a string so the type of `a` is implicit. Don't need to do `let a: string = getName();`
- Functions should have explicitly typed arguments and return types (unless the return type is `void`).

```ts
function isOne(num: number): boolean {
    return num == 1;
}
```
- The `any` type should ideally be never used. However there are cases where it is unavaoidable. In such cases,
  the lint disabling statement should only be done for the line where any is used.
- Prefer `const` to `let` while declaring variables unless the value of the variable changes.
```ts
let val = 1;
// do something
if(condition){
    val = 2;
}
```
- Avoid doing multiple loops when dealing with arrays. Specifically in our code base, where we deal with large amounts of data. Consider the below example,
```ts
const data = ['foo', 'bar', 'foobar', undefined, null, 'barfoo'];

// case 1
const filteredLengths = data.filter(x => x !== undefined && x !== null)
                         .map(y => y.length);

// case 2
const filteredLengths: number[] = [];
for(const item of data){
    if(item !== undefined && item !== null){
        filteredData.push(item.length);
    }
}
```
In this case, it is easy to chose case 1 due to inline nature and ease of use. It probably is not a big deal from a performance standpoint too but note that case 2 does 1 iteration over the items while case 1 does 2 iterations. 1st for `.filter` and then for `.map`. Especially when there are a large # of items in the array, the cost of operation in case 1, increases significantly. Consider using reduce() in such cases to reduce number of iterations.
- Defined interfaces in all cases. Predominantly use `interface`.
- Reach out to type when you want a combination. This should be generally avoided but only case where we should reach out for the `type` option.
```ts
export interface Foo{
    id: number:
    name: string
}

export interface Bar{
    data: number[]:
    values: string[]
}

// case 1:
export type FooAndBar = Foo & Bar;

export interface SomeInterface{
    val: FooAndBar
}

// case 2:
export interface SomeInterface{
    val: Foo & Bar
}

```
case 1 is preferred because of the semantics and also makes it easier to replace and refer later on.

- Use [optional chaining and nullish coalescing](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-7.html)

 ## Unit Testing
### Do's
-	Write your tests the way they would be used in the UI. This means that you should not care about rendering with props. React has that covered. Instead test how you would use that component.
-	Look through the react-testing-library cheatsheet, it has a great beginning guide to testing with that library https://testing-library.com/docs/react-testing-library/cheatsheet
-	Test every action that your component will perform
-	Test your disabled state and ensure that nothing is editable in that state
-	Test your reducers and ensure every action works.
-	Use `it.skip` instead of commenting out your unit tests. This still makes the test visible instead of a unit test that might be lost.
-	Use `describe.each` instead of just describe to run multiple sets of data.
### Don'ts
-	Use any long timeout’s. Use react testing library’s waitForElement or wait, both of which perform a poll and will exit faster than a time out.
-	Test width’s and heights unless absolutely necessary. Use a visual regression tool to handle this.
-	Use enzyme anymore. Enzyme tests the internals of react, so it’s going to be behind the times always.

## Folder structure
Each component should have its own folder with following items:
 -  tsx filed for the component (component.tsx)
 -  test file for component (component.test.tsx)
 - story file for the component (component.stories.tsx)
 - styles for the component (component.scss)
 - util file if necessary (component.util.ts
 - README

### Note: We will fill in folder hierarchy soon


## Components

### Composing a component
* Prefer functional components with hooks. Class components are no longer encouraged and with hooks there is no need for class components anymore. [See hooks section for more info]
* Look for existing components with similar functionality, before adding a new one. Check in story book, search in the code base or reach out to [Component Guardians](#component-guardians) to see if one exsits.
* Define state and props via interfaces in typescript. FlowTypes will be unnecessary once our whole apps are typescript-powered.
* Add good unit tests.
* Add  readme.md for a component as needed.
* Add stories for storybook. Reference the readme if applicable.
* `HOC` and `render props` should be avoided unless really needed. Hooks solve all these use cases but there could be some legacy code which we may have to deal with which might need this but beyond those, we need not use this pattern.


### Component Guardians
Component Guardians are team members designated to oversee UI components being built, updated, or consumed. These team members will be responsible to ensure consistency, re-usability, and de-duplication. The Component Guardians will work with the UX team during initial phases of component UX design and engineering development to maintain a level of discipline and adherence to Best Practices to our client side development work.

##### When?
The Component Guardians will provide input and guidance in two general cases. However, they are available to answer any questions and help resolve any issues that may arise during implementation.
General Cases
1. When a component has been designed by the UX team, but before Engineering work has begun.
1. When **any** existing component is modified.

##### How?
Assign them as Reviewers to a PR

##### Why?
This will help reduce disjointed UI and an inconsistent user experience.

##### Who?
This needs to be discussed.

***Do's***

 - Before building a base component, look if a similar component exists already. If there is inconsistencies in style, consult the Component Guardians to make sure design is consistent across the application. This might be a mistake from the designer.


## Container components
Container components can wrap presentational component or another container component. This will house the logic to fetch data, process it and display.
It can also be responsible to handle page specific UX.

## State Management
We can use the following strategies

### React-redux
React-Redux with typescript is our state management framework, when necessary.

***Do's***
- Ensure all actions, types, reducer functions, payloads are typed throughout. `any` should be avoided.
- Place all `compose` code within the component file. That is, `mapStateToProps`, `mapDispatchToProps`, store wrapped component export etc, should exist within the main component file.
- The component which is going to be glued to the store should be unaware of redux. Make sure the underlying component is completely detached from who is passing props to it. This is especially useful if we ever want to detach from redux and promotes loosely coupled code.

 - Define all the types, reducers and actions under `This needs to be updated` folder. Create separate file for each page.
 - Use redux middlewares as required when specific logic can be reused.


***Don'ts***
- Use Redux for forms

###  Context
React Context (useContext/ContextProvider) can be used when a piece of information needs to be propogated to the entire react tree of components within the page. Also when the whole page needs to react if anything changes.

***Do 's***
- Use if some data doesn't change over the life cycle of the page and is used by a lot of components.

***Don't***
 - Avoid using context to maintain state of the page. Prefer Redux in such cases. Each context update results in re-render of every component and children in the hierarchy, that refers the context and may lead to performance issues.

**_Gotcha's_**
- Class based component can refer to only one context with the static context association approach, this is different from render prop multiple nested contexts where you can have multiple contexts being provided. This gets revealed later on so need to be sure on the approach before choosing context as the component may already be referring to some other context. It is still possible to bundle contexts but it leads to a more convoluted flow of code.

```jsx
class Foo extends Component {
    //
}
Foo.contextType = SomeContext;
```

vs

```jsx
<Context1>
    <Context2>
        <Context3>
            <Foo />
        </Context3>
    </Context2>
</Context1>
```
- Function based components don't suffer from this problem as we can do,
```jsx
export const Foo = () => {
    const con1 = useContext(Context1);
    const con2 = useContext(Context2);
    const con3 = useContext(Context3);
}
```
- An approach should not ideally determine data flow but this is an important piece to be aware of when dealing with contexts in general.

### Forms
#### useReducer
useReducer + Context can be a great way to handle nested and complex forms.

***Do's***
- Make sure to define types for all the reducers.
- Write unit tests for all reducers

***Don'ts***
- Use in base level component

## Mutation
* It's very important to not mutate state or props or any object for that matter.[This blog](https://reactkungfu.com/2015/08/pros-and-cons-of-using-immutability-with-react-js/) explains why immutability is important for react and different ways of acheiving it.
* We can bring in and use `immer` if you have a deeply nested object that you are trying to mutate, and find the spread syntax too cubersome(which it is beyond a point)
* Don't reach out to `immer` for all use cases when it can be done with a simple spread.

## Memoization
* Use memoization wherever necessary to avoid unnecessary renders. Note that memoization should be done purely as a tool to improve performance by avoiding re-renders but not to circumvent any other functional issues.
  [This blog](https://medium.com/@rossbulat/how-to-memoize-in-react-3d20cbcd2b6e) has good discussion about when and how to use memoization.
* Profile the component if required to see if you really need memoization.
* `React.memo(() => {})` and `React.useMemo` are entirely [different](https://stackoverflow.com/questions/55466104/using-usememo-instead-of-react-memo-syntax-issue) and not to be confused with one another.
* `React.memo` uses shallow comparison and may lead to issues where you don't see a render when you have to so make sure to verify this. A Custom comparison function can be provided if you want deep comparison. Care has to be taken to see if the gain we get from `React.memo` is really beneficial when using custom comparison, as the custom comparison can be more expensive than the re-render in some cases.

## Hooks
As React's implementation has changed predominantly to totally shut down class based components, it is important to use function components with hooks for all use cases. (Except for maintaining existing components)
It's highly recommended to spend time understanding the motivation behind using hooks, their API and usage.
[React's official documentation](https://reactjs.org/docs/hooks-intro.html) is the go to source for this.

Some key points on hooks
* Can't intermingle hooks with class based components. So before writing, please check if your component chain involves an existing component which is a class.
* One of the [most elaborate blogs](https://overreacted.io/a-complete-guide-to-useeffect/) on a rather _interesting_ and powerful hook - `useEffect`. Though a lengthy read, please spend all the time to go through each and every aspect as it opens up a lot of blocks on hooks in general.
* Hooks promote reuse so if you feel something can be reused, please go ahead an create it as a custom hook with the general naming convention.

## Resources
### Dev Tools
Following dev tools are recommended to debug/troubleshoot issues:
 - [React dev tools](https://reactjs.org/blog/2019/08/15/new-react-devtools.html)
 - [Redux dev tools](https://github.com/reduxjs/redux-devtools)

 ### React
  - [React official docs](https://reactjs.org/docs/react-api.html)
  - [Overreacted.io](https://overreacted.io/)
  - [React Training](https://cdb.reacttraining.com/)

### Redux
  - [Redux](https://redux.js.org/api/api-reference)
  - [Redux Fandamentals](https://egghead.io/courses/getting-started-with-redux )
  - [Building redux applications](https://egghead.io/courses/building-react-applications-with-idiomatic-redux)

  ## Unit Testing
  - [React testing library](https://testing-library.com/docs/react-testing-library/intro)
