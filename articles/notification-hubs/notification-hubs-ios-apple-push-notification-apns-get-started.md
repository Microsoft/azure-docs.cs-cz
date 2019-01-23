---
title: Zasílání nabízených oznámení aplikacím pro iOS službou Azure Notification Hubs | Microsoft Docs
description: V tomto kurzu zjistíte, jak používat Azure Notification Hubs k odesílání nabízených oznámení do aplikace iOS.
services: notification-hubs
documentationcenter: ios
keywords: nabízené oznámení;nabízená oznámení;nabízená oznámení ios
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 63fb04e6b31fe4026b93cef09d88601d6182101a
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54448305"
---
# <a name="tutorial-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Kurz: Odesílání nabízených oznámení do aplikace pro iOS pomocí Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

V tomto kurzu se dozvíte, jak používat Azure Notification Hubs k zasílání nabízených oznámení aplikaci pro iOS. Vytvoříte prázdnou aplikaci pro iOS, která přijímá nabízená oznámení [služby Apple Push Notification (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vygenerujete soubor s žádostí o podepsání certifikátu
> * Požádáte aplikaci o nabízená oznámení
> * Vytvoření zřizovacího profilu pro aplikaci
> * Konfigurace centra oznámení pro nabízená oznámení iOS
> * Připojíte aplikaci pro iOS ke službě Notification Hubs
> * Odešlete nabízená oznámení
> * Ověříte, že aplikace přijímá oznámení

Dokončený kód v tomto kurzu lze najít v části [Github](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted). 

## <a name="prerequisites"></a>Požadavky

* Aktivní účet Azure. Pokud účet nemáte, můžete si [vytvořit si bezplatný účet Azure](https://azure.microsoft.com/free) během několika minut.
* [Windows Azure Messaging Framework]
* Poslední verze jazyka [Xcode]
* Zařízení podporující iOS 10 (nebo novější verzi)
* Členství v [programu pro vývojáře Apple](https://developer.apple.com/programs/).
  
 > [!NOTE]
 > Z důvodu požadavků na konfiguraci pro nabízená oznámení musíte nasadit a otestovat nabízená oznámení na fyzickém zařízení iOS (iPhone nebo iPad) namísto simulátoru iOS.
  
Dokončení tohoto kurzu je předpokladem pro všechny ostatní kurzy Notification Hubs pro aplikace iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Konfigurace centra oznámení pro nabízená oznámení iOS

V této části vytvoříte centrum oznámení a nakonfigurujete ověřování službou APNS s dříve vytvořeným nabízeným certifikátem **.p12**. Pokud chcete použít centrum oznámení, které jste již vytvořili, můžete přeskočit na krok 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-your-notification-hub-with-apns-information"></a>Konfigurace centra oznámení s použitím informací o službě APNS

1. V části **Notification Services** vyberte **Apple (APNS)**.
2. Vyberte **Certifikát**.
3. Vyberte **ikonu souboru**.
4. Vyberte soubor **.p12**, který jste exportovali v předchozích krocích.
5. Zadejte správné **heslo**.
6. Vyberte režim **Sandbox**. Používejte pouze režim **Výroba**, pokud chcete zasílat nabízená oznámení uživatelům, kteří si zakoupili aplikaci z obchodu s aplikacemi.

    ![Konfigurace certifikační služby APNS na webu Azure Portal][7]

Právě jste své centrum oznámení nakonfigurovali pro práci se službou APNS. Zároveň máte připojovací řetězce, pomocí kterých můžete svou aplikaci zaregistrovat pro odesílání nabízených oznámení.

## <a name="connect-your-ios-app-to-notification-hubs"></a>Připojte aplikaci iOS k centru oznámení

1. V Xcode vytvořte nový projekt iOS a vyberte šablonu **Jediné zobrazení aplikace**.

    ![Xcode – jediné zobrazení aplikace][8]

2. Když nastavujete možnosti pro nový projekt, nezapomeňte použít stejný **Název produktu** a **Identifikátor organizace**, který jste použili při nastavení identifikátoru sady na portálu pro vývojáře Apple.

    ![Xcode – možnosti projektu][11]

3. V části Navigátor projektu klikněte na název vašeho projektu, klikněte na kartu **Obecné** a vyhledejte **Podepisování**. Nezapomeňte vybrat odpovídající Tým pro váš účet vývojáře Apple. XCode by na základě vašeho identifikátoru sady mělo automaticky stáhnout profil zřizování, který jste vytvořili dříve.

    Pokud nevidíte nový profil zřizování, který jste vytvořili v Xcode, pokuste se aktualizovat profily pro podpisové identity. Klikněte na tlačítko **Xcode** na panelu nabídek, klikněte na tlačítko **Předvolby**, klikněte na kartu **Účet**, klikněte na tlačítko **Zobrazit podrobnosti**, klikněte na podpisovou identitu a pak klikněte na tlačítko Aktualizovat v pravém dolním rohu.

    ![Xcode – profil zřizování][9]

4. Vyberte kartu **Možnosti** a nezapomeňte povolit Nabízená oznámení.

    ![Xcode – možnosti nabízení][12]

5. Stáhněte soubor rozhraní [Windows Azure Messaging Framework] a rozbalte ho. V Xcode klikněte pravým tlačítkem na projekt a klikněte na možnost **Přidat soubory do** a přidejte složku **WindowsAzureMessaging.framework** do projektu Xcode. Vyberte **Možnosti**, ujistěte se, že je vybraná možnost **Kopírovat položky v případě potřeby**, a pak klikněte na **Přidat**.

    ![Rozbalte Azure SDK][10]

6. Přidejte nový soubor záhlaví projektu s názvem `HubInfo.h`. V tomto souboru jsou konstanty vašeho centra oznámení. Přidejte následující definice a nahraďte zástupné symboly literálu řetězce ve vašem *názvu centra* a *DefaultListenSharedAccessSignature*, který jste si předtím poznamenali.

    ```objc
    #ifndef HubInfo_h
    #define HubInfo_h

        #define HUBNAME @"<Enter the name of your hub>"
        #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"

    #endif /* HubInfo_h */
    ```

7. Otevřete váš soubor `AppDelegate.h` a přidejte následující direktivy importu:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>
    #import "HubInfo.h"
    ```
8. Ve vaší `AppDelegate.m` přidejte následující kód `didFinishLaunchingWithOptions` metody založené na vaší verzi iOS. Tento kód zaregistruje popisovač vašeho zařízení do APN:

    ```objc
    UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
        UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

    [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    [[UIApplication sharedApplication] registerForRemoteNotifications];
    ```

9. Do stejného souboru přidejte následující metody:

    ```objc
        - (void) application:(UIApplication *) application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
        SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                    notificationHubPath:HUBNAME];

        [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
            else {
                [self MessageBox:@"Registration Status" message:@"Registered"];
            }
        }];
        }

    -(void)MessageBox:(NSString *) title message:(NSString *)messageText
    {
        UIAlertController *alert = [UIAlertController alertControllerWithTitle:title message:messageText preferredStyle:UIAlertControllerStyleAlert];
        UIAlertAction *okAction = [UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil];
        [alert addAction:okAction];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:alert animated:YES completion:nil];
    }
    ```

    Tento kód se připojí k centru oznámení pomocí informací o připojení zadaných do souboru HubInfo.h. Poté přiřadí token zařízení do centra oznámení tak, aby centrum oznámení mohlo odesílat oznámení.

10. Do stejného souboru přidejte následující metodu pro zobrazení **UIAlert**, pokud bylo přijato oznámení, že je aplikace aktivní:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

11. Ověřte, zda nedochází k žádným chybám tak, že sestavíte a spustíte aplikaci na vašem zařízení.

## <a name="send-test-push-notifications"></a>Odešlete nabízená oznámení

Příjem oznámení ve vaší aplikaci můžete otestovat pomocí možnosti *Testovací odeslání* na webu [Azure Portal]. Zařízení se odešle testovací nabízené oznámení.

![Azure Portal – Testovací odeslání][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="verify-that-your-app-receives-push-notifications"></a>Ověření, že aplikace přijímá nabízená oznámení

Chcete-li otestovat nabízená oznámení na iOS, musíte aplikaci nasadit do fyzického zařízení iOS. Nabízená oznámení Apple nelze odeslat pomocí simulátoru iOS.

1. Spusťte aplikaci a ověřte, zda byla registrace úspěšná a stiskněte klávesu **OK**.

    ![Test registrace nabízených oznámení aplikace iOS][33]

2. V dalším kroku odešlete testovací nabízené oznámení z webu [Azure Portal] podle popisu v předchozí části.

3. Nabízená odeslání se zašlou na všechna zařízení, která jsou registrovaná pro příjem oznámení z konkrétní centra oznámení.

    ![Test příjmu nabízených oznámení aplikace iOS][35]

## <a name="next-steps"></a>Další postup

V tomto příkladu jste vysílali nabízená oznámení pro všechna vaše registrovaná zařízení iOS. Pokud se chcete naučit zasílat nabízená oznámení určitým zařízením s iOSem, pokračujte následujícím kurzem:

> [!div class="nextstepaction"]
>[Zasílání nabízených oznámení do konkrétních zařízení](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png

<!-- URLs. -->
[Windows Azure Messaging Framework]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs Notify Users for iOS with .NET backend]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Azure Portal]: https://portal.azure.com
