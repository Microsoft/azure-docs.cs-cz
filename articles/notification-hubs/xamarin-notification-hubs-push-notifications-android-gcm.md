---
title: Posílání nabízených oznámení do aplikací pro Xamarin. Android pomocí Azure Notification Hubs | Microsoft Docs
description: V tomto kurzu zjistíte, jak používat Azure Notification Hubs k odesílání nabízených oznámení do aplikace Xamarin Android.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 08/01/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 08/01/2019
ms.openlocfilehash: 95e244f956940f9d75df50243714f0fb8f8d0654
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424165"
---
# <a name="tutorial-send-push-notifications-to-xamarinandroid-apps-using-notification-hubs"></a>Kurz: odesílání nabízených oznámení do aplikací pro Xamarin. Android pomocí Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Přehled

V tomto kurzu zjistíte, jak používat Azure Notification Hubs k odesílání nabízených oznámení do aplikace systému Xamarin.Android. Vytvoříte prázdnou aplikaci pro Xamarin.Android, která přijímá nabízená oznámení ze služby Firebase Cloud Messaging (FCM). Centrum oznámení použijete k vysílání nabízených oznámení do všech zařízení, na kterých běží vaše aplikace. Dokončený kód je k dispozici v ukázce [aplikace NotificationHubs](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/Xamarin/GetStartedXamarinAndroid).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoříte projekt Firebase a povolíte službu Firebase Cloud Messaging
> * Vytvoříte centrum oznámení.
> * Vytvoříte aplikaci pro Xamarin.Android a připojíte ji k centru oznámení
> * Odešlete z webu Azure Portal zkušební oznámení

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
* [Visual Studio s Xamarinem] ve Windows nebo [Visual Studio pro Mac] v systému OS X.
* Aktivní účet Google

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Vytvoříte projekt Firebase a povolíte službu Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging-xamarin.md)]

## <a name="create-a-notification-hub"></a>Vytvoříte centrum oznámení.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcmfcm-settings-for-the-notification-hub"></a>Konfigurace nastavení GCM/FCM pro Centrum oznámení

