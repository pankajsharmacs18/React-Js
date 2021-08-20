# react-Js Quick Tutorial


## Install

This package is [ESM only](https://gist.github.com/sindresorhus/a39789f98801d908bbc7ff3ecc99d99c):
Node 12+ is needed to use it and it must be `import`ed instead of `require`d.

[npm][]:

```sh
npm install react-markdown
```

## Why this one?

There are other ways for markdown in React out there so why use this one?
The two main reasons are that they often rely on `dangerouslySetInnerHTML` or
have bugs with how they handle markdown.
`react-markdown` uses a syntax tree to build the virtual dom which allows for
updating only the changing DOM instead of completely overwriting.
`react-markdown` is 100% CommonMark (optionally GFM) compliant and has
extensions to support custom syntax.

## Use

A basic hello world:

```jsx
import React from 'react'
import ReactMarkdown from 'react-markdown'
import ReactDom from 'react-dom'

ReactDom.render(<ReactMarkdown># Hello, *world*!</ReactMarkdown>, document.body)
```

<details>
<summary>Show equivalent JSX</summary>

```jsx
<h1>
  Hello, <em>world</em>!
</h1>
```

</details>

Here is an example that shows passing the markdown as a string and how
to use a plugin ([`remark-gfm`][gfm], which adds support for strikethrough,
tables, tasklists and URLs directly):

```jsx
import React from 'react'
import ReactDom from 'react-dom'
import ReactMarkdown from 'react-markdown'
import remarkGfm from 'remark-gfm'

const markdown = `Just a link: https://reactjs.com.`

ReactDom.render(
  <ReactMarkdown children={markdown} remarkPlugins={[remarkGfm]} />,
  document.body
)
```

<details>
<summary>Show equivalent JSX</summary>

```jsx
<p>
  Just a link: <a href="https://reactjs.com">https://reactjs.com</a>.
</p>
```

</details>

## API

This package exports the following identifier: `uriTransformer`.
The default export is `ReactMarkdown`.

### `props`

*   `children` (`string`, default: `''`)\
    Markdown to parse
*   `className` (`string?`)\
    Wrap the markdown in a `div` with this class name
*   `skipHtml` (`boolean`, default: `false`)\
    Ignore HTML in Markdown completely
*   `sourcePos` (`boolean`, default: `false`)\
    Pass a prop to all components with a serialized position
    (`data-sourcepos="3:1-3:13"`)
*   `rawSourcePos` (`boolean`, default: `false`)\
    Pass a prop to all components with their [position][]
    (`sourcePosition: {start: {line: 3, column: 1}, end:…}`)
*   `includeElementIndex` (`boolean`, default: `false`)\
    Pass the `index` (number of elements before it) and `siblingCount` (number
    of elements in parent) as props to all components
*   `allowedElements` (`Array.<string>`, default: `undefined`)\
    Tag names to allow (can’t combine w/ `disallowedElements`).
    By default all elements are allowed
*   `disallowedElements` (`Array.<string>`, default: `undefined`)\
    Tag names to disallow (can’t combine w/ `allowedElements`).
    By default no elements are disallowed
*   `allowElement` (`(element, index, parent) => boolean?`, optional)\
    Function called to check if an element is allowed (when truthy) or not.
    `allowedElements` / `disallowedElements` is used first!
*   `unwrapDisallowed` (`boolean`, default: `false`)\
    Extract (unwrap) the children of not allowed elements.
    By default, when `strong` is not allowed, it and it’s children is dropped,
    but with `unwrapDisallowed` the element itself is dropped but the children
    used
*   `linkTarget` (`string` or `(href, children, title) => string`, optional)\
    Target to use on links (such as `_blank` for `<a target="_blank"…`)
*   `transformLinkUri` (`(href, children, title) => string`, default:
    [`./uri-transformer.js`][uri], optional)\
    URL to use for links.
    The default allows only `http`, `https`, `mailto`, and `tel`, and is
    exported from this module as `uriTransformer`.
    Pass `null` to allow all URLs.
    See [security][]
*   `transformImageUri` (`(src, alt, title) => string`, default:
    [`./uri-transformer.js`][uri], optional)\
    Same as `transformLinkUri` but for images
*   `components` (`Object.<string, Component>`, default: `{}`)\
    Object mapping tag names to React components
*   `remarkPlugins` (`Array.<Plugin>`, default: `[]`)\
    List of [remark plugins][remark-plugins] to use.
    See the next section for examples on how to pass options
*   `rehypePlugins` (`Array.<Plugin>`, default: `[]`)\
    List of [rehype plugins][rehype-plugins] to use.
    See the next section for examples on how to pass options

## Examples

### Use a plugin

This example shows how to use a remark plugin.
In this case, [`remark-gfm`][gfm], which adds support for
strikethrough, tables, tasklists and URLs directly:

```jsx
import React from 'react'
import ReactMarkdown from 'react-markdown'
import ReactDom from 'react-dom'
import remarkGfm from 'remark-gfm'

const markdown = `A paragraph with *emphasis* and **strong importance**.

> A block quote with ~strikethrough~ and a URL: https://reactjs.org.

* Lists
* [ ] todo
* [x] done

A table:

| a | b |
| - | - |
`

ReactDom.render(
  <ReactMarkdown children={markdown} remarkPlugins={[remarkGfm]} />,
  document.body
)
```

<details>
<summary>Show equivalent JSX</summary>

```jsx
<>
  <p>
    A paragraph with <em>emphasis</em> and <strong>strong importance</strong>.
  </p>
  <blockquote>
    <p>
      A block quote with <del>strikethrough</del> and a URL:{' '}
      <a href="https://reactjs.org">https://reactjs.org</a>.
    </p>
  </blockquote>
  <ul>
    <li>Lists</li>
    <li>
      <input checked={false} readOnly={true} type="checkbox" /> todo
    </li>
    <li>
      <input checked={true} readOnly={true} type="checkbox" /> done
    </li>
  </ul>
  <p>A table:</p>
  <table>
    <thead>
      <tr>
        <td>a</td>
        <td>b</td>
      </tr>
    </thead>
  </table>
</>
```

</details>

### Use a plugin with options

This example shows how to use a plugin and give it options.
To do that, use an array with the plugin at the first place, and the options
second.
[`remark-gfm`][gfm] has an option to allow only double tildes for strikethrough:

```jsx
import React from 'react'
import ReactMarkdown from 'react-markdown'
import ReactDom from 'react-dom'
import remarkGfm from 'remark-gfm'

ReactDom.render(
  <ReactMarkdown remarkPlugins={[[remarkGfm, {singleTilde: false}]]}>
    This ~is not~ strikethrough, but ~~this is~~!
  </ReactMarkdown>,
  document.body
)
```

<details>
<summary>Show equivalent JSX</summary>

```jsx
<p>
  This ~is not~ strikethrough, but <del>this is</del>!
</p>
```

</details>

### Use custom components (syntax highlight)

This example shows how you can overwrite the normal handling of an element by
passing a component.
In this case, we apply syntax highlighting with the seriously super amazing
[`react-syntax-highlighter`][react-syntax-highlighter] by
[**@conorhastings**][conor]:

```jsx
import React from 'react'
import ReactDom from 'react-dom'
import ReactMarkdown from 'react-markdown'
import {Prism as SyntaxHighlighter} from 'react-syntax-highlighter'
import {dark} from 'react-syntax-highlighter/dist/esm/styles/prism'

// Did you know you can use tildes instead of backticks for code in markdown? ✨
const markdown = `Here is some JavaScript code:

~~~js
console.log('It works!')
~~~
`

ReactDom.render(
  <ReactMarkdown
    children={markdown}
    components={{
      code({node, inline, className, children, ...props}) {
        const match = /language-(\w+)/.exec(className || '')
        return !inline && match ? (
          <SyntaxHighlighter
            children={String(children).replace(/\n$/, '')}
            style={dark}
            language={match[1]}
            PreTag="div"
            {...props}
          />
        ) : (
          <code className={className} {...props}>
            {children}
          </code>
        )
      }
    }}
  />,
  document.body
)
```

<details>
<summary>Show equivalent JSX</summary>

```jsx
<>
  <p>Here is some JavaScript code:</p>
  <pre>
    <SyntaxHighlighter language="js" style={dark} PreTag="div" children="console.log('It works!')" />
  </pre>
</>
```

</details>

### Use remark and rehype plugins (math)

This example shows how a syntax extension (through [`remark-math`][math])
is used to support math in markdown, and a transform plugin
([`rehype-katex`][katex]) to render that math.

```jsx
import React from 'react'
import ReactDom from 'react-dom'
import ReactMarkdown from 'react-markdown'
import remarkMath from 'remark-math'
import rehypeKatex from 'rehype-katex'

import 'katex/dist/katex.min.css' // `rehype-katex` does not import the CSS for you

ReactDom.render(
  <ReactMarkdown
    children={`The lift coefficient ($C_L$) is a dimensionless coefficient.`}
    remarkPlugins={[remarkMath]}
    rehypePlugins={[rehypeKatex]}
  />,
  document.body
)
```

<details>
<summary>Show equivalent JSX</summary>

```jsx
<p>
  The lift coefficient (
  <span className="math math-inline">
    <span className="katex">
      <span className="katex-mathml">
        <math xmlns="http://www.w3.org/1998/Math/MathML">{/* … */}</math>
      </span>
      <span className="katex-html" aria-hidden="true">
        {/* … */}
      </span>
    </span>
  </span>
  ) is a dimensionless coefficient.
</p>
```

</details>

## Architecture

```txt
                                                           react-markdown
+-------------------------------------------------------------------------------------------------------------------------------------------+
|                                                                                                                                           |
|            +----------+        +----------------+        +---------------+       +----------------+       +------------+                  |
|            |          |        |                |        |               |       |                |       |            |                  |
| -markdown->+  remark  +-mdast->+ remark plugins +-mdast->+ remark-rehype +-hast->+ rehype plugins +-hast->+ components +-react elements-> |
|            |          |        |                |        |               |       |                |       |            |                  |
|            +----------+        +----------------+        +---------------+       +----------------+       +------------+                  |
|                                                                                                                                           |
+-------------------------------------------------------------------------------------------------------------------------------------------+
```

relevant links: [markdown](https://commonmark.org), [remark](https://github.com/remarkjs/remark), [mdast](https://github.com/syntax-tree/mdast), [remark plugins](https://github.com/remarkjs/remark/blob/main/doc/plugins.md), [remark-rehype](https://github.com/remarkjs/remark-rehype), [hast](https://github.com/syntax-tree/hast), [rehype plugins](https://github.com/rehypejs/rehype/blob/main/doc/plugins.md), [components](#appendix-b-components)

To understand what this project does, it’s very important to first understand
what unified does: please read through the [`unifiedjs/unified`](https://github.com/unifiedjs/unified)
readme (the part until you hit the API section is required reading).

react-markdown is a unified pipeline — wrapped so that most folks don’t need to
directly interact with unified.  The processor goes through these steps:

*   Parse Markdown to mdast (markdown syntax tree)
*   Transform through remark (markdown ecosystem)
*   Transform mdast to hast (HTML syntax tree)
*   Transform through rehype (HTML ecosystem)
*   Render hast to react with components

## Appendix A: HTML in markdown

`react-markdown` typically escapes HTML (or ignores it, with `skipHtml`)
because it is dangerous and defeats the purpose of this library.

However, if you are in a trusted environment (you trust the markdown), and
can spare the bundle size (±60kb minzipped), then you can use
[`rehype-raw`][raw]:

```jsx
import React from 'react'
import ReactDom from 'react-dom'
import ReactMarkdown from 'react-markdown'
import rehypeRaw from 'rehype-raw'

const input = `<div class="note">

Some *emphasis* and <strong>strong</strong>!

</div>`

ReactDom.render(
  <ReactMarkdown rehypePlugins={[rehypeRaw]} children={input} />,
  document.body
)
```

<details>
<summary>Show equivalent JSX</summary>

```jsx
<div class="note">
  <p>Some <em>emphasis</em> and <strong>strong</strong>!</p>
</div>
```

</details>

**Note**: HTML in markdown is still bound by how [HTML works in
CommonMark][cm-html].
Make sure to use blank lines around block-level HTML that again contains
markdown!

## Appendix B: Components

You can also change the things that come from markdown:

```js
<ReactMarkdown
  components={{
    // Map `h1` (`# heading`) to use `h2`s.
    h1: 'h2',
    // Rewrite `em`s (`*like so*`) to `i` with a red foreground color.
    em: ({node, ...props}) => <i style={{color: 'red'}} {...props} />
  }}
/>
```

The keys in components are HTML equivalents for the things you write with
markdown (such as `h1` for `# heading`)**†**

**†** Normally, in markdown, those are: `a`, `blockquote`, `code`, `em`, `h1`,
`h2`, `h3`, `h4`, `h5`, `h6`, `hr`, `img`, `li`, `ol`, `p`, `pre`, `strong`, and
`ul`.
With [`remark-gfm`][gfm], you can also use: `del`, `input`, `table`, `tbody`,
`td`, `th`, `thead`, and `tr`.
Other remark or rehype plugins that add support for new constructs will also
work with `react-markdown`.

The props that are passed are what you probably would expect: an `a` (link) will
get `href` (and `title`) props, and `img` (image) an `src` (and `title`), etc.
There are some extra props passed.

*   `code`
    *   `inline` (`boolean?`)
        — set to `true` for inline code
    *   `className` (`string?`)
        — set to `language-js` or so when using ` ```js `
*   `h1`, `h2`, `h3`, `h4`, `h5`, `h6`
    *   `level` (`number` beween 1 and 6)
        — heading rank
*   `input` (when using [`remark-gfm`][gfm])
    *   `checked` (`boolean`)
        — whether the item is checked
    *   `disabled` (`true`)
    *   `type` (`'checkbox'`)
*   `li`
    *   `index` (`number`)
        — number of preceding items (so first gets `0`, etc.)
    *   `ordered` (`boolean`)
        — whether the parent is an `ol` or not
    *   `checked` (`boolean?`)
        — `null` normally, `boolean` when using [`remark-gfm`][gfm]’s tasklists
    *   `className` (`string?`)
        — set to `task-list-item` when using [`remark-gfm`][gfm] and the
        item1 is a tasklist
*   `ol`, `ul`
    *   `depth` (`number`)
        — number of ancestral lists (so first gets `0`, etc.)
    *   `ordered` (`boolean`)
        — whether it’s an `ol` or not
    *   `className` (`string?`)
        — set to `contains-task-list` when using [`remark-gfm`][gfm] and the
        list contains one or more tasklists
*   `td`, `th` (when using [`remark-gfm`][gfm])
    *   `style` (`Object?`)
        — something like `{textAlign: 'left'}` depending on how the cell is
        aligned
    *   `isHeader` (`boolean`)
        — whether it’s a `th` or not
*   `tr` (when using [`remark-gfm`][gfm])
    *   `isHeader` (`boolean`)
        — whether it’s in the `thead` or not

Every component will receive a `node` (`Object`).
This is the original [hast](https://github.com/syntax-tree/hast) element being
turned into a React element.

Every element will receive a `key` (`string`).
See [React’s docs](https://reactjs.org/docs/lists-and-keys.html#keys) for more
info.

Optionally, components will also receive:

*   `data-sourcepos` (`string`)
    — see `sourcePos` option
*   `sourcePosition` (`Object`)
    — see `rawSourcePos` option
*   `index` and `siblingCount` (`number`)
    — see `includeElementIndex` option
*   `target` on `a` (`string`)
    — see `linkTarget` option


