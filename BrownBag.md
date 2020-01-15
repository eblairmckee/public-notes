# Styled Component Brown Bag
## Table of Contents
- [Styled Component Brown Bag](#styled-component-brown-bag)
  - [Table of Contents](#table-of-contents)
- [Intro](#intro)
  - [What you'll learn](#what-youll-learn)
  - [Stats](#stats)
    - [Chopshop UI](#chopshop-ui)
    - [Connect Components](#connect-components)
    - [Other UI Libraries](#other-ui-libraries)
- [Syntax](#syntax)
  - [If you're coming from preprocessors](#if-youre-coming-from-preprocessors)
- [How It Works](#how-it-works)
  - [The Basics](#the-basics)
  - [The Magic Behind Styled Components](#the-magic-behind-styled-components)
  - [advantages](#advantages)
  - [disadvantages](#disadvantages)
- [You Can Style Anything](#you-can-style-anything)
  - [HTML elements](#html-elements)
  - [Components](#components)
  - [Another Styled Component](#another-styled-component)
    - [Inline Styles](#inline-styles)
  - [Change elements dynamically](#change-elements-dynamically)
  - [Global Styles](#global-styles)
- [Props](#props)
  - [Basic Example](#basic-example)
  - [Usage](#usage)
    - [Exception!](#exception)
    - [for example](#for-example)
    - [a more complex example](#a-more-complex-example)
    - [a little less complex](#a-little-less-complex)
  - [State based props](#state-based-props)
  - [Dynamic props](#dynamic-props)
  - [Caveats](#caveats)
- [Partials/Blocks](#partialsblocks)
- [Mixin Functions](#mixin-functions)
- [Best Practices](#best-practices)
  - [Declare Outside Render](#declare-outside-render)
  - [Combining with Stylesheets](#combining-with-stylesheets)
    - [For example](#for-example)
  - [Deeply Nested Styles](#deeply-nested-styles)
    - [Example](#example)
    - [Example 2](#example-2)
- [Passing Props](#passing-props)
  - [Theme-ing](#theme-ing)
- [Storybook](#storybook)
- [Testing](#testing)
  - [TypeScript](#typescript)
  - [Writing Tests](#writing-tests)
- [What does this mean for Chop Shop?](#what-does-this-mean-for-chop-shop)
  - [Architecture/Organization](#architectureorganization)
  - [Angular Migration](#angular-migration)
  - [Stylus Migration](#stylus-migration)
    - [Classes](#classes)
    - [A more complicated example](#a-more-complicated-example)
    - [Contextual modifiers](#contextual-modifiers)
    - [Variants](#variants)
    - [Mixins](#mixins)
  - [Reusable/Shared Components in Connect Components](#reusableshared-components-in-connect-components)
    - [Known Issues](#known-issues)
  - [Constants](#constants)
- [Further reading](#further-reading)

# Intro
Last year we decided to use styled components in chopshop for all new components, and refactor old ones still using stylus. The idea was:
- to encapsulate css class names and styles to a component that will never conflict or collide with any other components
- and interpolate common styles across multiple components without breaking encapsulation
[ADR](https://github.com/AudaxHealthInc/chopshop-ui/blob/master/adrs/0007-styled-components.md)

Styled components are only being used in 21 components out of the 220 React components in chopshop UI.
Long term, we want to get off of stylus for component styles and move towards encapsulated, modular styling using styled components.
And build a system of reusable components that can be shared across views.

## What you'll learn
- how to migrate existing components using stylus styles to styled components
- a system of best practices to maintain consistency
- how to use and build reusable, shareable components

## Stats
### Chopshop UI
21 components use styled components out of 220 React Components

### Connect Components
49 components use styled components out of 71 React Components

### Other UI Libraries
Upcoming Rally Design System `ui-components` library will be web-components built with Lit Element...
which are styled using template literals that are injected into the component using the shadow DOM

- Supports mixin-like functionality
- css variables
- shadow DOM makes scoping absolute to each component
  - no way to override with stylesheets

So styled components **should** be a nice segway towards the RDS as we'll be getting in the habit of scoping styles to each component and getting rid of stylesheets.

# Syntax
written like regular CSS... so none of that CSS in JS camelCase bull#&%$

## If you're coming from preprocessors
- prepend selectors and classes with `&` to "attach" them to the component, eg: `&:after`
- nest classes and selectors if you want to refer to children

Styled components support:
- selectors
- pseudo elements
- nesting
- extending styles
- dynamic styling based on passed in props 🙌🏻
  - also supports css variables
- mixins (kind of... )

# How It Works
## The Basics
Styled components render as the HTML element you pass in with hashed class(es)

so this
```javascript
const Title = styled.h1`
    color: palevioletred;
`;
```

will compile as this
```html
<style>
    .h39edk {
        color: palevioletred;
    }
</style>

<h1 class="h39edk">Hello, World!</h1>
```

It will also create a stylesheet at runtime with all these hashed classes and stick it at the end of the `<header>`

## The Magic Behind Styled Components
styled components use tagged template literals which allow for `${interpolations}`

that means you can inject variables and methods as interpolations..
similar to stylus and sass `$constants` but even cooler... because you can inject functions 🤯

**Further Reading:**
the [magic](https://mxstbr.blog/2016/11/styled-components-magic-explained/) behind styled components 🧙‍♂️

## advantages
- styles are scoped to each component
  - so no more bloated stylesheets
- don't have to deal with class names, specificity issues, bad naming conventions, BEM
- all the functionality of preprocessors
  - vendor prefixes
  - mixins and more

## disadvantages
- 🤷‍♀️

# You Can Style Anything
## HTML elements
```javascript
const Link = styled.a`
    color: lightsteelblue;
`;
```

## Components
```javascript
const MyComponent = props => {
    return <h1>I'm a Component</h1>
}

const styledComponent = styled(MyComponent)`
    font-size: 2em;
`;
```

## Another Styled Component
You can style another styled component similar to the `@extend` method in sass and stylus

```javascript
const Button = styled.button`
    border: 1px solid;
    border-color: blue;
    color: blue;
`;

const DangerButton = styled(Button) `
    border-color: red;
    color: red;
`;

const Component = ({ className }) => {
    return <DangerButton className={className}>I'm a dangerous button, rawr!</DangerButton>
}
```

`DangerButton` "extends" `Button` styles in a new component

make sure you pass in `className` as a prop otherwise styled component won't know what node to attach the styles to

or use the appropriate interface like `React.HTMLAttributes<HTMLButtonElement>` and spread in `{...passThrough}` as a `prop` which will add the type and `className`

```javascript
interface Props extends React.HTMLAttributes<HTMLButtonElement> {};

const Component: React.FC<Props> = ({ props, ...passThrough }) => {
    return <Button {...passThrough}>I'm a button</Button>
}
```

If you inspect the new component you'll see two `className`s with the new styles first (lower in the cascade), followed by extended styles.

```css
.hf93hf {
    border: 1px solid;
    border-color: blue; //nope
    color: blue; //not happening
}

.dk2ki3 {
    border-color: red;
    color: red;
}
```

### Inline Styles
If you have a one-off that only going to happen once.... you can `{{inline}}` it instead of extending the component. But use this with discretion. Only intended for a single style change.

```javascript
<Container style={{ marginTop: '12px' }}>What color am I?</Container>
```

## Change elements dynamically
Have a styled component and want to change the HTML element it renders as? Use `as`.

```javascript
const Button = styled.button`
    border: 1px solid;
    border-color: blue;
    color: blue;
`;

const Component = () => {
    return (
        <>
            <Button>I'm a button</Button>
            <Button as="a">I'm a link now!</Button>
        </Button>
    )
}
```

## Global Styles
Since everything is styled by component.. what about global styles like `body` and `html`?

Styled components includes a utility called `createGlobalStyle` which injects styles in the `header` before your styled components' styles
```javascript
import { createGlobalStyle } from 'styled-components/macro';

export default createGlobalStyle`
  html {
    font-size: 10px;
  }
`;
```
and then import the `createGlobalStyle` component into the root of the app as a sibling to `<App/>`

# Props
you can pass `props` into styled components and render different styles.

- `props` are booleans and if used in a component, will activate any associated styles.
- `props` can be used for multiple styles in a single component.
- you can have multiple `props` in a component with conditional styling using `if ... else`

## Basic Example
```javascript
const Container = styled.div`
    max-width: ${props => props.fluid ? 'auto' : '1224px'};
    margin: auto;
`;

//render as
const MyContainer = () => {
    return <Container fluid>
}
```

## Usage
use `props` for any one-off styles, subtle variations, state based changes

**In other words:** if you ever feel compelled to add a `className` to a styled component.... you don't have to.
this is a one-off and it should be replaced with `props`

### Exception!
although if you're only going to be using the one-off style once, just extend the component and add your styles. props are intended for multiple uses.

### for example
this
```javascript
const Button = styled.button`
    color: palevioletred;
    background: rebeccapurple;

    &.darkMode {
        color: lightgrey;
        background: black;
    }
`;
```

should be
```javascript
const Button = styled.button`
    color: ${props => props.darkMode ? 'lightgrey' : 'palevioletred'};
    background: ${props => props.darkMode ? 'black' : 'rebeccapurple'};
`;
```

### a more complex example
`props` can be more than simple booleans.... can interpolate `if... then` logic

```javascript
const Button = styled.button`
    color: ${props => {
        if (props.darkMode) => 'lightgrey'
        else if (props.secondary) => 'mediumorchid'
        return 'palevioletred' //default
    };
    background: ${props => {
        if (props.darkMode) => 'black'
        else if (props.secondary) => 'lavenderblush'
        return 'rebeccapurple' //default
    };
`;
```

a word of caution... be careful with these... it's tempting to use them liberally but it can also clutter your code.

instead... try this

### a little less complex
```javascript
const Button = styled.button`
  background-color: ${props =>
        (props.type === 'primary' && 'blue') ||
        (props.type === 'danger' && 'red') ||
        (props.type === 'warning' && 'yellow') ||
        'transparent' //default value
    }
`;

<Button type="primary">
```

prettier, right?

## State based props
if you have a big block of styles (more than 2 styles is a good rule of thumb) that will change based on a prop... encapsulate those styles and only activate them when the prop is true

this is the preferred method over `if... then` logic

```javascript
const Button = styled.button`
    color: palevioletred;
    background: rebeccapurple;
    border: 1px solid;
    border-color: palevioletred;

    ${({darkMode}) =>
        darkMode &&
        `
            color: lightgrey;
            background: black;
            border-color: lightgrey;
        `
    };
`;
```

you can store this prop in state and if `this.state.darkMode` is true, it will activate the `darkMode` code block

```javascript
<Button darkMode={this.state.darkMode} />
```

## Dynamic props
this is a great alternative to one-off classes for margins and padding

```javascript
const Container = styled.div`
  margin-top: ${props => props.marginTop};
`;

<Container marginTop="2em" />
```

although, anyone could input anything as the value which can cause some errors. In that case, you'll want to create an interface for those prop values.

## Caveats
Don't use `props` or inline styles on children to determine spacing: margins or padding. That should be the responsibility of the parent.

# Partials/Blocks
just like sass `%partials` and stylus blocks, you can reuse fragments.
just declare them as a regular constant

either as a string
```javascript
export const PrimaryColor = '#bada55';
```

or as a template literal
```javascript
export const AfterPseudo = props => `
    content: '';
    display: block;
    position: absolute;
    height: 100%;
    width: 100%;
`;
```

and use them as a regular interpolation
```javascript
const Overlay = styled.div`
    &:after {
        ${AfterPseudo};
        background-color: ${PrimaryColor};
    }
`;
```

you can also use props and interpolations inside your partials 😱

# Mixin Functions
You can write your own mixins! 🙃

```javascript
const boxShadowMixinFunc = (top = 0, left = 0, blur = 0, color, inset = false) => {
 return `box-shadow: ${inset ? 'inset' : ''} ${top}px ${left}px ${blur}px ${color};`;
}

const StyledComp = styled.div`
  ${boxShadowMixinFunc(0, 0, 4, 'rgba(0, 0, 0, 0.5)')}
`;
```

or something like this (which was adapted by Yi from our stylus mixins)
```javascript
import * as styleConsts from './constants';

export const phoneOnly = cssRulesStringBlock => `
    @media (max-width: ${styleConsts.$phoneWidthMax}) {
        ${cssRulesStringBlock}
    }
`;

const Button = styled.button`
    display: block;
    ${phoneOnly(`
        text-transform: uppercase;
        width: 100%;
    `)};
`;
```

# Best Practices
## Declare Outside Render
Always declare styled components OUTSIDE of the render

Even better... outside of the component

Or in a common file or repo so it can shared (more on that later)

## Combining with Stylesheets
If you **have** to use a global class just know that if there are conflicting styles, the styled component will win

### For example
```javascript
const MyComponent = styled.span`
    color: red;
`

<style>
    .blue {
        color: blue;
    }
</style>

<MyComponent className="blue">What color am I?</MyComponent>
```

Spoiler alert.. it will be red. Because Styled components compile at run time and generate a stylesheet at the end of the header, so the styled component styles will be later in the cascade.

the best way to override is to increase the specificity (and no that doesn't mean use `!important`)

```javascript
<style>
    .blue .blue {
        color: blue;
    }
</style>
```

or
```javascript
<MyComponent style={{ color: 'blue' }}>What color am I?</MyComponent>
```

but please don't do this? 🙏🏼 unless you absolutely have to

part of the allure of styled components is that we don't have to chase down the rendered component to see what styles were added... everything is encapsulated in the styled component declaration.

## Deeply Nested Styles
remember this $*#@?

```scss
.block {
    //styles
    &__element {
        //more styles
        &--modifier {
            //why not more
        }
    }
}
```

since we're componentizing styles and everything is scoped to each component... you really don't have to do this anymore

modifiers can be replaced with `props`

block and element naming conventions should be used, but if the component is reusable across "blocks", then stick to a generic name and extend it in each block with a more specific name and block specific styles.

### Example
search results `result` has many nested children. how would you refactor this into styled components without using `classNames`?

```scss
.searchResults { //view/
    .results {
        .result { //element
            border-bottom: 1px solid $hrColor;
            padding: 30px 0;
            position: relative;

            &.noPadding { //this is a modifier and should be a prop
                padding-bottom: 0;
            }
            &.program-result { //this is a modifier and should be a prop
                padding-right: 25%;
                .program-image { //nested child with the same prop
                    position: absolute;
                    right: 12.5%;
                    top: 50%;
                    transform: translate(50%, -50%);
                    width: 18%;
                }
            }
        }
    }
}
```

what i would do...

since result is used in other views, create a generic `<Result>` that we can extend in the search results view

```javascript
export const Result = styled.div`
    //there are actually no common styles so this was a bad example.. but we're going to show it anyway
    display: block;
`;

export const SearchResult = styled(Result)`
    border-bottom: 1px solid $hrColor;
    padding: ${props =>
        (props.noPadding && '30px 0 0 0')
        (props.programResult && '30px 25% 30px 0')
        return '30px 0'
    };
    position: relative;
    ${({programResult}) =>
        programResult && `
            img {
                position: absolute;
                right: 12.5%;
                top: 50%;
                transform: translate(50%, -50%);
                width: 18%;
            }
        `;
    }
`;
```

### Example 2
Component that has different styles depending on where it is rendered

instead of creating a new instance for each view, create one generic (store in a shared `styled-components` folder) and extend/style the generic component...
```javascript
const FilterAccordionButton = styled(AccordionButton)`
    //view specific styles
`;
```

This preserves the original, agnostic component and allows it to continue to be reused in other views.

# Passing Props
We know how props work, but what happens if you change the prop of the parent and want it to propogate down to its children?

There are two ways:
1. you can create conditional overrides in the parent for each child (preferred method)
2. you can prop drill
   a. or use context API (next topic)

For [example](https://codesandbox.io/s/practical-brook-gsvly):
```javascript
 const Button = styled.button`
    padding: 5px 20px;
    font-size: 2em;
    background: palevioletred;
    color: ${props => (props.darkMode ? "#eee" : "#333")}; //this won't work
 `;

 const Container = styled.div`
    display: grid;
    place-content: center center;
    height: 100vh;
    background: ${props => (props.darkMode ? "#333" : "white")};
    ${Button} {
        //but this does
        color: ${props => (props.darkMode ? "papayawhip" : "#333")};
    }
 `;

 export default function App() {
    const [darkMode, setDarkMode] = useState(false);
    const toggleDarkMode = () => {
        setDarkMode(!darkMode);
    };
    return (
        <Container darkMode={darkMode}>
            <Button onClick={toggleDarkMode}>Hi, I'm a button!</Button>
        </Container>
    );
 }
```

## Theme-ing
styled components come with their own `<ThemeProvider>` that uses the Context API.

Context lets you wrap your app or component in a `<Provider>` which will pass down `props` to all consuming components (without having to pass props manually through each component in the heirarchy).

🚫 so no more prop drilling!

here's an example:
```javascript
const theme = {
    primaryColor: '#bada55';
}

<ThemeProvider theme={theme}>
    <App/>
</ThemeProvider>
```

access theme props just like you would any other prop
```javascript
const deeplyNestedChildButton = styled.button`
    background: ${props => props.theme.primaryColor};
`;
```

Ideally this should replace having to import theme constants files each time we want to use a theme style.

# Storybook
Use Storybook [Addon Knobs](https://www.npmjs.com/package/@storybook/addon-knobs) to illustrate how the different props you can pass in change the UI, rather than creating a different story for each variation.

See [proof of concept](https://github.com/AudaxHealthInc/connect-components/blob/master/src/Layout/Containers/Containers.stories.tsx) in connect components

[Live example](http://localhost:9005/?path=/story/containers--flex-container) if you have connect-components running locally

Currently storybook is not being used (although installed) in chopshop UI... we need to get in the habit of adding reusable components to storybook. A good rule of thumb, if a component can be used across views, it should be in storybook. If the UI can be altered by `props` then add knobs.

# Testing
## TypeScript
Types for each prop
```javascript
interface StyledProps {
    primary?: boolean;
    secondary?: boolean;
}
```

and use the interface like
```javascript
export const StyledButton = styled.button <StyledProps>`
    background-color: ${(props: StyledProps) => props.primary && themeValue('primaryColor')};
`;
```

Here's a more [complicated example](https://github.com/AudaxHealthInc/connect-components/blob/master/src/Layout/Containers/FlexContainer.tsx)

## Writing Tests
Write tests that render the component with an accepted `prop` and check that it renders the correct styles for that `prop`.

```javascript
describe('<Container />', () => {
    it('accepts props and renders a fluid container', () => {
        const wrapper = render(<Container fluid>test</Container>);
        expect(window.getComputedStyle(wrapper.getByText(/test/i)).maxWidth).toEqual('100%');
    });
});
```
# What does this mean for Chop Shop?
- We will use styled components with passed in props for one-off and style variations.
- We will strive to make components reusable and easily shareable across views and app-wide.
  - We will use storybook to illustrate state changes in reusable components.
- We will stop using classes and stylus dependencies.
- (eventually) We will adopt design tokens as soon as possible and stop using stylus for constants.

## Architecture/Organization
This is still up for debate and seems to depend on each project/scope/etc. but current methodology is:
- If it's specific to a component... declare styled components within the component file
- If it can be reused across multiple components within the same project folder or is contextual to a project... store it in a common file within the project folder
- If it can be reused throughout chopshop UI, put it in `app/scripts/components/react/shared/styled-components/` folder in chopshop UI
  - same for mixins and shared style utilities
  - eventually we want to store these in the [connect components](https://github.com/AudaxHealthInc/connect-components) repo

## Angular Migration
Since the migration from angular to react is still in progress, sometimes you may have styled component that you need to render in an angular view. You'll only run into issues if you have to declare any props in the component, eg: a dynamic style or state-based prop. In that case, you'll need to set it to `my-prop="true"` when you render it and include the prop in the `react2angular` dependencies list for that component.

Although, I've been able to avoid this thus far by setting the default styles to whatever I want in the angular view, and then dropping in a prop to the react rendered components that override it.

## Stylus Migration
We want to break the habit of depending on classes for styling and instead rely solely on the functionality of styled components, meaning props and everything else we talked about already.

The first step is to migrate the smallest components and mixins and work our way up. Ideally, you want to create a generic/base version of the component first, and then use props, mixins, prop-based code-blocks, and other techniques to add additional context and state based styles and modifiers.

### Classes
replace classes with blocks/chunks/partials:
```javascript
export const outerContainer = `
    max-width: 1224px;
    margin: auto;
    padding-left: 0;
    padding-right: 0;
`
```

and store it in the common file `app/scripts/components/react/shared/styled-components/`

and use it like:
```javascript
import { outerContainer } from '../path/to/file.ts';

const Section = styled.div`
    ${outerContainer};
    //other styles
`;
```

this preserves the cascade and is basically the same as adding the `.outer-container` class to the component, but this way all the styles are declared in the styled component declaration and not when you render it.

`outer-container` is a bad example, because ideally it should be its own component... but that's a huge refactor so it should only be used for transition.

### A more complicated example
[live version](https://codesandbox.io/s/divine-bird-di39p)

`CostBandButton` has:
- base styles
- conditional logic depending on props passed down from the parent
- renders as a different element, with differet classes depending on the view
- and a slew of `classNames`

Here's what it looks like now
```javascript
<a className={`section action-btn ${props.buttonValue === 'costDetailsButton' ? 'negative ' : ''} costsButton`}>copy</a>
```

First, we'll create base styles, taken from the `costsButton` class and store it in a common file. These styles will be reused in other components later, so instead of just declaring a styled component with these styles, let's turn it into a reusable chunk.

Think of this like a sass `%partial`... it's not a component, just a block of styles we want to reuse.
```javascript
export const costBandBaseStyles = `
    padding: 12px 20px;
    position: absolute;
    right: 20px;
    top: 50%;
    transform: translateY(-50%);
`
```
later, we'll import it into our styled component declaration

Since this is a contextual variant of `ActionBtn` we extend it. (`ActionBtn` hasn't been converted yet, but let's pretend we did)

and then add modifiers (our replacement for `classNames`) to the common file

```javascript
export const section = `
    &:last-child {
        @media(min-width: ${phoneWidthMax}) {
            margin-bottom: 0;
        }
    }
`
```

this is what it looks like now
```javascript
import { costBandBaseStyles, section } from "../common";
import { ActionBtn } from "./ActionButton";

const CostBandButtonStyles = styled(ActionBtn)`
    ${costBandBaseStyles};
    ${section};
`;
```

and lastly, conditionals for props passed down from the parent.

since `.negative` is actually a child class of `ActionBtn` (which we've already converted to be a prop) we can drop it in as a conditional prop when we render it

```javascript
export const CostBandButton = props => {
  return (
    <CostBandButtonStyles negative={props.ButtonValue === "costsDetailsButton"}>
      {props.children}
    </CostBandButtonStyles>
  );
};
```

### Contextual modifiers
Contextual styles should not use snippets. Instead, you should extend a base version of the component and then add on your context/view specific styles.

```javascript
import { BaseButton } from './styled-components';

const ResultButton = styled(BaseButton)`
    //contextual styles
`;
```

### Variants
Not contextual, not one-off styles... these are variations on a common component, eg: a button with variations like: primary, secondary, inverse... etc.

Variations should be properties you can drop into a base component and render the variant styles like this:
```javascript
export const ActionBtn = styled.button<StyledProps>`
  background: blue;
  border-color: blue;
  color: white;
  transition: ease 0.4s;

  &:hover {
    background: darkblue;
    border-color: darkblue;
    color: #eee;
  }

  ${({ negative }) =>
    negative &&
    `
      background: transparent;
      border-color: blue;
      color: blue;

      &:hover {
          background: rgba(0,0,0,0.1);
      }

    `}
`;

//render as
const App = () => {
    return <ActionBtn negative>
}
```

### Mixins
Yi already got a head start on the [responsive mixins](https://github.com/AudaxHealthInc/chopshop-ui/pull/5528/files/5fb3fea01c0d664b8ffabc051ee8bd5371c48bb6#diff-05b2ec5c594b7605920d11689efcc764R41)

just turn them into regular functions and store them in the `react/shared/styled-components` folder

## Reusable/Shared Components in Connect Components
As mentioned earlier, we want to make shareable components. If you build a component that can be shared across multiple views, throughout chopshop UI we suggest moving it to connect-components and import it as an npm package.

### Known Issues
Existing connect-components use styled components with classes. This poses a few issues with extending, eg you still have to look up class definitions and do weird overrides like this:

```javascript
const PositionedBackButton = styled.div`
    .back-btn {
        flex: 0 0 auto;
        margin-right: 38px;
    }
`;
```

Ideally, we want to refactor these components to be styled components with passed in props to avoid this.

Currently there is an [ADR in connect components](https://github.com/AudaxHealthInc/connect-components/blob/master/adrs/0004-reusable-styled-components.md) that advocates for using styled components with passed in props instead of one-off styles.

Should this part of the angular -> react conversion efforts?

## Constants
We're currently using stylus constants for colors, spacing, etc. Naming conventions are rudimentary at best. Very little consistency.

Ideally, we should start using the RDS [design tokens](https://github.com/AudaxHealthInc/ui-themes). Released in alpha Q4 2019. Which are CSS variables you import from a stylesheet that's included in your build. That means no more importing a constants file 🙃. You can access these variables anywhere in the app.

```javascript
const MyComponent = styled.span`
    color: var(--primary-color);
`
```

# Further reading
- migration [cheatsheet](https://jsramblings.com/2017/10/29/migrating-to-styled-components-cheatsheet.html)
- 7 must know [features](https://blog.cloudboost.io/getting-the-most-out-of-styled-components-7-must-know-features-acba3cc15b5)
- 10 useful [tips](https://medium.com/@pitipatdop/10-useful-tips-for-styled-components-b7710b021e6a)
- storybook [knobs](https://storybooks-official.netlify.com/?path=/story/addons-knobs-withknobs--tweaks-static-values)
