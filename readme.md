# Architecture

This document has the purpose to specify the architecture of [this](https://xd.adobe.com/view/e5a9d78f-bae8-4fe1-43ce-4ab2e2c67ea0-9398/?fullscreen) web application, using React and Redux. The architecture should specify the following items:
- **Separation of modules:** What criteria should be used to group modules by.
- **Module comunication:** To whom should modules comunicate with and how should this comunication happen.
- **Folder structure:** How should the project's modules be organized, and where should new modules be placed.

Note: a module is simply a javascript file that exports code.

## Layers
In a macroscopic view, the project should be viewed as separate layers that comunicate with each other. Layers are a group of modules, held together by their core purpose. Each Layers should have a very broad purpose, but should also be easily discerned between each other.

The project will be separated by four layers: **API layer**, **State control layer**, **Visual layer** and **Logic layer**. Their relationships are represented below:

<img src='./gobrain-architecture.svg' style='width: 100%; height: 512px;'>

### 1 - Visual layer
Responsible for the interface itself, displaying data and handling user input. It's modules will mainly contain styling and DOM related code, alongside raw event-handling and data displaying. This layer will, by far, contain the largest amount of code.

### 2 - Logic layer
Responsible for holding application-wide logic and values. If some algorithm is used in many pages/components, it should be grouped into a module and placed into the logic layer. Some examples are: data transformation (celcius -> fahrenheit), authentication assertion (make sure user is logged in), state-dependent rendering (only render components if some state is available).

### 3 - State control layer
Responsible for storing and managing application state. It's modules will be constituted by Redux actions, redux reducers and cache-related logic. It should also be responsible for managing the browser's local storage and for making sure all states are being properly updated

### 4 - API layer
Responsible for talking to the backend. Some of it's duties are managing authorization tokens, making sure the correct headers are being sent, reporting to the Visual layer the correct error message for any failed request, etc...

### Layer comunication

Layers shouldn't be free to comunicate in every way, they must have specific purposes. This will be described below:
- **API layer:** The API layer is the only one allowed to maked HTTP requests, and therefore the only one capable of comunicating with the backend. It should only export functions as request wrappers, such as `function fetchUser(userId: number) : APIResponse<User>`. This layer will only export it's wrappers to the _State control layer_ to allow for data cache control and to make sure all necessary components are updated when new data arrives.
- **State control layer:** This layer will receive api handlers from the _API layer_ and export functions for reading and writing state values to the _Visual layer_. If needed, this layer will use login from the _Logic layer_.
- **Visual layer:** This layer will only import resources from other layers. It will import state-manipulation functions from the _State control layer_ and logic-related functions from the _Logic layer_. It will use these functions for displaying and updating data.
- **Logic layer:** This layer will only export resources to other layers. It provide self-contained logic in the form of modules. These modules could be used by the _Visual layer_ and the _State control layer_.

## Folder structure

now that we have a very solid understanding of the layer structure, it's time we move to a more code-related language: folder structure. The proposed model is ilustrated by the image below:

<img src='./goBrain-folder-structure.svg'>

Note that all folders (except `/src/global-assets`) belong to a layer. That means the contents of the folders should fit the folder's layer's definition and purpose.

Each folder will be throughoutly explained in the next sessions.

### Root folder - `/`

Holds configuration-related files, such as `package.json`, `.babelrc`, `tsconfig.json` and etc... Should also hold any auto-generated files or folders, such as `build/`, `out/`, `node_modules/`, etc...

### `/src`

Contains the all user-written code and assets. It should have a single `index.js` file that bootstraps the whole project, and the 9 other folders already shownd in the previous image.

### `/src/pages`

Has all page-related code. It should only contain one folder for each page. This folder must have an `index.js` file that exports the page itself, and could contain other files that contain code specific to that page (such as a component or an image that is only used in that page). A possible example is ilustrated by the following image:

<image src='./pages-example.svg'>

### `/src/components`
Any component that is used in multiple points of the application should be placed here. This folder's structure is very similar to `/src/pages`. This folder should have one folder for each of it's components, and that folder must have an `index.js` file, and could have other files that are only related to that component. Here's an example:

<image src='./components-example.svg'>

### `/src/router`

This folder should have all routing-related code. It must have at least two files: `router.js`, which is responsible for showing the correct page at the correct browser path, and `paths.js`, which exports all of the application's path strings.

### `/src/libs`

Contains all code related to the _Logic layer_. It could contain single `.js` files, and could also hold any kind of folder. This folder is very flexible, since logic code is usualy separated by modules, not folders.

### `/src/constants`

Should hold any constant values that are user application-wide and, if necessary, must be easily changeable. This folder could contain any `.js` files and any folders, but files should never contain application-related logic. Some examples of possible files are: `colors.js` that exports the application's used colors, `env` that exports a boolean indicating whether this is a production or development build, etc...

### `/src/redux`

Contains all redux-related code. Should have a `store.js` file that exports the redux store, a `reducers` folder that contains all reducers and a `actions` folder that contains all action creators.

The `reducers` folder should contain multiple modules, each exporting a reducer related to a part of the global state. it should also have and `index.js` file that imports all reducers, combines them and exports that combination.

The `actions` folder is very similar to the `reducers` folder, but it exports actions creators, and should not have an `index.js`.

Here's an ilustration:

<img src='./redux-example.svg'>

### `/src/services`

This folder has any self-container code whose only purpose is to maintain application state. Some examples of services are: `screen-listener.js` whose job is to update the application state with the current screen size, `user-fetcher` whose job is to update the user's information every few minutes, etc...

This folder can contain both `.js` files and folders, organized at the programmer's leisure.

### `/src/api`
This folder should hold all _API layer_ related code. It should export all request handlers through `index.js`. The folder can contain other `.js` files or folders, but they must only be API related, and cannot export code to files outsize `/src/api`. Here's an example:

<img src='./api-example.svg'>

### `/src/global-assets`

This folder simply holds all assets that are used through the application. Since it does not hold any code or programming logic, it's organization dont have to be very rigid, and could be modified if the programmer sees fit. A possible structure sugestion would be this:

<img src='./global-assets-example.svg'>