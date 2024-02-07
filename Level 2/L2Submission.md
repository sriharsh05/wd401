# Webpack Configuration Setup:

```ts
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env'],
          },
        },
      },
      {
        test: /\.css$/,
        use: [
          MiniCssExtractPlugin.loader,
          'css-loader',
        ],
      },
      {
        test: /\.(png|svg|jpg|gif)$/,
        use: [
          'file-loader',
        ],
      },
    ],
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html',
      filename: 'index.html',
    }),
    new MiniCssExtractPlugin({
      filename: 'styles.css',
    }),
  ],
};
```

## Explaination:
- The entry point is set to ./src/index.js.
- The output is set to bundle.js in the dist folder. The path.resolve method is used to resolve the path to the dist folder.
- The module rules are set to use babel-loader for .js files, css-loader for .css files, and file-loader for image files.
- The plugins are set to use html-webpack-plugin and mini-css-extract-plugin.
- The html-webpack-plugin is used to generate an HTML file with the bundle.js and styles.css files injected.
- The mini-css-extract-plugin is used to extract the CSS into a separate file called styles.css.



# Code Splitting & Lazy Loading:
  
  ```ts
  import React, { Suspense, lazy } from 'react';
  import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';

  const HomePage = lazy(() => import('./HomePage'));
  const SportsPage = lazy(() => import('./SportsPage'));

  const App = () => (
    <Router>
      <Suspense fallback={<div>Loading...</div>}>
        <Switch>
          <Route path="/" component={HomePage} />
          <Route path="/about" component={SportsPage} />
        </Switch>
      </Suspense>
    </Router>
  );

  export default App;
  ```

## Explaination:
- The lazy function is used to dynamically import components using React.lazy and Suspense.
- The Suspense component is used to specify a fallback UI while the component is being loaded.
- The BrowserRouter, Route, and Switch components are used from react-router-dom to define the routes for the components.
- The path prop is used to specify the path for the component.
- The component prop is used to specify the component to render when the route matches the current location.

# Introduction to import maps:

Import Maps is a web standard that provides a way to map module specifiers to URLs, allowing more flexibility in managing module dependencies in JavaScript.

Import maps are defined using a JSON-like syntax and can be used to define mappings for module specifiers, making it easier to load modules without having to specify the full URL for each module. This can simplify module loading and provide a centralized way to manage module dependencies.

# Import maps syntax:

The import maps syntax is defined using a JSON-like syntax, with an imports object that contains mappings for module specifiers to URLs. The imports object is used to define the mappings for the packages, allowing developers to specify the URLs for modules without having to specify the full URL for each module.

The following is an example of the import maps syntax:

```json
{
  "imports": {
    "react": "/node_modules/react/umd/react.production.min.js",
    "react-dom": "/node_modules/react-dom/umd/react-dom.production.min.js",
    "react-router-dom": "/node_modules/react-router-dom/umd/react-router-dom.production.min.js"
  }
}
```

In this example, the imports object is used to define mappings for the react, react-dom, and react-router-dom packages. Each mapping specifies the module specifier and the corresponding URL for the module.

# Import maps usage:

Import maps can be used in web applications to simplify module loading and manage module dependencies. By defining import maps, we can specify the URLs for modules without having to specify the full URL for each module, making it easier to load modules.

The following is an example of using import maps in a web application:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Sports Scheduler</title>
  <script type="importmap">
    {
      "imports": {
        "sportslib": "/libs/sportslib.js",
        "schedule-ui": "/libs/schedule-ui.js",
        "schedule-styles": "/libs/schedule-styles.css"
      }
    }
  </script>
</head>
<body>
  <div id="schedule-app"></div>
  <script type="module">
    import SportsLib from 'sportslib';
    import ScheduleUI from 'schedule-ui';
    import 'schedule-styles';

    const sportsLibrary = new SportsLib();

    const schedules = fetchSchedulesFromServer();

    const scheduleUI = new ScheduleUI('#schedule-app');
    scheduleUI.renderSchedules(schedules);

    function fetchSchedulesFromServer() {
      return [
        { date: '2024-02-10', sport: 'Basketball', teams: ['Team A', 'Team B'], venue: 'Arena 1' },
        { date: '2024-02-12', sport: 'Football', teams: ['Team C', 'Team D'], venue: 'Stadium 2' },
        { date: '2024-02-15', sport: 'Baseball', teams: ['Team E', 'Team F'], venue: 'Field 3' }
      ];
    }
  </script>
</body>
</html>
```

## Explaination:
- The importmap script tag is used to define the import maps for the web application.
- The imports object is used to define mappings for the sportslib, schedule-ui, and schedule-styles modules.
- The type="module" attribute is used to specify that the script is a module, allowing the use of import statements to load modules.
- The import statements are used to load the modules using the module specifiers defined in the import maps.
- The modules are used to fetch schedules from the server and render the schedules using the ScheduleUI component.


## Benefits of import maps:

- Import maps provide a way to define mappings for module specifiers, making it easier to load modules without having to specify the full URL for each module.
- Import maps allow for management of module dependencies at one place, making it easier to update and maintain module mappings.
- Import maps can improve performance by reducing the number of HTTP requests required to load modules, especially when loading modules from different sources.
- Import maps can simplify the process of loading modules in web applications, making it easier to manage module dependencies and improve the overall developer experience.


