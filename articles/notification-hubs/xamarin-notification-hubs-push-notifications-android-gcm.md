---
title: Začínáme s centrem oznámení pro aplikace Xamarin Android | Dokumentace Microsoftu
description: V tomto kurzu zjistíte, jak používat Azure Notification Hubs k odesílání nabízených oznámení do aplikace Xamarin Android.
author: jwhitedev
manager: kpiteira
editor: ''
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 7fee7813bbdcf902d5f5ae2d0af7540c8899ad25
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-notification-hubs-for-xamarinandroid-apps"></a>Začínáme se službou Notification Hubs pro aplikace Xamarin.Android
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Přehled
V tomto kurzu zjistíte, jak používat Azure Notification Hubs k odesílání nabízených oznámení do aplikace systému Xamarin.Android. Vytvoříte prázdnou aplikaci Xamarin.Android, která bude přijímat nabízená oznámení pomocí služby Firebase Cloud Messaging (FCM). Jakmile budete hotovi, budete moci používat vaše centra oznámení k vysílání nabízených oznámení pro všechna zařízení používající vaši aplikaci. Dokončený kód je k dispozici v ukázce [aplikace NotificationHubs][GitHub].

Tento kurz představuje scénář jednoduchého vysílání přes centra oznámení.

## <a name="before-you-begin"></a>Než začnete
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Dokončený kód pro tento kurz najdete [tady][GitHub] na GitHubu.

## <a name="prerequisites"></a>Požadavky
V tomto kurzu budete potřebovat následující:

* [Visual Studio s Xamarinem] ve Windows nebo [Visual Studio pro Mac] v systému OS X.
* Aktivní účet Google

Dokončení tohoto kurzu je předpokladem pro všechny ostatní kurzy Notification Hubs pro Xamarin.Android Apps.

> [!IMPORTANT]
> K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).
> 
> 

## <a name="enable-firebase-cloud-messaging"></a>Povolení služby Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-your-notification-hub"></a>Konfigurace centra oznámení
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li><p>Zvolte kartu <b>Konfigurovat</b> v horní části, zadejte hodnotu <b>Klíč rozhraní API</b> získanou v předchozí části a pak vyberte <b>Uložit</b>.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Vaše centrum oznámení je nakonfigurováno pro práci se službou FCM. Zároveň máte připojovací řetězce, pomocí kterých můžete svou aplikaci zaregistrovat pro příjem oznámení a odesílání nabízených oznámení.

## <a name="connect-your-app-to-the-notification-hub"></a>Připojte aplikaci k centru oznámení
Nejprve vytvoříte nový projekt. 

1. V sadě Visual Studio zvolte **Nové řešení** > **Aplikace pro Android** a pak vyberte **Další**.
   
      ![Visual Studio – Vytvoření nového projektu pro Android][22]

2. Zadejte **Název aplikace** a **Identifikátor**. Zvolte **Cílové platformy**, které chcete podporovat, a pak zvolte **Další** a **Vytvořit**.
   
      ![Visual Studio –Konfigurace aplikace pro Android][23]

    Dojde k vytvoření nového projektu pro Android.

3. Otevřete vlastnosti projektu kliknutím pravým tlačítkem myši na nový projekt v zobrazení Řešení a zvolte **Možnosti**. Vyberte položku **Aplikace pro Android** v části **Sestavení**.
   
    Zajistěte, aby první písmeno **Názvu balíčku** bylo malé písmeno.
   
   > [!IMPORTANT]
   > První písmeno názvu balíčku musí být malé. Jinak se zobrazí chyby manifestu aplikace při registraci vaší aplikace pro nabízená oznámení níže.
   > 
   > 
   
      ![Visual Studio – Možnosti projektu pro Android][24]
4. Volitelně můžete nastavit **Minimální verzi systému Android** na jinou úroveň rozhraní API.
5. Volitelně můžete nastavit **Cílovou verzi systému Android** na jinou verzi rozhraní API, kterou chcete zacílit (musí se jednat o úroveň rozhraní API 8 nebo vyšší).
6. Zvolte **OK** a zavřete dialogové okno Možnosti projektu.

