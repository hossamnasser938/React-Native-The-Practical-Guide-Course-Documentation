# Handling User Input

## Module Introduction
* In this module we will have a look at how to:
  * **handle user input**:
    * capture it.
    * validate it.
    * sends useful feedback guiding the user what to enter.
  * **handle the doft keyboard**[The keyboard of the physical device].
    * What characters to show for a specific ` TextInput `.
    * When to show/hide it.


## Managing Input Control State
* Let's start by saving user inputs for ` AuthScreen ` in the ` state ` so that we can validate these input whenever they encounter any change from the user.
* For that we need to keep 3 values in the ` state `: ` email `, ` password `, and ` confirmPassword `(Our inputs). We will also keep for each input, its validity ` state `(Whether th user input is valid or not) as they change whenever the user updates input.
* However, the instructor will follow a pretty good, and new [at least for me], way of handling user inputs. Instead of **dispersing** the logic of input validation, he will combine all this logic into a single ` function ` that validates all inputs.
* For that reason, we will not only store the value and validity of these inputs in the ` state `, but also will store the **validation rules**(the rules that check whether a given input is valid or not) there. Absolutely, These **validation rules** do not change in **running time** so why we store them in the ` state `? Actually no reason except for keeping related things next to each other so we have for each input: its value, its validity state, and its validation rules all in one place.
```js
constructor( props ) {
    super( props );
    this.state = {
        viewMode: (Dimensions.get( "window" ).width < 500)? "portrait": "landscape",
        controls: {
            email: {
                value: "",
                valid: false,
                validationRules: {
                    "isEmail": true
                }
            },
            password: {
                value: "",
                valid: false,
                validationRules: {
                    "minLength": 6
                }
            },
            confirmPassword: {
                value: "",
                valid: false,
                validationRules: {
                    "equalTo": "password"
                }
            }
        } 
    }

    Dimensions.addEventListener( "change", this.onDimensionsChange );
}
```

* We will also provide a ` function ` that updates ` state ` whenever the user updates inputs. Since it is only one ` function ` That updates 3 inputs, it accepts not only the ` value ` entered by the user but also the ` key ` whether ` email `, ` password ` or ` confirmPassword `.
```js
onUpdateInputs = ( key, val ) => {
    this.setState( prevState => {
        return {
            controls: {
                ...prevState.controls,
                [key]: {
                    ...prevState.controls[key],
                    value: val
                }
            }
        };
    } );
};
```

* After that we will ` bind ` these values from ` state ` to their corresponding ` TextInput `s ` value ` ` prop `. We will also ` bind ` this ` function ` with ` onTextChange ` ` prop `.
```js
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
                <DefaultInput 
                    placeholder = "E-maill" 
                    style = { styles.input }
                    value = { this.state.controls.email.value }
                    onChangeText = { val => { this.onUpdateInputs( "email", val ) } }  
                />
                <View style = { 
                    ( this.state.viewMode === "portrait" )
                    ? styles.portraitPasswordContainer
                    : styles.landscapePasswordContainer
                    }>

                    <DefaultInput 
                        placeholder = "Password" 
                        style = { [styles.input, this.state.viewMode === "portrait"? styles.portraitInput: styles.landscapeInput ] }
                        value = { this.state.controls.password.value }
                        onChangeText = { val => { this.onUpdateInputs( "password", val ) } }
                    />

                    <DefaultInput 
                        placeholder = "Confirm Password" 
                        style = { [styles.input, this.state.viewMode === "portrait"? styles.portraitInput: styles.landscapeInput ] }
                        value = { this.state.controls.confirmPassword.value }
                        onChangeText = { val => { this.onUpdateInputs( "confirmPassword", val ) } }
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
```


## Adding Custom Validation Logic
* Now we have our inputs connected to our ` state ` we need to build the validaion logic.

* As we said the instructor used a way that will let us build a single ` function ` to validate all inputs.

* Let's create a ` directory ` under ` src ` named ` utility ` to hold utility functions.

* Under ` utility ` directory, let's create a file named ` validation.js ` that will `export ` our general validation ` function `. This ` function ` accepts 3 args:
  * ` value ` to be validated.
  * ` validationRules ` to validate the ` value `.
  * any other ` connectedValues ` needed in validation.

* For each validation rule, we will define a helper ` function `.

