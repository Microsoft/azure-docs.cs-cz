---
title: Zasílání nabízených oznámení aplikacím pro Xamarin.Android službou Azure Notification Hubs | Microsoft Docs
description: V tomto kurzu zjistíte, jak používat Azure Notification Hubs k odesílání nabízených oznámení do aplikace Xamarin Android.
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: f75671e2e5511054f3db550a8c24e62d031492c3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776708"
---
# <a name="tutorial-push-notifications-to-xamarinandroid-apps-using-azure-notification-hubs"></a>Kurz: Zasílání nabízených oznámení aplikacím pro Xamarin.Android službou Azure Notification Hubs
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Přehled
V tomto kurzu zjistíte, jak používat Azure Notification Hubs k odesílání nabízených oznámení do aplikace systému Xamarin.Android. Vytvoříte prázdnou aplikaci pro Xamarin.Android, která přijímá nabízená oznámení ze služby Firebase Cloud Messaging (FCM). Centrum oznámení použijete k vysílání nabízených oznámení do všech zařízení, na kterých běží vaše aplikace. Dokončený kód je k dispozici v ukázce [aplikace NotificationHubs][GitHub].

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoříte projekt Firebase a povolíte službu Firebase Cloud Messaging
> * Vytvoříte centrum oznámení
> * Vytvoříte aplikaci pro Xamarin.Android a připojíte ji k centru oznámení
> * Odešlete z webu Azure Portal zkušební oznámení

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
- [Visual Studio s Xamarinem] ve Windows nebo [Visual Studio pro Mac] v systému OS X.
- Aktivní účet Google

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Vytvoříte projekt Firebase a povolíte službu Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="create-a-notification-hub"></a>Vytvoření centra oznámení
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-settings-for-the-notification-hub"></a>Konfigurace nastavení GCM pro centrum oznámení

