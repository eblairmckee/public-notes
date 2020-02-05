# Stylus 🖊 -> Styled Components 💅
## Strategy
1. utilities, colors, themes
2. mix-ins and grid system (everything we're using in kouto-swiss)
   1. hand roll our own and try to mimic RDS
3. shared components (buttons, typography, etc... follow same [roadmap](https://docs.google.com/presentation/d/1dzoGn0sT6KyBJvVyFtYYrIOleyuPFxusYSG3Llj2BfA/edit?ts=5de8373f#slide=id.g5bda82ce2f_0_235) as design system)
4. the rest of the components (part of the angular -> react conversion)
5. views (angular -> react conversion)
6. theme provider (requires the root of chopshop to be react, but may be able to do by view once we get more converted)
7. global styles (requires the root of chopshop to be react)

Stylus will have to coexist at the same time until the migration is complete

Ideally we don't want to 1:1 anything... instead focus on reusability and try to make as many components as possible shareable. Then move on to contextual.

As we move things to styled components, we will deprecate the use of classes, because they are a #&$%@ to override. This will require a significant refactor of existing react components. Use the patterns [established here](https://github.com/eblairmckee/public-notes/blob/master/BrownBag.md#stylus-migration).

## Utilities
### Constants & Helpers
⬜️ constants (exported `consts`)
⬜️ helperClasses (partials/exported strings and template strings, but many should be props)
⬜️ animations (use the `keyframes` utility)

keyframes in styled components:
```javascript
import styled, { keyframes } from 'styled-components'

const fadeIn = keyframes`
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
`
```

and use it like
```javascript
const FadeInButton = styled.button`
  animation: 1s ${fadeIn} ease-out;
`
```

### Colors & Themes
⬜️ colors
⬜️ defaultTheme
⬜️ advantageTheme
⬜️ bcbsTheme

## Mixins & Grid
⬜️ grid (functions, similar to mixins)
⬜️ mixins (`em()` to `px`)

import these as needed

### Migrate off of kouto swiss
establish what all we are using and what needs to be migrated

## Components
Start with small, commonly used components and work way up
Probably good to start with grid system, typography, buttons... similar pattern to RDS
- store in `shared/styled-components` folder
- Add to storybook with knobs
- no more responsive folders. use breakpoint utilities instead and within each styled components (never separate).

Then move on to contextual components

## Views/pages
ideally views/pages won't need their own styled components at this point. Instead, they will import and extend/style existing shared components and apply contextual styles

## Global styles
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

## Themes
need logic that decides what theme constants to use based on the theme in redux

eventually we'll be using `ThemeProvider` which will be added to `withBaseProviders`

### things we need fix
right now have to hard code theme colors in styled components and can't switch if theme switches
themes all reference other variables so it doesn't resolve in styled components because it requires compile to get those values

theme switching
currently loaded in the `chrome` component? i think... has a theme binding which i have no idea where that's set... but it then stores that in redux. nothing in `cs.app.js`

### solution
Multiple `ThemeProviders`.... first it defaultTheme, the child returns the theme object based on what's in redux store.

# RDS prep
What patterns from RDS can we incorporate in the migration?
- Variations are their own components
- Props are used for modifiers
- `rem` instead of `em` or `px`

When we build any new reusable component, check the RDS to see what props they are using. Want to be as similar as possible.

# Testing
jest snapshots for testing

long term, would want to use a react testing library solution (given that we move to RTL)

# What are other teams doing?
## Activate UI
[styled system](https://styled-system.com/)
[flexbox container](https://rebassjs.org/reflexbox/)
👎 too similar to bootstrap... have to declare everything in props

reusable components (primitives) [repo](https://github.com/AudaxHealthInc/activate-ui/blob/master/src/js/components/primitives)

## who else?
need to do more investigative work on this
