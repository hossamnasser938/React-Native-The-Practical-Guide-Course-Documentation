# Plishing the App

## Module Introduction
* In this module we will try to identify how can we improve our application before heading to the **Deployment** module which will be the last one in the course.


## Identifying Improvement Potential
* On playing around with our applications, we can find a bunch of issues that we can improve including:
    * On adding a new place and going back to ` FindPlace ` ` screen `, we do not see the place we just added. That happens because we load places in ` componentDidMount ` which is executed only when we ` startTabBasedApp `. ` React Native Navigation ` keeps ` screens ` in memory to provide a better performance. On going back ` componentDidmoun ` is not invoked.

    * When the user successfully add a place, we need to reset ` AddPlace ` ` screen ` and redirect him immediately to ` FindPlace ` ` screen `.

    * The process of adding a new place takes much time. This happens because most probably the images are a bit big in size and uploading such images takes some time. We wish to decrease the images sizes before uploading so adding a new place does not take much time..


## Shrinkin Image Sizes
* ` react-native-image-picker ` package provides us with a nice feature. We can specify a ` maxWidth ` and a ` maxHeight ` for the image picked so if the image picked is larger, it shrinks it which results in decreasing its size. In ` components/PickImage.js ` we call the mwthod ` showImagePicker ` which we pass a configuration object. We can add ` maxWidth ` and ` maxHeight ` in this configuration object.
```js
imagePickerHandler = () => {
        ImagePicker.showImagePicker(
            {
                title: "Pick image for your place",
                maxWidth: 800,
                maxHeight: 600
            },
            // the rest of the method
```


## Resetting the Share Place Screen
* In ` AddPlaceScreen ` we have 3 things to reset when the user hits ` submit `. In ` AddPlaceScreen.js ` we define a method named ` reset ` in which we will reset those 3 things. 
    1. The ` state ` in the ` AddPlaceScreen `.
    ```js
    reset = () => {
        this.setState( {
            controls: {
                placeName: "",
                location: null,
                image: null
            }
        } );
    }   
    ```
    We need to call this method in ` addPlaceHandler `.
    ```js
    addPlaceHandler = () => {
        this.props.onAddPlace( 
            this.state.controls.placeName, 
            this.state.controls.location,
            this.state.controls.image 
        );

        this.reset();
    }
    ```

    2. In ` PickImage ` ` Component ` we need to reset the ` pickedImage ` to ` null `.
    ```js
    reset = () => {
        this.setState( {
            pickedImage: null
        } );
    };
    ```
    We need to call this method from inside ` AddPlaceScreen ` in ` reset ` method so we need a reference to ` PickImage ` ` component `.
    ```js
    <PickImageComponent 
        updateImageHandler = { this.updateImageHandler }
        ref = { ref => this.pickImageComponent = ref }
    />
    ```
    ```js
    reset = () => {
        this.setState( {
            controls: {
                placeName: "",
                location: null,
                image: null
            }
        } );

        this.pickImageComponent.reset();
    }
    ```

    3. In ` AddLocation ` ` Component ` we define ` reset ` also but since the ` state ` to reset is huge we won't duplicate the code but we will reuse the ` reset ` method in ` componentWillMount `. We also can follow this approach in previous 2 points.
    ```js
    componentWillMount() {
        this.reset();
    }

    reset = () => {
        this.setState( {
            focusedRegion: {
                latitude: 30.4641,
                longitude: 30.9358,
                latitudeDelta: 0.0122,
                longitudeDelta: (Dimensions.get("window").width / Dimensions.get("window").height) * 0.0122 
            },
            chosenRegion: false
        } );
    }; 
    ```
    For that to have effect we need to set ` region ` ` prop ` because ` initialRegion ` is used at the first time only. However, if we set it we lose our nice animation we get when we update location. To solve that we can check whether this is done as a ` reset ` or not. We can detect this is done as a ` reset ` from the ` chosenRegion ` attribute in the ` state ` if it is ` false ` that means that we reset it so we assign ` region ` if not that means that we updates the ` region ` ourselves which means we want ` region ` to be ` null ` and ` animateToRegion ` will do the job for us.
    ```js
    <MapView 
        style = { styles.mapContainer }
        initialRegion = { this.state.focusedRegion }
        region = { !this.state.chosenRegion? this.state.focusedRegion : null }
        onPress = { this.onPressHandler }
        ref = { ref => this.mapView = ref }
    >
        { marker }
    </MapView>
    ```
    As we did with ` Pick Image ` we need a reference to ` AdDLocation ` to call ` reset ` in ` AddPlaceScreen `.
    ```js
    <AddLocationComponent 
        updateLocationHandler = { this.updateLocationHandler }
        ref = { ref => this.addLocationComponent = ref }
    /> 
    ```
    ```js
    reset = () => {
        this.setState( {
            controls: {
                placeName: "",
                location: null,
                image: null
            }
        } );

        this.pickImageComponent.reset();
        this.addLocationComponent.reset();
    }
    ```


