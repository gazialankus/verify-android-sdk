#Nexmo Verify SDK
	Nexmo Verify SDK(beta version) for Android.

You use Nexmo Verify to check that a person has access to a specific phone number. 

To complete phone verification:
1. Verify delivers a PIN as an SMS and/or TTS (Text-to-speech) call to your user. 
2. The user enters this PIN into your App or website. 
3. You use Verify to check that the PIN entered by your user is the one you sent. 

Using the Nexmo Verify SDK for Android you easily integrate Verify into your Android App.

When you import this library into your App, you simply need the user's phone number and the SDK completes verification for you.

To add Nexmo Verify SDK to your App:
1. Prerequisites
2. Configure Android Studio
3. Configure the manifest
4. Implement the Verify SDK


<a name="Prerequisites"></a>
## Prerequisites

To develop with the Nexmo Verify SDK you need to:
1. Create a __Nexmo Account__. If you don't have one, register at: https://dashboard.nexmo.com/register.
2. Contact productfeedback@nexmo.com and obtain the following security keys: 
  * An __Application ID__: a 25 character AlNum that identifies each App that integrates the Nexmo Verify SDK.
  * A __Shared Secret__: a 15 character AlNum generated by the system.
  You use these identifiers so your App can interact with Nexmo services.  
   _These identifiers are completely separate from your Nexmo __Key__ and __Secret__. You MUST NOT embed your Nexmo Key and Secret in your App_.
   You will soon be able to use the Customer Dashboard to obtain these - Watch out for it in an upcoming update.
3. Install Android Studio.
3. Clone the [https://github.com/Nexmo/verify-android-sdk](Nexmo Verify SDK) locally.

<a name="configureas"></a>
##Configure Android Studio

To use the Nexmo Verify SDK, you need to: 

1. Integrate the Verify SDK library into Android Studio. To do this:
  1. Open or create a project in Android Studio.
  2. In the file explorer, copy `verify-beta<version>.aar` to `AndroidStudioProjects/<project name>/app/libs`. 
2. In Android Studio, update `build.gradle (Module: app)` to include:
```java
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])

compile 'com.nexmo.sdk:verify-beta<version number>@aar'
```

<a name="configureman"></a>
##Setup your application configuration

Import the VerifySample or VerifySample_PushEnabled application into Android Studio.
Set your NexmoAppId and NexmoSharedSecretKey, as requested in the Config.java
```java
    public static final String NexmoAppId = "yourAppId";
    public static final String NexmoSharedSecretKey = "YourSharedSecretKey";
```
Note that this is just a proposed solution, you may choose to persist this information on your own applications
as you wish.
Run the sample on your handset, using your own phone number.

<a name="implement"></a>
##Integrate the Verify SDK

Creating a new Nexmo Client:
```java
import com.nexmo.sdk.NexmoClient;

import com.nexmo.sdk.core.client.ClientBuilderException;

import com.nexmo.sdk.verify.client.VerifyClient;
import com.nexmo.sdk.verify.event.VerifyClientListener;
import com.nexmo.sdk.verify.event.VerifyError;

Context context = getApplicationContext();
try {
	NexmoClient nexmoClient = new NexmoClient.NexmoClientBuilder()
                    .context(context)
                    .applicationId(APPLICATION_ID) //your application key
                    .sharedSecretKey(SHARED_KEY) //your application secret
                    .build();
} catch (ClientBuilderException e) {
	e.printStackTrace();
}
```
REMINDER: For the security of your Nexmo account, you should not use your Nexmo Account ID or Nexmo Account Secret anywhere in the 
code.

Now let's aquire a new Verify Client object that does all the verification magic.
```java
verifyClient = new VerifyClient(nexmoClient);
```

Remember to register for receiving verify status events, in case you want to update you application's UI accordingly.
```java
verifyClient.addVerifyListener(new VerifyClientListener() {
        @Override
        public void onVerifyInProgress(final VerifyClient verifyClient) {
        }

        @Override
        public void onUserVerified(final VerifyClient verifyClient) {
        }

        @Override
        public void onError(final VerifyClient verifyClient, final com.nexmo.sdk.verify.event.VerifyError errorCode) {
        }

        @Override
        public void onException(final IOException exception) {
        }
    });
```
A new verification is initiated using a supplied country code and phone number.
```java
verifyClient.getVerifiedUser("GB", "07000000000");
```
Even if a user enters the phone number with the country code, the library will determine the correct internationalised 
phone number for use.

When the verification is successfully started VerifyClientListener.onVerifyInProgress(final VerifyClient verifyClient) is invoked.

If the verification cannot be started VerifyClientListener.onError(final VerifyClient verifyClient, final com.nexmo.sdk.verify.event.VerifyError errorCode); is invoked describing the error.

Anytime the PIN code has been received by the end user, it should be supplied to the verify client:
```java
verifyClient.checkPinCode("1234");
```

A successful verification will be completed once the VerifyClientListener.onUserVerified(final VerifyClient verifyClient) event is 
invoked.

Verify SDK generates an SMS to verify a user if the user is unverified. Once users are verified, Verify maintains this state
on the server. By default, any change in the combination - Device ID + Application ID + Phone Number - is considered a new user
and will be explicitly verified by 2FA. Once verified, users remain verified on the server for 30 days. We're working on 
enabling the following customisations in a future version of the library:
- If we should disregard either or both of Device ID and Application ID
- if a user should be reverified everytime, never, or a custom duration (other than 30 days) in between

Please note that sensitive user details, like the phone number will NOT be stored locally at any time. You can persist the 
information in your local storage if you so wish, however this may not be the best for your user's security.

License
=======

Copyright (c) 2015 Nexmo, Inc.
All rights reserved.
Licensed only under the Nexmo Verify SDK License Agreement (the "License") located at

	https://www.nexmo.com/terms-use/verify-sdk/

By downloading or otherwise using our software or services, you acknowledge
that you have read, understand and agree to be bound by the 
[Nexmo Verify SDK License Agreement][1] and [Privacy Policy][2].
    
You may not use, exercise any rights with respect to or exploit this SDK,
or any modifications or derivative works thereof, except in accordance with the License.

 [1]: https://www.nexmo.com/terms-use/verify-sdk/
 [2]: https://www.nexmo.com/privacy-policy/
