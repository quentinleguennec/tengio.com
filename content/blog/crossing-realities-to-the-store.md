+++
id = "0016"
date = "2017-05-23T13:00:00+00:00"
title = "Crossing realities: To the store!"
description = "Crossing realities is a series of blog posts where I share my knowledge and experiences as I explore the possibilities of VR. Here is so tips to have your app accepted on the Daydream Play Store."
author = "quentin"
tags = [ "Unity", "Daydream", "VR" ]
+++


![article-img-centered](/img/blog/0016/to_the_shore.jpg "Parley!")

### Crossing realities tutorial summary
1. [The beginning of a VR journey](/blog/crossing-realities-the-beginning-of-a-vr-journey/)
2. [Baby steps into VR](/blog/crossing-realities-baby-steps-into-vr/)
3. [Ready to Daydream!](/blog/crossing-realities-ready-to-daydream/)
4. [Tips & Tricks](/blog/crossing-realities-tips-and-tricks/)
5. [To the store!](/blog/crossing-realities-to-the-store/)**TODO: UPDATE OTHER**
5. To the store!

## Introduction

That's it. You did it. Your app is finally ready for the world to enjoy! After months of constant efforts, improvements and careful crafting your work finally comes to life! Now you feel the urge to share it with everyone, and what place could be better than the official Google Daydream store? So you jump into the ship armed with optimism, knowing that everyone will love your app, and set sail to the store. But then you see a dark spot running under the blue water, racing toward your vessel. And in a thunder of broken wood and torn expectations you see those words written in the blue sky as your ship slowly sinks into the abyss: *"Thanks for submitting your app for Daydream. We reviewed your app and noticed some eligibility issues."*

![article-img-centered](/img/blog/0016/outlaw.jpg "No you don't!")

Well, what a downer... Now you just want to grab a harpoon and start a meaningless quest for revenge. But let's first sit down and reflect before we try to shoot the messenger.

This mighty beast, also called "[Daydream App Quality Requirements](https://developers.google.com/vr/distribute/daydream/app-quality "Daydream App Quality Requirements")", is both a curse and a blessing. While at first it prevents you from reaching the store, it also protects it from poorly crafted apps, avoiding yours to be drown in a sea of mediocrity when you do reach the store.

![article-img-centered](/img/blog/0016/baby_kraken.jpg "Release the kraken!")

It is always very frustrating to see your app rejected (and believe me I know how it feels...). And often the reasons stated are not very clear, leading to more frustration. But going all gun blazing against Google is a bad idea, its power level is *over 9000* ! So let's just make our app even better so it passes all the requirements. Besides, most of those requirements are here to make sure users don't get sick (cybersickness and the like) and we for sure don't want any of this!