## Redirecting to Another Tab
* We wanna redirect the user to ` FindPlace ` tab when he successfully add a new place in ` AddPLace ` tab.

* For that we need to ` dispatch ` an ` Action ` in ` addPlace ` ` Action ` only when we successfully add a place and in resonse to that ` Action ` we can set an attribute in the ` state ` where we can use this attribute to redirect the user.
    1. Name the ` action ` in ` redux/action/ActionTypes.js `.
    ```js
    export const PLACE_ADDED = "PLACE_ADDED";
    ```

    2. Define an ` Action Creator ` in ` redux/actions/places.js `.
    ```js
    export const placeAdded = () => {
        return {
            type: PLACE_ADDED
        };
    };
    ```

    3. Respnd to that ` Action ` in ` redux/reducers/places.js `.
    ```js
    case PLACE_ADDED:
        coppiedState.placeAdded = true;
        break;
    ```
    We need to add this new attribute in ` initialState `.
    ```js
    const initialState = {
        places: [],
        placeAdded: false
    }; 
    ```

    4. We ` dispatch ` this new ` Action ` in ` addPlace ` ` Action ` when we successfully add a place.
    ```js
    .then( parsedResponse => { 
        console.log( parsedResponse );
        dispatch( placeAdded() );
        dispatch( uiStopLoading() ); 
    } );
    ```

    5. Finally we need to listen on that new attribute in ` state ` that will be changed on adding a new place and redirect the user. But where we can do that? Ofcourse in ` AddPLaceScreen ` but where there? Where we can check on that attribute? We need to do that whenever the user tries to add a new place because it may succeed or fail. A solution is that we can put that in ` componentDidUpdate ` because when the user add a new place we will stop the ` ActivityIndicator ` and display the ` Button ` again.
        1. First we need to ` map ` this ` state ` attribute to ` props `.
        ```js
        const mapStateToProps = ( state ) => {
            return {
                loading: state.uiReducer.loading,
                placeAdded: state.PlacesReducer.placeAdded
            };
        }
        ```

        2. We use it. ` react-native-navigation ` provides a method on ` navigator ` named ` switchToTab ` which switches between tabs. It accepts an object with an attribute named ` tabIndex ` which specifies the index of the tab to switch to.
        ```js
        componentDidUpdate() {
            if ( this.props.placeAdded ) {
                this.props.navigator.switchToTab( { tabIndex: 0 } );
            }
        }
        ```

    6. Now eveythingg works but there is a problem. If the user addedd a place he will be redirected to ` FindPlace ` tab successfully. However, if he decided to get back immediately an add another place, the ` placeAdded ` attribute is still ` true ` so on any change on the ` Component `like picking an image he will be redirected immediately to ` FindPlace ` tab. We need to clear this attribute when we enter ` AddPlace ` tab. For that we will define a new ` Action ` to clear this attribute.
        1. Name the ` Action ` in ` redux/actions/ActionTypes.js `.
        ```js
        export const CLEAR_PLACE_ADDED = "CLEAR_PLACE_ADDED";
        ```

        2. Define an ` Action Creator ` in ` redux/actions/places.js `.
        ```js
        export const clearPlaceAdded = () => {
            return {
                type: CLEAR_PLACE_ADDED
            };
        };
        ```

        3. ` export ` this ` Action ` in ` redux/actions/index.js ` since we will ` dispatch ` it from ` AddPlaceScreen `.
        ```js
        export { deletePlace, getPlaces, addPlace, clearPlaceAdded } from './places';
        ```

        4. Respond to this ` Action ` in ` redux/reducers/places.js `.
        ```js
        case CLEAR_PLACE_ADDED:
            coppiedState.placeAdded = false;
            break;
        ```

        5. In ` AddPlaceScreen ` ` map ` this ` Action ` into ` prop `.
        ```js
        const mapDispatchToProps = ( dispatch ) => {
            return {
                onAddPlace: ( newPlaceName, newPlaceLocation, newImage ) => dispatch( addPlace( newPlaceName, newPlaceLocation, newImage ) ),
                onClearPlaceAdded: () => dispatch( clearPlaceAdded() )
            };
        }; 
        ```

        6. In ` AddPlaceScreen ` we can make good use of the ` events ` fired by ` react-native-navigation ` which fires for us an ` event ` whenever the tab opens. We listen on these events, we respond to this specific event which we can get its info by logging the ` event ` comming.
        ```js
        if ( event.type === "ScreenChangedEvent" ) {
            if ( event.id === "willAppear" ) {
                if ( event.navigatorEventID === "screenInstanceID6_events" ) {
                    this.props.onClearPlaceAdded();
                }
            }
        }
        ```


