# Android-JCenter

Deploy your Android library to JCenter, today. 

If you're an Android developer, you are comfortable with installing dependencies in your `build.gradle` file: `implementation 'androidx.core:core-ktx:1.3.0'`. JCenter is very popular amongst Android developers. It's so popular that when you create a new Android Studio project, you will notice that your project is setup automatically for you to download dependencies from JCenter. 

```
allprojects {
    repositories {
        google()
        jcenter() // <----------
    }
}
```

Anyone can deploy Android libraries into JCenter. It's highly recommended to do so, too. JCenter is (1) fast, (2) more secure then alternatives, (3) super easy to use since Android Studio projects are setup already to download from it. 

This project aims to make deploying to JCenter as simple and easy as possible. We do that by...
1. Providing the documentation needed to teach you everything you want to know about JCenter and deploying to it. 
2. Providing configuration files to get up and running, fast. 
3. Provide other tools you can use to make your deployment process even easier. 

> Tip: See the [FAQ](#faq) to learn more about what JCenter, Maven, Bintray, or anything else discussed in this document that you are unsure about. 

*Note: This project assumes that you are building an open source Android library. This project does not cover how to use deploy private Android libraries.*

# Why use Android-JCenter-Configs? 

The goals of this project are:
* **Don't be a black box.** Publishing Android libraries is a skill that every Android developer should be able to learn how to do if they choose to do so. You can easily read this document, use the config files, and not learn how it all works but if you want to learn how you can. 
* **Quick and easy, yet flexible.** The way this is done is by the config files using a collection of variables that you define. Some required, some optional. This allows you to be able to change just about anything you wish to do while yet being able to publish to JCenter with as little work as possible. 
* **Dependable.** Use as little gradle plugins/dependencies as possible and for the ones to use, only use ones that are popular and stable. 
* **Enough to get the job done.** This project will not include *every* possible detail about JCenter or deploying to it. This project is instead meant to give you enough information to make you feel confident and get the job done. If you want to learn even more, that's up to you to do on your own beyond this project. 

# Getting started 

The steps to publish your Android library to JCenter is...

- [ ] Create your Android library! Write some code! This project does not cover how to do that. [Here is a popular video](https://www.youtube.com/watch?v=RYiZZprUNJw) that goes over how to do this. *Note: The video is a little out-of-date but the concepts still apply today*
- [ ] [Create a Maven repository in Bintray](#create-maven-repository-in-bintray)
- [ ] [Include configuration files from this project into your project](#include-configuration-files)
- [ ] [Finishing steps](#finishing-steps)

### Create Maven repository in Bintray

To get your Android library into JCenter, you first need to create a [Bintray.com](https://bintray.com) account. 

Before we get into the next step, there are some keywords to go over. Repository, packages, and artifacts. As an Android developer, you are already quite familiar with these terms. When you look at a dependency that you added to your `build.gradle` file, the dependency is split into 3 sections:

```
implementation "com.levibostian.boquila:boquila-testing:1.0.0"
```

This line of code above should look familiar to you. The `com.levibostian.boquila` part is called the package. The `boquila-testing` part is called the artifact. The `1.0.0` is the version of the artifact that you want. 

How you decide to organize your Android library is up to you. Know these rules to help you decide:
1. A Maven repository contains 1 or more packages. You can choose to create 1 Maven repository to store *all* of your Android libraries in it or you can make 1 Maven repository for each of your different projects. That is up to you. 
2. A package is usually named after a domain that you own. `com.your-name.project-name`, `com.github.your-github-username.project-name`. It's good to create 1 package name per project. Suppose that you create an Android library project that is split apart into multiple modules. This is how popular projects like Retrofit does it where there is a "core" Android library module and "plugin" modules all for the same 1 project of Retrofit:
```
implementation "com.squareup.retrofit2:retrofit:2.9.0"
implementation "com.squareup.retrofit2:converter-moshi:2.9.0"
```

See how the core retrofit artifact and the moshi converter artifact both have the same package name? Use 1 package name per project to organize all artifacts together. 
3. Artifacts align with modules in your Android Studio project. You can create 1 or more artifacts per package name. Like with the Retrofit example above. 

---

Now that all of that is said, it's time to create a Maven repository in your Bintray.com account if you have not created one already. After you login to Bintray, you should see a button to "Add New Repository". When you get to this page, you will be asked a few questions...
* Public/private - Select public assuming this is an open source, public Android library. 
* Name - The name of your repository you would like to create. Name this repository whatever you want. Remember that you can host 1 or more packages in a repository. So name this repository "Android libraries", "Project name here", "OSS", or whatever you want. 
* Type - select Maven. 
You can fill in or leave the rest blank if you wish. 

Great! You have created your Bintray Maven repository. The last step to finish your Maven repository is to create packages in your repository. In Bintray, if you click on the name of your new Maven repository, you will see an option to "Add New Package". You will be asked a few questions...
* Name - This is something like `com.your-name.project-name`, `com.github.your-github-username.project-name`. Remember that you will store 1 or more artifacts in this package so this package name represents your project so the name should include the project name in it. 
* Licenses - select the open source licenses that you are using for your project. 
* Version control - Copy/paste the GitHub, BitBucket, GitLab URL to your project. 

Your done! Because you have a Maven repository and package does not mean that your library is available in JCenter yet. Next up we will go over how to upload your Android library to your new Bintray Maven repository. After that, you have to submit a request to have your Android library be included in JCenter. If your request gets approved by the Bintray team, then you are done!

### Include configuration files

After you have a Bintray Maven repository to upload your Android library to, it's time to build and upload your Android library. This project has tried to make this as simple and easy to do by providing Gradle configuration files for you to use in your project. 

*Note: This configuration file requires that you define the version of your Android library in the project's `gradle.properties` file like this:*
```
version=1.0.0
```
*The configuration file reads this value for you automatically.*

To use the Gradle configuration files, follow these steps. 

1. Define environment variables. 

Define environment variables on your machine or your CI server. 

* `BINTRAY_USERNAME`: The username of your Bintray account. 
* `BINTRAY_KEY`: API key for your Bintray account. Find this at [Bintray's edit profile page](https://bintray.com/profile/edit) > API key. 

2. Apply the configuration file. 

Android libraries are modules in an Android Studio project. Each module has it's own `build.gradle` file (in this doc we will call this the module `build.gradle` file) and your Android Studio project has a `build.gradle` file for your entire project (we call this the root `build.gradle` file). 

For every Android library that you want to publish to JCenter, open up the module's `build.gradle` file and add the following:

```
// Put this at the bottom of the file.
apply from: "https://raw.githubusercontent.com/levibostian/Android-JCenter/master/publish.gradle"
```

```
// Put this towards the top of the file
ext {
    artifact = "module-name" 
}
```

If you try to perform a Gradle sync at this time, you will encounter some errors. You need to do 1 more step before a sync can be successful. 

3. Define variables. 

The Gradle configuration file that you have installed into your module's `build.gradle` file above depends on some variables that you define in order for the file to do it's job. 

There are required variables and there are optional variables. You have the option of specifying these variables in your root `build.gradle` file (variable values that are the same for all of the modules in your project) or specifying some in the module's `build.gradle` file (for variable values that are unique to each of the modules). 

For the root `build.gradle` file, you define variables like this:
```
buildscript {
    ext.kotlin_version = "1.3.72"

    ext {
        variableName = 'Value of variable' 
    }
}
```

For module `build.gradle` files, you define variables like this:
```
ext {
    variableName = 'Value of variable' 
}
```

Here are the required variables that you must define:

```
ext {
    projectName = 'Boquila' // Bintray Repo name. 
    packageName = 'com.levibostian.boquila' // Bintray package name inside of the repo. 
    libraryDescription = 'Small, consistent, flexible way to work with remote config.'
    siteUrl = 'https://github.com/levibostian/Boquila-Android'
    gitUrl = 'https://github.com/levibostian/Boquila-Android.git'
    allLicenses = ["MIT"]
}
```

Here are optional variables you can define:
```
ext {
    overwritePackageVersions = false // if you have already deployed an artifact with the same version you are trying to deploy, allow it to overwrite or not. (default: false)
    publicDownloadNumbers = false // if you want to make the download numbers for your artifact to be public. (default: false)
}
```

4. Include plugins the configuration file depends on. 

In the root `build.gradle` file, include this:

```
buildscript {
    dependencies {
        ...

        classpath 'com.github.dcendents:android-maven-gradle-plugin:2.0'
        classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:1.8.0'
    }
}
```

Now, if you perform a Gradle sync the sync should perform successfully. 

### Finishing steps 

Now that you have uploaded your Android library to your own Maven repository, it's time to get your library in JCenter. 

This is quite easy. Go to Bintray.com > login > select your Maven repository > select the package name where you uploaded your Android library > then click the button "Add to JCenter". 

You will be asked a couple of questions...
* is pom project - Check this box. 
* Host my snapshot build artifacts on the OSS Artifactory at https://oss.jfrog.org - This is up to you if you want to enable this or not. 
* Comments - I like to type a small description here of my project and a link to the version control README explaining the project. 

Send! The bintray team will now go through your request. You will receive an email notification (if you have them enabled on your Bintray account) as well as a Bintray direct message once the team decides. You may get accepted or denied. Communicate with the team if there are issues. 

After you have been accepted you are officially in JCenter. That means that Android devs can now use your library with 1 line of code added to their `build.gradle` file. Quick and easy!

# FAQ

Q: What is JCenter? 
A: JCenter is the name of a Maven repository. It's a popular Maven repository that hosts thousands of Android libraries. Today, it's the recommended place to host your Android library for the public to use. 

Anyone can publish Android libraries to JCenter. To do so, [check out the section on creating a maven repository in Bintray](#create-maven-repository-in-bintray).

Q: What is a Maven repository? 
A: A Maven repository is a server that hosts Maven dependencies. Gradle is a tool that can download Maven dependencies and install them in your project for you. Because Gradle downloads Maven dependencies, you need to have a server to download these dependencies from, right? 

Q: Why should I use JCenter instead of JitPack.io, Maven Central, GitHub Packages? 
A: JCenter, JitPack.io, Maven Central, GitHub Packages are all Maven repository options that you can go with. However, the biggest difference is the ease of use of JCenter compared to the rest. 

When you create a brand new Android Studio project, Android Studio automatically adds the JCenter Maven repository to your Gradle configuration settings:
```
allprojects {
    repositories {
        google()
        jcenter() // <----------
    }
}
```

This means that if your Android library is hosted on JCenter, all developers have to do to use your Android library is add one line of code to their `build.gradle` file: `implementation "com.foo.foo:bar:1.0.0"` and it will download. Easy! 

If you want to use a Maven repository such as JitPack.io (a tool I enjoy to use but prefer JCenter for the ease of use aspect), you would require that developers add more to their gradle configuration in order to download your library:

```
allprojects {
	repositories {
		google()
        jcenter() 
		maven { url 'https://jitpack.io' } // <----------
	}
}
```

Sure, that's easy to do but it's still an extra step. 

I have not been able to find out why Google has made JCenter a built-in Maven repository for Android projects. [JCenter has even made it possible for security vulnerabilities to exist in packages.](https://twitter.com/jakewharton/status/1073102730443526144?lang=en). Luckily, issues have not come up recently that I am aware of and Google has made it a built-in repository so we all trust that it's the recommended choice. 

Overall, it's up to you to decide where you want to host your libraries. 
