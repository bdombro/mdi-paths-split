# mdi-paths [![npm package](https://img.shields.io/npm/v/mdi-paths-split.svg?style=flat-square)](https://npmjs.org/package/mdi-paths-split) [![Material Design Icons version](https://img.shields.io/badge/mdi-v5.9.55-blue.svg?style=flat-square)](https://materialdesignicons.com)

STATUS: Moved to [@slimr/mdi-paths](https://npmjs.org/package/@slimr/mdi-paths)

[Material Design Icon](https://materialdesignicons.com) paths for any TS/JS project, packaged as single components.

- Each modules is named to match the name in [https://materialdesignicons.com](https://materialdesignicons.com), so easy to find
- Unlike most libraries, this one includes all the aliases found in [https://materialdesignicons.com](https://materialdesignicons.com)
- By splitting the paths into different files, lazy loading is easy and possible!
- Version is pinned to the version of `@mdi/svg` this was generated from

## Installation

```bash
npm install mdi-paths-split
# or if you use Yarn
yarn add mdi-paths-split
```

## Usage

Just search for an icon on [materialdesignicons.com](https://materialdesignicons.com) and look for its name.  
The name translates to PascalCase in `mdi-paths-split`.  

Also it's possible to import with an alias. You can find them on the detail page of the respective icon.

Simplest Usage:

```javascript
import HomePath from 'mdi-paths-split/Home'
export function HomeIcon ({ color = 'currentColor', size = 24, ...props }) {
  const className = 'mdi-icon ' + (props.class || props.className || '');
  return (
    <svg {...props} class={className} width={size} height={size} fill={color} viewBox="0 0 24 24">
      <path d={HomePath} />
    </svg>
  );
}
```

With Lazy-Loading/Code-Splitting and blank placeholder:

```javascript
export function HomeIcon ({ color = 'currentColor', size = 24, ...props }) {
  const className = 'mdi-icon ' + (props.class || props.className || '');
  const [path, setPath] = useState('')
  useEffect(() => { 
    import('mdi-paths-split/Home'))().then((module: any) => setPath(module.default)) 
  }, [])

  return (
    <svg {...props} class={className} width={size} height={size} fill={color} viewBox="0 0 24 24">
      <path d={path} />
    </svg>
  );
}
```

And here is how I use it. I made a helper component and factory to make it crazy easy to add more icons while simultaneously reducing line-count per-icon. *Notice how each icon is code-split and lazy loaded with an empty placeholder!!!!*

```typescript
import {Account, Counter, Home} from '~/components/Icons'

export default function HeaderLogo() {
    return <div>
        <Account />
        <Counter />
        <Home />
    </div>
}
```

Source of helper `~/components/Icons`:

```typescript
import { h } from 'preact';
import { useEffect, useState } from 'preact/hooks';

export const Account =   I(() => import('mdi-paths-split/CardAccountDetailsOutline'))
export const Counter =   I(() => import('mdi-paths-split/Counter'))
export const Home =      I(() => import('mdi-paths-split/HomeOutline'))
// ... add as many as you want


function I(lazyPath: LazyPathType) { // aka Icon Factory, shortened to be easier to read
  return (props: IconProps) => <LazySvg lazyPath={lazyPath} {...props} />
}
type IconProps = Omit<LazySvgProps, 'lazyPath'>


function LazySvg({ lazyPath, color = 'currentColor', size = 24, ...props }: LazySvgProps) {
  const className = 'mdi-icon ' + (props.class || props.className || '');
  const [path, setPath] = useState('')
  useEffect(() => { lazyPath().then((module: any) => setPath(module.default)) }, [])

  return (
    <svg {...props} class={className} width={size} height={size} fill={color} viewBox="0 0 24 24">
      <path d={path} />
    </svg>
  );
};
type LazyPathType = () => Promise<any>
interface LazySvgProps {
  color?: string
  size?: number | string
  class?: string
  className?: string
  children?: never
  lazyPath: LazyPathType
}
```

## References

- [mdi-react](https://npmjs.com/package/mdi-react) - The generator in this package was adapted from that one (Thanks!). This package achieves similar things but does so with much less bandwidth penalty per icon.
- [materialdesignicons.com](https://materialdesignicons.com) - Where to browse icons
- [@mdi/js](https://npmjs.com/package/@mdi/js) - Very similar to this lib, but puts all the paths in one file
- [@mdi/svg](https://npmjs.com/package/@mdi/svg) - Where this lib gets the icons from