So let's rebuild our ship harder, better, faster, stronger (one more time)!

 I will share here some of the improvements I had to do for our game [Oz Chicken Slayer](https://play.google.com/store/apps/details?id=com.tengio.oz_chicken_slayer "Oz Chicken Slayer on Google Playstore") to be accepted.

## Never published an app on Google Play Store before?

This tutorial will not cover the process of publishing an app on Google Play Store, but fear not! If you never did it before you can follow this [cool tutorial](http://eim-games.com/publish-google-play-store-unity/2016/06/09 "Publish your app on Google Play") by Kendra Corpier.

Also, remember that if you want to publish a Daydream app on Google Play Store you need to set images for your App icon. See my [previous blog post](/blog/crossing-realities-ready-to-daydream/#while-building-an-app-for-google-play-store "Set app icon").

# Content:
1. [UX-D2: App maintains head tracking](#scene-transition)
2. [UX-C4: Cursor displays at the same depth as objects being targeted](#thou-shalt-always-draw-the-reticle-on-objects)
3. [FN-A2: App closes from VR correctly](#closing-your-app)
4. [UX-C2: App uses hand preference](#handily-handle-handedness)


## Scene transition

One point for which our app was rejected was the following:

*"The app does not maintain head tracking: The app loses head tracking when loading into a new game. Your app must maintain head-tracking."*

In most cases losing head tracking is a really really bad experience for your users. I tried a few prototype of VR headsets in the past, and some of them had tracking issues (like spinning you all around the place). I'm rather resistant to cybersickness (my years of hardcore FPS gaming finally pay off :p) but still some of those tracking issues really turned my stomach upside down.

Here the problem was that our was freezing for about 100ms when loading a new scene, so if you were turning your head at this moment the image would not follow your gaze. I was considering this to be OK because it was only 100ms and rather hard to fix. But keep in mind that what seems OK for a dev might be considered bad by users. And Google specifically requires that *"UX-D2: App maintains head tracking"*.

**TODO???: add gif without fade out/in.**

Well OK, but how am I suppose to prevent this freeze? Unity is not really good in this domain, as much as I know there is no way to avoid the freeze between scene loading. The best you could do is reduce it so that it becomes unnoticeable (10-20ms), but its often very hard to do if the scene you load contains many items. Unity offers a [*LoadSceneAsync*](https://docs.unity3d.com/ScriptReference/SceneManagement.SceneManager.LoadSceneAsync.html "SceneManager.LoadSceneAsync") function, but, like most asynchronous process, it's more difficult to manage than the standard [*LoadScene*](https://docs.unity3d.com/ScriptReference/SceneManagement.SceneManager.LoadScene.html "SceneManager.LoadScene") function, and it does not remove the freeze. When I first moved from using *LoadScene* to *LoadSceneAsync* it did reduced the freeze a little, but not enough.

The solution I came up with is actually a "sweep under the carpet" technic, we can't fix the issue so we make sure no one can see it, and the result is quite satisfying! Instead of trying to remove the freeze we do a fade out to black, then the scene load while the screen is fully black, and when the new scene is loaded we fade in. This removes the head tracking issue because users can't see anything, so they can't feel the freeze. It would have been cool to have an icon during the black screen, but then the freeze would be noticeable because the icon would not follow the user gaze during the freeze.  

![article-img-centered](/img/blog/0016/scene_transition.gif "Scene transition with fade out/in")

**TODO: add all code to tips&tricks post and add link**

## Thou shalt always draw the reticle on objects

*"UX-C4: Cursor displays at the same depth as objects being targeted."*

This is actually one of the cases where doing a game for VR is way more complicated than doing the same game for a classic 2D screen. There is nothing more simple than creating a reticle in any regular game, you just draw a cross or point in the middle of the screen as overlay and voila! But in VR you actually have a sense of depth (which makes VR awesome :3), so you can't just draw something as overlay, because it would feel like the reticle is only 2 centimeters away from your face. And you can't focus properly on something so close to your face (try to look at the tip of your nose and see by yourself).

![article-img-centered](/img/blog/0016/squint.jpg "Squinting")

So let's just draw it 15 meters from the camera to fix that! But then we run into an other issue… In a standard game everything is draw on a 2D screen, so you don't have depth perception (our brain is really good at faking depth so we feel that we do, but that's not real depth, see [Optical Illusions](https://en.wikipedia.org/wiki/Optical_illusion#Depth_and_motion_perception "Optical illusion on Wikipedia")). So if you have an object that's visible through all other objects (like your reticle), even if the reticle is farther than the object you are looking at, on a 2D screen it will feel like it's just on top of the object (rather than behind, where it really is). But in VR you have depth perception! So if you have an object 5 meters from you and the reticle is drawn 10 meters behind the object then it will be impossible to focus properly on the reticle (you will be squinting at both at the same time). And this is very uncomfortable.

![article-img-centered](/img/blog/0016/2d_to_3d_depth.jpg "Depth perception")

You can see on the previous image that the 3 colored cubes seem to be all at the same distance when looking through the camera. But they are not actually at the same distance at all! (as you can see on the scene view). If you were to use one of those cubes as your reticle for a non VR game it would be fine, but in VR you wouldn't be able to focus properly on the red and blue ones (green is fine because it's at the same level as the big grey cube).

**Note 1:** For those wondering I used a special material for the 3 colored cube so that you can see them through the big grey cube.

**Note 2:** You can actually see that the cubes are not at the same level by observing that there is a very slight difference between the left and right eye image: the blue cube slightly overlaps the green one on the left image but not on the right image. That's this small difference that creates the awesome "depth" effect of VR!

So you have to draw your reticle exactly on top of the objects you're pointing at, whether it's UI or meshes. Best way around this one is to add colliders to all the objects (and UIs!) in your scene (they can be trigger colliders), this way you're sure the raycast coming from the controller is always stopped by the objects.

![article-img-centered](/img/blog/0016/scene_colliders.jpg "Colliders everywhere!!!")
<center>Colliders everywhere!!!</center>


## Thou shalt always use the controller to interact with UIs.

*"UX-C1: Controller must be used as a laser pointer when clicking on UI targets".*

This means that you can't use gaze to select UIs, you have to use the controller (as a laser pointer). If you designed your game to always use the controller that shouldn't be an issue :).



At this point it's really worth considering if you **really** want a reticle in your game/app. The other alternative is to draw a laser coming out of the pointer, and have it extend for some distance (like 20-30 meters).

## Closing your app

*"FN-A2: App closes from VR correctly".*

Our app was reject for this one, and it took me a while to understand why. Let's read the big description:

*"Pressing the Close X button or the Back button in the system bar (accessible if the user removes their phone from the viewer) should immediately pause the application, including any audio that was playing. User and app state should be handled in a reasonable way. The user should be taken to the 2D phone launcher."*

To allow the user to close with the X button you can add this code in the update method of one of your persistent objects:

```csharp
private void Update() {
#if UNITY_ANDROID
    if (Input.GetKeyDown(KeyCode.Escape)) {
        Application.Quit();
    }
#endif
}
```

But our app already had this, so I guessed the part that was failing was: "The user should be taken to the 2D phone launcher". Now this sentence is rather vague, I'm not exactly sure what the "2D phone launcher" is. My best guess here is that it means the app should exit from VR to anything 2D (like the Android launchpad (TODO check what real name is). When launched from the launch pad our app was closing to the launchpad (so just fine). Same when launched from Daydream app in 2D mode, the app close and go back to Daydream app in 2D. But when launched from Daydream app *in VR mode*, our app was closing and going back to Daydream app *in VR mode*.

The stated reason for the rejection was the following: *"The app does not exit when the user selects the back or close button. The app must exit to 2D when the Back button is pressed, instead of Daydream Home"*. This seems to confirm my guess.

While most requirements make sense I think this one doesn't. You shouldn't have to care what app was launched before yours, and you certainly shouldn't be allowed to close it, that's going against Android (and good programming in general).

I read all the doc I could find to see if the GVR API had something to manage this but I didn't find anything. So I just tried random functions with names that remotely suggested they could be helpful.

![article-img-centered](/img/blog/0016/dilbert.png "From Dilbert comic")

And I did find that calling `UnityEngine.VR.VRSettings.enabled = false;` just before `Application.Quit();` was causing the Daydream app to close itself. This is a ugly hack but the requirement is ugly too so it's a match! And so we passed the requirement! Let's just hope that Google will fix this requirement so that it makes sense (if anyone from Google is reading this please pass the word :D).

```csharp
private void Update() {
#if UNITY_ANDROID
    if (Input.GetKeyDown(KeyCode.Escape)) {
        UnityEngine.VR.VRSettings.enabled = false;
        Application.Quit();
    }
#endif
}
```

## Handily handle handedness

*UX-C2: App uses hand preference*

Our app was rejected for this one too at the beginning (when you had to handle this manually). But now GVR SDK handles this for you if you use the provided arm model! Hooray!


TODO
[Crossing Realities: Baby steps into VR](/blog/crossing-realities-baby-steps-into-VR/)