1. V nabídce vlevo vyberte **Google (GCM/FCM)/** v části **Nastavení** .
2. Zadejte **klávesu serveru** , kterou jste si poznamenali v konzole Google Firebase.
3. Na panelu nástrojů vyberte **Uložit**.

    ![Snímek centra oznámení na webu Azure Portal s zvýrazněnou možností Google G C M F M a označená červeně](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Vaše centrum oznámení je nakonfigurováno pro práci se službou FCM. Zároveň máte připojovací řetězce, pomocí kterých můžete svou aplikaci zaregistrovat pro příjem oznámení a odesílání nabízených oznámení.

## <a name="create-a-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Vytvoření aplikace Xamarin. Android a její připojení k centru oznámení

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Vytvoření projektu sady Visual Studio a přidání balíčků NuGet

> [!NOTE]
> Kroky popsané v tomto kurzu jsou pro Visual Studio 2017. 

1. V aplikaci Visual Studio otevřete nabídku **soubor** , vyberte možnost **Nový**a pak vyberte možnost **projekt**. V okně **Nový projekt** proveďte tyto kroky:
    1. Rozbalte položku **nainstalované**, **Visual C#** a potom klikněte na možnost **Android**.
    2. V seznamu vyberte **aplikace pro Android (Xamarin)** .
    3. Zadejte **název** projektu.
    4. Vyberte **umístění** projektu.
    5. Vybrat **OK**

        ![Dialogové okno Nový projekt](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog-new.png)
2. V dialogovém okně **Nová aplikace pro Android** vyberte **prázdná aplikace**a pak vyberte **OK**.

    ![Snímek obrazovky, který zvýrazní šablonu prázdné aplikace](./media/partner-xamarin-notification-hubs-android-get-started/new-android-app-dialog.png)
3. V okně **Průzkumník řešení** rozbalte **Vlastnosti** a klikněte na **AndroidManifest.xml**. Aktualizujte název balíčku, aby odpovídal názvu balíčku, který jste v konzole Google Firebase Console zadali, když jste do projektu přidávali službu Firebase Cloud Messaging.

    ![Název balíčku v GCM](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
4. Nastavte cílovou verzi Androidu pro projekt na **android 9,0 (výseč)** pomocí následujících kroků: 
    1. Klikněte pravým tlačítkem na projekt a vyberte **vlastnosti**. 
    1. V poli **kompilovat pomocí verze Androidu: (cílové rozhraní)** vyberte **Android 9,0 (výseč)**. 
    1. V okně se zprávou vyberte **Ano** , pokud chcete pokračovat se změnou cílové architektury.
1. Pomocí následujících kroků přidejte do projektu požadované balíčky NuGet:
    1. Klikněte pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**.
    1. Přepněte na kartu **Installed (instalovat** ), vyberte **Xamarin. Android. support. Design**a v pravém podokně vyberte **aktualizovat** , aby se balíček aktualizoval na nejnovější verzi.
    1. Přepněte na kartu **Procházet** . Vyhledejte **Xamarin. GooglePlayServices. Base**. V seznamu výsledků vyberte **Xamarin.GooglePlayServices.Base**. Pak vyberte **Nainstalovat**.

        ![Balíček NuGet služeb Google Play](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
    6. V okně **Správce balíčků NuGet** najděte **Xamarin.Firebase.Messaging**. V seznamu výsledků vyberte **Xamarin.Firebase.Messaging**. Pak vyberte **Nainstalovat**.
    7. Potom najděte **Xamarin.Azure.NotificationHubs.Android**. V seznamu výsledků vyberte **Xamarin.Azure.NotificationHubs.Android**. Pak vyberte **Nainstalovat**.

### <a name="add-the-google-services-json-file"></a>Přidání souboru JSON služeb Google

1. Zkopírujte `google-services.json` soubor, který jste stáhli z konzoly Google Firebase do složky projektu.
2. Přidejte `google-services.json` do projektu.
3. `google-services.json`V okně **Průzkumník řešení** vyberte.
4. V podokně **Vlastnosti** nastavte akci sestavení na **GoogleServicesJson**. Pokud se **GoogleServicesJson** nezobrazuje, zavřete Visual Studio a znovu ho spusťte. Pak znovu otevřete projekt a zkuste to znovu.

    ![Akce sestavení GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Nastavte centra oznámení v projektu

#### <a name="registering-with-firebase-cloud-messaging"></a>Registrace ve službě Firebase Cloud Messaging

1. Otevřete `AndroidManifest.xml` soubor a vložte `<receiver>` do prvku následující prvky `<application>` :

    ```xml
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

2. Přidejte následující příkazy **před element aplikace** .

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    ```

3. Shromážděte následující informace pro aplikaci Android a centrum oznámení:

   * **Připojovací řetězec naslouchání:** Na řídicím panelu na webu [Azure Portal] zvolte **Zobrazit připojovací řetězce**. Zkopírujte `DefaultListenSharedAccessSignature` připojovací řetězec pro tuto hodnotu.
   * **Název centra**: Pojmenujte centrum na webu [Azure Portal]. Například *mynotificationhub2*.
4. V okně **Průzkumník řešení** klikněte pravým tlačítkem myši na **projekt**, vyberte možnost **Přidat**a poté možnost **Třída**.
5. Vytvořte `Constants.cs` třídu pro projekt Xamarin a ve třídě definujte následující konstantní hodnoty. Nahraďte zástupné symboly vašimi hodnotami.

    ```csharp
    public static class Constants
    {
        public const string ListenConnectionString = "<Listen connection string>";
        public const string NotificationHubName = "<hub name>";
    }
    ```

6. Přidejte následující příkazy using do `MainActivity.cs` :

    ```csharp
    using Android.Util;
    using Android.Gms.Common;
    ```

7. Do třídy MainActivity přidejte následující vlastnosti. Proměnná značky se použije k zobrazení dialogového okna výstrahy, když je aplikace spuštěná:

    ```csharp
    public const string TAG = "MainActivity";
    internal static readonly string CHANNEL_ID = "my_notification_channel";
    ```

8. Do třídy MainActivity přidejte následující metodu. Kontroluje, zda jsou **služby Google Play** k dispozici na zařízení.

    ```csharp
    public bool IsPlayServicesAvailable()
    {
        int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.Success)
        {
            if (GoogleApiAvailability.Instance.IsUserResolvableError(resultCode))
                Log.Debug(TAG, GoogleApiAvailability.Instance.GetErrorString(resultCode));
            else
            {
                Log.Debug(TAG, "This device is not supported");
                Finish();
            }
            return false;
        }

        Log.Debug(TAG, "Google Play Services is available.");
        return true;
    }
    ```

9. Přidejte následující metodu do třídy MainActivity, která vytvoří kanál oznámení.

    ```csharp
    private void CreateNotificationChannel()
    {
        if (Build.VERSION.SdkInt < BuildVersionCodes.O)
        {
            // Notification channels are new in API 26 (and not a part of the
            // support library). There is no need to create a notification
            // channel on older versions of Android.
            return;
        }

        var channelName = CHANNEL_ID;
        var channelDescription = string.Empty;
        var channel = new NotificationChannel(CHANNEL_ID, channelName, NotificationImportance.Default)
        {
            Description = channelDescription
        };

        var notificationManager = (NotificationManager)GetSystemService(NotificationService);
        notificationManager.CreateNotificationChannel(channel);
    }
    ```

10. Do `MainActivity.cs` přidejte následující kód `OnCreate` po `base.OnCreate(savedInstanceState)` :

    ```csharp
    if (Intent.Extras != null)
    {
        foreach (var key in Intent.Extras.KeySet())
        {
            if(key!=null)
            {
                var value = Intent.Extras.GetString(key);
                Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
            }
        }
    }

    IsPlayServicesAvailable();
    CreateNotificationChannel();
    ```

15. Přidejte do projektu třídu s názvem `MyFirebaseMessagingService` . 
16. Přidejte následující příkazy using do `MyFirebaseMessagingService.cs` .

    ```csharp
    using Android.Util;
    using Firebase.Messaging;
    using Android.Support.V4.App;    
    using WindowsAzure.Messaging;
    ```

17. Do deklarace třídy přidejte následující a vaše třída zdědí `FirebaseMessagingService` :

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class MyFirebaseMessagingService : FirebaseMessagingService
    ```

18. Do třídy přidejte následující kód `MyFirebaseMessagingService` pro zpracování zpráv, které jsou přijaty. 

    ```csharp
        const string TAG = "MyFirebaseMsgService";
        NotificationHub hub;
    
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            if (message.GetNotification() != null)
            {
                //These is how most messages will be received
                Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
                SendNotification(message.GetNotification().Body);
            }
            else
            {
                //Only used for debugging payloads sent from the Azure portal
                SendNotification(message.Data.Values.First());
    
            }
        }
    
        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);
    
            var notificationBuilder = new NotificationCompat.Builder(this, MainActivity.CHANNEL_ID);
    
            notificationBuilder.SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetShowWhen(false)
                        .SetContentIntent(pendingIntent);
    
            var notificationManager = NotificationManager.FromContext(this);
    
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

19. Přidejte následující metody do třídy MyFirebaseMessagingService (napravo pod kódem přidaným v předchozím kroku) k získání registračního tokenu FCM a jeho odeslání do instance Notification Hubs (centra). 

    ```csharp
        public override void OnNewToken(string token)
        {
            Log.Debug(TAG, "FCM token: " + token);
            SendRegistrationToServer(token);
        }

        void SendRegistrationToServer(string token)
        {
            // Register with Notification Hubs
            hub = new NotificationHub(Constants.NotificationHubName,
                                        Constants.ListenConnectionString, this);

            var tags = new List<string>() { };
            var regID = hub.Register(token, tags.ToArray()).RegistrationId;

            Log.Debug(TAG, $"Successful registration of ID {regID}");
        }
    ```
1. **Sestavte** projekt.
1. **Spuštění** aplikace na zařízení nebo zavedeném emulátoru

## <a name="send-test-notification-from-the-azure-portal"></a>Odeslání zkušebního oznámení z webu Azure Portal

Příjem oznámení ve vaší aplikaci můžete otestovat pomocí možnosti **Testovací odeslání** na webu [Azure Portal]. Do zařízení se odešle testovací nabízené oznámení.

![Portál Azure – testovací odeslání](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

Nabízená oznámení se většinou posílají ve službě back-end, jako je služba Mobile Services, nebo v technologii ASP.NET pomocí kompatibilní knihovny. Pokud pro váš back-end není dostupná žádná knihovna, můžete k posílání oznámení použít také REST API přímo.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste poslali oznámení všem zařízením s Androidem registrovaným back-endem. Pokud se chcete naučit zasílat nabízená oznámení určitým zařízením s Androidem, pokračujte následujícím kurzem:

> [!div class="nextstepaction"]
>[Nabízená oznámení odesílaná konkrétním zařízením](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio s Xamarinem]: /visualstudio/install/install-visual-studio
[Visual Studio pro Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Azure Portal]: https://portal.azure.com/
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Android]: /previous-versions/azure/dn282661(v=azure.100)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-android