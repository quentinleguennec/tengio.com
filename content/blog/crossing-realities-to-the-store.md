+++
id = "0016"
date = "2017-05-23T13:00:00+00:00"
title = "Crossing realities: To the store!"
description = "Crossing realities is a series of blog posts where I share my knowledge and experiences as I explore the possibilities of VR. Here are some tips to get your app accepted on the Daydream Play Store."
author = "quentin"
tags = [ "Unity", "Daydream", "VR" ]
+++


![article-img-centered](/img/blog/0016/to_the_shore.jpg "Parley!")

### Crossing realities tutorial summary
1. [The beginning of a VR journey](/blog/crossing-realities-the-beginning-of-a-vr-journey/)
2. [Baby steps into VR](/blog/crossing-realities-baby-steps-into-vr/)
3. [Ready to Daydream!](/blog/crossing-realities-ready-to-daydream/)
4. [Tips & Tricks](/blog/crossing-realities-tips-and-tricks/)
5. To the store!

## Introduction

That's it. You did it. Your app is finally ready for the world to enjoy! After months of constant efforts, improvements and careful crafting your work finally comes to life! Now you feel the urge to share it with everyone, and what place could be better than the official Google Daydream store? You jump into the ship armed with optimism, knowing that everyone will love your app, and set sail to the store. But then you see a dark spot running under the blue water, racing toward your vessel. And in a thunder of broken wood and torn expectations you see those words written in the blue sky as your ship slowly sinks into the abyss: *"Thanks for submitting your app for Daydream. We reviewed your app and noticed some eligibility issues."*

![article-img-centered](/img/blog/0016/outlaw.jpg "No you don't!")

Well, what a downer... Now you just want to grab a harpoon and start a meaningless quest for revenge. But let's first sit down and reflect before we try to shoot the messenger.

This mighty beast, also called "[Daydream App Quality Requirements](https://developers.google.com/vr/distribute/daydream/app-quality "Daydream App Quality Requirements")", is both a curse and a blessing. While at first it prevents you from reaching the store, it also protects it from poorly crafted apps, avoiding yours to be drowned in a sea of mediocrity when you do reach the store.

![article-img-centered](/img/blog/0016/baby_kraken.jpg "Release the kraken!")
<center>Release the Kraken!</center>

