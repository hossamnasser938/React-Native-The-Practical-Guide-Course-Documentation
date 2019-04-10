# Navigation in React Native Apps

## Module Introduction
* In this module we're looking at one of the core concepts of mobile app development. It's **navigation** or simply how to move from A to B in a mobile app.
* **Navigation** in the web is done using **URLs**. However, this is not the case in mobile apps.
* In **Androd** We have ` Activity ` which is a Screen or a bunch of screens bundled to serve related purposes. We can move from any ` Activity ` to another in an Android app. Ofcourse **IOS** has its own solutions to provide **navigation**.
* In ` React Native ` we have different solutions for **navigation**. In this module we will look at these different solutions and cover in details one of them. We will also see how to pass data around. As well as we will see how to use ` Redux ` besides this **navigation** solution.


## Navigation in Web Apps VS Native Apps
* In web apps we have **URLs** that can be used to display different ` React ` ` components ` based on where the user in.
* In mobile apps we do not have **URLs**. Instead we can have:
    * **Stack-based**  
    in which the user goes from one page to another by hitting a button and can go back by hitting the back button in a **stack** way.  
    * **Tab-based**  
    in which the user have multile tabs within the same screen and can navigate between them by selecting different tabs.
* Whatever we use **stack-based** or **tab-based**, we still need to ` render ` a ` React ` ` Component ` there.


## Exploring Native Navigation Solutions
* In ` React Native ` we have differen solutions for **navigation**:
    * There is built-in ` components ` that work on **IOS** only. Ofcourse we need a cross-platform solution. The following are some cross-platform solutions.
    * ` React Navigation `  
    A **JavaScript** library that provides a simple way to navigate in ` React Native ` apps. Since it is a **JavaScript** library, this meas that it does not provide *native* **navigation**. It is still a good and easy solution. However, the instructor has a negative feedback on it(It is still buggy). It also has performance issues since it is not *native*.
    * ` Native Navigation `    
    a *native* solution.
    * ` React Native Navigation `  
    a *native* solution developed by **Wix**. This is the library that we will use through the course.
