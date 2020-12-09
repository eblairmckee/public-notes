# DraftJS Deep Dive

## Table of Contents

- [DraftJS Deep Dive](#draftjs-deep-dive)
  - [Table of Contents](#table-of-contents)
- [Intro to DraftJS](#intro-to-draftjs)
  - [The Facts](#the-facts)
  - [Usecases](#usecases)
- [How It Works](#how-it-works)
  - [contenteditable](#contenteditable)
    - [Pros](#pros)
    - [Cons](#cons)
- [The DraftJS Difference](#the-draftjs-difference)
- [Demo Time](#demo-time)
  - [EditorState](#editorstate)
  - [Helpful Methods](#helpful-methods)
- [Rich Text Styles](#rich-text-styles)
- [What are Entities?](#what-are-entities)
  - [plugins](#plugins)
- [how to store data](#how-to-store-data)

# Intro to DraftJS

DraftJS is a rich text editor for React; built and supported by Facebook. It has an extensive ecosystem, widespread support and advocacy, it's super easy to get up and running, and you can customize it to do pretty much whatever you want.

## The Facts

- supports inline styles, block styles (lists, quotes), links, mentions, and embedded media
- outputs as JSON
  - plugins for markdown and other outputs
- if you don't want to build your own UI, there are libraries and plugins for prebuilt rich text editor components
- immutable
  - supports undo/redo

## Usecases

- headless CMS
- forms

# How It Works

not a `<textarea>` or a `<div>`, instead, they take advantage of the native `contentEditable` HTMLElement property

## contenteditable

It's an attribute that can be applied to a `div` to make the inner content editable, stylable, and enables rich text functionality.

```html
<div contenteditable="true"></div>
```

inner content will initially be a string, but when rich styles are applied it will create new DOM nodes contained within appropriate HTML elements. ie: italicized text will be wrapped in `<i>` or `<em>` (more on that later).

### Pros

- cursor and selection behaviors right out of the box
- automatically resizes based on content
- supports hot keys for inline styles (eg: bold, italic)
- you can copy and paste styles and preserve rich text natively
- support internationalization
- accessibility friendly
- cross browser support
- it's been around for over a decade so lots of support

### Cons

- it's renouned to be a PITA
- each browser implements styles differently
  - ie: paragraphs (hitting enter) can become `<p>` or `<div>` or `<br>` depending on the browser

# The DraftJS Difference

DraftJS solves this by using controlled inputs, preventing default browser behaviors, and replaces them with their own that are uniform cross-browsers. They also expose these methods so you can customize them to your liking.

The only problem with this, is that DOM API's are no longer useable. You have to rely solely on the DraftJS API to do really anything:

- selection
- parse and transform the editor state
- detect inline and block styles
- ... really anything

So, to save you an afternoon of wasted debugging... I put together a sort of cheat sheet of DraftJS methods and best practices. (more on that later)

# Demo Time

[demo](https://codesandbox.io/s/contenteditable-b0sqv?file=/src/App.tsx:400-434)

Really easy to setup. Import the `Editor` component from `draft-js` as well as the `EditorState`. Initialize your state with `EditorState.createEmpty()` and return your editor like so:

```javascript
import React, { useState } from "react";
import { Editor, EditorState } from "draft-js";

const MyEditor = () => {
  const [editorState, setEditorState] =
    useState < EditorState > EditorState.createEmpty();
  return (
    <Editor
      editorState={editorState}
      onChange={(editorState: EditorState) => setEditorState(editorState)}
    />
  );
};
```

## EditorState

if you `console.log` your `EditorState` it returns the entire history of the editor's state as well as methods.

```javascript
{
  // records
  _immutable: {
  }
  // methods
  _proto: {
  }
}
```

the immutable object is made up of immutable `Records` which are objects that store the content and associated styling and inline styles. Records are only recreated when they are modified. So if you modify a single content block, the histories of the unchanged blocks will persist, and only the new content block will be recreated.

You can see the current state of the editor by using the `editorState.getCurrentContent()` method. But you'll want to convert it to raw `JSON` using exported helper `convertToRaw()` to actually read the content.

This method is super helpful for debugging and making sure your styles are actually getting applied:

```javascript
const raw = convertToRaw(editorState.getCurrentContent());
```

returns blocks and entities. let's say we have a bunch of text that we applied some inline styles to, and added a link...

```javascript
{
    blocks: [
        0: {
        "key": "af61f",
        "text": "My puppy just sat next to me on the couch for the first time in months and I'm geeking so hard right now! ",
        "type": "unstyled",
        "depth": 0,
        // where inline styles are applied
        "inlineStyleRanges": [
            {
                "offset": 1,
                "length": 9,
                "style": "BOLD"
            }
        ],
        // this is where the link is applied
        "entityRanges": [
            {
            "offset": 17,
            "length": 14,
            "key": 0
            }
        ],
        "data": {}
        }
    ],
    entityMap: [
        0: {
        "type": "LINK",
        "mutability": "MUTABLE",
        "data": {
            "url": "https://www.rallyhealth.com/"
        }
        }
    ]
}
```

## Helpful Methods

`editorState.getCurrentContent()`
returns current state. useful for applying focus or selection

returns `ContentState`

```javascript
{
  _map: {
  }
  // methods
  _proto: {
  }
}
```

can chain additional methods like `getBlockForKey` and then find that block's type

```javascript
const getInlineStyle = editorState.getCurrentInlineStyle();
```

based on current selection or if no selection, entire editor inline style

```javascript
const getBlockType = editorState
  .getCurrentContent()
  .getBlockForKey(selection.getStartKey())
  .getType();
```

# Rich Text Styles

ships with a bunch of [methods](https://draftjs.org/docs/api-reference-rich-utils) to help you debug and add your own customizations to rich style behaviors (eg: toggle styles, customize what `tab` does for ordered lists, etc.).

pass in previous state and the type of style:

- `toggleInlineStyle()`
- `toggleBlockType()`

it will look at the currently selected text or current block and apply the style transformation.

# What are Entities?

We already saw how DraftJS stores meta data in objects called [entities](https://draftjs.org/docs/advanced-topics-entities/), but how do you create one?

```javascript
const contentState = editorState.getCurrentContent();
const contentStateWithEntity = contentState.createEntity("LINK", "MUTABLE", {
  url: "http://www.zombo.com",
});
const entityKey = contentStateWithEntity.getLastCreatedEntityKey();
const contentStateWithLink = Modifier.applyEntity(
  contentStateWithEntity,
  selectionState,
  entityKey
);
const newEditorState = EditorState.set(editorState, {
  currentContent: contentStateWithLink,
});
```

they are a huge pain. I've personally loathed my experiences trying to do this manually, and have opted for plugins for various reasons...

## plugins

[`draft-js-plugins`](https://github.com/draft-js-plugins/draft-js-plugins) (3.6k+ github stars) reexports the `Editor` component and makes it super easy to plug and play [different plugins](https://www.draft-js-plugins.com/), like:

- inline and side toolbars
- undo/redo
- links, mentions, and hashtags
- embedded content (emojis, images, GIFs, and videos)

most of them do all the heavy lifting for you. eg: anchor plugin validates the input, updates state with the new entity, and will also remove link entities with just 3 lines of code.

all you have to do is instantiate the plugin, add your configurations (optional), and pass that in to the `plugin` prop on the `Editor` component.

# how to store data

DraftJS comes with [helpers](https://draftjs.org/docs/api-reference-data-conversion):

- `convertToRaw()` => JSON

and then how to read the data and make it into something the editor can actually work with (`EditorState`):

- `convertFromRaw()`
- `convertFromHTML()` (needs some other methods, though)

I wrote a little helper method that converts whatever you have stored into `EditorState`:

```javascript
export const getStateFromRawJSONOrString = (toBeConverted: RawDraftContentState | string) => {
    const isRaw = typeof toBeConverted === 'object';
    const rawJSON = typeof toBeConverted === 'string' && convertFromHTML(toBeConverted);
    const createContentBlock = rawJSON && ContentState.createFromBlockArray(rawJSON.contentBlocks, rawJSON.entityMap);
    const convertedContentFromHtml = createContentBlock && EditorState.createWithContent(createContentBlock);
    return isRaw ? convertFromRaw(toBeConverted as RawDraftContentState) : convertedContentFromHtml;
};
```

libraries for converting to markdown
