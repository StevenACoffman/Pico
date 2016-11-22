# CSS in JS
React: [CSS in JS](https://speakerdeck.com/vjeux/react-css-in-js) techniques comparison.

## Usage
Inside each package folder, run:

```bash
npm i
npm run build && open index.html
```

## Features

**How to read the table**

More crosses doesn't mean "better", it depends on your needs.
For example, if a package supports the css file extraction you can run the autoprefixing at build time.

| Package | Version | Automatic Vendor Prefixing | Pseudo Classes | Media Queries | Styles As Object Literals | Extract CSS File | Watch | Star | Fork |
|---------|:-------:|:--------------------------:|:--------------:|:-------------:|:-------------------------:|:----------------:|:-------:|:-------:|:-------:|
| [aphrodite](https://github.com/Khan/aphrodite) | 0.1.2 | x | x | x | x | x | 80 | 1,510 | 54  |
| [css-loader](https://github.com/webpack/css-loader) | 0.15.6 | | x | x | | x | 32 | 1,091 | 1394|
| [jsxstyle](https://github.com/petehunt/jsxstyle) | 0.0.14 | x | | | x | | 53 | 1,228 | 35|
| [radium](https://github.com/FormidableLabs/radium) | 0.13.5 | x | x | x | x | | 126 | 4,214 | 192|
| [react-css-modules](https://github.com/gajus/react-css-modules) | 3.0.2 | | x | x | | x | 52 | 2,545 | 103 |
| [react-native-web](https://github.com/necolas/react-native-web) | 0.0.11 | x | | | x | x | 129 | 2,947 | 171 |
| [react-style](https://github.com/js-next/react-style) | 0.5.5 | | | x | x | x | 61 | 1,625 | 89 |
| [reactcss](https://github.com/casesandberg/reactcss) | 0.3.2 | x | | | x | | 25 | 1,252 | 60 |

[Star History Chart](http://www.timqian.com/star-history/#Khan/aphrodite&FormidableLabs/radium&webpack/css-loader&gajus/react-css-modules&js-next/react-style&casesandberg/reactcss&petehunt/jsxstyle&styled-components/styled-components)

### Other stuff

1. [CSS to React](http://staxmanade.com/CssToReact/)
2. [HTML to JSX](http://magic.reactjs.net/htmltojsx.htm)
