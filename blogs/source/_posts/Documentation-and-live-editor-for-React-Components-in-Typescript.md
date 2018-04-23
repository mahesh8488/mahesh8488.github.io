---
title: Documentation and Live Editor for React Components in Typescript
date: 2018-04-21 23:31:43
tags: ReactJS Typescript Documentation
---
Its all really exciting to follow the Create-React-app and build your first React applicaiton in minutes. Once you get the taste of it, you start building multiple pages in no time. Then, you enter a phase where you want more people to reuse the components you have built. While promoting your components, you suddenly realize that you didnt give any attention to documentation.
    An easier solution, not only to get a documentation without any code changes to solution but also get a page that can act as a playground for others to try out your components, is [React Docgen](https://github.com/reactjs/react-docgen). React Docgen hit 2 birds in one stone.

Below steps will guide you in creating a basic react app with documentation in typescript.

* Configure your **package.json** as shown below. You may not need many of the packages for a sample react component. But you may need those later.

```js
    {
  "scripts": {
    "build": "webpack"
  },
  "dependencies": {
    "@types/react": "^16.3.12",
    "babel-core": "^6.26.0",
    "babel-loader": "^7.1.4",
    "classnames": "^2.2.5",
    "css-loader": "^0.28.11",
    "glob": "^7.1.2",
    "mobx": "^4.2.0",
    "mobx-react": "^5.0.0",
    "moment": "^2.22.1",
    "path": "^0.12.7",
    "prop-types": "^15.6.1",
    "react": "^16.3.2",
    "react-docgen": "^2.20.1",
    "react-docgen-typescript": "^1.3.0",
    "react-dom": "^16.3.2",
    "react-styleguidist": "^7.0.8",
    "style-loader": "^0.21.0",
    "ts-loader": "^4.2.0",
    "typescript": "^2.8.3",
    "webpack": "^4.6.0"
  }
}

```

* Create a ***styleguide.config.js*** at the root level as shown below:

```js
const path = require('path');
const glob = require('glob');

module.exports = {
    components: function () {
        return glob.sync(path.resolve(__dirname, 'components/*.tsx'))
          .filter(function (module) {
            return /\/[A-Z]\w*\.tsx$/.test(module);
          });
      },
    resolver: require('react-docgen').resolver.findAllComponentDefinitions,
    propsParser: require('react-docgen-typescript').withDefaultConfig({ propFilter: { skipPropsWithoutDoc: true } }).parse,
    webpackConfig: {
        module: {
          rules: [
            // Babel loader, will use your project’s .babelrc
            {
              test: /\.jsx?$/,
              exclude: /node_modules/,
              loader: 'babel-loader'
            },
            {
                test: /\.tsx?$/,
                loader: 'ts-loader',
                exclude: /node_modules/,
            },
            // Other loaders that are needed for your components
            {
              test: /\.css$/,
              //loader: 'style-loader!css-loader?modules'
              use: [
                { loader: "style-loader" },
                { loader: "css-loader" },
              ]
            }
          ]
        }
      }
  };
  ```


* Add a simple component ***components/Column.tsx***
```js
import * as React from 'react';
import { Component } from 'react';

/**
 * Column properties.
 */
export interface IColumnProps extends React.HTMLAttributes<any> {
  /** prop1 description */
  prop1?: string;
  /** prop2 description */
  prop2: number;
  /**
   * prop3 description
   */
  prop3: () => void;
  /** prop4 description */
  prop4: 'option1' | 'option2' | 'option3';
}

/**
 * Form column.
 */
export class Column extends Component<IColumnProps, {}> {
  render() {
    return <div>Column rendered</div>;
  }
}

export default Column;
```


* Add ***Column.md***


    Column - component description
    ```js
    <Column prop1="test" />

* Run ***yarn*** to install the packages and then run the ***styleguidist***

    yarn
    npx styleguidist server

This will serve the below output, which has 2 parts to it:
1. Prop types declaration
2. Playground for your react component

### 1. Declaration

As shown in Column.tsx, just adding comment above the prop results into out shown below:

      /** prop2 description */
      prop2: number;

![Styleguidist Props](https://github.com/mahesh8488/static-warehouse/blob/master/images/styleguidist-props.PNG?raw=true)

### 2. Playground for your React components

Each component gets its own demo and live editor.

![Styleguidist Props](https://github.com/mahesh8488/static-warehouse/blob/master/images/styleguidist-code.PNG?raw=true)


Using ReactDocgen you can create a platform that makes your code readable and reusable. [Here](https://github.com/mahesh8488/react-doc-gen-typescript) is the github link for sample project I created.
