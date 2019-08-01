# nodejs-android-prebuilt-binaries
For a guide on how to use this please read my article on Medium [here](https://medium.com/@aaronwatson427/nodejs-on-android-root-7c158a9ac9a7/ "NodeJS on Android")

Prebuilt binaries of NodeJS for android(arm,arm64,x86,x64,mipsel), full or limited(by --without-snapshot --without-inspector --without-intl)

These binaries are prebuilt on Linux docker container, see [Build Nodejs For Android Perfectly](https://github.com/sjitech/build-nodejs-for-android).

## Why would you want to run Node / Javascript on Android? ##
The reason I originally looked into this was that I had a WebRTC application that needed a signaling server and I wanted to run it on the embedded system so I wouldn’t need a second computer. Another use case might be that you have a bunch of code written in Javascript and you want to leverage that on Android. Adding Node to the embedded Android toolkit is a huge bonus as well since there are a lot of problems that can easily be solved using it.

## Download Node ##
The first thing that will be required to run any Javascript natively on Android will be Node, the way we can get Node for Android is to download a pre-compiled binary. You will need one that will run on the CPU architecture that your android device has. In my case, I will be using a Snapdragon 820 dev kit so I’ll be getting an arm64 binary. If you are unsure of what version you need I would start with arm and if that doesn’t work try x86. You can download the binary you need from GitHub here. I recommend just downloading the single file from the browser since it is a lot of precompiled binaries to clone the entire repo.

## Install the binary on the Android device ##
To use the Node binary, we will need to push it to our device and add execute permissions. The best way to do this is over ADB, if you are unfamiliar with ADB it is beyond the scope of this tutorial however a great place to find information on it would be the documentation here.

Once you download the appropriate binary for your device it’s time to push it to the device. For the sake of this guide, I will be installing it in the system partition so it will directly accessible from the shell. However you can place it wherever you like but you will need to call it with a fully qualified path.

We need to remount /system as r/w so we can push the binary, you can achieve this with the following command.

___adb remount___

Next, we need to push the binary to the device, you will need to change the source folder to point to the location that you downloaded the Node binary earlier.

___adb push “E:\sourcetree\nodejs-android-prebuilt-binaries\nodejs-7.8.0-android-arm64-full\bin\node” /system/bin___

Finally, to use it we will need to change the permissions to allow execution.

___adb shell “chmod +x /system/bin/node”___

## Hello world from Javascript ##
To test the Node binary we will do a simple version check in the system, run the following command, if it doesn’t return a version number either you have the wrong binary or something else is wrong.

___adb shell “node -v”___

For fun let’s create a simple hello world in javascript and execute it on the system. You will need to have a folder on the device that you are using to store the Javascript files, for the remainder of the tutorial I will be using /data/local/tmp however you can use whatever folder you want.

You can create a hello world in Javascript in just one line so let’s just create it using ADB shell. First we’ll need to enter the shell and then call the echo command once we’re in (You should see a # or $ at the prompt).

___adb shell___

___echo “console.log(‘Hello Android!’);” > /data/local/tmp/hello.android.js___

To execute the file simply call Node the same way you would on a Linux computer. Also if you installed Node to a different location don’t forget to call it using a fully qualified path.

___node /data/local/tmp/hello.android.js___

If everything went correctly you should see the text “Hello Android!” in the output! Great, let’s take it to the next level.

## Launching a full Express server on Android ##
Let’s launch an Express server on our device, for this part of the guide we will need the files for the express server. I’ll be using ProjectRTC which is a WebRTC signaling server built on Node using Express. You can download the source here.
You will need to download the Node packages locally so you will need to have NPM installed on your computer since the Android device doesn’t have NPM and can’t download the required packages.
To install the Node packages you will need to cd into the folder that contains the package.json file in the root of the project and then execute the following command.

___npm i___

Once that command completes you should see a node_modules folder with thousands of files in it. Now we need to push all the contents of the root project folder to the device, again I’ll be using /data/local/tmp but feel free to place it anywhere you like.
My project folder is located at E:\sourcetree\ProjectRTC however you will need to change this to point to your folder, also this might take a while so feel free to get a snack while it copies.

___adb push “E:\sourcetree\ProjectRTC” “/data/local/tmp”___

And you can launch it with

___adb shell “node /data/local/tmp/ProjectRTC/app.js”___

If you see the text “Express server listening on port 3000” it means it’s working!

## Awesome!##
There you have it, you just launched Javascript code on an Android device. Now you can turn your phone into a fully-fledged webserver if you like! (Although I wouldn’t recommend it)

Thanks for taking the time to read this, I hope you found it helpful. I know I wished this was here when I originally had to get this working, it would have saved me a bunch of time. If you have any questions or comments don’t hesitate to drop me a line.