* Inside the general validation ` function ` we first declare a variable named ` valid ` and initialize it with ` true `. Then we loop through each rule in ` validationRules ` and ` AND ` the value of ` valid ` variable with the result of the validator ` function ` for that rule. We do that because if ` valid ` became ` false ` for some rule, we do not want another rule that evaluates to ` true ` to override the previous ` false ` value.
```js

```

* Now let's ` import ` and invoke ` validate ` ` function ` in ` AuthScreen ` whenever the user updates input. So we need to invoke it in ` onUpdateInputs ` ` function `. 

* Note that we need to instantiate ` connectedValues ` object to hold the ` password ` whenever the user updates either ` password ` or ` confirmPassword ` because in either case we want to validate ` confirmPassword ` input.

* However, in case of ` password ` we need to pass the ` value ` we got in ` onUpdateInputs ` because it is the last value of ` password ` while in case of ` confirmPassword ` we need to pass the value of ` password ` from ` state `.

* Note also that in case of ` password `, we need to validate ` password ` and ` confirmPassword `. To solve this issue We always update the ` confirmPassword ` ` valid ` property. In case of ` password ` we validate it. In any other case, we set it to the old ` valid ` value.
```js
onUpdateInputs = ( key, val ) => {
    let connectedValues = {};
    if ( key == "confirmPassword" ) {
        connectedValues["equalTo"] = this.state.controls.password.value;
    }

    if ( key == "password" ) {
        connectedValues["equalTo"] = val;
    }

    this.setState( prevState => {
        return {
            controls: {
                ...prevState.controls,
                "confirmPassword": {
                    ...prevState.controls.confirmPassword,
                    valid: key === "password"
                        ? validate( prevState.controls.confirmPassword.value, prevState.controls.confirmPassword.validationRules, connectedValues )
                        : prevState.controls.confirmPassword.valid
                },
                [key]: {
                    ...prevState.controls[key],
                    value: val,
                    valid: validate( val, prevState.controls[key].validationRules, connectedValues )
                }
            }
        };
    } );
};
```