* ` React Native Navigation `  
    * If we head to their Github [repo](https://github.com/wix/react-native-navigation), we find that the last released version is v2. However, the instructor recorded the course before v2 was released. We will follow along with v1 with the instructor.
    * To get the documentation of v1, we change the ` master ` branch to ` v1 ` branch and then open ` docs ` folder. There you find the documentation you need to follow along.


## Adding React Native Navigation in Android
* To install ` React Native Navigation ` we can hit ` npm install --save react-native-navigation `. However, this will installl the last version which is v2. We need to install v1 so we hit ` npm install --save react-native-navigation@1 `.
* To complete the installation process on **Android**, we open the file ` installation-android.md ` from the ` docs ` folder and follow the instructions there. After that the app won't run unless we fix the issues listed in the course attached file named ` 5.2 rnn-installation-fixes-android.pdf.pdf `.
* Now the app should work but it will be empty. Nothing will be presented. This is because we wrap our app with ` Provider ` ` Component ` which is necessary to add ` redux ` to our app, However when using ` Reacy Native Navigation ` we have to do that in a different way. We can find how to start employing ` React Native Navigation ` in the file ` usage.md ` in ` docs ` folder.


## Finishing the Library Setup
* If the app still does not work, probably you need to create two new files ` index.android.js ` and ` index.ios.js ` and you populate them with what inside ` index.js ` file. Those files might be required by ` React Native Navigation ` and it makes sense if you want to define two different entry point on **Android** and **IOS**.


## Register and Render a Screen
* Now let's see how to use ` React Native Navigation `.
* In a ` React ` web app we have categorized our ` Components ` into 2 categories:
    * ` Containers `  
    These were the stateful ` components ` that wrap ` components `. They are also the ` components ` that are loaded using ` Router `.
    * ` Components `  
    These were the stateless ` components ` that are rendered inside ` Containers `.

* In a ` React Native ` mobile app we have a very similar partition in which we divide our ` components ` into:
    * ` Screens `  
    These are the ` components ` that are loaded by ` React Native Navigation `.
    * ` Components `  
    These are the ` components ` that are rendered inside ` Screens `.

* In ` src ` folder we create a new folder named ` screens ` to be a sibling to ` components ` folder. Under ` src/screens ` folder we create our first ` screen ` named ` Auth.js `(To hold authentication process in the future). For now we will keep it simple and ` render ` just a ` Text ` there.
```js
import React, { Component } from 'react';
import { View, Text } from 'react-native';

class Auth extends Component {
    render() {
        return(
            <View>
              <Text>Auth screen</Text>
            </View>
        );
    }
}

export default Auth;
```

* To begin with ` React Native Navigation ` we have to understand 2 things:
    * For any ` screen ` that will be loaded by ` React Native Navigation ` it must be ` registered ` in advance and we will see how to do that.
    * To ` render ` a ` screen ` we need to **start an app**. Starting an app is nothing but telling ` React Native Navigation ` what kind of navigation it will use to ` render ` the upcoming ` screen ` whether ` Stack-based `, ` tab-based ` or whatever. 

* Now let's do these two steps in our awesome_places app. We head to ` App.js ` and remove all the stuff there.
    1. We ` register ` our ` Auth ` ` screen `.
        ```js
        import { Navigation } from 'react-native-navigation';
        import AuthScreen from 'src/screens/Auth'; 

        Navigation.registerComponent("awesome_places.AuthScreen", () => Auth);
        ```
        Note that we ` imported ` ` Navigation ` from ` React Native Navigation ` that we use to ` register ` ` screens ` as well as starting app. ` registerComponent ` method accepts at least: 
        * a unique identifier for the ` screen ` to be used in navigation.
        * a ` function ` that ` returns ` the ` screen ` to be ` rendered ` in navigation.

    2. We start an app. To start a **stack-based** app we call ` startSigleScreenApp ` on ` Navigation `. To start a **tab-based** one we call ` startTabBasedApp ` .
        ```js
        Navigation.startSingleScreenApp({
            screen: {
                screen: "awesome_places.AuthScreen",
                title: "Login"
            }
        });
        ```
        ` startSingleScreenApp ` method accepts an object which can have multiple properties. The one mandatory there is the ` screen ` property which is assigned an object with at least ` screen ` property which is the unique identifier of the ` screen `. We add ` title ` property which is a string to be displayed in the nav bar.

* For all that to work we have to head to ` index.js ` or ` index.android.js ` and ` index.ios.js ` if they exist and just replace all the stuff there with a single line of code.
```js
import App from './App';
```
Note that we did not ` export ` any thing in ` App.js ` so, I guess, importing ` App ` in ` index.js ` will execute the script there in which we start an app.


## Adding a Tabs Screen(Tabs Navigation)
* We took a look at how to navigate using **stack-based** style. Let's have a look at how to navigate in a **tab-based** style.
* For that purpose let's create a folder under ` src/screens ` named ` main_tabs `. In this folder we're gonna create 3 files: 2 for 2 tabs and the 3rd to combine those 2 tabs.
    1. Create a file named ` AddPlace.js `
    ```js
    import React, { Component } from 'react';
    import { View, Text } from 'react-native';

    class AddPlace extends Component {
        render() {
            return(
                <View>
                    <Text>Add Place Tab</Text>
                </View>
            );
        }
    }

    export default FindPlace;
    ```
    
    2. Create a file named ` FindPlace.js `
    ```js
    import React, { Component } from 'react';
    import { View, Text } from 'react-native';

    class FindPlace extends Component {
        render() {
            return(
                <View>
                    <Text>Find Place Tab</Text>
                </View>
            );
        }
    }

    export default FindPlace;
    ```
    Those two files are the two tabs. Now let's create a file that holds a method which we can invoke to start a **tab-based** app holding those 2 tabs.
    
    3. Create a file named ` createMainTabs.js `
    ```js
    import { Navigation } from 'react-native-navigation';

    const createMainTabs = () => {
        Navigation.startTabBasedApp( {
            tabs: [
                {
                    screen: "awesome_place.FindPlaceTab",
                    label: "Find Place",
                    title: "Find Place"
                }, 
                {
                    screen: "awesome_place.AddPlaceTab",
                    label: "Find Place",
                    title: "Find Place"
                }
            ]
        } );
    }

    export default createMainTabs;
    ```
    ` startTabBasedApp ` method accepts an object of many properties. The property that matters now is the ` tabs ` property which is assigned an ` array ` of tabs objects. Each tab object contains at least one property named ` screen ` which is assigned a unique name of a ` screen ` to be loaded on selecting this tab. ` label ` property is the name displayed on the tab ` Button ` used to select the tab and ` title ` is the name displayed on the nav bar when the tab is selected.   
    
    4. Note that we must ` register ` ` AddPlace ` and ` FindPlace ` ` screen ` in ` App.js ` to be able to load them using ` React Native Navigation `.
    ```js
    import { Navigation } from 'react-native-navigation';
    import Auth from './src/screens/Auth';
    import AddPlace from './src/screens/main_tabs/AddPlace';
    import FindPlace from './src/screens/main_tabs/FindPlace';

    Navigation.registerComponent("awesome_place.AuthScreen", () => Auth);
    Navigation.registerComponent("awesome_place.FindPlaceTab", () => FindPlace);
    Navigation.registerComponent("awesome_place.AddPlaceTab", () => AddPlace);

    Navigation.startSingleScreenApp({
    screen: {
        screen: "awesome_place.AuthScreen",
        title: "Login"
    }
    });
    ```
    5. In ` Auth ` ` screen ` let's add a ` Button ` that fakes a successful ` login `.
    ```js
    import React, { Component } from 'react';
    import { View, Text, Button, StyleSheet } from 'react-native';
    import createMainTabs from './main_tabs/createMainTabs';

    class Auth extends Component {
        loginHandler() {
            createMainTabs()
        }

        render() {
            return(
                <View style={ styles.container }>
                    <Text>Auth Screen</Text>
                    <Button 
                    title="Login"
                    onPress={ this.loginHandler }
                    />
                </View>
            )
        }
    }

    const styles = StyleSheet.create({
        container: {
            flex: 1,
            justifyContent: "center",
            alignItems: "center"
        }
    })

    export default Auth; 
    ```
    Note that by hitting ` Login ` ` Button ` we call ` createMainTabs ` ` function ` which starts a **tab-based** app.


## Adding Icons to Tabs
* **Android** requires **icons** for tabs. It is a mandatory requirement for **Android**. So let's add icons to our tabs.
* In ` createMainTabs.js ` file we have an object for each tab:
```js
{
    screen: "awesome_place.FindPlaceTab",
    label: "Find Place",
    title: "Find Place"
}
```
Fortunately, this object has a property named ` icon ` that we can use to add an ` icon ` to a tab. Fortunately too, we have setup the ` react-native-vector-icons ` library that we can use to fetch icons. The bad thing is that ` icon ` property can not be assigned a ` Component ` like this ` <Icon /> `. Instead, if we look at the official documentation we see the way we can assign this property:
```js
icon: require('../img/one.png'), 
```
This way is used if we have an ` icon ` locally. However, we need to fetch it from ` react-native-icons`. Fortunately, ` react-native-vector-icons ` has a method that can solve this issue: ` getImageSource `. To use ` getImageSource `, we ` import ` first the ` Ionicons ` or whatever icons you use from ` react-native-vector-icons `.
```js
import Icon from 'react-native-vector-icons/Ionicons';
```
and then we can use ` getImageSource `.
```js
icon: Icon.getmageSource(""),
```
However, this also would not work. The reason is that ` getImageSource ` is an **asynchronous** ` function ` that returns a ` Promise ` and a ` Promise ` can not be assigned to ` icon ` property. The solution is to let this ` Promise ` resolves and then invoke the ` function ` ` createMainTabs `. However, a new problem appears. We need to fetch 2 ` icons ` for 2 tabs. Fortunately, ` Promise ` object has a method named ` all ` that lets you pass multple ` Promises ` and provide a callback to be executed when all passed ` Promises ` are resolved. In ` createMainTabs `.
```js
import { Navigation } from 'react-native-navigation';
import Icon from 'react-native-vector-icons/Ionicons';

const createMainTabs = () => {
    Promise.all( [
        Icon.getImageSource("md-search", 30),
        Icon.getImageSource("md-add", 30)
    ] ).then( sources => {
        Navigation.startTabBasedApp( {
            tabs: [
                {
                    screen: "awesome_place.FindPlaceTab",
                    label: "Find Place",
                    title: "Find Place",
                    icon: sources[0]
                }, 
                {
                    screen: "awesome_place.AddPlaceTab",
                    label: "Add Place",
                    title: "Add Place",
                    icon: sources[1]
                }
            ]
        } );
    });
}

export default createMainTabs;
```

* After all of that work I got an error saying ` RNVectorIcons module is not available ` So I returned to do the setup of ` react-native-vector-icons ` manually instead of the recommended way(With Gradle).


## Connecting Screens to Redux
* Till now we managed to navigate around but did not add the functionality of our app: adding place, and finding place. To do that we will need to pass data between ` screens `. This is possible in ` React Native Navigation ` but not as good as we wish.
* The good thing is that we still can use ` Redux `. So let's ` subscribe ` our ` screens ` to the ` store `. To do that all we need to do is to pass the app ` store ` as a 3rd argument and the ` Provider ` ` component ` as 4th argument to ` registerComponent ` method in ` App.js `. We do that with ` screens ` that need ` Redux ` to work.
    1. ` import ` ` Provider ` ` Component `.
    ```js
    import { Provider } from 'react-redux';
    ```
    2. ` import ` ` configStore ` ` function ` and create ` store `.
    ```js
    import configureStore from './src/redux/configStore';

    const store = configureStore();
    ```
    3. pass ` store ` and ` Provider ` to ` register ` ` Component `.
    ```js
    Navigation.registerComponent("awesome_place.AuthScreen", () => Auth);

    Navigation.registerComponent("awesome_place.FindPlaceTab", 
        () => FindPlace, 
        store, 
        Provider);

    Navigation.registerComponent("awesome_place.AddPlaceTab", 
        () => AddPlace, 
        store, 
        Provider);
    ```
    Note that for now we do not need to ` register ` ` Auth ` ` Screen ` to ` Redux ` ` store `. After that we can ` connect ` our ` screens ` exactly as we did before by defining ` mapStateToProps ` and ` mapDispatchToProps ` and use ` connect ` ` function ` to ` export ` ` screens `.


## Updating Redux
* Now let's add the functionality of adding place, finding place, and displaying a place.
* In displaying a place we were using a ` Modal `. Now we will use a normal ` screen ` for that so we won't need the ` selectPlace ` and ` deselectPlace ` ` actions `. So now I'm gonna remove all the related stuff in:
    * ` src/redux/reducers/Places.js `.
    * ` src/redux/actions/placesTypes.js `.
    * ` src/redux/actions/places.js `.
    * ` src/redux/actions/index.js `.

* Now let's add the add places functionality in ` AddPlace ` ` screen `. We already have the ` AddPlace ` ` Component `. So all we need to do is to:
    1. ` render ` this ` component ` in ` AddPlace ` ` screen `.
    2. ` connect ` ` AddPlace ` ` screen ` to ` Redux `.
    3. define ` addPlaceHandler `.
    4. pass ` addPlaceHandler ` to the ` AddPlace ` ` Component `[I will named all ` screens ` with ` Screen ` word in the end to avoid the ambiguity we see now.].
```js
import React, { Component } from 'react';
import { View, Text } from 'react-native';
import AddPlaceComponent from '../../components/AddPlace';
import { addPlace } from '../../redux/actions/index';
import { connect } from 'react-redux';

class AddPlaceScreen extends Component {
    addPlaceHandler = ( newPlaceName ) => {
        this.props.onAddPlace( newPlaceName );
    }

    render() {
        return(
            <View>
                <AddPlaceComponent 
                  addPlaceHandler={ this.addPlaceHandler }  
                />
            </View>
        );
    }
}

const mapDispatchToProps = ( dispatch ) => {
    return {
        onAddPlace: ( newPlaceName ) => dispatch( addPlace( newPlaceName ) )
    };
};

export default connect( null, mapDispatchToProps )( AddPlaceScreen );
```

* Now let's add find place functionality in ` FindPlaceScreen `. In ` FindPlaceScreen ` all we need to do is to:
    1. ` render ` the ` PlacesList ` ` Component ` which displays a list of all places there.
    2. ` connect ` ` FindPlaceScreen ` to ` Redux `.
    3. pass ` places ` array from ` state ` to ` PlacesList `.
    4. define ` selectItemHandler ` and pass it to ` PlacesList `[We will leave that for now].
```js
import React, { Component } from 'react';
import { View, Text } from 'react-native';
import { connect } from 'react-redux';
import PlacesList from '../../components/PlacesList'

class FindPlaceScreen extends Component {
    render() {
        return(
            <View>
                <PlacesList 
                  places = { this.props.places }
                />
            </View>
        );
    }
}

const mapStateToProps = ( state ) => {
    return {
        places: state.PlacesReducer.places
    };
} 

export default connect( mapStateToProps )( FindPlaceScreen );
```


## Pushing Pages(Navigating Forward)
* Now we want to add displaying place functionality. Before we did that using a ` Modal ` and we still could. However, we need to practice how to move from one ` screen ` to another so we will do that in a ` screen `.
    1. Convert the ` PlaceDetails ` ` Component ` into a normal ` Screen ` instead of a ` Modal ` and move it to ` src/screens ` folder.
    ```js
    import React from 'react';
    import { View, Text, Image, TouchableOpacity, StyleSheet } from 'react-native';
    import Icon from 'react-native-vector-icons/Ionicons';

    const PlaceDetails = props => (
        <View style={ styles.container }>
            <Image
            source={ props.selectedPlace.image }
            style={ styles.place_image }
            />
            <Text style={ styles.place_name }>{ props.selectedPlace.name }</Text>
            <TouchableOpacity 
            onPress={ () => {
                props.removeItemHandler( props.selectedPlace.key )
            }
            }>
            <Icon 
                name="md-trash"
                size={ 30 }
                color="red"
            />
            </TouchableOpacity>
        </View>
    );

    const styles = StyleSheet.create( {
        container: {
        alignItems: "center",
        },
        place_image: {
        width: "80%",
        height: "50%",
        margin: 16
        },
        place_name: {
        fontWeight: "bold",
        fontSize: 28,
        marginBottom: 16
        }
    }
    );

    export default PlaceDetails;
    ```
    Note that we need to ` register ` this ` screen ` in ` App.js `.
    ```js
    Navigation.registerComponent(
        "awesome_places.PlaceDetailsScreen", 
        () => PlaceDetailsScreen
    );
    ```

    2. Now we need to display ` PlaceDetailsScreen ` on clicking on an item in ` PlacesList ` ` component `. Note that ` PlacesList ` gets a ` prop ` from ` FindPlaceScreen ` named ` selectItemHandler ` which is a ` function ` to be called when an item is clicked. So let's pass this ` prop ` from ` FindPlaceScreen `. Inside ` selectItemHandler ` we will open a new ` screen ` which is ` PlaceDetailsScreen ` and pass the ` selectedPlace ` as a ` prop `. Each ` screen ` loaded by ` React Native Navigation ` is passed a ` prop ` named ` navigator ` which has a bunch of methods. One of these methods is ` push ` which accepts an object of multiple attributes. The most important attribute is ` screen ` which is the unique identifier of the ` screen ` to be pushed. Another important attribute is ` title ` which is the name to be displayed on the nav bar. Another important attribute is ` passProps ` which is an object to pass ` props ` to the ` screen ` you ` push `.
    ```js
    import React, { Component } from 'react';
    import { View, Text } from 'react-native';
    import { connect } from 'react-redux';
    import PlacesList from '../../components/PlacesList'

    class FindPlaceScreen extends Component {
        selectItemHandler = ( key ) => {
            console.log( "selectItemHandler" )
            const selectedPlace = this.props.places.find( place => place.key === key );
            this.props.navigator.push({
                screen: "awesome_places.PlaceDetailsScreen",
                title: selectedPlace.name,
                passProps: {
                    selectedPlace: selectedPlace
                }
            });
        };

        render() {
            return(
                <View>
                    <PlacesList 
                    places = { this.props.places }
                    selectItemHandler = { this.selectItemHandler }
                    />
                </View>
            );
        }
    }

    const mapStateToProps = ( state ) => {
        return {
            places: state.PlacesReducer.places
        };
    } 

    export default connect( mapStateToProps )( FindPlaceScreen );
    ```


## Poping Pages(Navigating Backwards)
* Now we know how to navigate forward by pushing ` screens `. To navigate backward or to return to the previous ` screen `, it is very simple. Using ` navigator ` ` prop ` we have a method named ` pop ` which accepts an optional object and gets you to the previous ` screen `.
* Now we need to handle deleting an item when clicking the trash button. To do that let's ` register ` ` PlaceDetailsScreen ` to ` redux ` to ` dispatch ` delete ` action ` there. In ` PlaceDetailsScreen `.
```js
import React, { Component } from 'react';
import { View, Text, Image, TouchableOpacity, StyleSheet } from 'react-native';
import Icon from 'react-native-vector-icons/Ionicons';
import { connect } from 'react-redux';
import { deletePlace } from '../redux/actions/index';

class PlaceDetailsScreen extends Component {
  removeItemHandler = ( key ) => {
    this.props.onRemoveItem( key );
  }
  
  render() {
    return(
      <View style={ styles.container }>
        <Image
          source={ this.props.selectedPlace.image }
          style={ styles.place_image }
        />
        <Text style={ styles.place_name }>{ this.props.selectedPlace.name }</Text>
        <TouchableOpacity 
          onPress={ () => {
            this.removeItemHandler( this.props.selectedPlace.key )
            this.props.navigator.pop();
          }
        }>
          <Icon 
            name="md-trash"
            size={ 30 }
            color="red"
          />
        </TouchableOpacity>
      </View>
    )
  }
}

const styles = StyleSheet.create( {
    container: {
      alignItems: "center",
    },
    place_image: {
      width: "80%",
      height: "50%",
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
        
* We need to ` register ` ` PlaceDetailsScreen ` with ` redux ` in ` App.js `
```js
Navigation.registerComponent("awesome_places.PlaceDetailsScreen", 
    () => PlaceDetailsScreen, 
    store,
    Provider );
```


## More Navigator Methods
* ` navigator ` has many methods that we can use. One of them is ` popToRoot ` which accepts an optional object and gets you back to the root ` screen ` of the started application.


## Adding a Side Drawer
* Now let's add a side drawer to our app. We need to have a side drawer after login which means we will add it in the tabs app where we have ` AddPlaceScreen ` and ` FindPlaceScreen `.
* To add a side drawer, we need to create a normal ` React ` ` Component ` to hold the content of the side drawer. So let's start by creating a ` screen ` under ` src/screens ` and we will name it ` SideDrawerScreen `.
```js
import React, { Component } from 'react';
import { View, Text } from 'react-native';

const SideDrawerScreen = ( props ) => {
    return(
        <View>
            <Text>Side Drawer</Text>
        </View>
    );
}

export default SideDrawerScreen;
```
Note that we need to ` register ` this ` screen ` in ` App.js ` to use it as a side drawer.
```js
Navigation.registerComponent( "awesome_places.SideDrawerScreen", () => SideDrawerScreen );
```

* After that we can add a ` drawer ` object when starting an app. So on ` startTabBasedApp ` where we pass our tabs, we need to pass an object to the ` drawer ` attribute there. This object has two attributes ` left ` and ` right `, we use one of them to define whether the side drawer is displayed on the left or the right. In ` createMainTabs `.
```js
Navigation.startTabBasedApp( {
            tabs: [
                {
                    screen: "awesome_place.FindPlaceTab",
                    label: "Find Place",
                    title: "Find Place",
                    icon: sources[0],
                }, 
                {
                    screen: "awesome_place.AddPlaceTab",
                    label: "Add Place",
                    title: "Add Place",
                    icon: sources[1]
                }
            ],
            drawer: {
                left: {
                    screen: "awesome_places.SideDrawerScreen"
                }
            }
        } );
```

* What we did till now actually renders a side drawer for us but the user does not know that there is a side drawer there because ` React Native ` does not show a button for that by default. You have to add the button on your own. To do that We assign ` navigatorButtons ` attribute in the object we assign for ech tab in the tabs array in ` createMainTabs `. In this object we assign an array to ` leftButtons ` or ` rightButtons ` containing objects for buttons. Note that to dislay the button on both tabs we have to duplicate some code in both tabs.
```js
Promise.all( [
        Icon.getImageSource("md-search", 30),
        Icon.getImageSource("md-add", 30),
        Icon.getImageSource("md-menu", 30)
    ] ).then( sources => {
        Navigation.startTabBasedApp( {
            tabs: [
                {
                    screen: "awesome_place.FindPlaceTab",
                    label: "Find Place",
                    title: "Find Place",
                    icon: sources[0],
                    navigatorButtons: {
                        leftButtons: [
                            {
                                icon: sources[2],
                                title: "Menu"
                            }
                        ]
                    }
                }, 
                {
                    screen: "awesome_place.AddPlaceTab",
                    label: "Add Place",
                    title: "Add Place",
                    icon: sources[1],
                    navigatorButtons: {
                        leftButtons: [
                            {
                                icon: sources[2],
                                title: "Menu"
                            }
                        ]
                    }
                }
            ],
            drawer: {
                left: {
                    screen: "awesome_places.SideDrawerScreen"
                }
            }
        } );
    });
```
Note that we grabed more ` Icon ` for the button. Till now this will not work on **Android** but we will fix that later.


## Using Navigation Events & Toggling the Drawer
* Till now we added a button to our nav bar to toggle the drawer but we did not specify that this button should toggle the drawer. ` React Native ` does not know that by default so we should specify that.
* Fortunately ` React Native Navigation ` fire extremely useful ` events ` for us including:
    * Opening a ` screen `.
    * Closing a ` screen `.
    * Pressing a button on the nav bar.
    * and more.
* Fortunately as well we can listen for these events in any ` screen `. The ` navigator ` ` prop ` has a method named ` setOnNavigatorEvent ` which accepts a ` function ` to be called when an ` event ` fires. So let's head to one of the tabs either ` AddPlaceScreen ` or ` FindPlaceScreen ` and specify that:
```js
constructor( props ) {
    super( props );
    props.navigator.setOnNavigatorEvent( ( event ) => console.log( event ) );
}
```
Note that for each event we have two useful attributes: ` type ` and ` id ` which allow you to capture the ` event ` you're interested in and respond to it. On pressing the toggle buton we see an ` event ` with ` type `: ` NavBarButtonPress ` and ` id `: ` null ` so let's add an ` id ` attribute to our buttons in ` createMainTabs `.
```js
navigatorButtons: {
    leftButtons: [
        {
            icon: sources[2],
            title: "Menu",
            id: "toggleDrawerButton"
        }
    ]
}
```
Now we can detect toggle ` event ` and respond to it. Since we have a single button on both ` AddPlaceScreen ` and ` FindPlaceScreen ` and we respond to it the same way in both, it's good to define the ` function ` that responds in a separate file to reuse it. Under ` src/screen/main_tabs ` we create a new file named ` onNavigatorEvent.js `.
```js
const onNavigatorEvent = function( event ) {
    console.log( event );
    if ( event.type === "NavBarButtonPress" ) {
        if ( event.id === "toggleDrawerButton" ) {
            this.props.navigator.toggleDrawer( {
                side: "left"
            } );
        }
    }
};

export default onNavigatorEvent;
```
Note that we use ` toggleDrawer ` ` function ` on ` navigator ` ` prop ` and we pass an object wrapping ` side ` attribute which defines the drawer comes from which side and it is a must in **Android**. Now we can ` import ` ` onNavigatorEvent ` ` function ` in both ` AddPlaceScreen ` and ` FindPlaceScreen ` and register it with the ` setOnNavigatorEvent `.
```js
import onNavigatorEvent from './onNavigatorEvent';
```
```js
constructor( props ) {
    super( props );
    props.navigator.setOnNavigatorEvent( onNavigatorEvent.bind( this ) );
}
```
Note that we used a normal ` function ` style not arrow ` function ` to ` bind ` it with ` this ` to access ` props.navigator `. I tried to use arrow ` function ` and ` bind ` but it did not work.

* Now the drawer is working. However, for some purpose in **Android** we may need to specify exactly the width of the drawer ` screen ` to work so let's do that in ` SideDrawerScreen `. To set the width of a ` screen ` responsively we need to get the width of the current device ` screen ` or ` window `. To do that we ` import ` ` Dimensions ` from ` react-native ` and get the width of the ` window ` 
```js
Dimensions.ger("window").width
```
and then we can multiply that by a factor 0.7 or 0.8 or whatever and assign the result to the width of the container ` View ` of the ` SideDrawerScreen `.
```js
import React from 'react';
import { View, Text, Dimensions } from 'react-native';

const SideDrawerScreen = ( props ) => {
    return(
        <View style={ { width: Dimensions.get("window").width * 0.8 } }>
            <Text>Side Drawer</Text>
        </View>
    );
}

export default SideDrawerScreen;
```


## Finishing the Drawer
* The last thing to do is to style the drawer a little bit. 
* We can add multiple styles to an element and wrap those multiple styles in an array and assign that array to ` style ` ` prop ` of that item. This helps in this case when we want to recalculate the ` width ` of the ` window ` dynamically when the user rotates the device for example. In ` SideDrawerScreen `.
```js
import React from 'react';
import { View, Text, Dimensions, StyleSheet } from 'react-native';

const SideDrawerScreen = ( props ) => {
    return(
        <View style={[ styles.container, width: Dimensions.get("window").width * 0.8 ]}>
            <Text>Side Drawer</Text>
        </View>
    );
}

const styles = StyleSheet.create( {
    container: {
        flex: 1,
        backgroundColor: "white"
    }
} );

export default SideDrawerScreen;
```
