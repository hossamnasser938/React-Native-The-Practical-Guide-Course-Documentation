# Linking and Using Third-Party Libraries

## Module Introduction
* **Third-part libraries** are essential in ` React Native ` apps. You hardly can develop an app without making use of such third-party libraries.
* We already added libraries such as ` Redux ` and ` react-redux ` but these are just concerned with **JavaScipt** code. We need to add libraries that provide **native** features like using the **Maps** for example. For adding these libraries, you will need to add native code through **Android Studio** or **XCode**.
* For now we will start by adding ` react-native-vector-icons ` which enables you to add **icons** to buttons or to the status bar.


## Installing Libraries
* Let's see how to install such library:  
The 1st step **always** is to find the **Github repository** of the library in which you can find instructions of installation. For this library I found it [here](https://github.com/oblador/react-native-vector-icons).
    1. On reading the installation instructions, the 1st step says: Run: ` npm install react-native-vector-icons --save `. So simply, open your terminal, reach the project directory, and then hit this command to install the library.
    
    2. The 2nd step there is that you follow the installation instruction for your platform whether **Android** or **IOS**. For me I will head to **Android** section.
    
    3. On the **Android** section we have to options to complete the installation. The 1st option is: ` With Gradle ` and the 2nd one is: ` Manually `. Since ` With Gradle ` option is the recommended as stated there, I will proceed with it.
    
    4. In ` android/app/build.gradle ` ( NOT android/build.gradle ) We either add this line only:
    ```
    apply from: "../../node_modules/react-native-vector-icons/fonts.gradle"
    ```
    which will copy all files. The other option is to choose which files to copy from the library:
    ```
    project.ext.vectoricons = [
    iconFontNames: [ 'MaterialIcons.ttf', 'EvilIcons.ttf' ] 
    ]
    apply from: "../../node_modules/react-native-vector-icons/fonts.gradle"
    ```
    Do not forget to replace these files ` [ 'MaterialIcons.ttf', 'EvilIcons.ttf' ] ` with the files you wanna copy. Since I will use ` Ionicons ` as the instructor did, I will copy only this file ` [ 'Ionicons.ttf' ] `.

    5. By now we successfully installed the library and it is ready to be used. The next step is to figure out how to use the library. Since I will use ` Ionicons `, I will head [there](). There you find the icons you can use. Find the name of the ` Icon ` you wanna use.
    6. Head to your ` Component ` where you want to use the ` Icon `. Let's replace the ` Delete ` button name with ` Icon `. In ` PlaceDetails.js `.
        1. ` import ` the ` Icon ` ` Component `.  
        ```js
        import Icon from 'react-native-vector-icons/Ionicons';
        ```
        2. Now you can wrap ` Icon ` ` Component ` with ` TouchableOpacity ` ` Component ` to convert it to a ` Touchable ` ` Component `.
        ```js
        <TouchableOpacity 
          onPress={ () => {
            props.removeItemHandler( props.selectedPlace.key )
            props.closeModalHandler()
          } 
        }>
          <Icon 
            name="md-trash"
            siz={ 30 }
            color="red" 
          />
        </TouchableOpacity>
        ```
        Note that if you're building for **Android**, you precede the name of the ` Icon ` with **"md-"**. If you're building for **IOS**, you precede the name with **"ios-"**.     