## Using the Validation State
* Till now we have our ` state ` of ` AuthScreen ` reflecting the validity of the input but we're not giving the user a visual feedback about the input validity state. So let's do that.
    1. Let's add 2 new styles to ` DefaultInput `. One should be applied whenever its input is **valid** and the other whenever it is **invalid**. We expect to get 2 new ` props ` to reflect the validity state. One is ` valid ` which reflects whether the value there is **valid** or not and the other is ` touched ` which reflects whether the user touched this ` DefaultInput ` or not because we don't want to bother the user with ` invalid ` ` style ` when he has not taken any chance to enter something yet. We apply ` valid ` ` style `  whenever ` valid ` is ` true `. And we apply ` invalid ` ` style ` whenever ` valid ` is ` false ` and ` touched ` is ` true `. Otherwise(when ` valid ` is ` false ` and ` touched ` is ` false `) we stick with the default style.
    ```js
    import React from 'react';
    import { TextInput, StyleSheet } from 'react-native';

    const DefaultInput = props => (
        <TextInput 
        underlineColorAndroid = "transparent"
        { ...props }
        style = { [styles.input, props.style, props.valid? styles.valid : null, (!props.valid && props.touched)? styles.invalid : null] }
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
        },
        valid: {
            borderColor: "green"
        },
        invalid: {
            borderColor: "red"
        }
    } );

    export default DefaultInput;
    ```

    2. Let's provide these ` props ` from ` AuthScreen ` to ` DefaultInput `. We have already ` valid ` of each input. We need to add the logic of ` touched ` ` prop `. We add ` touched ` attribute to the object of each input in ` state.controls ` and we initialize it with ` false ` indicating that it has not been touched yet. Whenever the user updates any input(in ` onUpdateInputs `) we set the updated input ` touched ` attribute to ` true `. The first time this has an effect while the subsequent times this has no effect but it hurts nothing.
        1. Add ` touched ` to ` state `.
        ```js
        constructor( props ) {
            super( props );
            this.state = {
                viewMode: (Dimensions.get( "window" ).width < 500)? "portrait": "landscape",
                controls: {
                    email: {
                        value: "",
                        valid: false,
                        validationRules: {
                            "isEmail": true
                        },
                        touched: false
                    },
                    password: {
                        value: "",
                        valid: false,
                        validationRules: {
                            "minLength": 6
                        },
                        touched: false
                    },
                    confirmPassword: {
                        value: "",
                        valid: false,
                        validationRules: {
                            "equalTo": "password"
                        },
                        touched: false
                    }
                } 
            }
            Dimensions.addEventListener( "change", this.onDimensionsChange );
        }
        ```
        
        2. Update ` touched ` in ` onUpdateInputs `.
        ```js
        onUpdateInputs = ( key, val ) => {
            let connectedValues = {};
            if ( key == "confirmPassword" ) {
                connectedValues["equalTo"] = this.state.controls.password.value;
            }

            if ( key == "password" ) {
                connectedValues["equalTo"] = val;
            }

            this.setState( prevState => {
                return {
                    controls: {
                        ...prevState.controls,
                        "confirmPassword": {
                            ...prevState.controls.confirmPassword,
                            valid: key === "password"
                                ? validate( prevState.controls.confirmPassword.value, prevState.controls.confirmPassword.validationRules, connectedValues )
                                : prevState.controls.confirmPassword.valid
                        },
                        [key]: {
                            ...prevState.controls[key],
                            value: val,
                            valid: validate( val, prevState.controls[key].validationRules, connectedValues ),
                            touched: true
                        }
                    }
                };
            } );
        };
        ```

        3. pass ` valid ` and ` touched ` to the ` DefaultInput `s.
        ```js
        <View style = {styles.inputContainer }>
            <DefaultInput 
                placeholder = "E-maill" 
                style = { styles.input }
                value = { this.state.controls.email.value }
                onChangeText = { val => { this.onUpdateInputs( "email", val ) } }
                valid = { this.state.controls.email.valid }
                touched = { this.state.controls.email.touched }
            />
            <View style = { 
                ( this.state.viewMode === "portrait" )
                ? styles.portraitPasswordContainer
                : styles.landscapePasswordContainer
                }>

                <DefaultInput 
                    placeholder = "Password" 
                    style = { [styles.input, this.state.viewMode === "portrait"? styles.portraitInput: styles.landscapeInput ] }
                    value = { this.state.controls.password.value }
                    onChangeText = { val => { this.onUpdateInputs( "password", val ) } }
                    valid = { this.state.controls.password.valid }
                    touched = { this.state.controls.password.touched }
                />

                <DefaultInput 
                    placeholder = "Confirm Password" 
                    style = { [styles.input, this.state.viewMode === "portrait"? styles.portraitInput: styles.landscapeInput ] }
                    value = { this.state.controls.confirmPassword.value }
                    onChangeText = { val => { this.onUpdateInputs( "confirmPassword", val ) } }
                    valid = { this.state.controls.confirmPassword.valid }
                    touched = { this.state.controls.confirmPassword.touched }
                />
            </View>
        </View>
        ```

    3. The last thing to do is to **disable** the ` submit ` ` Button ` whenever any input is invalid. We use one of ` Touchable ` ` components `(based on the platform) to ` render ` our ` DefaultButton `. ` Touchable ` ` components ` have property named ` disabled ` when it is ` true ` the ` Touchable ` is not touchable any more. However, this has no visual effect. So we need to define a ` style ` for ` disabled ` state.
    ```js
    import React from 'react';
    import { TouchableOpacity, TouchableNativeFeedback, View, Text, StyleSheet, Platform } from 'react-native';

    const DefaultButton = props => {
        const content = (
            <View style = { [styles.container, props.style, props.disabled? styles.disabled : null] }>
                <Text style = { { color: props.textColor || "white" } }>
                    { props.title }
                </Text>
            </View>
        );
        
        if ( Platform.OS === "android" ) {
            return(
                <TouchableNativeFeedback onPress = { props.onPress } disabled = { props.disabled }>
                    { content }
                </TouchableNativeFeedback>
            );
        }
        else {
            return(
                <TouchableOpacity onPress = { props.onPress } disabled = { props.disabled }>
                    { content }
                </TouchableOpacity>
            );
        }
    }

    const styles = StyleSheet.create( {
        container: {
            margin: 4,
            padding: 4,
            backgroundColor: "grey",
            borderWidth: 1,
            borderRadius: 3,
            borderColor: "black"
        },
        disabled: {
            opacity: 0.3
        }
    } );

    export default DefaultButton;
    ```

    4. Let's provide this ` disabled ` ` prop ` from ` AuthScreen ` to ` DefaultButton `.
    ```js
    <DefaultButton
        title = "Submit"
        onPress = { this.loginHandler }
        disabled = { 
            !this.state.controls.email.valid ||
            !this.state.controls.password.valid ||
            !this.state.controls.confirmPassword.valid
        }
    />
    ```


