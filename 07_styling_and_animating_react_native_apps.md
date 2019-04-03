# Styling and Animating React Native Apps

## Module  Introduction
* In this module we will look at how to:
  * style ` react native ` apps in general.
  * style apps differently for different platforms: **Android** and **IOS** especially choosing different icons for different platforms.
  * style apps differently for different screen sizes (**responsive design**) especially responding to **orientation changes**.


## Using StyleSheet VS. Normal JS Objects
* Before moving on, you might ask yourself why we need to use ` StyleSheet ` to creat objects for our style or the question in other words: Is there a difference between:
  * using ` StyleSheet ` for defining a style object like this
  ```js
  const styles = StyleSheet.create( {
      container: {
          flex: 1
      }
  } );
  ```
  * using normal JS object like this
  ```js
  const styles = {
      container: {
          flex: 1
      }
  } );
  ```

* The answer is: both ways work. However, using ` StyleSheet ` is better for two reasons:
  * It **validates** our styles so if we used a style property in a wrong way, it informs us.
  * It **transforms** our styles into *native* code much more efficirntly.

* For these reasons we should use ` StyleSheet ` to define our styles as much as we can because we will encounter cases in which there is no way to use ` StyleSheet `.


## Vanilla CSS VS. React Native Styles
* It's important to understand that we do not use **Vanilla CSS**(CSS used in the web with no additional libraries) in ` React Native `. Instead we are emulating CSS or simply we use styles that are similar to CSS. This also does not mean that we have an alternative for evety thing in CSS. We have our own set of style properties that are similar to those of Vanilla CSS.
* Another thing to know is that not all ` components ` are styleable and not all styleable ` components ` accept the same set of styles. Each styleable ` component ` accepts a set of styles.
* In general the most styleable ` components ` are ` View ` and ` Text `. Also ` Image `, ` TextInput ` and ` ScrollView ` are styleable.
* There is a very useful style [cheet sheet](https://github.com/vhpoet/react-native-styling-cheat-sheet) for ` React Native `.


## Flexbox in Action
* Up untill now we have not developed the Authentication ` screen ` functionality neither designed it. We wish now to design it and leave its functionality to a later module.
* In Authentication ` screen ` we should have:
  * a ` Text ` that says Login.
  * a ` Button ` that switches from login to signup mode.
  * Up to 3 ` TextInput ` for email, password, and confirm password[3 in signup mode, 2 in login mode].
  * a ` Button ` for submitting.

* So let's put all of this in ` AuthScreen.js `.
```js
render() {
    return(
        <View>
            <Text>ٍPlease Log In</Text>
            <Button 
                title = "Switch to Login"
            />
            <TextInput placeholder = "E-maill"/>
            <TextInput placeholder = "Password"/>
            <TextInput placeholder = "Confirm Password"/>
            <Button 
                title = "Submit"
            />
        </View>
    );
}
```

* Now we wish to center all these ` components `. Simply set the style of the ` components ` wrapper ` View ` to an object with ` flex: 1 ` to take the full ` screen ` and then center the content in this ` View ` by setting both ` justifyContent ` and ` alignItems ` to ` "center" `.
```js
const styles = StyleSheet.create({
    container: {
        flex: 1,
        justifyContent: "center",
        alignItems: "center"
    }
});
```
Finally set the ` style ` ` prop ` of the wrapper ` View `.
```js
<View style = { styles.container }>
```


## Styling with Relative Units
* Now we have our ` TextInput `s but they take different ` width ` and sometimes the platform does not respect the ` placeholder ` so it does not appear completely. We wish to set their ` width ` property.
* We can set it to a **fixed** value like ` 300 ` which means 300 pixels. This can work on our device but for small-screen devices it can overlap the available width and for large-screen devices it can appear very small.
* A better solution is to set it to a **relative** value like ` 80% ` which means 80% of the available width and by that it adapts to small and large screen devices.
```js
const styles = StyleSheet.create({
    container: {
        flex: 1,
        justifyContent: "center",
        alignItems: "center"
    },
    input: {
        width: "80%"
    }
});
```
And now we assign this styles to all our ` TextInput `s.
```js
render() {
    return(
        <View style={ styles.container }>
            <Text>ٍPlease Log In</Text>
            <Button 
                title = "Switch to Login"
            />
            <TextInput placeholder = "E-maill" style = { styles.input }/>
            <TextInput placeholder = "Password" style = { styles.input }/>
            <TextInput placeholder = "Confirm Password" style = { styles.input }/>
            <Button 
                title = "Submit"
            />
        </View>
);
}
```
* This solved the problem. However, a better solution is to wrap all ` TextInput `s in a ` View ` and assign it a style with ` width: "80%" ` and assign each ` TextInput ` a style with ` width: "100%" `. This has the advantage of letting you define a default ` TextInput ` ` component ` with full width that can be reused all over your application and take its size from its wrapper.
```js
const styles = StyleSheet.create({
    container: {
        flex: 1,
        justifyContent: "center",
        alignItems: "center"
    },
    inputContainer: {
        width: "80%"
    },
    input: {
        width: "100%"
    }
});
```
```js
render() {
    return(
        <View style={ styles.container }>
            <Text>ٍPlease Log In</Text>
            <Button 
                title = "Switch to Login"
            />
            <View style = { styles.inputContainer }>
                <TextInput placeholder = "E-maill" style = { styles.input }/>
                <TextInput placeholder = "Password" style = { styles.input }/>
                <TextInput placeholder = "Confirm Password" style = { styles.input }/>
            </View>
            <Button 
                title = "Submit"
            />
        </View>
    );
}
```


## Global Styles with Custom Components
* Now we hope to create **prestyled reusable** ` components ` that we can use all over the application without duplicating the same style many times.
* To do that let's create a directory named ` UI ` under ` components ` where we define our own prestyled ` components `.
* Under ` UI ` let's create a file named ` DefaultInput.js ` to define a default prestyled ` TextInput `.
```js
import React from 'react';
import { TextInput, StyleSheet } from 'react-native';

const DefaultInput = props => (
    <TextInput 
      style = { styles.input }
      underlineColorAndroid = "transparent"
      { ...props }
    />
);

const styles = StyleSheet.create( {
    input: {
        width: "100%",
        borderColor: "#bbb",
        backgroundColor: "#eee",
        borderWidth: 2,
        padding: 4,
        margin: 4
    }
} );

export default DefaultInput;
```

* Notes:
  * ` underlineColorAndroid ` is a ` prop ` that we can use for **Android** only to set the color of an underline for the ` TextInput `.
  * We used the spreed syntax to let us pass ` props ` from outside ` DefaultInput `.

* Now we can ` import ` this ` DefaultInput ` in ` AuthScreen ` and use it instead of ` TextInput `. Note that now no need to include style for ` TextInput `. We have a prestyled ` Component `.
```js
import DefaultInput from '../components/UI/DefaultInput';
```
```js
<View style = {styles.inputContainer }>
    <DefaultInput placeholder = "E-maill"/>
    <DefaultInput placeholder = "Password"/>
    <DefaultInput placeholder = "Confirm Password"/>
</View>
```


## Synthetic Cascading of Styles
* Now everything is good unless if we want to **override** something from the style of our ` DefaultInput ` ` component `.

* If we applied a ` style ` ` prop ` on a ` DefaultInput ` ` Component `, we would lose all our default styles. We need a way to let us apply the default style + any styles coming from outside in a way that if we have the same property in both default and outside styles with different values, we take the outside one(**overriding** the default one).

* In ` DefaultInput.js ` we use the spread syntax and then we assign the ` style ` ` prop ` an array with our default style as the first item and the outside style as the second item[this order makes the outside style, if exists, overrides the default style]. 
```js
const DefaultInput = props => (
    <TextInput 
      underlineColorAndroid = "transparent"
      { ...props }
      style = { [styles.input, props.style] }
    />
);
```

* Now let's override the ` backgroundColor ` property in ` AuthScreen.js `.
```js
const styles = StyleSheet.create({
    container: {
        flex: 1,
        justifyContent: "center",
        alignItems: "center"
    },
    inputContainer: {
        width: "80%"
    },
    input: {
        backgroundColor: "white"
    }
});
```
```js
render() {
    return(
        <View style={ styles.container }>
            <Text>ٍPlease Log In</Text>
            <Button 
                title = "Switch to Login"
            />
            <View style = {styles.inputContainer }>
                <DefaultInput placeholder = "E-maill" style = { styles.input }/>
                <DefaultInput placeholder = "Password" style = { styles.input }/>
                <DefaultInput placeholder = "Confirm Password" style = { styles.input }/>
            </View>
            <Button 
                title = "Submit"
            />
        </View>
    );
}
```

Now we successfully get the default style except for the ` backgroundColor ` which we overriden it to ` "white" `.


## Styling Text
* Now let's make ` Please Log In ` ` Text ` take as much space as it should. In ` UI ` directory let's create a reusable ` component ` to be used for **headings**. We'll name this file ` HeadingText.js `.
```js
import React from 'react';
import { Text, StyleSheet } from 'react-native';

const HeadingText = props => (
    <Text 
      { ...props }
      style = { [styles.heading, props.style] }
    >
        { props.children }
    </Text>
);

const styles = StyleSheet.create( {
    heading: {
        fontSize: 28,
        margin: 8
    }
} );

export default HeadingText;
```

Now we can ` import ` and use this prestyled ` Component ` in ` AuthScreen.js `.
```js
import HeadingText from '../components/UI/HeadingText';
```
```js
<HeadingText>Please Log In</HeadingText>
```


## Cascading Text Styles
* Suppose that we need to choose a global font family for all ` Text `s in our applications. One way to do that is to add ` fontFamily ` property to the style of all prestyled ` Text ` ` components ` we have. However, what would be the case when we want to update that ` fontFamily `. We have to go all over these ` components ` and update them which results in a **less maintainable** application.

* We can not wrap our application by a ` View ` and set this property in this wrapper. Generally we can not set styles related to ` Text ` on a ` View ` ` component `.

* Fortunately, in ` React Native ` we can have  ` Text ` within a ` Text ` and in this case the styles will be **cascading** which means that the inner ` Text ` takes the style from the outer unless we override it in the inner.

* Based on that a better solution would be to wrap all ` Text ` ` components ` by, let's say, a ` MainText ` in which we set the style that we want to apply to all ` Text ` ` components ` out there. We still have to wrap all ` Text ` ` components ` by this ` MainText ` but now it is one place that you update to see your updates all across the app.

* In ` UI ` directory let's create ` MainText.js `.
```js
import React from 'react';
import { Text, StyleSheet } from 'react-native';

const MainText = props => (
    <Text style = { styles.mainText }>
        { props.children }
    </Text>
);

const styles = StyleSheet.create( {
    mainText: {
        fontFamily: "Roboto"
    }
} );

export default MainText;
``` 

* In ` AuthScreen.js ` we can ` import ` ` MainText ` and wrap ` HeadingText ` with it.
```js
import MainText from '../components/UI/MainText';
```
```js
<MainText>
    <HeadingText>Please Log In</HeadingText>
</MainText>
```

* This works well except for something. The ` margin ` property defined on ` HeadinText ` style now has no effect after ` HeadingText ` is wrapped with ` MainText ` . Why this? because nested ` Text ` still have little styling options. Refer to [this](https://react-native.canny.io/feature-requests/p/richer-styling-options-for-nested-text).


## Adding a Background Image
* Let's add a background image for our ` AuthScreen `. 
* Unfortunately, we do not have a property for background image that we can set on the wrapper ` View ` as we have in **Android**. But fortunately, we have a ` Component ` for that which is the ` ImageBackground `.
* ` ImageBackground ` should wrap all your content so let's try to replace the wrapper ` View ` with ` ImageBackground ` and apply to it the same style of the wrapper ` View `. In ` AuthScreen.js `.
  * Let's ` import ` our image which we located at ` src/assets ` directory.
  ```js
  import BackImage from '../assets/background.jpg';
  ```
  * Let's ` import ` ` ImageBackground ` ` Component `.
  ```js
  import { View, Button, ImageBackground, StyleSheet } from 'react-native';
  ```
  * Let's replace the wrapper ` View ` with ` ImageBackground `.
  ```js
  <ImageBackground style={ styles.container } source = { BackImage }>
  ```
  It works perfectly but do not forget to set the ` source ` ` prop `.


## Creating a Reusable Custom Button
* ` Button ` ` Component ` looks differently on different platforms **Android** and **IOS**. 
* Let's create our custom ` Button ` that looks the same on both platforms. In ` UI ` directory we create a file named ` DefaultButton.js `
```js
import React from 'react';
import { TouchableOpacity, View, Text, StyleSheet } from 'react-native';

const DefaultButton = props => (
    <TouchableOpacity onPress = { props.onPress }>
        <View style = { [styles.container, props.style] }>
            <Text style = { { color: props.textColor || "white" } }>
                { props.title }
            </Text>
        </View>
    </TouchableOpacity>
);

const styles = StyleSheet.create( {
    container: {
        margin: 4,
        padding: 4,
        backgroundColor: "grey",
        borderWidth: 1,
        borderRadius: 3,
        borderColor: "black"
    }
} );

export default DefaultButton;
```
Let's use our custom ` DefaultButton ` in ` AuthScreen `.
  * ` import ` the ` DefaultButton ` ` Component `
  ```js
  import DefaultButton from '../components/UI/DefaultButton';
  ```
  * Use it instead of ` Button `.
  ```js
  render() {
        return(
            <ImageBackground style={ styles.container } source = { BackImage }>
                <MainText>
                    <HeadingText>Please Log In</HeadingText>
                </MainText>
                <DefaultButton 
                  title = "Switch to Login"
                />
                <View style = {styles.inputContainer }>
                    <DefaultInput placeholder = "E-maill" style = { styles.input }/>
                    <DefaultInput placeholder = "Password" style = { styles.input }/>
                    <DefaultInput placeholder = "Confirm Password" style = { styles.input }/>
                </View>
                <DefaultButton
                  title = "Submit"
                />
            </ImageBackground>
        );
    }
  ```


## Editing the Share Place Screen
* Up until now in ` AddPlaceScreen ` we have a ` TextInput ` for the name of the place to be added and a ` Button ` next to it to add the place.
* Later in the course we will let the user pick an image for the place and specify its position on the maps. So let's design our ` AddPlaceScreen ` to have those items in a vertical order:
  * Header that says "Share place with us".
  * a ` Button ` to pick image.
  * Placeholder to show place image.
  * a ` Button ` to choose the current location. 
  * placeholder for map spot.
  * ` TextInput ` for the place name.
  * a submit ` Button `.

* Let's start in ` AddPlaceScreen.js `.
```js
import React, { Component } from 'react';
import { View, ScrollView, Text, StyleSheet } from 'react-native';
import HeadingText from '../../components/UI/HeadingText';
import MainText from '../../components/UI/MainText';
import DefaultInput from '../../components/UI/DefaultInput';
import DefaultButton from '../../components/UI/DefaultButton';
import { addPlace } from '../../redux/actions/index';
import { connect } from 'react-redux';
import onNavigatorEvent from './onNavigatorEvent';

class AddPlaceScreen extends Component {
    constructor( props ) {
        super( props );
        props.navigator.setOnNavigatorEvent( onNavigatorEvent.bind( this ) );
    }

    addPlaceHandler = ( newPlaceName ) => {
        this.props.onAddPlace( newPlaceName );
    }

    render() {
        return(
            <ScrollView>
                <View style = { styles.container }>
                    <MainText>
                        <HeadingText>Share Place with Us</HeadingText>
                    </MainText>
                    <DefaultButton title = "Pick Image"/>
                    <View style = { styles.placeholderContainer }>
                        <Text>Image Placeholder</Text>
                    </View>
                    <DefaultButton title = "Locate Me"/>
                    <View style = { styles.placeholderContainer }>
                        <Text>Map Placeholder</Text>
                    </View>
                    <View style = { styles.inputContainer }>
                        <DefaultInput placeholder = "Enter place name"/>
                    </View>
                    <DefaultButton title = "submit"/>
                </View>
            </ScrollView>
        );
    }
}

const styles = StyleSheet.create( {
    container: {
        flex: 1,
        alignItems: "center"
    },
    placeholderContainer: {
        width: "80%",
        height: 150,
        borderColor: "#bbb",
        borderWidth: 1,
        margin: 8
    },
    inputContainer: {
        width: "80%"
    }
} );

const mapDispatchToProps = ( dispatch ) => {
    return {
        onAddPlace: ( newPlaceName ) => dispatch( addPlace( newPlaceName ) )
    };
};

export default connect( null, mapDispatchToProps )( AddPlaceScreen );
```
Note that assigning styles for the ` ScrollView ` is done through ` contentContainerStyle ` ` prop ` not ` style ` ` prop `. However, in **Android** it does not work. A possible solution is to wrap all your content in a ` View ` inside the ` ScrollView ` and assign the style to the wrapper ` View `.


## Styling & Splitting the Share Place Screen
* The ` AddPlaceScreen ` got extremely big. Let's try to decompose it into smaller ` components `.
* We start by separating the place name ` TextInput ` and the submit ` Button ` into a ` Component `. We already have a ` Component ` named ` AddPLace ` in which we previously had the ` TextInput ` and the ` Button ` in a horizontal way. Now lets add them in a vertical way.
```js
import React, { Component } from 'react';
import { View, StyleSheet } from 'react-native';
import DefaultInput from './UI/DefaultInput';
import DefaultButton from './UI/DefaultButton';


export default class AddPlace extends Component {
  state = {
    typedPlace: ""
  };

  onChangeTextHandler = ( typedText ) => {
    this.setState( {
      typedPlace: typedText
    } );
  };

  render() {
    return(
      <View style={ styles.container }>
        <View style = { styles.inputContainer }>
            <DefaultInput 
              placeholder = "Enter place name"
              value={ this.state.typedPlace }
              onChangeText={ this.onChangeTextHandler }  
            />
        </View>
        <DefaultButton 
          title = "submit"
          onPress={ () => this.props.addPlaceHandler( this.state.typedPlace ) }  
        />
      </View>
    );
  }
}

const styles = StyleSheet.create( {
  container: {
    width: "100%",
    alignItems: "center"
  },
  inputContainer: {
    width: "80%"
  }
} );
```
let's use ` AddPlace ` in ` AddPlaceScreen `.
  * ` import ` it first.
  ```js
  import AddPLaceComponent from '../../components/AddPlace';
  ```
  * use it.
  ```js
  render() {
        return(
            <ScrollView>
                <View style = { styles.container }>
                    <MainText>
                        <HeadingText>Share Place with Us</HeadingText>
                    </MainText>
                    <DefaultButton title = "Pick Image"/>
                    <View style = { styles.placeholderContainer }>
                        <Text>Image Placeholder</Text>
                    </View>
                    <DefaultButton title = "Locate Me"/>
                    <View style = { styles.placeholderContainer }>
                        <Text>Map Placeholder</Text>
                    </View>
                    <AddPLaceComponent 
                      addPlaceHandler = { this.addPlaceHandler }
                    />
                </View>
            </ScrollView>
        );
  }
  ```

* Then let's separate the image-related ` components ` in a new ` Component ` named ` PickImage `.
```js
import React from 'react';
import { View, Text, StyleSheet } from 'react-native';
import DefaultButton from './UI/DefaultButton';

const PickImage = props => (
    <View style = { styles.container }>
        <DefaultButton title="Pick Image" />
        <View style = { styles.placeholderContainer }>
            <Text>Image Placeholder</Text>
        </View>
    </View>
);

const styles = StyleSheet.create({
    container: {
        width: "100%",
        alignItems: "center"
    },
    placeholderContainer: {
        width: "80%",
        height: 150,
        borderColor: "#bbb",
        borderWidth: 1,
        margin: 8
    }
});

export default PickImage;
```
Let's use ` PickImage ` in ` AddPlaceScreen `.
  * ` import ` it first.
  ```js
  import PickImageComponent from '../../components/PickImage';
  ```
  * use it.
  ```js
  render() {
      return(
          <ScrollView>
              <View style = { styles.container }>
                  <MainText>
                      <HeadingText>Share Place with Us</HeadingText>
                  </MainText>
                  <PickImageComponent />
                  <DefaultButton title = "Locate Me"/>
                  <View style = { styles.placeholderContainer }>
                      <Text>Map Placeholder</Text>
                  </View>
                  <AddPLaceComponent 
                    addPlaceHandler = { this.addPlaceHandler }
                  />
              </View>
          </ScrollView>
      );
  }
  ```

* Finally let's separate Map-related ` components ` in a new ` Component ` named ` AddLocation `.
```js
import React from 'react';
import { View, Text, StyleSheet } from 'react-native';
import DefaultButton from '.UI/DefaultButton';

const AddLocation = props => (
    <View style = { styles.container }>
        <DefaultButton title = "Locate Me" />
        <View style = { styles.placeholderContainer }>
            <Text>Map Placeholder</Text>
        </View>
    </View>
);

const styles = StyleSheet.create({
    container: {
        width: "100%",
        alignItems: "center"
    },
    placeholderContainer: {
        width: "80%",
        height: 150,
        borderColor: "#bbb",
        borderWidth: 1,
        margin: 8
    }
});

export default AddLocation;
```
Let's use ` AddLocation ` in ` AddPlaceScreen `.
  * first ` import ` it.
  ```js
  import AddLocationComponent from '../../components/AddLocation';
  ```
  * then use it.
  ```js
  render() {
        return(
            <ScrollView>
                <View style = { styles.container }>
                    <MainText>
                        <HeadingText>Share Place with Us</HeadingText>
                    </MainText>

                    <PickImageComponent />
                    
                    <AddLocationComponent />
                    
                    <AddPLaceComponent 
                      addPlaceHandler = { this.addPlaceHandler }
                    />
                </View>
            </ScrollView>
        );
  }
  ```


## Styling the Side Drawer
* Now let's add a **logout** ` Button ` to ` SideDrawerScreen ` and style it a little bit.
* We will add an ` Icon ` besides **Log out** ` Text ` besides each other.
```js
import React from 'react';
import { View, Text, TouchableOpacity, StyleSheet } from 'react-native';
import Icon from 'react-native-vector-icons/Ionicons';
import MainText from '../components/UI/MainText';

const SideDrawerScreen = ( props ) => (
    <View style = { styles.container }>
        <TouchableOpacity>
            <View style = { styles.logoutContainer }>
                <Icon
                    name = "md-log-out"
                    size={ 30 }
                />
                <MainText>
                    <Text>Log Out</Text>
                </MainText>
            </View>
        </TouchableOpacity>
    </View>
);

const styles = StyleSheet.create( {
    container: {
        flex: 1,
        backgroundColor: "white",
        paddingTop: 24
    },
    logoutContainer: {
        flexDirection: "row",
        alignItems: "center",
        backgroundColor: "#eee",
        padding: 8
    }
} );

export default SideDrawerScreen;
```


## Intro Cross Platform Styles & Responsiveness
* Over the next lectures we will focus on 2 problems:
  * How to specify **different styles** on **different platforms**.
  * How to achieve **responsive design** that adapts to different screen sizes and rotation issues.


## Cross Platform Styling Made Easy
* We have 2 tools or techniques that allow us to build different things for different platforms:
  * We have 2 independent **start points**: ` index.android.js ` and ` index.ios.js `. These files are start points in the sense that your **android** app starts in ` index.android.js ` and your **IOS** app starts in ` index.ios.js `. Using these files we can build an entire different app for each platform[the same functionality with different design and scenario]. The instructor stated that this approach is **not uncommon**.
  * We have the ` Platform ` **Api** provided by ` React Native `. This **Api** allows us to check on which platform the app is running in **running time**. This facilitates not only designing but also developing differently for different platforms.


## Using the Platform Api
* Using the ` Platform ` **Api** is very straightforward. 
  1. ` import ` it from ` react-native `.
  ```js
  import { Platform } from 'react-native';
  ```
  2. Then you have access to ` os ` property on ` Platform ` which has 2 possible values[that matter to us] either ` "android" ` or ` "ios" `.
  ```js
  if ( Platform.os === "android" ) {
      // do something for android
  }
  else {
      // do something for ios
  }
  ```

* Let's use this awesome **Api** to provide different ` DefaultButton ` for each platform. We have ` TouchableNativeFeedback ` that works only in **Android** so let's use it for **Android** and use the ` TouchableOpacity ` for **IOS**. In ` DefaultButton.js `.
```js
const DefaultButton = props => {
    const content = (
        <View style = { [styles.container, props.style] }>
            <Text style = { { color: props.textColor || "white" } }>
                { props.title }
            </Text>
        </View>
    );
    
    if ( Platform.OS === "android" ) {
        return(
            <TouchableNativeFeedback onPress = { props.onPress }>
                { content }
            </TouchableNativeFeedback>
        );
    }
    else {
        return(
            <TouchableOpacity onPress = { props.onPress }>
                { content }
            </TouchableOpacity>
        );
    }
}
```


## Loading Different Icons for Different Platforms
* ` Ionicons ` has got different icons for different platforms so we hope to make good use of that.
* Each ` Icon ` name shoud start with ` md- ` for **Android** or ` ios- ` for **IOS**. So for example the ` Icon ` with name ` search ` would be ` md-search ` for **Android** and ` ios-search ` for **IOS**. So all we need to do is to configure this prefix based on the value of ` Platform.os `. In ` createMainTabs.js `.
```js
const createMainTabs = () => {
    const prefix = (Platform.OS === "android")? "md-": "ios-"; 

    Promise.all( [
        Icon.getImageSource(prefix + "search", 30),
        Icon.getImageSource(prefix + "add", 30),
        Icon.getImageSource(prefix + "menu", 30)
    ] ).then( sources => {
        // the rest of code
```


## Cross Platform UI Libraries
* Instead of doing the whole work of designing differently for different platforms ourselves, we can **delegate** this to a third-party library.
* We are already using ` React Native Navigation ` and it does some of this for us including the tabs, nav bar and more.
* For ` Icons ` and default ` Components ` like ` Button ` and others, we can delegate this to another third-party library.
* Unfortnately, ` React Native ` does not have many of such libraries that work well. However, we still have a few. One of them is [` Native Base `](https://nativebase.io/).


## The Responsive Styling Problem
* It may seem that we have a good design on our device but what happens when we **rotate** the device or test on a **small-screen** device or even on a **large-screen** one? Do we still have a good design?
* This issue is known as **responsiveness** which means the ability to **adapt** to different screen sizes.
* To design ` screens ` that **adapt** to devices of different screen sizes, we may need to adjust them a little bit or we may need to have a totally different design for different categories of devices according to screen sizes.
* ` React Native ` provides tools that let you get the width and height of the running device and your mission is to use these measures to provide different designs. Let's look at these tools.


## Responsive Design Solutions
* ` React Native ` provides us with ` Dimensions ` **Api** which we use to:
  * **get** the device width and height.  
  ` Dimensions ` **Api** provides a method named ` get ` which **accepts** either ` "screen" ` or ` "window" ` and **returns** us an object with ` width ` and ` height ` properties. The difference between ` "screen" ` and ` "window" ` is, only in **Android**, ` "window" ` does not include the menu bar in measures while ` "screen" ` does[However, I tested it on Android and they are the same. May be that was in an old version].  
  * **listen** for ` Dimensions ` changes when the user **rotates** the device.  
  ` Dimensions ` **Api** provides a method named ` addEventListener ` which accepts:
    * event name which is ` "change" ` all the time.
    * the callback to be executed on firing this event in which we usually check the ` width ` or ` height ` and change the **UI** accordingly.


## Using the Dimensions Api && Adjusting Styles Dynamically && A Better Responsive Solution
* Let's use the ` Dimensions ` **Api** to make ` AuthScreen ` **responsive**.
* We need to have a different design for each mode: **portrait** and **landscape**. The **portrait** design is what we have now. The **landscape** one is done[not necessarily] by removing the header text and displaying **passwrod** and **confirm password** ` TextInput `s in one row.
* We define a ` state ` attribute to represent what mode we are in and we ` listen ` for ` Dimensions ` ` change ` to update this mode.  
* We define different styles in ` styles ` and assign them dynamically.
* In ` AuthScreen.js `.
```js
import React, { Component } from 'react';
import { View, ImageBackground, StyleSheet, Dimensions } from 'react-native';
import createMainTabs from './main_tabs/createMainTabs';
import DefaultInput from '../components/UI/DefaultInput';
import HeadingText from '../components/UI/HeadingText';
import MainText from '../components/UI/MainText';
import BackImage from '../assets/background.jpg';
import DefaultButton from '../components/UI/DefaultButton';

class AuthScreen extends Component {
    constructor( props ) {
        super( props );
        this.state = {
            viewMode: (Dimensions.get( "window" ).width < 500)? "portrait": "landscape" 
        }

        Dimensions.addEventListener( "change", this.onDimensionsChange );
    }

    onDimensionsChange = ( dims ) => {
        this.setState( {
            viewMode: (Dimensions.get( "window" ).width < 500)? "portrait": "landscape" 
        } );
    }

    loginHandler() {
        createMainTabs();
    }

    render() {
        let headingText = ( this.state.viewMode === "landscape" )
            ? null
            : (
                <MainText>
                    <HeadingText>Please Log In</HeadingText>
                </MainText>
            );

        return(
            <ImageBackground style={ styles.container } source = { BackImage }>
                { headingText }
                <DefaultButton 
                  title = "Switch to Login"
                />
                <View style = {styles.inputContainer }>
                    <DefaultInput placeholder = "E-maill" style = { styles.input }/>
                    <View style = { 
                        ( this.state.viewMode === "portrait" )
                        ? styles.portraitPasswordContainer
                        : styles.landscapePasswordContainer
                     }>

                        <DefaultInput 
                          placeholder = "Password" 
                          style = { [styles.input, this.state.viewMode === "portrait"? styles.portraitInput: styles.landscapeInput ] }
                        />

                        <DefaultInput 
                          placeholder = "Confirm Password" 
                          style = { [styles.input, this.state.viewMode === "portrait"? styles.portraitInput: styles.landscapeInput ] }
                        />
                    </View>
                </View>
                <DefaultButton
                  title = "Submit"
                  onPress = { this.loginHandler }
                />
            </ImageBackground>
        );
    }
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        justifyContent: "center",
        alignItems: "center"
    },
    inputContainer: {
        width: "80%"
    },
    input: {
        backgroundColor: "white",
        marginTop: 8,
        marginBottom: 8
    },
    portraitInput: {
        width: "100%"
    },
    landscapeInput: {
        width: "45%"
    },
    portraitPasswordContainer: {
        flexDirection: "column"
    },
    landscapePasswordContainer: {
        flexDirection: "row",
        justifyContent: "space-between"
    }
});

export default AuthScreen;
```


## Bug Fix Centering with Margins
* You may notice that our fields in ` AuthScreen ` are nor perfectly centered. That's because we apply ` margin ` on them. Ofcourse if we center something, that means that there is already a free space in which it is centered. That means no need for ` margin ` on the left and right. However, we may need a ` margin ` on top and bottom. For that situation we should not use ` margin ` but use ` marginTop ` and ` marginBottom ` because setting ` margin ` is equal to setting ` marginTop `, ` marginBottom `, ` marginRight ` and ` marginLeft `. setting ` marginLeft ` and ` marginRight ` is what causes propblems in centering.


## Cleaning up Dimensions Listeners
* When we ` add ` a listener on ` Dimensions ` we need to ` remove ` this listener when the ` screen ` does not exist anymore because ` React Native ` does not do that for us. So in ` AuthScreen.js ` let's ` remove ` our listener whenever ` componentWillUnmount ` gets called.
```js
componentWillUnmount() {
    Dimensions.removeEventListener( "change", this.onDimensionsChange );
}
```


## Styling Navigation Items (react-native-navigation)
* Using ` react-native-navigation ` we get a default styling for ` components ` such as menu buttons and tabs.
* To adjust this default styling we should visit their documentation.
  * To change the color of the selected tabs in a tab-based app, we should add 2 new objects to the object given to the method ` startTabBasedApp ` which is called on ` Navigation `. These new objects are ` tabsStyle ` and ` appStyle `.
  ```js
  Navigation.startTabBasedApp( {
      tabs: {

      },
      tabsStyle: {

      },
      appStyle: {
      
      }
  } );
  ```
  Inside these objects( ` tabsStyle `, ` appStyle ` ) we assign our preferred color to a property named ` tabBarSelectedButtonColor `[Note that doing the same in 2 objects is required only in **Android** but for **IOS** it is only required in ` tabsStyle `].

* We have also a property named ` tabBarButtonColor ` which defines the color of the unselected tabs. We can use it the same way as ` tabBarSelectedButtonColor `.

* To change the color of nav bar ` Button `s we need to do something new to us. Reach out to the ` screen ` or ` screes ` where these ` Buttons ` exist in the nav bar and add this:
```js
static navigatorStyle = {
    navBarButtonColor: "#bbb"
};
```
We know this ` static ` keyword meaning that the ` navigatorStyle ` belongs to the class itself not each instance. In our case we need to change the ` Button ` that opens the ` SideDrawer ` and it exists in both tabs(` AddPlaceScreen `, ` FindPlaceScreen `) so we duplicate this code there.


## Preparing the App for Animation
* Let's add a ` Button ` in ` FindPlaceScreen ` that the user hits to get places. 
* It does not  make sense now but later in the course we will fetch these places from a server so it makes sense to add a ` Button ` that the user hits to fetch places. This ` Button ` will be animated very soon.
* For now we want to initialy ` render ` a ` Button ` in the center of the ` screen ` and when the user hits it disapears and the places are ` rendered ` instead.
* For that let's make a ` state ` property that declares whether to ` render ` a ` Button ` or the list of places. We also respond to hitting the ` Button ` by changing this property.
* In ` FindPlacesScreen.js `.
```js
import React, { Component } from 'react';
import { View, Text, TouchableOpacity, StyleSheet } from 'react-native';
import { connect } from 'react-redux';
import PlacesList from '../../components/PlacesList'
import onNavigatorEvent from './onNavigatorEvent';

class FindPlaceScreen extends Component {
    constructor( props ) {
        super( props );
        props.navigator.setOnNavigatorEvent( onNavigatorEvent.bind( this ) );

        this.state = {
            placesFetched: false
        }
    }

    static navigatorStyle = {
        navBarButtonColor: "#bbb"
    }

    onFindPlaces = () => {
        this.setState( {
            placesFetched: true
        } );
    }

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
        let content = ( this.state.placesFetched )
            ? (
                <PlacesList 
                  places = { this.props.places }
                  selectItemHandler = { this.selectItemHandler }
                />
            )
            : ( 
                <TouchableOpacity onPress = { this.onFindPlaces }>
                    <View style = { styles.buttonWrapper }>
                        <Text style = { styles.buttonText }>Find Places</Text>
                    </View>
                </TouchableOpacity>
             );

        return(
            <View style = { this.state.placesFetched? null: styles.buttonContainer }>
                { content }
            </View>
        );
    }
}

const styles = StyleSheet.create( {
    buttonContainer: {
        flex: 1,
        justifyContent: "center",
        alignItems: "center"
    },
    buttonWrapper: {
        padding: 16,
        borderColor: "#bbb",
        borderWidth: 2,
        borderRadius: 16
    },
    buttonText: {
        fontSize: 24
    }
} );

const mapStateToProps = ( state ) => {
    return {
        places: state.PlacesReducer.places
    };
} 

export default connect( mapStateToProps )( FindPlaceScreen );
```


## Using the Animated Api
* Now we hope to animate the ` Button ` such that when the user hits it comes toward the screen by getting larger and larger and finally it disapears and the places list appear instead.
* **Animation** in general is nothing more than a property or a set of properties get updated over time. The main challenge with **Animation** is **performance**. 
* To achieve that we can use the ` Animated ` **Api** provided by ` React Native `. 
* ` Animated ` **Api** provides ` Animated.Value ` which is a ` class ` we can instantiate ` instances ` out of it. These ` instances ` can be used in **Animation** because ` Animated ` **Api** provides a simple and efficient way to update their values over time. So not to be confused, an instance of ` Animated.Value ` is not a primitive value, it provides us a value and a way to update this value over time.
* We can assign the instance of ` Animated.Value ` to a ` style ` property and we use a method from ` Animated ` **Api** to update the value.
  * ` Animated ` **Api** provides us with ` components ` that can be animated such as ` Animated.View `, ` Animated.Text ` and others. We should use these ` components ` because ` Animated.Value ` is not a primitive value. For that reason it is used with ` components ` that understand it.
  * For now we will use ` timing ` which is a method we can use to update the value over a time stamp defined by us.
* Finally, we need a ` style ` property that we can update to achieve our goal. We have ` transform ` property which is used to define certain transformation such as ` scale ` and ` rotation `. ` transform ` is assigned an array of objects. Each object has a key which is the kind of transformation, in our case it is ` scale `, and a value, which is in our case the ` Animated.Value ` instance from the ` state `.  
* I hope that it is a bit clear till now how we will achieve animation. Let's start:
  1. First ` import ` the ` Animated ` **Api**.
  ```js
  import { View, Text, TouchableOpacity, StyleSheet, Animated } from 'react-native';
  ```

  2. Then we define a property in the ` state ` and assign it, guess what, an instance of ` Animated.Value `.
  ```js
  this.state = {
      scale: new Animated.Value(1),
      placesFetched: false
  }
  ```
  The value passed to ` Animated.Value ` is the initial value.
  
  3. Then let's wrap our ` Button ` with ` Animated.View ` ` Component ` and define ` style ` with ` transform ` property as we declared above.
  ```js
  <Animated.View style = { { transform: [{ scale: this.state.scale }] } }>
      <TouchableOpacity onPress = { this.onFindPlaces }>
          <View style = { styles.buttonWrapper }>
              <Text style = { { fontSize: this.state.fontSize }  }>Find Places</Text>
          </View>
      </TouchableOpacity>
  </Animated.View>
  ```

  4. Then let's use ` timing ` method to specify how and when to animate. We animate when the user hits the ` Button ` so we use ` timing ` in ` onFindPlaces ` ` function `. After calling ` timing ` we call ` start ` to start the animation and optionally pass to ` start ` the callback to be invoked when animation is done which is in our case updating ` placesFetched ` property in the ` state `. ` timing ` accepts: 
    * the instance of ` Animated.Value ` to update.
    * a configuration object that in our case will hold 3 properties:
      * ` toValue ` which specifies the value to reach at end[Note that we defined the initial value when we instantiated the instance].
      * ` duration ` which specifies the time in ms that the animation should covers.
      * ` useNativeDriver ` which we assign it ` true ` to let do animation natively intead of doing it in ` JavaScript ` which has a significant effect on performance.
  ```js
  onFindPlaces = () => {
      Animated.timing( 
          this.state.scale,
          {
              toValue: 12,
              duration: 1000
          }
          ).start( ( finished ) => {
              this.setState( {
                  placesFetched: true
              } );
          } );
      }
  ```   
  