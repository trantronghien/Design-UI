## Overview

When first building Android apps, many developers might start by relying on Model View Controller (MVC) patterns and usually end up writing most of the core business logic in activities or fragments.  The challenge is that writing tests that can validate the app's behavior is difficult to do because the code is often so closely tied to the Android framework and the various lifecycle events.  While automated UI testing could be written to validate individual activities or fragments, maintaining and running them over the long-term is often difficult to sustain.

Clean architecture principles, as espoused by Robert Martin (also known as "Uncle Bob"), attempt to focus the developer on thinking through the app's core functionality.  It does so by separating the architecture of app into three major layers: how the app shows the data to the user (**presentation layer**), what are the core functions of the app (**domain or use case layer**), and how the data can be accessed (**data layer**).  The presentation layer sits as the outermost layer, the domain layer sits in the middle layer, and the data layer resides in the inner layer.  

<img src="http://i.imgur.com/tJxzrx2.png" />

It's important to note that each layer has its own data model, and data can only be exchanged between layers and usually flows only in one direction (i.e. outer to inner, or inner to outer)  Anytime data needs to be exchanged, usually a converter is used to map one layer's model to another.  In this way, a boundary of separation is created that helps limit changes in one layer to cause unintended side effects in other layers.

At the data layer, every source (i.e. file, network, memory) of data should be represented using the repository data pattern.  There are many different ways of implementing this repository pattern, but the ultimate goal is to expose an interface that defines the different queries that need to be performed in order to abstract away the type of data source used.  The underlying implementations can therefore be swapped regardless of the type of data source used.

Clean architecture introduces more abstractions and attempts to apply [single responsibility principles](https://en.wikipedia.org/wiki/Single_responsibility_principle) in Android development.  While there may be concerns about this approach adding more complexity, slow performance, and poor testability, it has been shown to work successfully in production apps (see [this Droidcon talk](https://www.youtube.com/watch?v=-oZswd1j5H0) or [this Droidcon 2016 talk](https://www.youtube.com/watch?v=R89ufpJI3SY)).

### Migrating to Clean Architecture

If you're attempting to migrate towards clean architecture without necessarily rewriting your entire code base, your best approach is to first try to isolate and encapsulate your logic by moving it outside of your Activities or Fragments.  Moving towards the Model-View-Presenter (MVP), which is a popular way of structuring your presentation layer, should probably be your first step.  There is no exact way of implementing this approach, so here are a few recommended links:

* <https://github.com/Arello-Mobile/Moxy/>
* <http://www.tinmegali.com/en/model-view-presenter-android-part-1/>
* <https://medium.com/mobiwise-blog/android-basic-project-architecture-for-mvp-72f4b33252d0>
* <http://antonioleiva.com/mvp-android/>
* <https://github.com/konmik/konmik.github.io/wiki/Introduction-to-Model-View-Presenter-on-Android>
* <http://thefinestartist.com/android/mvp-pattern>
* <https://www.youtube.com/watch?v=BlkJzgjzL0c>
* <https://github.com/antoniolg/androidmvp>

## Templates

The following template projects are built to act as a starting point for a preferred architecture:

 * <https://github.com/dmilicic/Android-Clean-Boilerplate>
 * <https://github.com/android10/Android-CleanArchitecture>
 * <https://github.com/googlesamples/android-architecture>

## References

Clean architecture:

* <https://medium.com/@dmilicic/a-detailed-guide-on-developing-android-apps-using-the-clean-architecture-pattern-d38d71e94029>
* <https://www.youtube.com/watch?v=BlkJzgjzL0c>
* <http://fernandocejas.com/2014/09/03/architecting-android-the-clean-way/>
* <http://fernandocejas.com/2015/07/18/architecting-android-the-evolution/>
* <https://github.com/dmilicic/android-clean-sample-app>
* <https://speakerdeck.com/romainpiel/ingredients-for-a-healthy-codebase/>
* <http://macoscope.com/blog/model-view-presenter-architecture-in-android-applications/>
* <https://github.com/macoscope/RoomBookerMVP/tree/master/mvp/src/main/java/com/macoscope/>

MVVM Pattern:
* <https://labs.ribot.co.uk/approaching-android-with-mvvm-8ceec02d5442>

