---
title: Posílání nabízených oznámení do systému iOS pomocí Azure Notification Hubs a sady iOS SDK verze 3.0.0 Preview 1
description: V tomto kurzu se naučíte používat Azure Notification Hubs a službu Apple Push Notification Service k odesílání nabízených oznámení do zařízení se systémem iOS (verze 3.0.0-preview1).
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: f905bfa830bfc78caa6ebb02ae49d4839168367b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100627351"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs-sdk-for-apple"></a>Kurz: odesílání nabízených oznámení do aplikací pro iOS pomocí Azure Notification Hubs SDK pro Apple

V tomto kurzu se dozvíte, jak používat Azure Notification Hubs k odesílání nabízených oznámení do aplikace pro iOS pomocí sady Azure Notification Hubs SDK pro Apple.

Tento kurz se zabývá následujícími kroky:

- Vytvořte si ukázkovou aplikaci pro iOS.
- Připojte svoji aplikaci pro iOS k Azure Notification Hubs.
- Odesílat testovací nabízená oznámení.
- Ověřte, že aplikace přijímá oznámení.

Kompletní kód pro tento kurz si můžete stáhnout [z GitHubu](https://github.com/Azure/azure-notificationhubs-ios/tree/main/SampleNHAppObjC).

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu budete potřebovat následující požadavky:

- Počítač Mac se systémem [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)spolu s platným certifikátem pro vývojáře nainstalovaným do řetězce klíčů.
- IPhone nebo iPad se systémem iOS verze 10 nebo novější.
- Vaše fyzické zařízení je zaregistrované na [portálu Apple](https://developer.apple.com/)a přidruženo k vašemu certifikátu.

Než budete pokračovat, přečtěte si předchozí kurz na webu Začínáme s [Azure Notification Hubs pro aplikace pro iOS](ios-sdk-get-started.md)a nastavte a nakonfigurujte přihlašovací údaje pro nabízené oznámení v centru oznámení. I když nemáte žádné předchozí zkušenosti s vývojem pro iOS, měli byste být schopni postupovat podle těchto kroků.

> [!NOTE]
> Z důvodu požadavků na konfiguraci pro nabízená oznámení musíte místo emulátoru iOS nasadit a otestovat nabízená oznámení na fyzickém zařízení iOS (iPhone nebo iPad).

## <a name="connect-your-ios-app-to-notification-hubs"></a>Připojte aplikaci iOS k centru oznámení

1. V Xcode vytvořte nový projekt iOS a vyberte šablonu **Jediné zobrazení aplikace**.

   :::image type="content" source="media/ios-sdk/image1.png" alt-text="Vybrat šablonu":::

2. Když nastavujete možnosti pro nový projekt, nezapomeňte použít stejný **Název produktu** a **Identifikátor organizace**, který jste použili při nastavení identifikátoru sady na portálu pro vývojáře Apple.

3. V části Navigátor projektu vyberte název vašeho projektu v části **cíle** a pak vyberte kartu **možnosti podepisování &** . Ujistěte se, že jste vybrali příslušný **tým** pro svůj účet Apple Developer. XCode by na základě vašeho identifikátoru sady mělo automaticky stáhnout profil zřizování, který jste vytvořili dříve.

   Pokud nevidíte nový profil zřizování, který jste vytvořili v Xcode, pokuste se aktualizovat profily pro podpisové identity. Klikněte na tlačítko **Xcode** na panelu nabídek, klikněte na tlačítko **Předvolby**, klikněte na kartu **Účet**, klikněte na tlačítko **Zobrazit podrobnosti**, klikněte na podpisovou identitu a pak klikněte na tlačítko Aktualizovat v pravém dolním rohu.

   :::image type="content" source="media/ios-sdk/image2.png" alt-text="Zobrazit podrobnosti":::

4. Na kartě **Možnosti podpisového &** vyberte **+ funkce**. Dvojím kliknutím na **nabízená oznámení** ji povolte.

   :::image type="content" source="media/ios-sdk/image3.png" alt-text="Funkce":::

5. Přidejte moduly Azure Notification Hubs SDK.

   Sadu Azure Notification Hubs SDK můžete integrovat do své aplikace pomocí [Cocoapods](https://cocoapods.org/) nebo ručním přidáním binárních souborů do projektu.

   - Integrace přes Cocoapods: přidejte do svého souboru podfile následující závislosti, které budou zahrnovat sadu Azure Notification Hubs SDK do vaší aplikace:

      ```ruby
      pod 'AzureNotificationHubs-iOS'
      ```

      - Spuštěním instalace pod nainstalujete instalaci nově definovaného bodu pod a otevřete. xcworkspace.

         Pokud se zobrazí chyba, jako je například, **že se nepovedlo najít specifikaci pro AzureNotificationHubs-iOS** při spuštění po instalaci, spusťte příkaz, `pod repo update` abyste získali nejnovější lusky z úložiště Cocoapods a pak spustíte pod instalací.

   - Integrace přes Carthage: přidejte do svého Cartfile následující závislosti, které budou zahrnovat sadu Azure Notification Hubs SDK do vaší aplikace:

      ```ruby
      github "Azure/azure-notificationhubs-ios"
      ```

      - Potom aktualizujte závislosti sestavení:

      ```shell
      $ carthage update
      ```

      Další informace o použití Carthage najdete v [úložišti GitHub Carthage](https://github.com/Carthage/Carthage).

   - Integrace zkopírováním binárních souborů do projektu:

      Můžete integrovat zkopírováním binárních souborů do projektu následujícím způsobem:

        - Stáhněte si rozhraní [Azure Notification HUBS SDK](https://github.com/Azure/azure-notificationhubs-iOS/releases/) , které je k dispozici jako soubor zip, a rozbalte ho.

        - V Xcode klikněte pravým tlačítkem na projekt a klikněte na možnost **Přidat soubory do** a přidejte složku **WindowsAzureMessaging.framework** do projektu Xcode. Vyberte **Možnosti**, ujistěte se, že je vybraná možnost **Kopírovat položky v případě potřeby**, a pak klikněte na **Přidat**.

          :::image type="content" source="media/ios-sdk/image4.png" alt-text="Přidat rozhraní":::

6. Přidejte nebo upravte soubor s názvem **DevSettings. plist** , který obsahuje dvě vlastnosti, `CONNECTION_STRING` pro připojovací řetězec do centra oznámení Azure a `HUB_NAME` pro název centra oznámení Azure.

7. Do příslušné části přidejte informace pro připojení k Azure Notification Hubs `<string></string>` .  Nahraďte zástupné symboly řetězcového literálu `--HUB-NAME--` a `--CONNECTION-STRING--` názvem centra a **DefaultListenSharedAccessSignature**, v uvedeném pořadí, jak jste předtím získali z portálu:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
        <key>HUB_NAME</key>
        <string>--HUB-NAME--</string>
        <key>CONNECTION_STRING</key>
        <string>--CONNECTION-STRING--</string>
    </dict>
    </plist>
    ```

8. Ve stejném souboru **AppDelegate. m** nahraďte veškerý kód za `didFinishLaunchingWithOptions` následujícím kódem:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>

    // Extend the AppDelegate to listen for messages using MSNotificationHubDelegate and User Notification Center
    @interface AppDelegate () <MSNotificationHubDelegate>

    @end

    @implementation AppDelegate
    
    @synthesize notificationPresentationCompletionHandler;
    @synthesize notificationResponseCompletionHandler;

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

        NSString *path = [[NSBundle mainBundle] pathForResource:@"DevSettings" ofType:@"plist"];
        NSDictionary *configValues = [NSDictionary dictionaryWithContentsOfFile:path];
        
        NSString *connectionString = [configValues objectForKey:@"CONNECTION_STRING"];
        NSString *hubName = [configValues objectForKey:@"HUB_NAME"];

        if([connectionString length] != 0 && [hubName length] != 0) {
            [[UNUserNotificationCenter currentNotificationCenter] setDelegate:self];
            [MSNotificationHub setDelegate:self];
            [MSNotificationHub initWithConnectionString:connectionString withHubName:hubName];
    
            return YES;
        }

        NSLog(@"Please setup CONNECTION_STRING and HUB_NAME in DevSettings.plist and restart application");

        exit(-1);
    }

    - (void)notificationHub:(MSNotificationHub *)notificationHub didReceivePushNotification:(MSNotificationHubMessage *)message {
        // Send message using NSNotificationCenter with the message
        NSDictionary *userInfo = [NSDictionary dictionaryWithObject:message forKey:@"message"];
        [[NSNotificationCenter defaultCenter] postNotificationName:@"MessageReceived" object:nil userInfo:userInfo];
    }

    @end
    ```

    Tento kód se připojí k centru oznámení pomocí informací o připojení, které jste zadali v **DevSettings. plist**. Pak přiřadí token zařízení do centra oznámení tak, aby centrum mohlo odesílat oznámení.

### <a name="create-notificationdetailviewcontroller-header-file"></a>Vytvořit soubor hlaviček NotificationDetailViewController

1. Podobně jako v předchozích pokynech přidejte další hlavičkový soubor s názvem **SetupViewController. h**. Nahraďte obsah nového hlavičkového souboru následujícím kódem:

   ```objc
   #import <UIKit/UIKit.h>

   NS_ASSUME_NONNULL_BEGIN

   @interface SetupViewController : UIViewController

   @end

   NS_ASSUME_NONNULL_END
   ```

2. Přidejte implementační soubor **SetupViewController. m**. Nahraďte obsah souboru následujícím kódem, který implementuje metody UIViewController:

   ```objc
   #import "SetupViewController.h"

    static NSString *const kNHMessageReceived = @"MessageReceived";
    
    @interface SetupViewController ()
    
    @end

    @implementation SetupViewController

    - (void)viewDidLoad {
        [super viewDidLoad];
        
        // Listen for messages using NSNotificationCenter
        [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(didReceivePushNotification:) name:kNHMessageReceived object:nil];
    }

    - (void)dealloc {
        // Clean up subscription to NSNotificationCenter
        [[NSNotificationCenter defaultCenter] removeObserver:self name:kNHMessageReceived object:nil];
    }

    - (void)didReceivePushNotification:(NSNotification *)notification {
        MSNotificationHubMessage *message = [notification.userInfo objectForKey:@"message"];

        // Create UI Alert controller with message title and body
        UIAlertController *alertController = [UIAlertController alertControllerWithTitle:message.title
                             message:message.body
                      preferredStyle:UIAlertControllerStyleAlert];
        [alertController addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleCancel handler:nil]];
        [self presentViewController:alertController animated:YES completion:nil];
        
        // Dismiss after 2 seconds
        dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
            [alertController dismissViewControllerAnimated:YES completion: nil];
        });

    }

    @end
   ```

3. Ověřte, zda nedochází k žádným chybám tak, že sestavíte a spustíte aplikaci na vašem zařízení.

## <a name="send-test-push-notifications"></a>Odešlete nabízená oznámení

Příjem oznámení ve vaší aplikaci můžete otestovat pomocí možnosti **Testovací odeslání** na webu [Azure Portal](https://portal.azure.com/). Do zařízení se odešle testovací nabízené oznámení.

:::image type="content" source="media/ios-sdk/image6.png" alt-text="Odeslat test":::

Nabízená oznámení se většinou posílají ve službě back-end, jako je služba Mobile Apps, nebo v technologii ASP.NET pomocí kompatibilní knihovny. Pokud pro váš back-end není dostupná žádná knihovna, můžete k posílání oznámení použít také REST API přímo.

Tady je seznam některých dalších kurzů, které byste mohli chtít zkontrolovat při odesílání oznámení:

- Azure Mobile Apps: Příklad odesílání oznámení z back-endu Mobile Apps, který je integrovaný do Notification Hubs, najdete v tématu [Přidání nabízených oznámení do vaší aplikace pro iOS](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).
- ASP.NET: [použijte Notification Hubs k odesílání nabízených oznámení uživatelům](notification-hubs-aspnet-backend-ios-apple-apns-notification.md).
- Sada Azure Notification Hubs Java SDK: Informace o odesílání oznámení z Javy najdete v článku [Jak používat Notification Hubs z Javy](notification-hubs-java-push-notification-tutorial.md). Tato metoda prošla pro potřeby vývoje pro Android testováním v Eclipse.
- PHP: [Jak používat Notification Hubs z PHP](notification-hubs-php-push-notification-tutorial.md).

## <a name="verify-that-your-app-receives-push-notifications"></a>Ověření, že aplikace přijímá nabízená oznámení

Chcete-li otestovat nabízená oznámení na iOS, musíte aplikaci nasadit do fyzického zařízení iOS. Nabízená oznámení Apple nemůžete odesílat pomocí simulátoru iOS.

1. Spusťte aplikaci a ověřte, zda byla registrace úspěšná a stiskněte klávesu **OK**.

   :::image type="content" source="media/ios-sdk/image7.png" alt-text="Zaregistrovat":::

2. Dále odešlete testovací nabízené oznámení z [Azure Portal](https://portal.azure.com/), jak je popsáno v předchozí části.

3. Nabízené oznámení se odešle na všechna zařízení, která jsou zaregistrovaná pro příjem oznámení z daného centra oznámení.

   :::image type="content" source="media/ios-sdk/image8.png" alt-text="Odeslat test":::

## <a name="next-steps"></a>Další kroky

V tomto jednoduchém příkladu vysíláte nabízená oznámení do všech registrovaných zařízení se systémem iOS. Pokud chcete zjistit, jak odesílat nabízená oznámení na konkrétní zařízení s iOS, přejděte k následujícímu kurzu:

[Kurz: nabízená oznámení na konkrétní zařízení](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

Další informace najdete v následujících článcích:

- [Přehled služby Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [Rozhraní REST API pro Notification Hubs](/rest/api/notificationhubs/)
- [Sada SDK Notification Hubs pro back-endové operace](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs SDK na GitHubu](https://github.com/Azure/azure-notificationhubs)
- [Registrovat do back-endu aplikace](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Správa registrací](notification-hubs-push-notification-registration-management.md)
- [Práce s značkami](notification-hubs-tags-segment-push-message.md)
- [Práce s vlastními šablonami](notification-hubs-templates-cross-platform-push-messages.md)
- [Řízení přístupu Service Bus pomocí sdílených přístupových podpisů](../service-bus-messaging/service-bus-sas.md)
- [Generování tokenů SAS prostřednictvím kódu programu](/rest/api/eventhub/generate-sas-token)
- [Apple Security: běžné šifrování](https://developer.apple.com/security/)
- [Čas v epocha systému UNIX](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)