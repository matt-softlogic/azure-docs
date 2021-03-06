---
title: Get Started with authentication for Mobile Apps in Xamarin Android
description: Learn how to use Mobile Apps to authenticate users of your Xamarin Android app through a variety of identity providers, including AAD, Google, Facebook, Twitter, and Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: adrianhall
manager: dwrede
editor: ''

ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: adrianha

---
# Add authentication to your Xamarin.Android app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

This topic shows you how to authenticate users of a Mobile App from your client application. In this tutorial, you add authentication to the quickstart project using an identity provider that is supported by Azure Mobile Apps. After being successfully authenticated and authorized in the Mobile App, the user ID value is displayed.

This tutorial is based on the Mobile App quickstart. You must also first complete the tutorial [Create a Xamarin.Android app]. If you do not use the downloaded quick start server project, you must add the authentication extension package to your project. For more information about server extension packages, see [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register"></a>Register your app for authentication and configure App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="permissions"></a>Restrict permissions to authenticated users
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

In Visual Studio or Xamarin Studio, run the client project on a device or emulator. Verify that an unhandled exception with a status code of 401 (Unauthorized) is raised after the app starts. This happens because the app attempts to access your Mobile App backend as an unauthenticated user. The *TodoItem* table now requires authentication.

Next, you will update the client app to request resources from the Mobile App backend with an authenticated user.

## <a name="add-authentication"></a>Add authentication to the app
The app is updated to require users to tap the **Sign in** button and authenticate before data is displayed.

1. Add the following code to the **TodoActivity** class:
   
        // Define a authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook);
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");
   
                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }
   
        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;
   
                // Load the data.
                OnRefreshItemsSelected();
            }
        }
   
    This creates a new method to authenticate a user and a method handler for a new **Sign in** button. The user in the example code above is authenticated by using a Facebook login. A dialog is used to display the user ID once authenticated.
   
   > [!NOTE]
   > If you are using an identity provider other than Facebook, change the value passed to **LoginAsync** above to one of the following: *MicrosoftAccount*, *Twitter*, *Google*, or *WindowsAzureActiveDirectory*.
   > 
   > 
2. In the **OnCreate** method, delete or comment-out the following line of code:
   
        OnRefreshItemsSelected ();
3. In the Activity_To_Do.axml file, add the following *LoginUser* button definition before the existing *AddItem* button:
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Add the following element to the Strings.xml resources file:
   
        <string name="login_button_text">Sign in</string>
5. In Visual Studio or Xamarin Studio, run the client project on a device or emulator and sign in with your chosen identity provider.
   
       When you are successfully logged-in, the app will display your login ID and the list of todo items, and you can make updates to the data.

<!-- URLs. -->
[Create a Xamarin.Android app]: app-service-mobile-xamarin-android-get-started.md
