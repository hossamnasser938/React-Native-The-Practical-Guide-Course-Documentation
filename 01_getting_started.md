# Getting Started

## Welcome
* We know that ` ReactJS ` is a **JavaScript** library or a framework that is used to build front-end **web** applications.
* ` React native ` is a **JavaScript** library that uses ` ReactJS ` to build *native* **mobile** apps.
* In this course we're gonna build an entire application through which we will learn:
    * accessing device camera.
    * using maps.
    * navigation.
    * storing data on a server.
    * uploading images.
    * authenticating users.
    * and more.


## What is React Native?
* What is ` React Native `?
    * We can express ` React Native ` using this popular sentence ` Learn once, write everywhere `. What this means is that you ` learn ` one thing which is **JavaScript** and use it to build *native* mobile apps for both platforms **Android** and **IOS**.
    * So You don not have to learn **Java** to build **Android** *native* apps and **Swift** to build **IOS** *native* apps. You learn only one thing which is **JavaScript**.
    * If you thought that using ` React Native` you will write one code base for both platforms, let me tell you that this not accurate. you will have to make adjustments related to each platform but the good thing is that you **reuse** much code.
    * So it is not the case where you ` Write once, use everywhere ` which is the case in technologies such as ` Ionic ` and ` Cordova `. Using these technologies you can write one code base that runs on both platforms. These technologies are **accelerating** the development but this has a significant effect on **performance**.
* What is ` React Native ` apps?
    * A ` React Native ` app has two main parts:
        * **UI**  
        which is built using ` Components ` provided by ` React Native ` which are similar to the ` Components ` of ` ReactJS `. But the most important thing is that these ` Components ` are **compiled** to *native* ` Components ` whether **Android** or **IOS**. So they are not ` DOM ` elements like ` <div> `, ` <span> ` or any other **DOM** element because we do not have a **Dom** in mobiles.
        * **Logic**  
        which is built using **JavaScript**. However, this part is **not compiled** to ` native ` code. It remains **JavaScript**.
    * A ` React Native ` app is not a **web** app that is displayed on a mobile using a browser which is the case in ` Ionic ` and ` Cordova `.
    * Having real *native* apps gives us a **better performance** and also an **access** to the underlying **operating system**.


## A Closer Lock
* In ` ReactJS ` we use **DOM** elements to construct ` Components ` to be displayed by the browser. We use these **DOM** elements since the browser knows these **DOM** elements.
* In ` React Native ` we're building **UI** for mobiles. Mobiles do not know something called **DOM**. Instead, as we said, we use ` Components ` defined by ` React Native ` for us that are eventually, when everything compiled, will be translated into *native* ` Components ` depending on the platform.
* For example:
    * In ` ReactJS ` we use ` <div> ` to wrap a **UI** item.
    * In *native* **Android** we use ` android.view ` which is used by **Android** platform to wrap a **UI** item.
    * In *native* **IOS** we use **UIView** which is used by **IOS** platform to wrap a **UI** item.
    * In ` React Native ` we use ` <View> ` which is a ` Component `  provided by the library to wrap a **UI** item. On compiling the project ` <View> ` will be translated into either ` android.view ` or ` UIView ` depending on which platform you are building the app for.


## What happens to JavaScript?
* Now we now what happens to the **UI**. But the question is What happens to the **logic** written in **JavaScript**? Is it also compiled to native code?
* The answer is no. It remains JavaScript as it is.
* What happens is that ` React Native ` establishes an environment for JavaScript to run. Like what ` Node.js ` does to run **JavaScript** code outside a browser. [To be honest, this is not clear for me yet].


## Creating our First React Native App
* To get everything compiled into **Android** or **IOS**, we need a **project setup** to do that for us. We can build this **project setup** manually and definitely we should in real projects to be able to run our app as a *native* app. However, for now we will use a simple **project setup** to get started and soon in the course we will see how to do that manual **project setup**.
    1. We need to install ` Node.js `. We won't write ` Node.js ` code. However, with ` Node.js `, we can use ` NPM `(Node package manager) to install and manage packages or libraries in our app. It also provides us with  a **development server** which let's us live update our app while it is running on a real device or a simulator(When we alter any thing in the code base the development server updates the app automatically on the real device or the simulator). Note that your PC as well as your mobile device should be connected on network using Wi-Fi. We discussed how to install both ` Node.js ` and **NPM** in previous courses[Head to: **Setup Workspace with Webpack** in **ReactJS Basics** tutorial].
    2. We need to install a package named ` create-react-native-app `. To install it hit ` npm install -g create-react-native-app `. ` -g ` means install it **globally** not just for this app.
    3. We need to install ` expo-cli ` package. To install it hit ` npm install -g expo-cli `.
    4. If you do not have ` Git ` installed, you need to do so.
    5. Navigate to the directory where you want to create your project.
    6. Hit ` create-react-native-app [app_name] ` and choose a name to your app.
    7. Navigate to the folder created for your app and hit ` npm start ` to start the development server which will live update your app running on a real app or a simulator.
    8. You may get an error. Just copy the commands below the error, execute them and then everything will be running smoothly.
    9. After running it will show a **QR** code that you can scan using ` Expo ` app(which you should install onto your device from app store) and then ` Expo ` app will wrap your app.
