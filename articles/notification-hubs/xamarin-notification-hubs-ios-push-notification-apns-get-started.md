---
title: "Začínáme se službou Azure Notification Hubs pro aplikace Xamarin.iOS | Dokumentace Microsoftu"
description: "V tomto kurzu zjistíte, jak používat Azure Notification Hubs k odesílání nabízených oznámení do aplikace Xamarin iOS."
services: notification-hubs
keywords: "nabízená oznámení ios,nabízení zpráv,nabízená oznámení,nabízená zpráva"
documentationcenter: xamarin
author: jwhiteDev
manager: kpiteira
editor: 
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 38ad8a15fcc4077926e735e01f877a4ee66718ef
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="get-started-with-azure-notification-hubs-for-xamarinios-apps"></a>Začínáme se službou Azure Notification Hubs pro aplikace Xamarin.iOS
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Přehled
> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).
> 
> 

V tomto kurzu zjistíte, jak používat Azure Notification Hubs k odesílání nabízených oznámení do aplikace systému iOS. Vytvoříte prázdnou aplikaci Xamarin.iOS, která přijímá nabízená oznámení pomocí [služby nabízených oznámení Apple (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html). 

Jakmile budete hotovi, budete moci používat vaše centra oznámení k vysílání nabízených oznámení pro všechna zařízení používající vaši aplikaci. Dokončený kód je k dispozici v ukázce [aplikace NotificationHubs][GitHub].

Tento kurz představuje scénář jednoduchého vysílání zprávy oznámení pomocí centra oznámení.

## <a name="prerequisites"></a>Požadavky
V tomto kurzu budete potřebovat následující:

* Poslední verze jazyka [XCode][Install Xcode]
* Zařízení kompatibilní s iOS 10 (nebo novější verzí)
* Členství v [programu pro vývojáře Apple](https://developer.apple.com/programs/).
* [Visual Studio pro Mac]
  
  > [!NOTE]
  > Z důvodu požadavků na konfiguraci pro nabízená oznámení iOS musíte nasadit a otestovat vzorovou aplikaci na fyzickém zařízení iOS (iPhone nebo iPad) namísto simulátoru.
  > 
  > 

Dokončení tohoto kurzu je předpokladem pro všechny ostatní kurzy služby Notification Hubs pro aplikace Xamarin.iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Konfigurace centra oznámení pro nabízená oznámení iOS
Tato části vás provede jednotlivými kroky vytvoření nového centra oznámení a konfigurace ověřování s použitím služby APNS a dříve vytvořeného nabízeného certifikátu **.p12**. Pokud chcete použít centrum oznámení, které jste již vytvořili, můžete přeskočit na krok 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p>Klikněte na tlačítko <b>Notification Services</b> a pak vyberte <b>Apple (APNS)</b>. Nezapomeňte vybrat <b>Certifikát</b>, klikněte na ikonu souboru a vyberte soubor <b>.p12</b>, který jste vyexportovali dříve. Ujistěte se, že zadáváte správné heslo.</p>

<p>Nezapomeňte vybrat režim <b>Sandboxu</b>, protože se jedná o vývoj. Používejte pouze režim <b>Výroba</b>, pokud chcete zasílat nabízená oznámení uživatelům, kteří si zakoupili aplikaci z obchodu s aplikacemi.</p>
</li>
</ol>

&emsp;&emsp;&emsp;&emsp;![Konfigurace služby APNS na webu Azure Portal][6]

&emsp;&emsp;&emsp;&emsp;![Konfigurace certifikační služby APNS na webu Azure Portal][7]

Vaše centrum oznámení je nyní nakonfigurováno pro práci se službou APNS. Zároveň máte připojovací řetězce, pomocí kterých můžete svou aplikaci zaregistrovat pro odesílání nabízených oznámení.

## <a name="connect-your-app-to-the-notification-hub"></a>Připojte aplikaci k centru oznámení
#### <a name="create-a-new-project"></a>Vytvoření nového projektu
1. V sadě Visual Studio vytvořte nový projekt pro iOS, vyberte šablonu **Aplikace s jedním zobrazením** a klikněte na **Další**.
   
     ![Visual Studio – Výběr typu aplikace][31]

2. Zadejte název aplikace a identifikátor organizace, klikněte na **Další** a pak na **Vytvořit**.

3. V zobrazení Řešení dvakrát klikněte na soubor *Info.plist* a v části **Identita** se ujistěte, že identifikátor sady odpovídá identifikátoru použitému při vytváření profilu zřizování. V části **Podepisování** zkontrolujte, že v části **Tým** je vybraný váš vývojářský účet, možnost Automatically manage signing (Automaticky se starat o podepisování) je vybraná a váš podpisový certifikát a profil zřizování jsou automaticky vybrané.

    ![Visual Studio – Konfigurace aplikace pro iOS][32]

4. Přidejte balíček zasílání zpráv Azure. V zobrazení Řešení klikněte pravým tlačítkem a na projekt a vyberte **Přidat** > **Přidat balíčky NuGet**. Vyhledejte balíček **Xamarin.Azure.NotificationHubs.iOS** a přidejte ho do svého projektu.

5. Přidejte do vaší třídy nový soubor, pojmenujte ho **Constants.cs**, přidejte následující proměnné a nahraďte zástupné symboly literálu řetězce ve vašem *názvu centra* a *DefaultListenSharedAccessSignature*, který jste si předtím poznamenali.
   
    ```csharp
        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";
    ```

6. Do souboru **AppDelegate.cs** přidejte následující pomocí příkazu:
   
    ```csharp
        using WindowsAzure.Messaging;
    ```

7. Deklarujte instanci **SBNotificationHub**:
   
    ```csharp
        private SBNotificationHub Hub { get; set; }
    ```

8. V souboru **AppDelegate.cs** aktualizujte **FinishedLaunching()** tak, aby odpovídaly následujícímu:
   
    ```csharp
        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());
   
                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }
   
            return true;
        }
    ```

9. Potlačte metodu **RegisteredForRemoteNotifications()** v **AppDelegate.cs**:
   
    ```csharp
        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);
   
            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }
   
                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }
    ```

10. Potlačte metodu **ReceivedRemoteNotifications()** v **AppDelegate.cs**:
   
    ```csharp
        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
    ```

11. Vytvořte následující metodu **ProcessNotification()** v **AppDelegate.cs**:
   
    ```csharp
        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
   
                string alert = string.Empty;
   
                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();
   
                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }
    ```
   > [!NOTE]
   > Můžete se rozhodnout přepsat **FailedToRegisterForRemoteNotifications()** pro řešení situací jako chybějící síťové připojení. To je obzvláště důležité, když by uživatel mohl spustit aplikaci v režimu offline (například letadlo) a vy chcete zpracovávat scénáře zpráv oznámení specifické pro vaši aplikaci.
  

12. Spusťte aplikaci v zařízení.

## <a name="sending-test-push-notifications"></a>Odesílání testovacích nabízených oznámení
Příjem oznámení ve vaší aplikaci můžete otestovat pomocí možnosti *Testovací odeslání* na webu [Azure Portal]. Tím se do vašeho zařízení odešle testovací nabízené oznámení.

![Azure Portal – Testovací odeslání][30]

Nabízená oznámení se většinou posílají ve službě back-end, jako je služba Mobile Apps, nebo v technologii ASP.NET pomocí kompatibilní knihovny. Pokud pro váš back-end není dostupná žádná knihovna, můžete k zasílání oznámení použít také přímo rozhraní REST API.

Jako další krok pro odesílání oznámení z back-endu ASP.NET doporučujeme absolvovat kurz [Použití Notification Hubs k odeslání nabízených oznámení uživatelům](notification-hubs-aspnet-backend-ios-apple-apns-notification.md). Následující přístupy lze však použít pro zasílání oznámení:

Tady je seznam některých dalších kurzů, které se týkají zasílání oznámení:
* Rozhraní REST: Nabízené oznámení můžete podporovat na jakékoli platformě back-end pomocí [rozhraní REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).
* **Microsoft Azure oznámení centra .NET SDK**: Ve správci balíčků Nuget pro Visual Studio spusťte položku [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).
* Node.js: [Jak používat Notification Hubs z Node.js](notification-hubs-nodejs-push-notification-tutorial.md).
* Java/PHP**: Příklad odesílání nabízených oznámení pomocí rozhraní REST API najdete v části Jak používat Notification Hubs z Javy/PHP ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

## <a name="next-steps"></a>Další kroky
V tomto příkladu jste vysílali nabízená oznámení pro všechna vaše zařízení iOS. Chcete-li se zaměřit na konkrétní uživatele, využijte kurz [Použití Notification Hubs k odeslání nabízených oznámení uživatelům]. Pokud chcete segmentovat uživatele podle zájmových skupin, můžete si přečíst kurz [Používání centra oznámení k odesílání novinek]. Další informace o tom, jak používat centra oznámení, naleznete v tématu [Průvodce centry oznámení] a v tématu [Centra oznámení s postupy pro iOS].

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png



<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Windows Azure Messaging Framework]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Průvodce centry oznámení]: http://msdn.microsoft.com/library/jj927170.aspx
[Centra oznámení s postupy pro iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio pro Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Použití Notification Hubs k odeslání nabízených oznámení uživatelům]: /manage/services/notification-hubs/notify-users-aspnet
[Používání centra oznámení k odesílání novinek]: /manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]:https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Azure Portal]: https://portal.azure.com
