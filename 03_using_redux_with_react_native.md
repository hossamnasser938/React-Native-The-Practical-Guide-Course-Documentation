# Using Redux With React Native
* Since We have studied the [` ReactJS and Redux `](https://github.com/hossamnasser938/ReactJS-Basics-Course-Documentation) tutorial by **Maximillian**, We know how ` Redux ` works. Using ` Redux ` in ` React Native ` is exactly the same as using it in ` ReactJS ` . Actually the connection is done between ` ReactJS ` and ` Redux ` while ` React Native ` is standing away. 
* Before we start, we have an attribute in ` App ` ` Component ` ` state ` that should not be there. It is ` placeName ` which holds the value typed by the user in the ` TextInput ` in ` AddPlace ` ` Component `. Since this ` attribute ` is related to ` AddPlace ` ` Component ` only, we can neglect it in ` Redux ` and treat it as a local ` state ` in ` AddPlace ` ` Component `.
    1. We convert ` AddPlace ` to a class-based ` Components `.
    ```js
    export default class AddPlace extends Component {
    ```
    2. Define a ` state ` object.
    ```js
    state = {
      typedPlace: ""
    };
    ```
    3. Define a method to update this ` state ` object.
    ```js
    onChangeTextHandler = ( typedText ) => {
    this.setState( {
        typedPlace: typedText
      } );
    };
    ```
    4. Register this method with the ` TextInput ` ` onChangeText ` ` prop `.
    ```js
    <TextInput 
        style={ { width:"70%", borderColor:"black", borderWidth:1 } }
        placeholder="Enter an Awesome Place"
        placeholderTextColor="orange"
        value={ this.state.typedPlace }
        onChangeText={ this.onChangeTextHandler } 
    />
    ```
    5. Now the method ` selectItemHandler ` receives the ` typedPlace ` as an argument inside the ` AddPlace ` ` Component `.
    ```js
    <Button
        style={ { width: "30%" } }
        title="Add"
        onPress={ () => this.props.addPlaceHandler( this.state.typedPlace ) } 
    />  
    ```
* Now Let's embed ` Redux ` in our ` React Native ` application step by step.
    1. The 1st step is to install the dependencies needed which are ` redux ` and ` react-redux ` by hitting ` npm install redux react-redux --save `.

    2. The 2nd step is to create the directoies that will structure our code.
        1. We create a directory named ` redux ` under ` src ` directory to wrap everything related to ` Redux `.
        2. Under ` redux ` we create 2 directories:
            * ` reducers ` in which we define our ` reducers ` of the app.
            * ` actions ` in which we define ` actions ` creators of the app.

    3. Under ` actions ` directory we create a file named ` placesTypes ` which will hold the names of the ` actions ` related to places[Later this file will be used to hold the names of all ` actions ` not just those related to places and hence the file will be named ` ActionsTypes ` which is more appropriate]. This is only recommended to avoid any typo when using these names either in the ` reducer ` or the ` action ` creators.
    ```js
    export const ADD_PLACE = "ADD_PLACE";
    export const DELETE_PLACE = "DELETE_PLACE";
    export const SELECT_PLACE = "SELECT_PLACE";
    export const DESELECT_PLACE = "DESELECT_PLACE";
    ```

    4. Under ` reducers ` directory we create a file named ` places ` which will hold the ` reducer ` for places.
        ```js
        import { ADD_PLACE, DELETE_PLACE, SELECT_PLACE, DESELECT_PLACE } from '../actions/placesTypes';
        import ImageSource from '../../assets/beautiful-place.jpg';

        const initialState = {
            selectedPlace: null,
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
                        key: Math.random(),
                        name: action.payload,
                        image: ImageSource
                    } );
                    break;
                case DELETE_PLACE :
                    coppiedState.places = coppiedState.places.filter( place => place.key != action.payload );
                    break;
                case SELECT_PLACE :
                    coppiedState.selectedPlace = coppiedState.places.find( place => place.key == action.payload );
                    break;
                case DESELECT_PLACE :
                    coppiedState.selectedPlace = null;
                    break;
            }

            return coppiedState;
        };

        export default placesReducer;
        ```

        Note that ` Math.random() ` has 2 problems:
        * Its value is not a ` string `. We can solve that by converting the floating-point value into ` string ` using ` toString ` ` method `.
        * It does not guarantee generating unique values. We can solve that by using a package named ` uuid ` which generates unique ` string ` keys.   

    5. Under ` actions ` directory we create a file named ` places ` which will hold the ` actions ` creators for ` places ` ` reducer `.
    ```js
    import { ADD_PLACE, DELETE_PLACE, SELECT_PLACE, DESELECT_PLACE } from './placesTypes';

    export const addPlace = ( place ) => {
        return {
            type: ADD_PLACE,
            payload: place
        };
    };

    export const deletePlace = key => {
        return {
            type: DELETE_PLACE,
            payload: key
        };
    };

    export const selectPlace = key => {
        return {
            type: SELECT_PLACE,
            payload: key
        };
    };

    export const deselectPlace = () => {
        return {
            type: DESELECT_PLACE
        };
    };
    ```

    6. It is recommended when having many ` reducers ` and accordingly many ` actions ` creators to create a file that wraps all these ` actions ` creators so that whenever we need any ` action ` we ` import ` it from one place. So under ` actions ` directory we create a file named ` index.js ` that ` exports ` all ` actions ` from all ` actions ` creators in our app.
    ```js
    export { addPlace, deletePlace, selectPlace, deselectPlace } from './places';
    ```

    7. Now under ` redux ` directory we create a file named ` configStore ` that ` imports ` all ` reducers`, ` combines ` them together, and ` export ` our app ` store `. Note this name ` configStore ` is chosen because app ` store ` is configured differently depending on the platforem **Android** or **IOS** but on that soon.
    ```js
    import { createStore, combineReducers } from 'redux'; 
    import PlacesReducer from './reducers/Places';

    const rootReducer = combineReducers( { PlacesReducer } );

    const configStore = () => {
        return createStore( 
            rootReducer
        );
    }

    export default configStore;
    ```

    8. Now ` redux ` stuff is ready. Let's do the ` react-redux ` stuff. We have to ` connect ` only ` App ` ` Component ` with ` redux ` and the rest of ` components ` will be **deaf** receiving what needed from ` App ` ` Component `.
    ```js
    import React, {Component} from 'react';
    import {StyleSheet, View} from 'react-native';
    import AddPlace from './src/components/AddPlace';
    import PlacesList from './src/components/PlacesList';
    import PlaceDetails from './src/components/PlaceDetails';
    import { connect } from 'react-redux';
    import { addPlace, deletePlace, selectPlace, deselectPlace } from './src/redux/actions/index'; 

    class App extends Component {

        addPlaceHandler = ( placeName ) => {
            if( placeName.trim() !== "" ) {
                this.props.addPlace( placeName );
            }
        }

        removeItemHandler = key => {
            this.props.deletePlace( key );
        }

        selectItemHandler = ( key ) => {
            this.props.selectPlace( key );
        }

        closeModalHandler = () => {
            this.props.deselectPlace();
        }

        render() {
            return (
              <View style={styles.container}>
                <PlaceDetails
                selectedPlace={ this.props.selectedPlace }
                closeModalHandler={ this.closeModalHandler }
                removeItemHandler={ this.removeItemHandler }
                />
                <AddPlace
                addPlaceHandler={ this.addPlaceHandler }
                />
                <PlacesList
                places = { this.props.places }
                selectItemHandler = { this.selectItemHandler }
                />
              </View>
            );
        }
    }

    const styles = StyleSheet.create({
        container: {
            flex: 1,
            padding: 32,
            backgroundColor: '#fff',
            justifyContent: 'flex-start',
            alignItems: 'center'
        }
    });

    const mapStateToProps = state => {
        return {
            places: state.PlacesReducer.places,
            selectedPlace: state.PlacesReducer.selectedPlace
        };
    };

    const mapDispatchToProps = dispatch => {
        return {
            addPlace: place => dispatch( addPlace( place ) ),
            deletePlace: key => dispatch( deletePlace( key ) ),
            selectPlace: key => dispatch( selectPlace( key ) ),
            deselectPlace: () => dispatch( deselectPlace() ) 
        };
    };

    export default connect( mapStateToProps, mapDispatchToProps )(App);
    ```

    9. Now the last and also the only different thing in ` React Native ` from ` React ` when connecting to ` Redux `. We need to **register** our app with ` redux ` ` store `. We did that in ` React ` by wrapping our ` App ` ` Component ` in a ` Provider ` ` Component `. Here it is the same with a bit difference. In ` index.js `.
    ```js
    import {AppRegistry} from 'react-native';
    import App from './App';
    import {name as appName} from './app.json';

    import React from 'react';
    import configStore from './src/redux/configStore';
    import { Provider } from 'react-redux';

    const appStore = configStore();

    const reduxApp = () => {
        return (
            <Provider store={appStore}>
                <App />
            </Provider>
        );
    }

    AppRegistry.registerComponent(appName, () => reduxApp);
    ```
    The difference is that ` registerComponent ` accepts not just the ` App ` ` Component ` wrapped with ` Provider ` ` Component ` but instead it accepts a ` function ` that returns a ` function ` that returns this ` Component `.       