## Dispatching An Auth Action
* Till now we're not doing anything when hitting ` submit `. We're just opening the Main Tabs. Ofcourse we have to reach the server to **authenticate** and **Sign In** or **Sign Up** but that will be in a later module. For now we need to prepare that ` Action `. So let's go and add an ` Auth ` ` Action `, ` Dispatch ` it on hitting ` submit ` and leave its ` Reducer ` for the later module.
    1. Let's name our ` Action ` in ` ActionTypes.js ` file that exists in ` src/redux/actions ` directory[We renamed this file from ` PlacesTypes.js ` to ` ActionTypes.js `].
    ```js
    export const ADD_PLACE = "ADD_PLACE";
    export const DELETE_PLACE = "DELETE_PLACE";
    export const AUTH_USER = "AUTH_USER";
    ```
    
    2. Let's create a file named ` auth.js ` under the same directory to define ` Auth ` ` Action ` there.
    ```js
    import { AUTH_USER } from './ActionTypes';

    export const authUser = authData => {
        return {
            type: AUTH_USER,
            payload: authData
        };
    };
    ```

    3. Let's ` export ` ` Auth ` ` Action ` in ` index.js ` in the same directory.
    ```js
    export { addPlace, deletePlace } from './places';
    export { authUser } from './auth';
    ```

    4. Since we will ` dispatch ` an ` Action ` from ` AuthScreen ` we need to ` connect ` it with ` redux `.
        1. In ` App.js ` let's ` register ` it  with ` redux `.
        ```js
        Navigation.registerComponent("awesome_place.AuthScreen", 
        () => AuthScreen,
        store,
        Provider);
        ```
        2. In ` AuthScreen.js ` let's ` connect ` it to ` redux `.
        ```js
        export default connect(null, mapDispatchToProps)(AuthScreen);
        ```
        Note that we need to ` import ` ` connect ` method from ` react-redux `.
        ```js
        import { connect } from 'react-redux';
        ```
    5. Now let's define our ` mapDispatchToProps ` ` function ` in which we will map ` authUser ` ` Action ` to a ` prop `.
    ```js
    const mapDispatchToProps = dispatch => {
        return {
            onLogin: authData => {
                dispatch( authUser( authData ) );
            }
        };
    };
    ```
    Note that we need to ` import ` ` authUser ` ` Action ` from ` index.js `.
    ```js
    import { authUser } from '../redux/actions/index';
    ```
    6. Finally we need to ` dispatch ` this ` Action ` in ` loginHandler `.
    ```js
    loginHandler = () =>  {
        const authData = {
            email: this.state.controls.email.value,
            password: this.state.controls.password.value
        }

        this.props.onLogin( authData );
        
        createMainTabs();
    }
    ```


