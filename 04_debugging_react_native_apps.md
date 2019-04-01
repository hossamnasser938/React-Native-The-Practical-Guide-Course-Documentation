# Debugging React Native Apps

## Module Introduction
* Errors, bugs, and failures are most common in software development in general. For that reason, developers spend most of their time **debugging**(Tracking and fixing bugs).
* In this module we are looking at how to **debug** ` React Native ` apps.
* We will learn:
    * how to use ` console.log `.
    * how to make good use of different **debugging** capabilities in ` React Native `.
    * how to **debug** ` Redux `.


## Using the Remote JavaScript Debugging console.log
* In **devmenu** we have some useful options related to **debugging**:
    * ` Toggle Inspector `  
    enables/disables the ` Inspector ` which gives us 4 tabs:
        * ` Inspect `  
        gives some info about UI elements like their padding and margin, width, etc. You need to hit an element to see its info.
        * ` Perf `  
        gives some info about **performance**.
        * ` Network `  
        gives some info about network requests made in the app.
        * ` Touchables `  
        on that later.
    * ` Show/Hide Perf Monitor `  
    shows/hides the performance monitor whcich provides info related to performance.
    * ` Debug JS Remotely `  
    The most important one which enables us to debug our logic(**JavaScript**) on the browser. 
        * After enabling it, you can use ` console.log ` to log something from your app somewhere in the code and you will see that on the browser while your app running on the emulator or on the device and connected to your PC. 
        * On enabling it, your browser will open on a page whose **console** is connected to your app. If it did not open, open it manually on ` localhost:8081/debugger-ui/ `. This feature works only for ` Chrome ` and the open source version ` Chromium `.

## Debugging with Breakpoints
* On Enabling ` Remote JS Debugging `, the developer tools of the browser contains some useful and some useless tabs:
    * ` console ` tab  
    is very useful since we see our logs there.
    * ` Elements ` tab  
    is useless since it holds the DOM of the web page not the app ` components `.
    * ` Sources ` tab  
    is very useful since it contains our app files. We can set **breakpoints** any where and rerun our application to stop at this point and debug from there. We can find our app files under ` debuggerWorker.js ` -> ` localhost:8081 `. To learn how to debug on the browser refer to this [article](https://javascript.info/debugging-chrome).


## Debugging with React Native Debugger
* Using ` debugger-ui ` does not give us what we need. Through [` React Native Debugger `](https://github.com/jhen0409/react-native-debugger) we can **inspect** elements, **debug** ` Redux ` and much more.
* We need to download and install this package, extract it, and run it. It is a standalone Desktop tool which is awesome because now no need to use a browser.
* To use it all we need to do is to close the web page we used before for debugging. If we did not, it will complain ` Another debugger is already connected `.


## Debugging Redux
* With ` React Native Debugger ` we can **debug** ` Redux `. To do that, head to [` React DevTools Integration `](https://github.com/jhen0409/react-native-debugger/blob/master/docs/react-devtools-integration.md#how-to-use-it-with-real-device) and there you will find the article [` Redux DevTools Integration `](https://github.com/jhen0409/react-native-debugger/blob/master/docs/redux-devtools-integration.md).
* Previously we used a ` Middleware ` named ` createLogger ` to debug ` Redux ` in ` ReactJS `. A ` Middleware ` is just a type of **enhancer**. In ` React Native ` we use another type of **enhancer** to debug ` Redux `.
* Working with ` Middleware ` required us to use ` applyMiddleware ` ` function `. However, now we work with the general type of ` Middleware ` which is **enhancer** so we use ` compose ` method instead.
* To debug ` Redux `, We have to configure our ` Store `. In ` configStore.js `:
    1. We need to ` import ` ` compose ` from ` redux `.
    ```js
    import { createStore, combineReducers, compose } from 'redux'; 
    ```
    
    2. In development mode We can use the ` compose ` ` function ` we get from **dev tools** which is ` window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ `.
    ```js
    let composeEnhancers = compose;

    if ( __DEV__ ) {
        composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;
    }
    ```
    Note that ` __DEV__ ` is a global variable ` exposed ` by ` React Native ` which is ` true ` only when we are in development mode.

    3. Last we need to apply this **enhancer** when creating the ` Store `.
    ```js
    const configStore = () => {
    return createStore( 
            rootReducer,
            composeEnhancers()
        );
    }
    ```
* Now in ` React Native Debugger` you should see ` @@INIT ` which means your ` Redux ` is connected. 

