# Networking _ Sending Http Requests

## Module Introduction
* In this module we will learn how to connect to a server. To be precise, we will learn how to store data on a server so that we acheive **data persistence** which means we can get the data after closing the app and returning back and also, which is much important, can share places between users.
 * Connecting to a server is done through ` http ` **requests** so we will have a look at:
     * how does it work?
     * ` fetch ` **Api** which allows us to make ` http ` **requests**.
     * how to handle requests & responses.
     * and finally as a special case how to upload images.


## Sending Http Requests Theory
* How does a ` React Native ` app, or generally a mobile app, communicate with a server?  
    * Since a mobile app is a standalone product that ` renders ` its own view, it does not need any view from the server[In contrast with web front-end applications, the web app needs its view from the server and then it ` renders ` this view].
    * What a mobile application needs from the server is **Restful Apis** which are **endpoints**(**URLs**) that return back data, usually from a database, in a format like **JSON** or **XML** or whatever.
* So what we do in a ` React Native ` app is sending an ` http ` **request** to the server and the server responds by some data like places, images based on the endpoint we use. We then receive and handle this data came from the server. 

## Creating the Server
* Now we need a server to which we can connect to store and retreive data.
* For that we have 2 possible solutions:
    * Build our own server using any **server-side** programing language such as ` PHP `,  ` Python `, ` NodeJS `, ` Java ` and more.
    * Use an existing service that provides us with a server with no or little server-side programming.
