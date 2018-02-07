---
title: Building a React Native app (IOS/Andrioid) using Expo on a windows machine
date: 2018-02-06 16:01:09
tags:
---

Some of the interesting dilemmas many developers and teams face while building a mobile application are:
1. What technology stack to use ?
2. Should we have 2 seperate stacks for android vs ios app ?
3. Should we invest in building a cross platform app and thus enable sharing some code across ?
4. Do the existing cross platform frameworks support all my needs ?
5. If I am building an enterprise app, will I be able to authenticate using Active directory ?

My needs were simple:
1. I want to able to build a IOS and ANDRIOD app.
2. I should be able to authenticate users using the Azure Active directory.
3. and ofcourse, I wanted to do this without needing a MAC.
4. My app mostly had some workflows for which I had APIs. These APIs were mostly the same one which my Website uses.
5. Push notifications.

I am blogging as I am learning these features one by one.

# Start:

* Download Expo Client on your phone:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Expo XDE](https://github.com/mahesh8488/mahesh8488.github.io/blob/master/images/expoClient.PNG?raw=true)

* Install Expo XDE:   Download [here](https://github.com/expo/xde/releases)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Expo XDE](https://github.com/mahesh8488/mahesh8488.github.io/blob/master/images/expoXde.PNG?raw=true)

* Create a new project

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Expo XDE](https://github.com/mahesh8488/mahesh8488.github.io/blob/master/images/newProject.PNG?raw=true)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Expo XDE](https://github.com/mahesh8488/mahesh8488.github.io/blob/master/images/newProject2.PNG?raw=true)



* Expo starts a new tunnel. Scan the QR code or copy the tunnel link to load the newly created APP on your phone.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Expo XDE](https://github.com/mahesh8488/mahesh8488.github.io/blob/master/images/appInit.PNG?raw=true)



## Building and deploying
You can use any texteditors such as NotePad++ to edit the react-native project created by Expo.
Once the app is loading on your phone, building and deploying is easy. Just save the changes made to the code and the App refreshes automatically.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Expo XDE](https://github.com/mahesh8488/mahesh8488.github.io/blob/master/images/phoneApp.jpg?raw=true)



---
---
Coming up next: **Authentication, Tabs, Push Notification, Publishing App**