1. V části **NASTAVENÍ OZNÁMENÍ** vyberte **Google (GCM)**. 
2. Zadejte **klíč původního serveru**, který jste si poznamenali ve službě Google Firebase Console. 
3. Na panelu nástrojů vyberte **Uložit**. 

    ![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Vaše centrum oznámení je nakonfigurováno pro práci se službou FCM. Zároveň máte připojovací řetězce, pomocí kterých můžete svou aplikaci zaregistrovat pro příjem oznámení a odesílání nabízených oznámení.

## <a name="create-xamainandroid-app-and-connect-it-to-notification-hub"></a>Vytvoření aplikace pro Xamarin.Android a její připojení k centru oznámení

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Vytvoření projektu sady Visual Studio a přidání balíčků NuGet
1. V sadě Visual Studio přejděte na **Soubor**, vyberte **Nový** a vyberte **Projekt**. 
   
      ![Visual Studio – Vytvoření nového projektu pro Android](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog.png)
2. V okně **Průzkumník řešení** rozbalte **Vlastnosti** a klikněte na **AndroidManifest.xml**. Aktualizujte název balíčku, aby odpovídal názvu balíčku, který jste v konzole Google Firebase Console zadali, když jste do projektu přidávali službu Firebase Cloud Messaging. 

      ![Název balíčku v GCM](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
3. Klikněte pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**. 
4. Vyberte kartu **Procházet**. Najděte **Xamarin.GooglePlayServices.Base**. V seznamu výsledků vyberte **Xamarin.GooglePlayServices.Base**. Pak vyberte **Nainstalovat**. 

      ![Balíček NuGet služeb Google Play](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
5. V okně **Správce balíčků NuGet** najděte **Xamarin.Firebase.Messaging**. V seznamu výsledků vyberte **Xamarin.Firebase.Messaging**. Pak vyberte **Nainstalovat**. 
6. Potom najděte **Xamarin.Azure.NotificationHubs.Android**. V seznamu výsledků vyberte **Xamarin.Azure.NotificationHubs.Android**. Pak vyberte **Nainstalovat**. 

### <a name="add-the-google-services-json-file"></a>Přidání souboru JSON služeb Google

1. Do složky projektu zkopírujte soubor **google-services.json**, který jste si do složky projektu stáhli ze služby Google Firebase Console.
2. Přidejte do projektu soubor **google-services.json**.
3. V okně **Průzkumník řešení** vyberte **google-services.json**.
4. V podokně **Vlastnosti** nastavte akci sestavení na **GoogleServicesJson**. Pokud se **GoogleServicesJson** nezobrazuje, zavřete Visual Studio a znovu ho spusťte. Pak znovu otevřete projekt a zkuste to znovu. 

      ![Akce sestavení GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Nastavte centra oznámení v projektu

#### <a name="registering-with-firebase-cloud-messaging"></a>Registrace ve službě Firebase Cloud Messaging

Otevřete soubor **AndroidManifest.xml** a vložte následující elementy `<receiver>` do elementu `<application>`:

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

1. Shromážděte následující informace pro aplikaci Android a centrum oznámení:
   
   * **Připojovací řetězec naslouchání:** Na řídicím panelu na webu [Azure Portal] zvolte **Zobrazit připojovací řetězce**. Zkopírujte připojovací řetězec *DefaultListenSharedAccessSignature* pro tuto hodnotu.
   * **Název centra**: Pojmenujte centrum na webu [Azure Portal]. Například *mynotificationhub2*.
     
2. V okně **Průzkumník řešení** klikněte pravým tlačítkem na **projekt**, přejděte na **Přidat** a vyberte **Třída**. 
4. Vytvořte třídu **Constants.cs** pro váš projekt Xamarin a definujte následující hodnoty konstant ve třídě. Nahraďte zástupné symboly vašimi hodnotami.
    
    ```csharp
        public static class Constants
        {
           public const string ListenConnectionString = "<Listen connection string>";
           public const string NotificationHubName = "<hub name>";
        }
    ```
3. Přidejte následující hodnoty pomocí příkazů do souboru **MainActivity.cs**:
   
    ```csharp
        using Android.Util;
    ```
4. Přidejte do souboru **MainActivity.cs** proměnnou instance, která se použije k zobrazení dialogového okna výstrah při spuštění aplikace:
   
    ```csharp
        public const string TAG = "MainActivity";
    ```
5. Přidejte následující kód do metody `OnCreate` v souboru **MainActivity.cs** za `base.OnCreate(savedInstanceState)`:

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
    ```
7. Vytvořte novou třídu **MyFirebaseIIDService** stejně jako jste vytvořili třídu **Constants**. 
8. Přidejte následující příkazy using do souboru **MyFirebaseIIDService.cs**:
   
    ```csharp
    using Android.Util;
    using WindowsAzure.Messaging;
    using Firebase.Iid;
    ```

9. Přidejte do souboru **MyFirebaseIIDService.cs** následující deklaraci **třídy** a zařiďte, aby vaše třída dědila z třídy **FirebaseInstanceIdService**:
   
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
        public class MyFirebaseIIDService : FirebaseInstanceIdService
    ```
10. Do souboru **MyFirebaseIIDService.cs** přidejte následující kód:
   
    ```csharp
        const string TAG = "MyFirebaseIIDService";
        NotificationHub hub;

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "FCM token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
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
11. Vytvořte pro váš projekt novou třídu **MyFirebaseMessagingService**.
12. Přidejte následující příkazy using do souboru **MyFirebaseMessagingService.cs**.
    
    ```csharp
        using Android.Util;
        using Firebase.Messaging;
    ```
13. Přidejte následující kód nad deklaraci vaší třídy a zařiďte, aby vaše třída dědila z třídy **FirebaseMessagingService**:
    
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class MyFirebaseMessagingService : FirebaseMessagingService
    ```    
14. Přidejte následující kód do souboru **MyFirebaseMessagingService.cs**:
    
    ```csharp
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            if(message.GetNotification()!= null)
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

            var notificationBuilder = new Notification.Builder(this)
                        .SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetContentIntent(pendingIntent);

            var notificationManager = NotificationManager.FromContext(this);

            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```
15. **Sestavte** projekt. 
16. **Spusťte** aplikaci na zařízení nebo na nahraném emulátoru.

## <a name="send-test-notification-from-the-azure-portal"></a>Odeslání zkušebního oznámení z webu Azure Portal
Příjem oznámení ve vaší aplikaci můžete otestovat pomocí možnosti *Testovací odeslání* na webu [Azure Portal]. Zařízení se odešle testovací nabízené oznámení.

![Azure Portal – Testovací odeslání](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

Nabízená oznámení se většinou posílají ve službě back-end, jako je služba Mobile Services, nebo v technologii ASP.NET pomocí kompatibilní knihovny. Pokud k vašemu back-endu není k dispozici knihovna, můžete zasílat oznámení přímo z rozhraní REST API.

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste poslali oznámení všem zařízením s Androidem registrovaným back-endem. Pokud se chcete naučit zasílat nabízená oznámení určitým zařízením s Androidem, pokračujte následujícím kurzem: 

> [!div class="nextstepaction"]
>[Zasílání nabízených oznámení určitým zařízením](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)


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
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio s Xamarinem]: https://docs.microsoft.com/visualstudio/install/install-visual-studio
[Visual Studio pro Mac]: https://www.visualstudio.com/vs/visual-studio-mac/

[Azure Portal]: https://portal.azure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Use Notification Hubs to push notifications to users]: /manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /manage/services/notification-hubs/breaking-news-dotnet
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
