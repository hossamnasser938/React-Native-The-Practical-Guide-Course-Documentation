# Using Native Device Features: Maps, Camera, and Image Gallery

## Module Introduction
* In this module we will look at how we could use the *native* features of the running platform. To be precise we will:
    * use the **Maps**.  
    to let the user locate the place before sharing.
    * use the device **Camera**.  
    to let the user take an image to the place to be shared.
    * access the **Image Gallery**.  
    to let the user choose an existing image from his gallery for the place to be shared.


## Installing react-native-maps
* ` React Native ` does not provide some way to use the **maps** so we have to use a third-party package to do that.

* **Airbnb** provides us with a package named ` react-native-maps ` which we can use for that purpose.

* In the official [repository](https://github.com/react-native-community/react-native-maps) README we can find an [**installation**](https://github.com/react-native-community/react-native-maps/blob/master/docs/installation.md) page that guides us through the installation process on both platfrms.
* A few notes:
    * I skipped the **project-wide properties** option and used the below one which adds dependencies to ` android/app/build.gradle ` file **not** ` android/build.gradle ` file.
    * You must sign up for Google-Maps Api to get your key which you use in ` Manifest `. To get a free Api key with no billing acount, refer to this [link](http://www.webassist.com/tutorials/Getting-your-Google-Map-API-Key). 


## Rendering a Map
* Now let's replace the placeholder for Map in ` AddPlace.js ` with a real ` Map ` ` component `.

* We first need to turn it into a **class-based** ` component ` to use some ` state ` there.

* ` react-native-maps ` ` exports ` by ` default ` a ` Map ` ` Component ` so we can ` import ` it like this.
```js
import MapView from 'react-native-maps';
```
The name ` MapView ` is up tp you since it is a ` default ` ` export `.

* After that we need to configure this ` MapView `. The most important thing to set for now is the ` initialRegion ` ` prop ` which is assigned an object contains information about which location or region to start with initialy. This object should wrap 4 attributes: 
    * ` latitude ` which is one coordinate.
    * ` longitude ` which is the other coordinate. These 2 attributes specify the centeral point of the region. We need 2 other attributes to specify the area of the region around this centeral point.
    * ` latitiudeDelta ` 
    * ` longitudeDelta ` which is calculated dynamically based on the aspect ratio and ` latitudeDelta ` value we choose.

* In ` AddLocation.js `.
```js
import React, { Component } from 'react';
import { View, StyleSheet, Dimensions } from 'react-native';
import DefaultButton from './UI/DefaultButton';
import MapView from 'react-native-maps';

class AddLocation extends Component {
    state = { 
        focusedRegion: {
            latitude: 30.4641,
            longitude: 30.9358,
            latitudeDelta: 0.0122,
            longitudeDelta: (Dimensions.get("window").width / Dimensions.get("window").height) * 0.0122 
        }
    };

    render() {
        return (
            <View style = { styles.container }>
                <DefaultButton title = "Locate Me" />
                <MapView 
                  style = { styles.mapContainer }
                  initialRegion = { this.state.focusedRegion }
                />
            </View>
        );
    }
}

const styles = StyleSheet.create({
    container: {
        width: "100%",
        alignItems: "center"
    },
    mapContainer: {
        width: "80%",
        height: 150,
        borderColor: "#bbb",
        borderWidth: 1,
        margin: 8
    }
});

export default AddLocation;
```

* Note you have to specify the ` width ` and ` height ` of ` MapView ` explicitly. After that I wish everything work. If there is a problem on **Android**, the instructor suggested 2 things:
    * In Android Studio making sure that in ` Tools -> Android -> SDK Manager -> SDK Tools  ` the ` Google Play Services ` is checked[That was an issue for me].
    * Downgrade the **Api Level**.


## Picking a Location on the Map
* Now we want to let the user traverse in the **map**. To be precise the user already can do so but what we want to do is to let him when hitting somewhere in the **map**, we recenter the map on this hit location.

* So we wanna listen to clicking on the map. Fortunately, ` MapView ` has a ` prop ` named ` onPress ` that lets us listen for any ` press ` on the map. ` onPress ` is assigned a callback to be executed whenver ` press ` occurs . This callback should expect to get an ` event ` that wraps an object named ` nativeEvent ` which indeed wraps an object named ` coordinate ` which wraps the attributes of the new hit which is the new ` latitude ` and ` longitude `. We also need to add one more ` prop ` which is ` region `. We already set ` initialRegion ` ` prop`  but that configures only the region initially. To update the region dynamically, we need to set ` region ` to our ` focusedRegion ` in the ` state ` and in response to ` onPress `, we need to update this ` focusedRegion ` to the new attributes.     

* In ` AddLocation `.
    1. define the callback.
    ```js
    onPressHandler = event => {
        const coordinates = event.nativeEvent.coordinate;
        this.setState( prevState => {
            return {
                focusedRegion: {
                    ...prevState.focusedRegion,
                    ...coordinates
                }
            }
        } )
    };
    ```
    2. ` bind ` this callback with ` onPress ` and set ` region ` ` prop `.
    ```js
    render() {
        return (
            <View style = { styles.container }>
                <DefaultButton title = "Locate Me" />
                <MapView 
                  style = { styles.mapContainer }
                  initialRegion = { this.state.focusedRegion }
                  region = { this.state.focusedRegion }
                  onPress = { this.onPressHandler }
                />
            </View>
        );
    }
    ```


## Adding a Map Marker
* Now we need to add a **marker** whenever the user hits the **map** to indicate where he pressed because that will be the place location he choosen.
* First we need to detect whether the user has hit the **map** or not yet. In case the user hit the **map** we ` render ` a ` MapView.Marker ` ` Component ` as a child to ` MapView ` ` Component `. ` MapView.Marker ` ` Component ` needs ` coordinate ` ` prop ` to be assigned an object wrapping ` latitude ` and ` longitude ` but we still can pass ` focusedRegion ` and it will ignore any attribute it does not need including ` latitudeDelta ` and ` longitudeDelta `.
    1. add an attribute in the ` state ` to reflect whether the user has hit or not yet.
    ```js
    state = { 
        focusedRegion: {
            latitude: 30.4641,
            longitude: 30.9358,
            latitudeDelta: 0.0122,
            longitudeDelta: (Dimensions.get("window").width / Dimensions.get("window").height) * 0.0122 
        },
        chosenRegion: false
    }; 
    ```

    2. update this attribute whenever the user hits the **map**.
    ```js
    onPressHandler = event => {
        const coordinates = event.nativeEvent.coordinate;
        this.setState( prevState => {
            return {
                focusedRegion: {
                    ...prevState.focusedRegion,
                    ...coordinates
                },
                chosenRegion: true
            }
        } )
    };
    ```

    3. Add that logic of checking whether the user hit or not and based on that decide to ` render ` ` MapView.Marker ` ` Component ` or not.
    ```js
    render() {
        let marker = this.state.chosenRegion
            ? (
                <MapView.Marker 
                  coordinate = { this.state.focusedRegion }
                />
            )
            : null;

        return (
            <View style = { styles.container }>
                <DefaultButton title = "Locate Me" />
                <MapView 
                  style = { styles.mapContainer }
                  initialRegion = { this.state.focusedRegion }
                  region = { this.state.focusedRegion }
                  onPress = { this.onPressHandler }
                >
                    { marker }
                </MapView>
            </View>
        );
    }
    ```


## Animating Map Movement
* We successfully respond to user hitting the **map** by moving to that location and set the **marker** there. However, that's not enough for us. We need to **animate** going from location to another and moving the **marker** from location to another.

* Fortunately, ` MapView ` ` component ` has a method named ` animateToRegion `. ` animateToRegion ` **accepts**:
    * the ` region ` to reach to. This object should include ` latitude `, ` longitude `, ` latitudeDelta `, and ` longitudeDelta `.
    * the duration to spend on animating. We can leave that for the default value which is a nice one.

* The problem is that ` animateToRegion ` is executed on ` MapView ` so how we can achieve that? We need to hold a reference to the ` MapView ` ` Component `. Fortunately, ` React ` prvides ` ref ` ` prop ` which allows us to keep track of a reference to a ` Component `. ` ref ` ` prop ` is assigned a ` function ` that accepts this reference to the ` Component ` and you usually assign this reference to an attriute of the class so you can access it form any where in the class using ` this `.

* Notice that after using ` animateToRegion ` no need to use ` region ` ` prop ` because we now change the ` Region ` dynamically using ` animateToRegion `.

* Let's do all of that.
    1. Get a reference to ` MapView ` and get rid of ` region ` ` prop `.
    ```js
    return (
        <View style = { styles.container }>
            <DefaultButton title = "Locate Me" />
            <MapView 
                style = { styles.mapContainer }
                initialRegion = { this.state.focusedRegion }
                onPress = { this.onPressHandler }
                ref = { ref => this.mapView = ref }
            >
                { marker }
            </MapView>
        </View>
    );
    ```

    2. Invoke ` animateToRegion ` whenever the user hits.
    ```js
    onPressHandler = event => {
        const coordinates = event.nativeEvent.coordinate;

        this.mapView.animateToRegion( {
            ...this.state.focusedRegion,
            ...coordinates
        } );

        this.setState( prevState => {
            return {
                focusedRegion: {
                    ...prevState.focusedRegion,
                    ...coordinates
                },
                chosenRegion: true
            }
        } )
    };
    ```
    Note that we used the old ` focusedRegion ` but we overriden the ` latitude ` and ` longitude ` attributes from the new location attributes got from the ` event `.


## Locating the User
* In ` AddLocation.js ` we have a ` Button ` named ` Locate Me `. It is intended to let the user use his current location for the place to be shared. Let's add this feature.
    1. We need to take the permission to use ` location ` of the device. In **Android** we add a ` use-permission ` tag in ` AndroidManifesy.xml `.
    ```js
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
    ```
    
    2. Now we can use ` navigator ` to get the location. This seems strange because ` navigator ` is an object that facilitates transition between screens in a ` React Native ` app, but this is how ` React Native ` provides a way to get location. ` navigator ` has  an attribute named ` geolocation ` that has a method named ` getCurrentPosition `. ` getCurrentPosition ` accepts 2 callbacks:
        * on success callback where you get a ` position ` object that has ` latitude ` and ` longitude ` of the current location.
        * on failure callback where you get an ` error `.  
    * We need to define a ` function ` to be ` binded ` with ` Locate Me ` ` Button  ` in which we use ` getCurrentLocation ` method and in the on success callback we construct an object having the same structure of the ` event ` we get from ` onPress ` that has ` nativeEvent ` that indeed has ` coordinate ` object which wraps ` latiude ` and ` longitude `. We do that to reuse ` onPressHandler ` to update the ` location ` in the ` state `.
    ```js
    locateMeHandler = () => {
        navigator.geolocation.getCurrentPosition(
            position => {
                const event = {
                    nativeEvent: {
                        coordinate: {
                            ...position
                        }
                    }
                };

                this.onPressHandler( event );
            },
            error => {
                console.log( "Error occurred on getting current location due to:", error );
                alert( "Failed to get your location. Please, specify it manually" );
            }
        )
    }   
    ```

    3. ` bind ` this ` function ` with ` Locate Me ` ` Button `.
    ```js
    <DefaultButton 
        title = "Locate Me" 
        onPress = { this.locateMeHandler }  
    />
    ```


## Storing the Picked Location with Redux
* Now we successfuly can take the location from the user. We need to keep track of this location. Whenever the user submits a new place we need to store the location of that place.
* What we will do is: updating the ` Action ` ` AddPlace ` to store not only the place name but also its location and updating both ` AddPlaceScreen ` and ` AddPlace ` ` Component ` accordingly.
    1. We will submit not only the place name but also its location so we can not submit it from ` AddPlace ` ` Component ` because there we know nothing about the location. We will store every thing related to the place in ` AddPlaceScreen ` and submit from there. For that we will turn ` AddPlace ` ` Componenet ` to a functional ` Componenet ` and update tha ` state ` of ` AddPlaceScreen ` on changing the text. We will accept 2 ` props ` from ` AddPlaceScreen `: the placeName from the ` state ` and a handler to update this ` state ` with the newly typed place name.
    ```js
    import React from 'react';
    import { View, StyleSheet } from 'react-native';
    import DefaultInput from './UI/DefaultInput';


    const AddPlace = props => (
        <View style={ styles.container }>
        <View style = { styles.inputContainer }>
            <DefaultInput 
                placeholder = "Enter place name"
                value={ props.placeName }
                onChangeText={ props.changeTextHandler }  
            />
        </View>
        </View>
    );


    const styles = StyleSheet.create( {
    container: {
        width: "100%",
        alignItems: "center"
    },
    inputContainer: {
        width: "80%"
    }
    } );

    export default AddPlace;  
    ```
    
    2. In ` AddPlaceScreen ` we will define ` state ` to hold everything about the place to be added. We will also define 2 handlers: one for updating place name and that will be passed to ` AddPlace ` ` Component ` and the other for updating place location and that will be passed to ` AddLocation ` ` Component `.
        1. Add ` state `.
        ```js
        constructor( props ) {
            super( props );
            props.navigator.setOnNavigatorEvent( onNavigatorEvent.bind( this ) );
            this.state = {
                controls: {
                    placeName: "",
                    location: null
                }
            }
        }
        ```

        2. Define 2 handlers.
        ```js
        changeTextHandler = typedPlaceName => {
            this.setState( prevState => {
                return {
                    controls: {
                        ...prevState.controls,
                        placeName: typedPlaceName
                    }
                }
            } );
        }

        updateLocationHandler = location => {
            this.setState( prevState => {
                return {
                    controls: {
                        ...prevState.controls,
                        location: location
                    }
                }
            } );
        }
        ```

        3. ` bind ` those handlers with their ` components `.
        ```js
        <AddLocationComponent 
          updateLocationHandler = { this.updateLocationHandler }
        />
        
        <AddPLaceComponent 
          placeName = { this.state.controls.placeName }
          changeTextHandler = { this.changeTextHandler }
        />
        ```

    3. In ` AddLocation ` ` Component ` we need to call the handler we got from ` AddPlaceScreen ` whenever the user updates the location.
    ```js
    onPressHandler = event => {
        const coordinates = event.nativeEvent.coordinate;

        this.mapView.animateToRegion( {
            ...this.state.focusedRegion,
            ...coordinates
        } );

        this.setState( prevState => {
            return {
                focusedRegion: {
                    ...prevState.focusedRegion,
                    ...coordinates
                },
                chosenRegion: true
            }
        } );

        this.props.updateLocationHandler( {
            ...coordinates
        } )
    };  
    ```

    4. We need to update the ` AddPlace ` ` Action ` to accept the place name as well as the place location.
        1. Update ` AddPlace ` ` Action ` invokation in ` AddPlaceScreen `.
        ```js
        const mapDispatchToProps = ( dispatch ) => {
            return {
                onAddPlace: ( newPlaceName, newPlaceLocation ) => dispatch( addPlace( newPlaceName, newPlaceLocation ) )
            };
        }; 
        ```

        ```js
        addPlaceHandler = () => {
            this.props.onAddPlace( 
                this.state.controls.placeName, 
                this.state.controls.location 
            );
        }
        ```

        2. Update the ` AddPlace ` ` Action ` in ` src/redux/actions/places.js `.
        ```js
        export const addPlace = ( placeName, placeLocation ) => {
            return {
                type: ADD_PLACE,
                payload: {
                    placeName,
                    placeLocation
                }
            };
        }; 
        ```

        3. Update the corresponding reducer in ` src/redux/reducers/places `.
        ```js
        const placesReducer = (state = initialState, action) => {
            var coppiedState = {
                ...state,
                places: [...state.places]
            }
            
            switch ( action.type ) {
                case ADD_PLACE :
                    coppiedState.places.push( {
                        key: String( Math.random() ),
                        name: action.payload.placeName,
                        image: ImageSource,
                        location: action.payload.placeLocation
                    } );
                    break;
                case DELETE_PLACE :
                    coppiedState.places = coppiedState.places.filter( place => place.key != action.payload );
                    break;
            }

            return coppiedState;
        }; 
        ```

    5. Finally disable the ` submit ` ` Button ` whenever either the place name or place location does not exist.
    ```js
    <DefaultButton 
        title = "submit"
        disabled = {
            this.state.controls.placeName === "" ||
            this.state.controls.location == null
        }
        onPress={ this.addPlaceHandler }  
    />
    ```


## Time to Practice Maps
* Now we have for each place its location so we need to display that location in ` PlaceDetailsScreen `.
```js
import React, { Component } from 'react';
import { View, ScrollView, Text, Image, TouchableOpacity, StyleSheet, Platform, Dimensions } from 'react-native';
import Icon from 'react-native-vector-icons/Ionicons';
import { connect } from 'react-redux';
import { deletePlace } from '../redux/actions/index';
import MapView from 'react-native-maps';

class PlaceDetailsScreen extends Component {
  removeItemHandler = ( key ) => {
    this.props.onRemoveItem( key );
  }
  
  render() {
    const region = {
      ...this.props.selectedPlace.location,
      latitudeDelta: 0.0122,
      longitudeDelta: (Dimensions.get("window").width / Dimensions.get("window").height) * 0.0122
    }

    return(
      <ScrollView contentContainerStyle={ styles.container }>
        <Image
          source={ this.props.selectedPlace.image }
          style={ styles.place_image }
        />
        <MapView
          style = { styles.place_image }
          initialRegion = { region }
        >
          <MapView.Marker 
            coordinate = { region }
          />
        </MapView>
        <Text style={ styles.place_name }>{ this.props.selectedPlace.name }</Text>
        <TouchableOpacity 
          onPress={ () => {
            this.removeItemHandler( this.props.selectedPlace.key )
            this.props.navigator.pop();
          }
        }>
          <Icon 
            name={Platform.OS === "android"? "md-trash": "ios-trash"}
            size={ 30 }
            color="red"
          />
        </TouchableOpacity>
      </ScrollView>
    )
  }
}

const styles = StyleSheet.create( {
    container: {
      alignItems: "center",
    },
    place_image: {
      width: "80%",
      height: 250,
      margin: 16
    },
    place_name: {
      fontWeight: "bold",
      fontSize: 28,
      marginBottom: 16
    }
  }
);

const mapDispatchToProps = ( dispatch ) => {
  return {
      onRemoveItem: ( key ) => dispatch( deletePlace( key ) )
  };
};

export default connect( null, mapDispatchToProps )(PlaceDetailsScreen);
```


## Installing react-native-image-picker
* Now we need to let the user either pick an image from his gallery or take a photo using his camera for the place he/she is sharing.
* For that we need a third-paty library. ` react-native-image-picker ` can do that for us.
* Let's install ` react-native-image-picker ` by hitting ` npm install --save react-native-image-picker `.
* Since ` react-native-image-picker ` package targets a *native* feature, it needs to be installed in **Android Studio** and **XCode**. To do that we follow the installation process in their official [repository](https://github.com/react-native-community/react-native-image-picker) and do not forget to add the permissions needed.
* On following the installation guide and reinstalling the app, I got this error ` Could not determine java version from '10.0.2' ` so I neglected the steps that downgrade gradle and it is then installed successfully.


## Using the Image Picker
* Now after installing ` react-native-image-picker `, let's use it to let the user pick an image for the place to be shared.
* ` react-native-image-picker ` ` exports ` by ` default ` an object that has a method named ` showImagePicker ` which, as the name suggests, shows a dialog for the user to pick an image. ` showImagePicker ` accepts:
    * a configuration object. In our case we only set the ` title ` property there to give the user a hint about what he/she is going to do.
    * a callback that should expect to get a ` response ` object. This ` response ` could have an ` error ` on failure or a path or ` uri ` to the image picked on success. ` response ` object has a method named ` didCancel ` which reflects whether the user canceled picking image or not.
* Let's employ this work in ` PickImage ` ` Component `.
```js
import React, { Component } from 'react';
import { View, Image, StyleSheet } from 'react-native';
import DefaultButton from './UI/DefaultButton';
import ImagePicker from 'react-native-image-picker';

class PickImage extends Component {
    state = {
        pickedImage: null
    }

    imagePickerHandler = () => {
        ImagePicker.showImagePicker(
            {
                title: "Pick image for your place"
            },
            response => {
                if ( response.didCancel ) {
                    console.log( "User cancelled the dialog" );
                }
                else if ( response.error ) {
                    alert( "Failed to pick an image" );
                }
                else {
                    this.setState( {
                        pickedImage: {
                            uri: response.uri
                        }
                    } );
                }
            }
        );
    }
    
    render() {
        return (
            <View style = { styles.container }>
                <DefaultButton 
                  title="Pick Image"
                  onPress = { this.imagePickerHandler }  
                />
                <Image
                  style = { styles.image }
                  source = { this.state.pickedImage }
                />
            </View>
        ); 
    }
}

const styles = StyleSheet.create({
    container: {
        width: "100%",
        alignItems: "center"
    },
    image: {
        width: "80%",
        height: 150,
        borderColor: "#bbb",
        borderWidth: 1,
        margin: 8
    }
});

export default PickImage;
```
* Note that:
    * the name ` ImagePicker ` is up to you since it is a ` default ` ` exxport `.
    * We assigned the ` pickedPlace ` an object wrapping ` uri ` property because that's what ` source ` ` prop ` on ` Image ` ` Component ` expects us to pass.


## Storing the Picked Images
* Now we need to keep track of the image in the place object in places array.
    1. In ` AddPlaceScreen.js `.
        1. Add ` image ` to ` state.controls `.
        ```js
        constructor( props ) {
            super( props );
            props.navigator.setOnNavigatorEvent( onNavigatorEvent.bind( this ) );
            this.state = {
                controls: {
                    placeName: "",
                    location: null,
                    image: null
                }
            }
        }
        ```
        2. Provide a handler to update this ` image ` attribute. 
        ```js
        updateImageHandler = image => {
            this.setState( prevState => {
                return {
                    controls: {
                        ...prevState.controls,
                        image
                    }
                };
            } )
        };
        ```
        3. ` bind ` this handler with ` PickImage ` ` Component `.
        ```js
        <PickImageComponent 
            updateImageHandler = { this.updateImageHandler }
        />
        ```
        4. pass the ` image ` in ` AddPlace ` ` Action `.
        ```js
        const mapDispatchToProps = ( dispatch ) => {
            return {
                onAddPlace: ( newPlaceName, newPlaceLocation, newImage ) => dispatch( addPlace( newPlaceName, newPlaceLocation, newImage ) )
            };
        };
        ```
        ```js
        addPlaceHandler = () => {
            this.props.onAddPlace( 
                this.state.controls.placeName, 
                this.state.controls.location,
                this.state.controls.image 
            );
        }
        ```
    2. In ` PickImage.js ` invoke ` updateImageHandler ` this handler on updating image.
    ```js
    imagePickerHandler = () => {
        ImagePicker.showImagePicker(
            {
                title: "Pick image for your place"
            },
            response => {
                if ( response.didCancel ) {
                    console.log( "User cancelled the dialog" );
                }
                else if ( response.error ) {
                    alert( "Failed to pick an image" );
                }
                else {
                    this.setState( {
                        pickedImage: {
                            uri: response.uri
                        }
                    } );
                    this.props.updateImageHandler( response.uri )
                }
            }
        );
    }
    ```
    3. In ` redux/actions/places ` update the ` AddPlace ` ` Action ` to pass the ` image ` of the place.
    ```js
    export const addPlace = ( placeName, placeLocation, placeImage ) => {
        return {
            type: ADD_PLACE,
            payload: {
                placeName,
                placeLocation,
                placeImage
            }
        };
    };
    ```
    4. In ` redux/reducers/places ` update the ` reducer ` to use the given image instead of the hardcoded one.
    ```js
    import { ADD_PLACE, DELETE_PLACE } from '../actions/ActionTypes';

    const initialState = {
        places: []
    };

    const placesReducer = (state = initialState, action) => {
        var coppiedState = {
            ...state,
            places: [...state.places]
        }
        
        switch ( action.type ) {
            case ADD_PLACE :
                coppiedState.places.push( {
                    key: String( Math.random() ),
                    name: action.payload.placeName,
                    image: {
                        uri: action.payload.placeImage
                    },
                    location: action.payload.placeLocation
                } );
                break;
            case DELETE_PLACE :
                coppiedState.places = coppiedState.places.filter( place => place.key != action.payload );
                break;
        }

        return coppiedState;
    };

    export default placesReducer;
    ```


## Image Picker and the Data It returns
* ` react-native-image-picker ` not only provides us with the ` uri ` of the image picked, but also with a ` base64 ` representation of the image. This is a string representation of the image which is very useful when pushing images to a server. This ` base64 ` representation is in the ` data ` attribute of ` response ` object. So let's pass this representation to be used in the future when working on netwoking. In ` PickImage.js `.
```js
imagePickerHandler = () => {
    ImagePicker.showImagePicker(
        {
            title: "Pick image for your place"
        },
        response => {
            if ( response.didCancel ) {
                console.log( "User cancelled the dialog" );
            }
            else if ( response.error ) {
                alert( "Failed to pick an image" );
            }
            else {
                this.setState( {
                    pickedImage: {
                        uri: response.uri,
                        base64: response.data
                    }
                } );
                this.props.updateImageHandler( response.uri )
            }
        }
    );
}
```
* Note that we can disable this feature if we do not want to use. Disabling this feature improves performance since it takes a little time to convert the image ro this ` base64 ` representation. We can disable it in the configuration object passed to ` showImagePicker ` method by setting ` noData ` attribute to ` false `.
```js
ImagePicker.showImagePicker(
    {
        title: "Pick image for your place",
        noData: true
    },response => {
        //        
    }
);
```