# mdi-paths [![npm package](https://img.shields.io/npm/v/mdi-paths-split.svg?style=flat-square)](https://npmjs.org/package/mdi-paths-split) [![Material Design Icons version](https://img.shields.io/badge/mdi-v5.9.55-blue.svg?style=flat-square)](https://materialdesignicons.com)

[Material Design Icon](https://materialdesignicons.com) paths for any TS/JS project, packaged as single components.

- Each modules is named to match the name in [https://materialdesignicons.com](https://materialdesignicons.com), so easy to find
- Unlike most libraries, this one includes all the aliases found in [https://materialdesignicons.com](https://materialdesignicons.com)
- By splitting the paths into different files, lazy loading is easy and possible!

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

Here is an example of a Preact/React component. *Notice how each icon is code-split and lazy loaded with an empty placeholder!!!!*

```typescript
import {ReactLogo, Counter, Home} from '~/components/Icons'

export default function HeaderLogo() {
    return <div>
        <ReactLogo />
        <Counter />
        <Home />
    </div>
}
```

Source of `~/components/Icons`:

```typescript
import { FunctionalComponent, h } from 'preact';
import { useEffect, useState } from 'preact/hooks';

const IconFactory: IconFactoryType = (lazyPath) => (props: IconProps) => <LazySvg lazyPath={lazyPath} {...props} />

export const React = IconFactory(() => import('mdi-paths-split/React'))
export const Counter = IconFactory(() => import('mdi-paths-split/Counter'))
export const Home = IconFactory(() => import('mdi-paths-split/HomeOutline'))

type IconProps = Omit<LazySvgProps, 'lazyPath'>
type IconType = FunctionalComponent<IconProps>;
type IconFactoryType = (lazyPath: () => Promise<any>) => IconType


export const LazySvg: LazySvgType = ({ lazyPath, color = 'currentColor', size = 24, ...props }) => {
  const className = 'mdi-icon ' + (props.class || props.className || '');
  const [path, setPath] = useState('')
  useEffect(() => { lazyPath().then((module: any) => setPath(module.default)) }, [])

  return (
    <svg {...props} class={className} width={size} height={size} fill={color} viewBox="0 0 24 24">
      <path d={path} />
    </svg>
  );
};
interface LazySvgProps {
  color?: string
  size?: number | string
  class?: string
  className?: string
  children?: never
  lazyPath: () => Promise<any>
}
type LazySvgType = FunctionalComponent<LazySvgProps>;
```

## References

- [mdi-react](https://npmjs.com/package/mdi-react) - The generator in this package was adapted from that one (Thanks!). This package achieves similar things but does so with much less bandwidth penalty per icon.
- [materialdesignicons.com](https://materialdesignicons.com) - Where to browse icons
- [@mdi/js](https://npmjs.com/package/@mdi/js) - Very similar to this lib, but puts all the paths in one file
- [@mdi/svg](https://npmjs.com/package/@mdi/svg) - Where this lib gets the icons from