### <a name="add-the-required-packages-to-your-project"></a>Přidání požadovaných balíčků do projektu

1. Klikněte pravým tlačítkem na váš projekt a vyberte **Přidat** > **Přidat balíčky NuGet**.
2. Vyhledejte balíček **Xamarin.Azure.NotificationHubs.Android** a přidejte ho do projektu.
3. Vyhledejte balíček **Xamarin.Firebase.Messaging** a přidejte ho do projektu.

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
   * **Název centra:** Toto je název vašeho centra z webu [Azure Portal]. Například *mynotificationhub2*.
     
2. Vytvořte třídu **Constants.cs** pro váš projekt Xamarin a definujte následující hodnoty konstant ve třídě. Nahraďte zástupné symboly vašimi hodnotami.
    
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

6. Klikněte pravým tlačítkem na váš projekt a přidejte soubor `google-services.json`, který jste dříve stáhli ze svého projektu Firebase. Klikněte pravým tlačítkem na přidaný soubor a nastavte akci sestavení na `GoogleServicesJson`.

    ![Visual Studio – Konfigurace souboru google-services.json][25]

7. Vytvořte novou třídu **MyFirebaseIIDService**.

8. Přidejte následující příkazy using do souboru **MyFirebaseIIDService.cs**:
   
    ```csharp
        using System;
        using Android.App;
        using Firebase.Iid;
        using Android.Util;
        using WindowsAzure.Messaging;
        using System.Collections.Generic;
    ```

9. Do souboru **MyFirebaseIIDService.cs**přidejte následující kód nad deklaraci **třídy** a zařiďte, aby vaše třída dědila z třídy **FirebaseInstanceIdService**:
   
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
        using System;
        using System.Linq;
        using Android;
        using Android.App;
        using Android.Content;
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

15. Spuštění aplikace na zařízení nebo nahraném emulátoru

## <a name="send-notifications-from-the-portal"></a>Odesílání oznámení z portálu
Příjem oznámení ve vaší aplikaci můžete otestovat pomocí možnosti *Testovací odeslání* na webu [Azure Portal]. Tím se do vašeho zařízení odešle testovací nabízené oznámení.

![Azure Portal – Testovací odeslání][30]

Nabízená oznámení se většinou posílají ve službě back-end, jako je služba Mobile Services, nebo v technologii ASP.NET pomocí kompatibilní knihovny. Pokud pro váš back-end není dostupná žádná knihovna, můžete k zasílání oznámení použít také přímo rozhraní REST API.

Tady je seznam některých dalších kurzů, které se týkají zasílání oznámení:

* ASP.NET: Viz [Použití centra oznámení pro nabízená oznámení uživatelům].
* Sada Azure Notification Hubs Java SDK: Informace o odesílání oznámení z Javy najdete v článku [Jak používat Notification Hubs z Javy](notification-hubs-java-push-notification-tutorial.md). Tato metoda prošla pro potřeby vývoje pro Android testováním v Eclipse.
* PHP: Viz [Jak používat Notification Hubs z PHP](notification-hubs-php-push-notification-tutorial.md).

## <a name="next-steps"></a>Další kroky
V tomto jednoduchém příkladu jste vysílali oznámení pro všechna zařízení Android. Chcete-li se zaměřit na konkrétní uživatele, využijte kurz [Použití centra oznámení pro nabízená oznámení uživatelům]. Pokud chcete segmentovat uživatele podle zájmových skupin, můžete si přečíst kurz [Používání centra oznámení k odesílání novinek]. Další informace o tom, jak používat centra oznámení, naleznete v tématu [Průvodce centry oznámení] a v tématu [Centra oznámení s postupy pro Android].

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
[Průvodce centry oznámení]: http://msdn.microsoft.com/library/jj927170.aspx
[Centra oznámení s postupy pro Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Použití centra oznámení pro nabízená oznámení uživatelům]: /manage/services/notification-hubs/notify-users-aspnet
[Používání centra oznámení k odesílání novinek]: /manage/services/notification-hubs/breaking-news-dotnet
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