## Switching Auth Modes
* Now let's let ` Switch to LogIn ` toggle ` Button ` do its job. We want to actually switching between ` LogIn ` and ` SignUp ` modes. In ` LogIn ` we need to ` render ` only ` email ` and ` password ` inputs while in ` SignUp ` we need to add to these the ` confirmPassword ` input.
    1. Keep track of ` Auth ` mode in ` state `.
    ```js
    this.state = {
        viewMode: (Dimensions.get( "window" ).width < 500)? "portrait": "landscape",
        authMode: "login",
    ``` 
    
    2. Define a ` function ` that responds to clicking this toggle ` Button ` and toggle ` authMode `.
    ```js
    authModeChangeHandler = () => {
        this.setState( prevState => {
            return {
                ...prevState,
                authMode: prevState.authMode === "login" ? "signup" : "login" 
            };
        } );
    };
    ```
    
    3. ` bind ` this ` function ` with clicking the toggle ` Button ` and set the ` Button ` ` title ` based on the auth mode.
    ```js
    <DefaultButton 
        title = { this.state.authMode === "login"? "Switch to SignUp" : "Switch to LogIn" }
        onPress = { this.authModeChangeHandler }
    />
    ```
    
    4. ` render ` ` confirmPassword ` input only in ` SignUp ` mode.
    ```js
    let confirmPasswordInput = this.state.authMode === "login"
        ? null 
        : (
            <DefaultInput 
                placeholder = "Confirm Password" 
                style = { [styles.input, this.state.authMode === "login" || this.state.viewMode === "portrait"? styles.portraitInput: styles.landscapeInput ] }
                value = { this.state.controls.confirmPassword.value }
                onChangeText = { val => { this.onUpdateInputs( "confirmPassword", val ) } }
                valid = { this.state.controls.confirmPassword.valid }
                touched = { this.state.controls.confirmPassword.touched }
            />
        );
    ```
    ```js
    <View style = {styles.inputContainer }>
        <DefaultInput 
            placeholder = "E-maill" 
            style = { styles.input }
            value = { this.state.controls.email.value }
            onChangeText = { val => { this.onUpdateInputs( "email", val ) } }
            valid = { this.state.controls.email.valid }
            touched = { this.state.controls.email.touched }
        />
        <View style = { 
            ( this.state.viewMode === "portrait" )
            ? styles.portraitPasswordContainer
            : styles.landscapePasswordContainer
            }>

            <DefaultInput 
                placeholder = "Password" 
                style = { [styles.input, this.state.authMode === "login" || this.state.viewMode === "portrait"? styles.portraitInput: styles.landscapeInput ] }
                value = { this.state.controls.password.value }
                onChangeText = { val => { this.onUpdateInputs( "password", val ) } }
                valid = { this.state.controls.password.valid }
                touched = { this.state.controls.password.touched }
            />

            { confirmPasswordInput }                        
        </View>
    </View>
    ```

    5. We need to ignore ` confirmPassword ` when checking whether to **disable** ` submit ` ` Button ` or not in ` LogIn ` auth mode.
    ```js
    <DefaultButton
        title = "Submit"
        onPress = { this.loginHandler }
        disabled = { 
            !this.state.controls.email.valid ||
            !this.state.controls.password.valid ||
            (!this.state.controls.confirmPassword.valid && this.state.authMode === "signup")
        }
    />
    ```

    6. Now everything **functionally** works well. However, in ` landscape ` view mode if we changed from ` LogIn ` to ` SignUp ` auth mode, we lose our **responsive design**. To fix that, We use the ` landscapeStyle ` only when we're in ` landscape ` view mode and not in ` LogIn ` mode. We update the ` style ` of both ` password ` and ` confirmPassword `.
    ```js
    style = { [styles.input, 
        this.state.authMode === "login" || this.state.viewMode === "portrait"
        ? styles.portraitInput
        : styles.landscapeInput ] }
    ```


## Configuring Text Input Components
* Let's configure our ` TextInput `s so that:
    * ` password ` and ` confirmPassword ` inputs hide the text written there. We do that by setting ` secureTextEntry ` ` prop ` to ` true `. We add this line to both ` DefaultInput `s.
    ```js
    secureTextEntry = { true }
    ```

    * ` email ` input shows the ` @ ` sign in the first page of the keyboard. We do that by setting the ` keyboardType ` ` prop ` to ` email-address `. In ` email ` input also we need to disable both ` autoCapitalize ` and ` autoCorrect `.
    ```js
    keyboardType = "email-address"
    autoCapitalize = "none"
    autoCorrect = { false }
    ```


## Handling the Soft Keyboard
* The last thing we wanna do in this module is to:
    * Let the keyboard push everything up a little bit so if we have an input below like ` confirmPassword ` we can reach. We do that by wrapping our content in a ` KeyboardAvoidingView ` and set ` behavior ` ` prop ` to ` padding `.
    ```js
    <KeyboardAvoidingView style = { styles.outerContainer } behavior = "padding">
        <ImageBackground style={ styles.innerContainer } source = { BackImage }>
    ```
    Do not forget the cloasing tag and also do not forgrt to ` import ` the ` KeyboardAvoidingView ` ` component ` from ` react-native `.

    * Dismiss the keyboard on touching any where. We do that by wrapping everything in a ` TouchableWithoutFeedback ` and set ` onDismiss ` ` prop ` to ` function ` that dismisses the keyboard which is something we can get from the ` Keyboard ` **Api** provided by ` react-native `.
    ```js
    <KeyboardAvoidingView style = { styles.outerContainer } behavior = "padding">
            <TouchableWithoutFeedback style = { styles.outerContainer } onPress = { Keyboard.dismiss }>
                <ImageBackground style={ styles.innerContainer } source = { BackImage }> 
    ```
    New styles: 
    ```js
    const styles = StyleSheet.create({
    outerContainer: {
        flex: 1
    },
    innerContainer: {
        flex: 1,
        justifyContent: "center",
        alignItems: "center"
    },
    ```

