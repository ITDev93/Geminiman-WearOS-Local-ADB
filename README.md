<div align="center">
	<br>
		<img src="img/header.svg" width="800" height="400">
	<br>
	<h3>...Don't forget to show some support if you are happy with what I am doing, it is highly appreciated...</h3>
</div>

---

# ⌚ Geminiman WearOS Local ADB

**Geminiman WearOS Local ADB** lets users run ADB commands directly from their Wear OS watch after setup.

This page is for developers who want to integrate with the app by sending ADB command requests using a deep link Intent.

> This repository is for documentation only.
> The application source code, local ADB implementation, and internal build process are not open source.

---

## 📥 Download

Get the app from Google Play:

[Download on Google Play](https://play.google.com/store/apps/details?id=com.geminiman.wearos.localadb&hl=en)

---

## ❤️ Support Development

If this app helps your workflow, you can support development here:
 
 + PayPal: https://paypal.me/dante63
 + Patreon: https://www.patreon.com/xda_dante63
 + Google Play: https://play.google.com/store/apps/details?id=com.geminiman.watchfaces

---

## What the App Does

Geminiman WearOS Local ADB allows ADB commands to be prepared, reviewed, saved, and executed directly from a Wear OS watch.

It is built for:

* Developers that need to run adb locally
* Wear OS power users
* Apps that need to guide users through ADB permission commands
* Tools that want to send safe command requests to the watch

The phone app is optional. The watch app can work on its own, while the phone app is mainly used to make command management easier from a larger screen.

---

## Key Features

### 🔌 Local ADB on Wear OS

Run ADB shell commands directly from the watch using a local ADB setup.

---

### 📡 Pair and Connect from the Watch

Users can pair with wireless debugging or reconnect to an existing debugging session directly from the watch.

---

### ⚡ Saved Commands

Users can create reusable commands for common actions, such as:

* Checking device information
* Listing packages
* Granting permissions
* Running shell commands
* Debugging Wear OS behavior

---

### 📥 Pending Command Queue

External command requests are not executed immediately.

When another app sends a command to Geminiman WearOS Local ADB, the command is added as a pending request. The user can then review it and choose whether to:

* Run it
* Save a copy of it
* Discard it

This protects users from blindly executing commands from external apps or links.

---

### 🧩 Batch Pending Commands

Users can review multiple pending commands, select what they want to run or discard, and then execute selected commands together.

---

### 📄 Command Output on Watch

Command results can be viewed directly inside the app after execution.

---

### 🛡️ Spam Guard Protection

Repeated external command requests are limited to reduce unwanted prompts and spam.

---

### 🎛️ Watch-Friendly Interface

The app is designed for Wear OS screens with:

* Clear controls
* Full-screen dialogs
* Rotary scrolling support
* Watch-friendly layouts

---

## Developer Integration

As mentioned, other apps can send command requests to Geminiman WearOS Local ADB using a deep link Intent.

This is useful when your app needs the user to run an ADB command manually, for example:

* Granting a special permission
* Enabling a setting
* Reading device information
* Running a debug command
* Helping the user fix a configuration issue

Instead of asking the user to type the command manually, or guide the user through different ADB apps, your app can send the command request to Geminiman WearOS Local ADB.

The user still has full control and must approve the command before it runs.

---

## Deep Link Format

```text
geminiman://command?title=TITLE&cmd=COMMAND&args=ARGS&description=DESCRIPTION&caller=CALLER_NAME
```

---

## Parameters

| Parameter     | Required | Description                                             |
| ------------- | -------: | ------------------------------------------------------- |
| `cmd`         |      Yes | The command to run                                      |
| `args`        |       No | Extra command arguments                                 |
| `title`       |       No | A readable title shown to the user                      |
| `description` |       No | Explanation shown to the user                           |
| `caller`      |       No | Name of the app, tool, or developer sending the request |

---

## Example Link

```text
geminiman://command?title=SDK%20Version&cmd=getprop&args=ro.build.version.sdk&description=Show%20Android%20SDK%20version&caller=My%20App
```

This sends a pending command request to show the Android SDK version.

---

## Java Example

```java
Uri uri = new Uri.Builder()
        .scheme("geminiman")
        .authority("command")
        .appendQueryParameter("title", "SDK Version")
        .appendQueryParameter("cmd", "getprop")
        .appendQueryParameter("args", "ro.build.version.sdk")
        .appendQueryParameter("description", "Show Android SDK version")
        .appendQueryParameter("caller", "My App")
        .build();

Intent intent = new Intent(Intent.ACTION_VIEW, uri);
intent.addCategory(Intent.CATEGORY_BROWSABLE);

startActivity(intent);
```

---

## Kotlin Example

```kotlin
val uri = Uri.Builder()
    .scheme("geminiman")
    .authority("command")
    .appendQueryParameter("title", "SDK Version")
    .appendQueryParameter("cmd", "getprop")
    .appendQueryParameter("args", "ro.build.version.sdk")
    .appendQueryParameter("description", "Show Android SDK version")
    .appendQueryParameter("caller", "My App")
    .build()

val intent = Intent(Intent.ACTION_VIEW, uri).apply {
    addCategory(Intent.CATEGORY_BROWSABLE)
}

startActivity(intent)
```

---

## Example: Request a Permission Grant Command

A common use case is helping the user grant a permission that normally requires ADB.

Example:

```text
geminiman://command?title=Grant%20Permission&cmd=pm&args=grant%20com.example.app%20android.permission.WRITE_SECURE_SETTINGS&description=Grant%20WRITE_SECURE_SETTINGS%20permission&caller=Example%20App
```

Java:

```java
Uri uri = new Uri.Builder()
        .scheme("geminiman")
        .authority("command")
        .appendQueryParameter("title", "Grant Permission")
        .appendQueryParameter("cmd", "pm")
        .appendQueryParameter("args", "grant com.example.app android.permission.WRITE_SECURE_SETTINGS")
        .appendQueryParameter("description", "Grant WRITE_SECURE_SETTINGS permission")
        .appendQueryParameter("caller", "Example App")
        .build();

Intent intent = new Intent(Intent.ACTION_VIEW, uri);
intent.addCategory(Intent.CATEGORY_BROWSABLE);

startActivity(intent);
```

---

## Important Behavior

Commands received from external apps or links are treated as **pending requests**.

They are not executed blindly.

The user must open/review the request inside Geminiman WearOS Local ADB and choose what to do with it.

This is intentional for safety.

---

## URL Encoding

When building links manually, make sure values are URL encoded.

For example, spaces should become:

```text
%20
```

Recommended approach:

* Use `Uri.Builder` in Android
* Avoid manually concatenating long URLs
* Encode command arguments properly

---

## Recommended Integration Flow

```text
1. Your app detects that an ADB command is needed
2. Your app explains to the user why the command is needed
3. Your app opens geminiman://command with the command details
4. Geminiman WearOS Local ADB receives the request
5. The command appears as a pending command
6. The user reviews it
7. The user chooses whether to run, save, or discard it
```

---

## Notes for Developers

Please keep command requests clear and understandable.

Recommended:

* Use a short readable `title`
* Add a helpful `description`
* Add your app name in `caller`
* Avoid sending dangerous or unclear commands
* Do not spam repeated command requests
* Do not assume the command will run automatically
* Do not assume the user will approve the request

Bad example:

```text
title=Fix
description=Run this
```

Better example:

```text
title=Grant Notification Listener Permission
description=Allows Example App to read notification events after user approval
caller=Example App
```

---

## Security Notice

ADB commands can affect apps, settings, permissions, and device behavior.

Geminiman WearOS Local ADB is designed to keep the user in control by requiring review before external commands are executed.

Developers should only send commands that are:

* Clearly explained
* Needed for the feature
* Safe for the user to understand
* Intended for devices the user owns or manages

---

## Requirements

To use the app, the user needs:

* A Wear OS device
* Developer options enabled
* Wireless debugging enabled
* Basic knowledge of ADB commands

---

## Disclaimer

Geminiman WearOS Local ADB is not affiliated with Google, Android, Wear OS, or any device manufacturer.

Use ADB responsibly. The developer is not responsible for misuse, data loss, device issues, or commands executed by the user.