* To show that this is our app and to see this live update, head to your app open the file ` App.js ` which defines the main or root ` Component ` of your app and modify the text there. Hopefully, you should see this update immediately on your real device. If not, shake your device to open the menu and then hit ` refresh `.
* Hints:
    * I got a terrible error when trying to install ` expo-cli `. The error was related to permissions(EACCESS error). It was suggested [here](https://docs.npmjs.com/resolving-eacces-permissions-errors-when-installing-packages-globally) to use ` NVM ` to solve these permission issues and I did by installing it from the official [source](https://github.com/creationix/nvm). However, it did not solve the problem. Finally the problem was solved by giving the permission to access npm directory using this command: ` sudo chown -R $(whoami) ~/.npm `.  
    * To update ` Node.js ` using ` NVM `(node version manager) hit : ` nvm install [new_version] --reinstall-package-from=[old_version] `.
    * To update ` npm ` to the latest version using ` NVM ` hit: ` nvm install-latest-npm `.
    * I got another error ` Error: ENOSPC ` when trying to use the ` Expo ` app to host my app. This error was solved [here](https://stackoverflow.com/questions/22475849/node-js-what-is-enospc-error-and-how-to-solve/32600959#32600959) by hitting this command ` echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p `.


## Dealing with Limitations of React Native
* ` React Native ` is a great tool that lets us build *cross-platform native* applications using **JavaScript**. However it has some **limitations** that make it a **hard work**. These limitations are:
    * No or at least **little styling** for ` Components `. We have to build ours or use third-party libraries to do that for us.
    * We have a **basic set of pre-built** ` Components `. We have to use these set to build ours or also depend on a third-party library.
    * We have to handle **responsiveness** using some tools.
    * ` React Native ` has got new versions every month which means it is a **fast-moving target**. This results in **breaking changes** frequently(changes that might break our code. The thing that works in an older version in a given way does not work the same way in a later version).
    * Third-party packages help us solve most of ` React Native ` problems. However, this makes ` React Native ` highly dependent on third-party packages.   
    * ` React Native ` is under active development which means it has **bugs** since it is not yet stable.
* Although ` React Native ` has all these limitations, it is still a great library. The greatness of ` React Native ` comes from the fact that it combines the **flexibility** of web development as well as the **better performance** and **easy access** to the underlying operating system of *native* code. ` React Native ` does not wait for an evidence on that. It has been already used to build large and complex applications such as ` Netflix ` and ` AirBNB `.


## What a Good Course offers?
* A good ` React Native ` course should:
    * introduce you to the core concepts.
    * teach you the basics.
    * introduce common third-party libraries since they are so important in ` React Native `.
    * show you how to use the tools provided by ` React Native `.
    * show you practical examples.
    * help you move on your own since it is impossible to cover all the use cases you might need in any course. So it is important to teach how to solve problems on your own.
    * show you how to find libraries and connect them in your app.
    * show you when to dive deep into docs.   


## Course Requirements
* Two requirement must be met before starting this course:
    * ` ReactJS ` basics.
    * ` Redux `. However, **Maximilian**(the instructor) will give a brief refresh on ` Redux `.  
* However in order to have the basics of ` ReactJS `, these requirements must be met:
    * Only familiarity with HTML and CSS.
    * **JavaScript** basics.
    * **ECMAScript 6**.
* Also familiarity with **Java** is a plus since sometimes we will need to dig into **Java** files and copy some code provided by third-party libraries.
* For hardware and software skills:
    * having **Windows** or **Linux** allows you to publish apps only for **Android** platform. However, you still can use **Windows** or **Linux** to build apps for **IOS** and we can use the ` Expo ` app to wrap our apps. But unfortunately to install your apps on real devices or publish them, you need **MACOS**.
    * having **MacOS** allows you to publish apps for both **Android** and **IOS** platforms.


## Course Outline
1. **Getting started**.
    * What is ` React Native ` and what are ` React Native ` apps.
    * Limitations of ` React Native `.
    * Building the first ` React Native ` app.
2. **The basics**.
    * How ` React Native ` works.
    * Which ` Components ` it ships.
    * How to style these ` Components ` basically.
3. ` Redux `.
    * How to use ` Redux ` in a ` React Native ` app.
4. **Debugging**.
    * How to debug ` React Native ` apps.
    * How to use the cool features of debugging.
5. **Linking third-party libraries**.
    * using special icons.
    * using maps.
    * and more.
6. **Using Navigation**.
    * The alternative to ` Routing ` In ` ReactJS `.
    * How to use tabs.
    * Hitting the back button.
    * and more.
7. **Styling & Animation**.
    * How to style and animate our ` Components `.
8. **Handling user input**.
    * Getting input from user.
    * Validate that input.
    * Handling it.
9. **Camera & Maps**.
    * How to use the Camera.
    * How to use the Maps.
10. **Using HTTP**.
    * How to fetch data from a server.
    * How to store data on a server.  
11. **Authenticating Users**         
    * Creating users.
    * Signing them in.
    * Managing their sessions.
12. **Polish & Publish**.
    * How to publish our apps into the app store.


## How to get the most out of this course?
* Watch the videos.
* Code by your own.
* Debug your own code.
* Ask whenever you want.
* Also answer other people questions.
