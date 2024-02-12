# 1. Configuring Webpack:

```ts
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  entry: "./src/index.js",
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist"),
  },
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
          options: {
            presets: ["@babel/preset-env", "@babel/preset-react"],
          },
        },
      },
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, "css-loader"],
      },
      {
        test: /\.(png|svg|jpg|jpeg|gif)$/i,
        type: "asset/resource",
        use: ["file-loader"],
      },
    ],
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: "./src/index.html",
      filename: "index.html",
    }),
    new MiniCssExtractPlugin({
      filename: "styles.css",
    }),
  ],
};
```

- To install these dependencies, use the following command

```bash
npm install webpack webpack-cli babel-loader @babel/core @babel/preset-env @babel/preset-react html-webpack-plugin mini-css-extract-plugin css-loader styles-loader
```

# 2. Advanced Bundling Techniques:

Enhancing the loading efficiency of JavaScript assets in a web application can be achieved through two impactful techniques: `code splitting` and `lazy loading`. These approaches significantly reduce the initial load time of a website, contributing to an enhanced overall user experience.

- Code splitting:
  Let us consider a React project that has two different components, Homepage and Sportspage.

--> Home.js

```javascript
import React from "react";

const Home = () => {
  react <h1>Home Component</h1>;
};

export default Home;
```

--> Sport.js

```javascript
import React from "react";

const Home = () => {
  react <h1>Sports Component</h1>;
};

export default Sport;
```

The process of importing these components will be as outlined below:

--> main.jsx

```javascript
import React from "react";
import Home from "../components/Home"; // importig the Home component
import Sport from "../components/Sport"; // importig the Sport component

const Main = () => {
  return (
    <div>
        <Home />;
        <div>
            <Sport />;
        </div>
    </div>;
  );
};

export default Main;
```

- Lazy Loading:
  Lazy loading is accomplished through the use of dynamic import() statements, ensuring that components are loaded on-demand, thus enhancing the initial page load performance.

Incorporating the previously mentioned Home.js and Sport.js components into the main react page.

--> main.jsx

```javascript
import React, { Suspense, lazy } from "react";
import { BrowserRouter as Router, Route, Switch } from "react-router-dom";

const home = lazy(() => import("../components/Home"));
const sports = lazy(() => import("../components/Sport"));

const Main = () => {
  <Router>
    <Suspense fallback={<div>Loading...</div>}>
      <Switch>
        <Route path="/" component={home} />
        <Route path="/sport" component={sports} />
      </Switch>
    </Suspense>
  </Router>;
};

export default Main;
```

Benefits

- We can also configure webpack to split chunks for better caching and optimization. It generates separate files for common dependencies shared between modules.
  Example:

```javascript
optimization: {
    splitChunks: {
        chunks: 'all',
    },
},
```

- The initial loading time of the web page is enhanced as only essential modules are loaded, leading to a smaller initial bundle size and faster rendering. Improved caching and the reuse of common dependencies across multiple chunks further optimize loading processes.
- Users can begin interacting with the page more promptly, even if certain components load later.

# 3. Introduction to Import Maps

Import Maps simplify the loading and organization of code by mapping module specifiers to actual URLs. They enable developers to establish associations between module names and their respective locations, removing the necessity for bundling or transpilation in the development phase.

- Advantages over traditional bundling techniques:

1. Firstly, it allows users to work with individual modules during development.
2. Loading modules at runtime reduces the initial loading time by fetching necessary modules as required, thereby improving performance.
3. Import Maps enable the integration of third-party modules and libraries within the project.

- Import Maps syntax:
  The import map syntax is a JSON-based configuration file that specifies mappings between module specifiers (names used to import modules) and their corresponding URLs (the locations of the modules). The basic structure of an import map looks like this:

```json
{
  "imports": {
    "math-lib": "/libs/math-library.js",
    "api-client": "https://api.example.com/client.js",
    "helpers/": "/src/helpers/",
    "ui-components/": "/src/components/ui/"
  }
}
```

Implementation of this is as follows:

1. Begin by creating a new import map file named `import-map.json`.
2. Within the import-map.json file, include the following code snippet to map the React module to its corresponding URL:

```json
{
  "imports": {
    "react": "https://cdnjs.cloudflare.com/ajax/libs/react/17.0.2/umd/react.production.min.js"
  }
}
```

3. In the HTML file, link the import-map.json file as follows:

```html
<script type="import-map" src="import-map.json"></script>
```

# 4. Import Maps Usage:

The following code snippet is an example of using the import maps in a web application:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>wd-sports-scheduler</title>
    <script type="import-map">
      {
      "imports": {
          "scheduler": "/src/scheduler.js",
          "teams": "/src/team-manager.js",
          "players": "/src/player-info.js",
          "utils": "/src/time-utils.js"
      }
      }
    </script>
  </head>
  <body>
    <script type="module">
      import Scheduler from "scheduler";
      import TeamManager from "teams";
      import PlayerInfo from "players";
      import TimeUtils from "utils";

      const scheduler = new Scheduler();
      const teamManager = new TeamManager();
      const playerInfo = new PlayerInfo();
      const timeUtils = new TimeUtils();

      console.log(scheduler);
      console.log(teamManager);
      console.log(playerInfo);
      console.log(timeUtils);
    </script>
  </body>
</html>
```

- Compatibility issues:

1. **Browser Support:** 
Varied support across modern browsers due to being a new feature.
2. **Server-Side Rendering (SSR):** 
Compatibility challenges in SSR environments due to import maps being primarily for client-side execution.
3. **Tooling Support:** 
Build tools and testing frameworks may not fully support import maps, requiring additional configuration.