It is always very frustrating to see your app rejected (and believe me I know how it feels...). And often the reasons stated are not very clear, leading to more frustration. But going all gun blazing against Google is a bad idea, its power level is *over 9000* ! So, let's just make our app even better so it passes all the requirements. Besides, most of those requirements are here to make sure users don't get sick (cybersickness and the likes) and we for sure don't want any of this! Let's just rebuild our ship harder, better, faster, stronger (one more time)!

 I will be sharing here some of the improvements I had to do for our game [Oz Chicken Slayer](https://play.google.com/store/apps/details?id=com.tengio.oz_chicken_slayer "Oz Chicken Slayer on Google Playstore") to be accepted on Daydream Play Store.

## Never published an app on Google Play Store before?

This tutorial will not cover the process of publishing an app on Google Play Store, but fear not! If you have never done it before you can follow this [cool tutorial](http://eim-games.com/publish-google-play-store-unity/2016/06/09 "Publish your app on Google Play") by Kendra Corpier.

Also, remember that if you want to publish a Daydream app on Google Play Store you need to set images for your App icon. See my [previous blog post](/blog/crossing-realities-ready-to-daydream/#while-building-an-app-for-google-play-store "Set app icon").


# Content:
1. [UX-D2: App maintains head tracking](#scene-transition)
1. [UX-C4: Cursor displays at the same depth as objects being targeted.](#thou-shalt-always-draw-the-reticle-on-objects)
3. [FN-A2: App closes from VR correctly](#closing-your-app)
4. [UX-C2: App uses hand preference](#handily-handle-handedness)


## Scene transition

One point for which our app was rejected was the following:

*"The app does not maintain head tracking: The app loses head tracking when loading into a new game. Your app must maintain head-tracking."*

In most cases losing head tracking is a really *really* bad experience for your users. I have tried a few prototypes of VR headsets in the past, and some of them had tracking issues (like spinning you all around the place). I'm rather resistant to cybersickness (my years of hardcore FPS gaming finally pay off :p) but still some of those tracking issues really turned my stomach upside down.

With our game, the problem was a freeze of about 100ms when loading new scenes. And if you were turning your head at this moment the image would not follow your gaze. I was considering this to be OK because it was only 100ms and rather hard to fix. But keep in mind that what seems OK for a dev might be considered bad by users. And Google specifically requires that *"UX-D2: App maintains head tracking"*.

![article-img-centered](/img/blog/0016/objection.jpg "Objection!")
<center>Google 1. Me 0...</center>

Well OK, but how am I supposed to prevent this freeze? Unity is not very good in this domain, as much as I know there is no way to avoid the freeze between scene loading. The best you could do is reduce it so that it becomes unnoticeable (10-20ms), but it's often very hard to do if the scene you load contains many items. Unity offers a [*LoadSceneAsync*](https://docs.unity3d.com/ScriptReference/SceneManagement.SceneManager.LoadSceneAsync.html "SceneManager.LoadSceneAsync") function, but, like most asynchronous process, it's more difficult to manage than the standard [*LoadScene*](https://docs.unity3d.com/ScriptReference/SceneManagement.SceneManager.LoadScene.html "SceneManager.LoadScene") function, and it does not remove the freeze. When I first moved from using *LoadScene* to *LoadSceneAsync* it did reduce the freeze a little, but not enough.

The solution I came up with is a "sweep under the carpet" technic, we can't fix the issue so we make sure no one can see it, and the result is quite satisfying! Instead of trying to remove the freeze we do a fade out to black, then the scene will load while the screen is fully black, and when the new scene is loaded we fade in. This removes the head tracking issue because users can't see anything, so they can't feel the freeze. It would have been cool to have an icon during the black screen, but then the freeze would be noticeable because the icon would not follow the user gaze during the freeze.  

![article-img-centered](/img/blog/0016/scene_transition.gif "Scene transition with fade out/in")
<center>Fade transition between scenes</center>

I posted the code on another blog post, and there is even a Github repo with a quick demo. [==> HERE <==](/blog/crossing-realities-tips-and-tricks/#scene-transition-in-unity-for-vr-preserve-head-tracking) to the good stuff!


## Thou shalt always draw the reticle on objects

*"UX-C4: Cursor displays at the same depth as objects being targeted."*

This is one of the cases where creating a game for VR is way more complicated than making the same game for a classic 2D screen. There is nothing simpler than creating a reticle in any regular game, you just draw a cross or point in the middle of the screen as overlay and voila! But in VR you do have a sense of depth (which makes VR awesome :3), so you can't just draw something as overlay, because it would feel like the reticle is only 2 centimetres away from your face. And you can't focus properly on something so close to your face (try to look at the tip of your nose and see for yourself).

![article-img-centered](/img/blog/0016/squint.jpg "Squinting")

Then let's just draw it 15 metres from the camera to fix that! But if we do that we run into another issue... In a standard game everything is draw on a 2D screen, so you don't have depth perception (our brain is very good at faking depth so we feel that we do, but that's not real depth, see [Optical Illusions](https://en.wikipedia.org/wiki/Optical_illusion#Depth_and_motion_perception "Optical illusion on Wikipedia")). If you have an object that's visible through all other objects (like your reticle), even if the reticle is farther than the object you are looking at, on a 2D screen it will feel like it's just on top of the object (rather than behind, where it really is). But in VR you have depth perception! If you have an object 5 metres from you and the reticle is drawn 10 metres behind the object then it will be impossible to focus properly on the reticle (you will be squinting at both at the same time). And this is very uncomfortable.

![article-img-centered](/img/blog/0016/2d_to_3d_depth.jpg "Depth perception")

You can see on the previous image that the 3 colored cubes seem to be all at the same distance when looking through the camera. But they are not actually at the same distance at all! (as you can see on the scene view). If you were to use one of those cubes as your reticle for a non-VR game it would be fine, but in VR you wouldn't be able to focus properly on the red and blue ones (green is fine because it's at the same level as the big grey cube).

**Note 1:** For those wondering, I used a special material for the 3 colored cubes to have them appear through big grey cube.

**Note 2:** You can actually see that the cubes are not at the same level by observing the very slight difference between the left and right eye image: the blue cube slightly overlaps the green one on the left image but not on the right image. That's this small difference that creates the awesome "depth" effect of VR!

So, you have to draw your reticle exactly on top of the objects you're pointing at, whether it's UI or meshes. Best way around this one is to add colliders to all the objects (and UIs!) in your scene (they can be trigger colliders), this way you're sure the *Raycast* coming from the controller is always stopped by the objects.

![article-img-centered](/img/blog/0016/scene_colliders.jpg "Colliders everywhere!!!")
<center>Colliders everywhere!!!</center>


## Closing your app

*"FN-A2: App closes from VR correctly".*

Our app was rejected because of this requirement, and it took me a while to understand why. Let's read the big description:

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

But our app already had something similar to this, so I guessed the part that was failing was: "The user should be taken to the 2D phone launcher". Now this sentence is rather vague, I'm not exactly sure what the "2D phone launcher" is. My best guess here is that it means the app should exit from VR to anything 2D (like your Home screen). When launched from the Home screen our app was closing to the home screen (so just fine). Same when launched from Daydream app in 2D mode, the app close and go back to Daydream app in 2D. But when launched from Daydream app *in VR mode*, our app was closing and going back to Daydream app *in VR mode*.

The stated reason for the rejection was the following: *"The app does not exit when the user selects the back or close button. The app must exit to 2D when the Back button is pressed, instead of Daydream Home"*. This seems to confirm my guess.

![article-img-centered](/img/blog/0016/daydream_close.gif "Daydream app should close like this when your app close itself")
<center>Daydream app should close like this when your app close itself</center>

While most requirements make sense, I think this one doesn't. You shouldn't have to care what app was launched before yours, and you certainly shouldn't be allowed to close it, that's going against Android (and good programming in general). As a matter of fact, you can't force Daydream app to close, it will only close itself if it wants to. And I guess they programmed it to close itself when it senses the app that was launched is closing.

The main issue on our side was that instead of closing the app I was pushing it back in the Android stack. So, instead of fully closing when pressing the back button, it was pausing itself and going in background (like most Android apps do). But then Daydream app was not closing because it was still waiting for our app to fully close! I removed this from our code and just used the `Application.Quit()` instead (see above). But we had some more trouble, because, at this time, it was not enough (Daydream app was still not closing). I found a hack around, but, as of 01/06/17, calling `Application.Quit()` seems to work fine again! I guess they fixed Daydream app since the time we had this bug, so we don't have to hack our way around anymore! :)

<details>
<summary>Just for the record here is the hack I used at the time (click to reveal):</summary>

**Note: You shouldn't need this anymore!**

I read all the doc I could find to see if the GVR API had something to manage this but I didn't find anything. So, I just tried random functions with names that remotely suggested they could be helpful.

![article-img-centered](/img/blog/0016/dilbert.png "From Dilbert comic")

And I did find that calling `UnityEngine.VR.VRSettings.enabled = false;` just before `Application.Quit();` was causing the Daydream app to close itself. This is an ugly hack but the requirement is ugly too so it's a match! And we passed the requirement! I'm glad we don't need it anymore though ^^

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
</details>


## Handily handle handedness

*UX-C2: App uses hand preference*

Our app was rejected for this one too at the beginning (when you had to handle this manually). But now GVR SDK handles this for you if you use the provided arm model! Hooray!

If you want to handle it personally you can use [*OnApplicationFocus()*](https://docs.unity3d.com/ScriptReference/MonoBehaviour.OnApplicationFocus.html "MonoBehaviour.OnApplicationFocus") to know when the *Home* button was pressed and the user returned to Daydream dashboard (that's when they might change the handedness). Then just check for [*GvrSettings.Handedness*](https://developers.google.com/vr/unity/reference/class/gvr-settings "GvrSettings") and update accordingly.

```csharp
private void OnApplicationFocus(bool gotFocus) {
    if (gotFocus) {
        // Do your updates here.
    }
}
```

## That's all for now!

I will update this article if we run into more issues, let's hope we don't! ^^