* For simplicity, we will choose the second option. 
* ` Firebase ` is a service provided by Google that ` exports ` a configurable server with no or little server-side programming which is what we need.
* First we need to create a project by: 
    1. Heading to the official [website](https://firebase.google.com/).
    2. Hitting ` Get Started `.
    3. Adding a new project or using existing one.
* On creating a new project, ` Firebase ` provides us with a bunch of features. The one we need for now is ` Database `. So 
    1. Head to ` Database ` section.
    2. Hit ` Create Database `.
    3. Use ` Test ` mode for now[Test mode allows anyine to write and read from the database which used in testing. However, in deployment we need to secure access to our database].
    4. Choose ` Realtime Database `[Realtime Database is a NoSQL-like database in which data is stored as JSON].
* From there you can see a **URL**(an **endpoint**) that you can use to reach your database or reach a nested element in your databse.


## Using the Fetch Api
* Now we need to use this endpoint provided by ` Firebase ` to access the database[Making ` http ` requests].
* In ` React Native ` documentation if we visited ` Networking ` [sction](https://facebook.github.io/react-native/docs/network) under ` The Basics `, we can find different approaches to acheive that purpose.
* We will use the ` Fetch ` **Api** which is a **browser Api** but we still can use in a ` React Native ` app.
* We need to make multiple different ` http ` requests for multiple different purposes. We will start by pushing places to the server.
* The question is: Where to do this ` http ` request? Do we do that in ` AddPlaceScreen ` where we ` dispatch ` an ` action ` to add a place to the ` state `, or do we do that in ` Action Creator `, or we do that in a ` Reducer ` in response to a specific ` Action `?
* Recall that an ` http ` request is an **asynchronous** operation. Ofcourse it is not convenient to do that in a ` Screen ` because simply ` Screens ` are **Views** which are only responsible for displaying the content. Based on that doing ` http ` requests in a ` Screen ` violate the **separation of concerns** concepts.
* Ofcourse you guessed now that we should do that in ` Redux ` stuff. But we are still confused about whether to do that in an ` Action Creator ` or in a ` Reducer ` as a response to an ` Action `? We know that a ` Reducer ` is just getting an ` Action ` and its data that affect ` state ` and the ` Reducer ` is reponsible for updating the ` state ` based on that ` Action ` and its data. For that reason we should do that ` http ` request in ` Action Creator ` and on getting the data the we ` dispatch ` ` Action ` to the ` Reducer ` with data we got. Then the ` Reducer ` wil handle this data for us. If we visited ` Redux ` official [documentation](https://redux.js.org/advanced/async-actions), we can find the answer to our question.
* So for now our ` http ` request will reside in ` src/redux/actions/places.js ` under ` addPlace ` ` Action Creator `.


## Storing Data in Firebase
* In ` addPlace ` ` Action Creator ` we need to run **asynchronous** operation. For that we need ` redux-think ` which is a ` middleware ` that allows performing **asynchronous** operations in ` Action Creator ` and, which's more important, allows ` dispatching ` ` Actions ` inbetween when getting data for example. We used ` redux-thunk ` in the [` React _ Redux tutorial `](https://github.com/hossamnasser938/ReactJS-Redux-Basics-Course-Documentation/blob/master/Documentation%20Files/10_async_actions.md). 

* To install ` redux-think ` simply hit ` npm install --save redux-thunk `.

* Then we need to apply this ` middlewre ` in ` configStore.js `.
    1. ` import ` ` applyMiddleware ` ` function ` from ` redux `.
    ```js
    import { createStore, combineReducers, compose, applyMiddleware } from 'redux'; 
    ```
    2. ` import ` ` redux-thunk ` **middleware**.
    ```js
    import thunk from 'redux-thunk';
    ```
    3. apply this **middleware**.
    ```js
    const configStore = () => {
        return createStore( 
            rootReducer,
            composeEnhancers( applyMiddleware( thunk ) )
        );
    }
    ```

* ` redux-thunk ` handles **asynchronous** ` Actions ` but the question is how to differ between **synchronous** and **asynchronous** ` Actions `? 

* ` redux-thunk ` identifies ` Actions ` when we follow a certain pattern in the ` Action Creator `. Instead of returning back an ` Action ` object with ` type ` and ` payload ` attributes, we return a ` function ` that accepts ` dispatch ` ` function ` as argument which we can call inside to ` dispatch ` ` Actions ` either when getting data or on failure and more.
```js
export const addPlace = ( placeName, placeLocation, placeImage ) => {
    return dispatch => {

    }
};
```

* Now we can use ` fetch ` **Api**.
    * ` fetch ` **method** is used with no prior ` import `.
    * ` fetch ` method accepts
        * the endpoint or the URL.
        * an optional object wrapping attributes that customize the request. Possible attributes we can use there are:
            * ` method ` which specifies the request ` method ` whether ` GET ` or ` POST `. ` GET ` is the default.
            * ` body ` which is mandatory with ` POST ` requests. In ` body ` property we specify the data to be ` posted `, written, or uploaded to the server. Before assigning our data, we need to make sure that it is in a ` string ` format. If it is an object, we can convert it using ` JSON.stringify ` method.

* We need to push a place to the database so we will use ` POST ` method.

* In ` Firebase ` we can get the **root** endpoint of our database. We add to that endpoint the specific sub object that we want to write in followed by ` .json ` which is something required only by ` Firebase `. For my case the root endpoint is ` "https://rn-course-practi-1553685361491.firebaseio.com/" ` and after adding the subobject I want to write in, it becomes ` "https://rn-course-practi-1553685361491.firebaseio.com/places.json" `.
```js
export const addPlace = ( placeName, placeLocation, placeImage ) => {
    return dispatch => {
        const place = {
            name: placeName,
            location: placeLocation
        };

        fetch("https://rn-course-practi-1553685361491.firebaseio.com/places.json", {
            method: "POST",
            body: JSON.stringify( place )
        }).catch( error => console.log( error ) )
          .then( response => response.json() )
          .then( parsedResponse => console.log( parsedResponse ) );
    }
};
```
Note that: 
  * we did not upload ` image ` because we will do that soon in a different approach.
  * ` fetch ` returns a ` Promise ` to which we can chain ` catch ` and ` then ` to handle on success and on failure.
  * on success we get data as a ` Body ` object which we can convert to ` JSON ` format using ` json ` method. ` json ` method returns a ` Promise ` which ` resolves ` with the data parsed in a ` JSON ` format.


## Storing Images && Finishing the Image Upload Function && Storing the Remaining data
* What we hope to do now is to store places images on the server.

* We already have ` base64 ` for each image which is a string representation and we, **theoretically**, can store this string in ` Firebase ` realtime database. However, this is an extremely long strong and the realtime is not designed to store such file-like strings.

* Instead ` Firebase ` provides us with ` Storage ` service where we can store files like images.

* The bad thing is that ` storage ` service have no endpoints we can connect to like what we did with realtime database. Instead ` Storage ` could be accessed through **SDK**(Software Development Kit whuch is a set of software tools that facilitate building apps for a specific platform like Android SDK for example).

* ` Firebase ` provides a JavaScript **SDK** that can be used to access the realtime database instead of ` Fetch ` **Api** approach we used. However, using this JavaScript **SDK** is not a solution for ` Storage ` service in ` React Native `. ` React Native ` does not work well with that yet.

* There is still a solution to access the ` Storage `. It is ` Cloud Functions ` which is a service by ` Firebase ` that lets us **deploy** ` functions ` written in ` NodeJS ` by us to be run on the ` Firebase ` itself(on the server). So we can define a ` function `, deploy it to ` Firebase `, ` Firebase ` will give us a URL for our own ` function ` to be used as if it were a Restful Api. Thin ` function ` should accept an image represented in ` base64 `, store this image on the ` Storage `, and give us back a URL that we can access to download the image in the future.

* To use ` Cloud Functions `, we need to install ` Firebase Tools ` which is just a CLI that wraps ` Cloud Functions ` inside and other services. We install ` Firebase Tools ` globally. Just hit ` sudo npm install -g firebase-tools `.

* To start using ` Cloud Functions ` we make sure that we're in the root directory of our project and then:
    1. hit ` firebase init `[You may be asked to login to ` Firebase `].
    2. Choose ` Firebase ` services you need in your project which, for our case, is only ` Cloud Functions `.
    3. choose your project.
    4. choose the programming language to be used in ` Cloud Functions `.
    5. Choose whether you want to use ` ESLint ` to validate your code or not. Allowing this enforces restrictions on your coding style.
    6. Accept to install sub dependencies. Getting some errors inbetween in fine as long as you got ` Firebase initialization complete! `. 
    7. Now you find a new directory named ` functions ` as well as some firebase-related files in your project root directory which means you're done with setup.

* In ` functions ` directory it has the same structure of a standalone project with ` node_modules ` directory, ` package.json ` file, and ` index.js `. There we can install any dependencies we want which will installed only in this ` functions ` directory. Inside ` index.js ` we write the ` functions ` we wanna deploy.

* In ` index.js ` you find a demo ` function ` named ` helloWorld `. You can use this ` function ` to gain insight on the pattern you should follow when defining ` Cloud Functions `. Recall that the code you write in ` index.js ` in ` NodeJS `.

* We update the name of ` helloWorld ` ` function ` to use its pattern to define our own ` storeImage ` ` function `.
```js
const functions = require('firebase-functions');

// // Create and Deploy Your First Cloud Functions
// // https://firebase.google.com/docs/functions/write-firebase-functions
//
exports.storeImage = functions.https.onRequest((request, response) => {
 
});
```
Note that:
  * ` exports.uploadImage ` this is the way of ` exporting ` in ` NodeJS `.
  * ` require ` this is the way of ` importing ` something in ` NodeJS `. ` NodeJS ` does not support yet the new ES6 syntax for ` importing ` and ` exporting `.
  * the ` function ` accepts:
    * ` request ` which wraps the request got from the client which, in our case, will hold the image.
    * ` response ` which is used to return something for the client.
 
* Now we need to install 2 additional packages in ` functions ` directory 
    * ` @google-cloud/storage ` to let us use the ` Storage ` service. To install it:
        1. navigate to ` functions ` directory.
        2. hit ` npm install --save @google-cloud/storage `[You mau need use ` sudo `].

    * ` cors ` to let us invoke the ` function ` from different origins. To install it:
        1. navigte.
        2. hit ` npm install --save cors `.

* Now we have everything setup, let's start writing our own ` function `.
    1. First we wrap everything we write in the ` cors ` ` middleware ` to be accessible from all origins.
        1. ` import ` the ` cors ` package. After ` importing `, pass an **options** object wrapping ` origin: true ` which is a basic setup to ` cors ` package.
        ```js
        const cors = require( "cors" )( { origin: true } );
        ```
        2. Then inside our ` function `, invoke ` cors ` ` function ` which accepts: ` request `, ` response `, and a ` function ` that will wrap all of our logic.
        ```js
        cors( request, response, () => {

        } );  
        ```
        3. The first thing in our logic is to extract or ` parse ` the image from ` request `
            1. ` parse `(convert from ` string ` into ` JSON `) the ` body ` object wrapped in ` request `.
            ```js
            const body = JSON.parse( request.body );
            ```
        4. We expect this ` body ` object to wrap the image to be stored. So what we will do is to extract this image, store it in a temporary location(` "/tmp/" `) defined by ` firebase ` that is cleared as soon as we end up, and last store it in the ` storage `.
            1. To work with the file system , We need to ` import ` the default file system package provided by ` NodeJS ` which is named ` fs `.
            ```js
            const fs = require( "fs" ); 
            ```

            2. ` fs ` has a method named ` writeFileSync ` which writes a file synchronously. This method accepts: the path of the file to be written, the content to be written which is the image in our case, the format of that content which is ` "base64" ` in our case, and finally a callback to be executed on failure.
            ```js
            fs.writeFileSync( "/tmp/image.jpg", body.image, "base64", error => {
                console.log( "error: ", error );
                return response.status( 500 ).json( { error } );
            } ); 
            ```
            Note that:
                * ` /tmp/ ` is mandatory while ` image ` is up to you, you can pick any name.
                * in the on failure callback we returned a response to the client a ` JSON ` object with the error encountered and we set the ` status ` to ` 500 ` to indicate that it is a server error.
                
        5. Now we can push to ` Google Cloud Storage `.
            1. First we need to ` import ` the ` Storage ` package we installed. But before that we need to define a configuration object to make sure that we have the right access to ` Storage `. This object should wrap: ` projectId ` which we can get from the firebase project console and ` keyFilename ` which is a file holding credentials we can download from the firebase project(To download it: project firebase console -> project settings -> service accounts -> NodeJS SDK -> Generate New Private Key -> Generate Key. Then locate it into project ` functions ` directory. Rename it as you want but leave the extension to ` .json `. I named it ` credentials.json `).
            ```js
            const gcsconfig = {
                projectId: "rn-course-practi-1553685361491",
                keyFilename: "credentials.json"
            };

            const gcs = require( "@google-cloud/storage" )( gcsconfig );   
            ```

            2. ` Google Cloud Storage ` store files in a ` bucket ` so what we will do is to get the ` bucket ` in which we store our images. We get the name of that ` bucket ` from ` Firebase Storage `. There you find the name of the bucket like this ` "gs://rn-course-practi-1553685361491.appspot.com" `. Remove this prefix ` "gs://" ` and use the result.
            ```js
            const bucket = gcs.bucket( "rn-course-practi-1553685361491.appspot.com" ); 
            ```

            3. ` bucket ` has a method named ` upload ` the we can use to store the file we saved in the temporary storage into the Google cloud storage. 
            * ` upload ` accepts: 
              * the ` path ` of the file

              * a configuration object wrapping 3 attributes
                * ` uploadType ` which is ` media ` in our case for image 
                * ` destination ` for which we need to specify a ` path ` to the file inside the ` bucket ` including the file name. For the file name, we need a unique name. There is a package named ` uuid-v4 ` that gives us unique names. Install it:
                ```
                npm install --save uuid-v4
                ```
                Then ` import ` it.
                ```js
                const uuid = require("uuid-v4");
                ```
                Generate a unique name.
                ```js
                const name = uuid();
                ```

                * ` metadata ` which is assigned an object wrapping
                  * ` contentType ` which is in our case ` "image/jpg" `.
                  * ` firebaseStorageDownloadTokens ` which is useful for getting a **URL** for the image at the end.
              * a callbasck to be executed once ` upload ` finishes either with success or error. This callback should expect to get 2 arguments: ` error `, and ` file ` with one of them ` null ` and the second with a value. On success we should return a **URL** to the image which we should construct ourselves with a certain pattern while on failure we should return an error.  
                
            * finally invoke ` upload `.
            ```js
            bucket.upload( "/tmp/image.jpg", {
                uploadType: "media",
                destination: "/places/" + name + ".jpg",
                metadata: {
                    metadata: {
                        contentType: "image/jpg",
                        firebaseStorageDownloadTokens: name
                    }
                } 
                }, ( error, file ) => {
                    if ( !error ) {
                        response.status( 201 ).json( {
                            imageUrl: "https://firebasestorage.googleapis.com/v0/b/" +
                                bucket.name +
                                "/o/" +
                                encodeURIComponent( file.name ) +
                                "?alt=media&token=" +
                                name
                        } );
                    }
                    else {
                        console.log( "error: ", error );
                        response.status( 500 ).json( { error } );
                    }
                }
            );
            ```

* Now we're done writing the ` function `. We then deploy it by 
    1. navigate back to the project root directory.
    2. hitting ` firebase deploy `.

* On deployment, I got this error ` require(...) is not a function ` in this line 
```js
const gcs = require( "@google-cloud/storage" ) ( gcsconfig );
```
After some search I realized that ` google-cloud ` v2 has changed. To fix that, refer to this stack overflow [question](https://stackoverflow.com/questions/47149560/google-cloud-functions-require-is-not-a-function-google-cloud).

* On finishing deployment , you get the **URL** that you can use to connect to this ` function ` as it were a restful Api.

* Now when the user ` dispatch ` an ` addPlace ` ` Action `, we need to ` upload ` the image first and then on getting its **URL** we push the place info including image **URL** to the firebase realtime database. So in ` redux/actions/places.js `.
```js
export const addPlace = ( placeName, placeLocation, placeImage ) => {
    return dispatch => {
        // push image first
        fetch( "https://us-central1-rn-course-practi-1553685361491.cloudfunctions.net/uploadImage", {
            method: "POST",
            body: JSON.stringify( { 
                image: placeImage.base64 
            } )
        } )
        .catch( error => {
            console.log( "error: ", error );
        })
        .then( response => response.json() )
        .then( parsedResponse => {
            const place = {
                name: placeName,
                location: placeLocation,
                imageUrl: parsedResponse.imageUrl
            };

            // push info to realtime database
            return fetch("https://rn-course-practi-1553685361491.firebaseio.com/places.json", {
                method: "POST",
                body: JSON.stringify( place )
            })
        } )
        .catch( error => console.log( error ) )
        .then( response => response.json() )
        .then( parsedResponse => console.log( parsedResponse ) );
   }
};
```


## Adding the Activity Indicator
* Now we hope to give the user a visual feedback while connecting to the server on uploading image and place data. ` React Native ` provides us with a ` Component ` named ` ActivityIndicator ` which is basically a spinneror a progress bar. The good thing about ` ActivityIndicator ` is that it adapts to the platform.

* We need to do 2 things:
    1. We need to  `dispatch ` an ` Action ` before start connecting to the server and in response to that ` Action ` we set a ` state ` property indicating that we are loading to show the ` ActivityIndicator `. We need also to ` dispatch ` another ` Action ` when returning back from the server either with success or failure and in response to that ` Action ` we need to clear the ` state ` property to indicate that we finished loading and the ` ActivityIndicator ` should disappera.
        1. In ` redux/actions/ActionTypes.js ` we add 2 new ` Actions `.
        ```js
        export const UI_START_LOADING = "UI_START_LOADING";
        export const UI_STOP_LOADING = "UI_STOP_LOADING";
        ```
        
        2. In ` redux/actions ` directory we create a new file named ` ui.js ` for those ` Action Creators `.
        ```js
        import { UI_START_LOADING, UI_STOP_LOADING } from './ActionTypes';

        export const uiStartLoading = () => {
            return {
                type: UI_START_LOADING
            };
        }

        export const uiStopLoading = () => {
            return {
                type: UI_STOP_LOADING
            };
        }
        ```

        3. In ` redux/actions/index.js ` We ` export ` these ` Actions `.
        ```js
        export { uiStartLoading, uiStopLoading } from './ui';
        ```

        4. In ` redux/reducers ` we create a new file named ` ui.js ` to define the ` reducer ` of these ` Actions `.
        ```js
        import { UI_START_LOADING, UI_STOP_LOADING } from '../actions/ActionTypes';

        const initialState = {
            loading: false
        };

        const uiReducer = ( state = initialState, action ) => {
            let coppiedState = {
                ...state
            };

            switch( action.type ) {
                case UI_START_LOADING:
                    coppiedState.loading = true;
                    break;
                case UI_STOP_LOADING:
                    coppiedState.loading = false;
                    break;
            }

            return coppiedState;
        }

        export default uiReducer;
        ```

        5. In ` redux/configStore.js ` we ` combine ` the new ` reducer `.
        ```js
        import uiReducer from './reducers/ui';

        const rootReducer = combineReducers( { PlacesReducer, uiReducer } ); 
        ```

        6. In ` redux/actions/places.js ` we ` dispatch ` ` Actions ` before start fetching and on ending either with success or error.
        ```js
        import { uiStartLoading, uiStopLoading } from './index';

        export const addPlace = ( placeName, placeLocation, placeImage ) => {
            return dispatch => {
                dispatch ( uiStartLoading() );

                fetch( "https://us-central1-rn-course-practi-1553685361491.cloudfunctions.net/uploadImage", {
                    method: "POST",
                    body: JSON.stringify( { 
                        image: placeImage.base64 
                    } )
                } )
                .catch( error => {
                    console.log( "error: ", error );
                    dispatch( uiStopLoading() );
                })
                .then( response => response.json() )
                .then( parsedResponse => {
                    const place = {
                        name: placeName,
                        location: placeLocation,
                        imageUrl: parsedResponse.imageUrl
                    };

                    return fetch("https://rn-course-practi-1553685361491.firebaseio.com/places.json", {
                        method: "POST",
                        body: JSON.stringify( place )
                    }).catch( error => { 
                        console.log( error );
                        dispatch( uiStopLoading() ); 
                    } )
                    .then( response => response.json() )
                    .then( parsedResponse => { 
                        console.log( parsedResponse );
                        dispatch( uiStopLoading() ); 
                    } );
                } )

                
            }
        };
        ```

    2. We need to use this ` state ` property to either display the ` Submit ` place ` Button ` or show the ` ActivityIndicator `. In ` AddPlaceScreen.js `.
        1. We ` import ` ` ActivityIndicator ` ` Component `.
        ```js
        import { View, ScrollView, ActivityIndicator, StyleSheet } from 'react-native';
        ```
        
        2. We define ` mapStateToProps ` in which we ` map ` ` loading ` ` state ` to our ` prop `.
        ```js
        const mapStateToProps = ( state ) => {
            return {
                loading: state.uiReducer.loading
            };
        }
        ```

        3. Pass ` mapStateToProps ` to ` connect `.
        ```js
        export default connect( mapStateToProps, mapDispatchToProps )( AddPlaceScreen );
        ```
        
        3. based on the value of ` loading ` we ` render ` either the ` Button ` or the ` ActivityIndicator `.
        ```js
        render() {
            let submitComponent = this.props.loading
                ? ( 
                    <ActivityIndicator />
                )
                : (
                    <DefaultButton 
                        title = "submit"
                        disabled = {
                            this.state.controls.placeName === "" ||
                            this.state.controls.location == null
                        }
                        onPress={ this.addPlaceHandler }  
                    />
                );

            return(
                <ScrollView>
                    <View style = { styles.container }>
                        <MainText>
                            <HeadingText>Share Place with Us</HeadingText>
                        </MainText>

                        <PickImageComponent 
                        updateImageHandler = { this.updateImageHandler }
                        />
                        
                        <AddLocationComponent 
                        updateLocationHandler = { this.updateLocationHandler }
                        />
                        
                        <AddPLaceComponent 
                        placeName = { this.state.controls.placeName }
                        changeTextHandler = { this.changeTextHandler }
                        />

                        { submitComponent }
                    </View>
                </ScrollView>
            );
        }
        ```


## Handling Errors
* Handling errors is very crucial. We need to inform the user when an error occurs.
* There are many ways to inform the user fpr example we could:
    * show a specific ` screen `.
    * show a ` modal `.
    * or just ` alert `.
* The idea is still the same you ` dispatch ` an ` Action ` indicating an error occurred and in response to that you update some ` state ` which in result shows somethig to the user.


## Getting Data from the Server && Fixing an Error
* Now we hope to ` fetch ` the places from the server to display them in ` FindPlaceScreen.js `.
    1. We start by defining an **asynchronous** ` Action ` in ` redux/actions/places.js ` named ` getPlaces ` that reaches to the server ` fetches ` places, process them, and then ` dispatch ` a **synchronous** ` Action ` named ` setPlaces ` to store these places in the ` state ` to be displayed.
    ```js
    import { ADD_PLACE, DELETE_PLACE, SET_PLACES } from './ActionTypes';
    import { uiStartLoading, uiStopLoading } from './index';

    export const getPlaces = () => {
        return dispatch => {
            fetch("https://rn-course-practi-1553685361491.firebaseio.com/places.json")
            .catch( error=> {
                console.log( "error: ", error );
                alert( "Error occurred!!" );
            } )
            .then( response => response.json() )
            .then( parsedResponse => {
                let places = [];

                for ( let key in parsedResponse ) {
                    places.push( {
                        ...parsedResponse[key],
                        image: {
                            uri: parsedResponse[key].imageUrl
                        },
                        key: key
                    } );
                }

                dispatch( setPlaces( places ) );
            } );
        }
    }

    export const setPlaces = places => {
        return {
            type: SET_PLACES,
            payload: places
        };
    };
    ```
    We define ` SET_PLACES ` name in ` ActionTypes.js `.
    ```js
    export const SET_PLACES = "SET_PLACES";  
    ```

    2. In ` redux/reducers/places ` we respond to ` setPlaces ` ` Action ` by setting the ` state ` ` places ` property to the received ` places ` in the ` Action ` ` payload `.
        1. ` import ` the ` Action `
        ```js
        import { ADD_PLACE, DELETE_PLACE, SET_PLACES } from '../actions/ActionTypes';
        ```
        2. handle the ` Action `.
        ```js
        case SET_PLACES:
            coppiedState.places = action.payload;
            break;
    ```

    3. In ` redux/actions/index.js ` we ` export ` ` getPlaces ` ` Action `.
    ```js
    export { addPlace, deletePlace, getPlaces } from './places';
    ```

    4. In ` FindPlaceScreen.js ` we ` dispatch ` this ` Action ` in ` componentDidMount ` to prepare the ` places ` because I do not want to wait untill the user hits ` Find Places ` ` Button ` to reach to the server.
        1. ` import ` the ` Action `.
        ```js
        import { getPlaces } from '../../redux/actions/index';
        ```
        2. ` map ` it to ` props `.
        ```js
        const mapDispatchToProps = dispatch => {
            return {
                onLoadPlaces: () => dispatch( getPlaces() )
            }
        }

        export default connect( mapStateToProps, mapDispatchToProps )( FindPlaceScreen );
        ``` 

        3. ` dispatch ` it.
        ```js
        componentDidMount() {
            this.props.onLoadPlaces();
        }
        ```


## Time to Practice Http
* Now hitting ` trash ` button deletes the place only from ` state ` but it is still on the server so on closing and reopening the app, the place still exists. We hop to delete it from the server. All we need is an **async** ` Action ` that calls ` fetch ` with the same endpoint of the database + the key of the place and specify the ` method ` to ` DELETE ` and then calling the **sync** old delete ` Action ` to delete the place form the ` state `. In ` redux/actions/places.js `.
```js
export const deletePlace = key => {
    return dispatch => {
        fetch("https://rn-course-practi-1553685361491.firebaseio.com/places/" + key + ".json", {
            method: "DELETE"
        }).catch( error => {
            console.log( "error: ", error );
            alert( "Error ocurred!!" );
        } )
        .then( response => response.json() )
        .then( parsedResponse => {
            console.log( parsedResponse );
            dispatch( deletePlaceFromState( key ) );
        } )
    }
};

export const deletePlaceFromState = ( key ) => {
    return {
        type: DELETE_PLACE,
        payload: key
    };
};
```