## Loading Places All the Time
* We need to fix the issue that places are loaded only at beginning. It is very simple. Instead of loading places in ` componentDidMount ` we can do that using ` events ` fired by ` rect-native-navigation ` as we did before. In ` FindPlaceScreen ` inside ` onNavigatorEvent `.
```js
if ( event.type === "ScreenChangedEvent" ) {
    if ( event.id === "willAppear" ) {
        if ( event.navigatorEventID === "screenInstanceID4_events" ) {
            this.props.onLoadPlaces();
        }
    }
}
```


## Improving Http Error Handling
* We mentioned before that the ` catch ` block chained to the ` Promise ` returned from ` fetch ` only catches network errors when we fail to reach to the server. However, we may reach to the server and the server returns us an error like ` 4xx ` or ` 5xx ` errors. We should handle these error.

* In the ` then ` block that follows ` fetch ` where we get ` response ` and we usually returns ` response.json() ` we can check before returning that ` ok ` property is ` true ` which means there were no errors. If not we can ` throw ` an ` error ` and that will be caught by the last ` catch ` block and we will avoid proceeding to the nest ` then ` with an ` error `.

* Instead of:
```js
.then( response => response.json() )
```
We can:
```js
.then( response => {
    if ( response.ok ) {
        return response.json();
    }
    else {
        throw new Error();
    }
} )
```


## Cleaning Stored Images (on Firebase)
* A finall issue we need to fix. When we delete a place, we only delete that place from the realtime database. However. the place image is still in the firebase storage. We need to delete a place image whenever we delete the place info from the realtime database.

* We can do that using **Firebase Cloud Functions**. To upload the image we used one feature from **Firebase Cloud Functions**. It is ` Call functions directly -> Call functions via HTTP requests `.
```js
exports.uploadImage = functions.https.onRequest((request, response) => {
```

* Firebase provides another feature. It is ` Trigger background functions -> Realtime Database triggers ` which are ` functions ` that are triggered when some operation you specify occurs on the realtime database. This is exactly what we want. A ` function ` to be triggered when a place is deleted from the realtime database. This ` function ` should delete the place image from the storage.

* If we google for ` firebase cloud functions ` we find the documentation for these previously mentioned features.

* To be able to remove that image, we need a reference for it in the realtime database. We have already the **download url** but this works only for download. We need to store the image ` path ` in the realtime database. For that we will return this path when we upload the image and push that path into realtime database.
    1. In ` functions/index.js ` return the image path besides the image download url.
    ```js
    if ( !error ) {
        response.status( 201 ).json( {
            imageUrl: "https://firebasestorage.googleapis.com/v0/b/" +
                bucket.name +
                "/o/" +
                encodeURIComponent( file.name ) +
                "?alt=media&token=" +
                name,
            imagePath: "/places/" + name + ".jpg"
        } );
    }
    ```

    2. In ` redux/actions/places.js ` in ` addPlace ` ` Action ` push that image path.
    ```js
    .then( parsedResponse => {
        const place = {
            name: placeName,
            location: placeLocation,
            imageUrl: parsedResponse.imageUrl,
            imagePath: parsedResponse.imagePath
        };
        // the rest of the function
    ```


* Now we can deploy our own ` function ` to be triggered once a place is deleted, and in response it deletes its corresponding image. in ` functions/index.js `.
```js
exports.deletImage = functions.database.ref( "places/{placeId}" ).onDelete( snapshot => {

} );
```
Here we use ` database ` not ` http `, then we specify the ` reference ` in the database we want to listen on, and last we specify the type of operation we want to trigger our ` function ` when it occurs whic is, in our case, ` onDelete `. Note that in ` {placeId} ` the curly braces here denotes that it is a variable and that variable will represent the ` id ` of the place deleted.

* Inside the ` function `.
    1. We need to get the place deleted which is passed by firebase in the ` snapshot `.
    ```js
    const placeDeleted = snapshot.val();
    ```

    2. We get the image path.
    ```js
    const imagePath = placeDeleted.imagePath;
    ```

    3. The last thing is to get the ` bucket ` where we save images and access the image by passing the image path to ` file ` method and then call ` delete ` method which returns us a ` Promise ` which we must return from the ` function ` in order to work.
    ```js
    const bucket = gcs.bucket( "rn-course-practi-1553685361491.appspot.com" );
    return bucket.file( imagePath ).delete();    
    ```

    4. Now we can deploy and test.