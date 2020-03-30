---
title: Přidání ověřování v iOS
description: Přečtěte si, jak pomocí Mobilních aplikací Azure ověřovat uživatele vaší aplikace pro iOS prostřednictvím poskytovatelů identit, jako jsou AAD, Google, Facebook, Twitter a Microsoft.
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: fd7860053e8c04ca9d5e355a721afd834835a441
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459019"
---
# <a name="add-authentication-to-your-ios-app"></a>Přidání ověřování do aplikace pro iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

V tomto kurzu přidáte ověřování do projektu [rychlého startu iOS] pomocí podporovaného zprostředkovatele identity. Tento výukový program je založen na kurzu [rychlého startu iOS,] který musíte nejprve dokončit.

## <a name="register-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Registrace aplikace pro ověřování a konfigurace služby App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Přidání aplikace do adres URL allowed externího přesměrování

Zabezpečené ověřování vyžaduje, abyste pro aplikaci definovali nové schéma adres URL.  To umožňuje ověřovacímu systému přesměrovat zpět do aplikace po dokončení procesu ověřování.  V tomto kurzu používáme _název aplikace_ schématu ADRES URL v celém textu.  Můžete však použít libovolné schéma adres URL, které zvolíte.  To by mělo být jedinečné pro vaši mobilní aplikaci.  Povolení přesměrování na straně serveru:

1. Na [webu Azure Portal]vyberte službu App Service.

2. Klepněte na možnost nabídky **Ověřování / Autorizace.**

3. V části **Zprostředkovatelé ověřování** klikněte na **službu Azure Active Directory.**

4. Nastavte **režim správy** na **Upřesnit**.

5. V **adresách URL allowed external redirect zadejte** `appname://easyauth.callback`.  _Název aplikace_ v tomto řetězci je schéma adresy URL pro vaši mobilní aplikaci.  Měl by se řídit běžnou specifikací adresy URL protokolu (používejte pouze písmena a čísla a začněte písmenem).  Měli byste si poznamenat řetězec, který zvolíte, protože budete muset upravit kód mobilní aplikace pomocí schématu adres URL na několika místech.

6. Klikněte na tlačítko **OK**.

7. Klikněte na **Uložit**.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Omezení oprávnění pro ověřené uživatele
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

V Xcode spusťte aplikaci stisknutím **klávesy Spustit.** Výjimka je vyvolána, protože aplikace se pokusí o přístup k back-endu jako neověřený uživatel, ale tabulka *TodoItem* nyní vyžaduje ověření.

## <a name="add-authentication-to-app"></a><a name="add-authentication"></a>Přidání ověřování do aplikace
**Cíl C**:

1. Na Macu otevřete *qstodolistviewcontroller.m* v Xcode a přidejte následující metodu:

    ```Objective-C
    - (void)loginAndGetData
    {
        QSAppDelegate *appDelegate = (QSAppDelegate *)[UIApplication sharedApplication].delegate;
        appDelegate.qsTodoService = self.todoService;

        [self.todoService.client loginWithProvider:@"google" urlScheme:@"appname" controller:self animated:YES completion:^(MSUser * _Nullable user, NSError * _Nullable error) {
            if (error) {
                NSLog(@"Login failed with error: %@, %@", error, [error userInfo]);
            }
            else {
                self.todoService.client.currentUser = user;
                NSLog(@"User logged in: %@", user.userId);

                [self refresh];
            }
        }];
    }
    ```

    Pokud *google* nepoužíváte jako poskytovatele identity, změňte google na *MicrosoftAccount*, *Twitter*, *Facebook*nebo *windowsazureactivedirectory.* Pokud používáte Facebook, musíte ve své aplikaci [zařadit domény Facebooku na seznam povolených.][1]

    Nahraďte **urlScheme** jedinečným názvem pro vaši aplikaci.  UrlScheme by měl být stejný jako protokol URL scheme, který jste zadali v poli **Povolené adresy URL externího přesměrování** na webu Azure Portal. UrlScheme se používá zpětné volání ověřování přepnout zpět do aplikace po dokončení požadavku na ověření.

2. Nahraďte `[self refresh]` v `viewDidLoad` *souboru QSTodoListViewController.m* následujícím kódem:

    ```Objective-C
    [self loginAndGetData];
    ```

3. Otevřete soubor `QSAppDelegate.h` a přidejte následující kód:

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Otevřete soubor `QSAppDelegate.m` a přidejte následující kód:

    ```Objective-C
    - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
        if ([[url.scheme lowercaseString] isEqualToString:@"appname"]) {
            // Resume login flow
            return [self.qsTodoService.client resumeWithURL:url];
        }
        else {
            return NO;
        }
    }
    ```

   Přidejte tento kód přímo `#pragma mark - Core Data stack`před řádek čtení .  Nahraďte _název aplikace_ hodnotou urlScheme, kterou jste použili v kroku 1.

5. Otevřete `AppName-Info.plist` soubor (nahrazení AppName názvem aplikace) a přidejte následující kód:

    ```XML
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Tento kód by měl `<dict>` být umístěn uvnitř prvku.  Nahraďte řetězec _název aplikace_ (v poli pro **CFBundleURLSchemes**) názvem aplikace, který jste zvolili v kroku 1.  Můžete také provést tyto změny v editoru `AppName-Info.plist` plist - klikněte na soubor v XCode pro otevření editoru plist.

    Nahraďte `com.microsoft.azure.zumo` řetězec pro **CFBundleURLName** identifikátorem svazku Apple.

6. Stisknutím *klávesy Spustit* aplikaci spusťte a přihlaste se. Když jste přihlášeni, měli byste být schopni zobrazit seznam úkolů a provést aktualizace.

**Rychlý**:

1. Na Macu otevřete *toDoTableViewController.swift* v Xcode a přidejte následující metodu:

    ```swift
    func loginAndGetData() {

        guard let client = self.table?.client, client.currentUser == nil else {
            return
        }

        let appDelegate = UIApplication.shared.delegate as! AppDelegate
        appDelegate.todoTableViewController = self

        let loginBlock: MSClientLoginBlock = {(user, error) -> Void in
            if (error != nil) {
                print("Error: \(error?.localizedDescription)")
            }
            else {
                client.currentUser = user
                print("User logged in: \(user?.userId)")
            }
        }

        client.login(withProvider:"google", urlScheme: "appname", controller: self, animated: true, completion: loginBlock)

    }
    ```

    Pokud *google* nepoužíváte jako poskytovatele identity, změňte google na *MicrosoftAccount*, *Twitter*, *Facebook*nebo *windowsazureactivedirectory.* Pokud používáte Facebook, musíte ve své aplikaci [zařadit domény Facebooku na seznam povolených.][1]

    Nahraďte **urlScheme** jedinečným názvem pro vaši aplikaci.  UrlScheme by měl být stejný jako protokol URL scheme, který jste zadali v poli **Povolené adresy URL externího přesměrování** na webu Azure Portal. UrlScheme se používá zpětné volání ověřování přepnout zpět do aplikace po dokončení požadavku na ověření.

2. Odeberte `self.refreshControl?.beginRefreshing()` `self.onRefresh(self.refreshControl)` řádky a `viewDidLoad()` na konci v *ToDoTableViewController.swift*. Přidat volání `loginAndGetData()` na jejich místo:

    ```swift
    loginAndGetData()
    ```

3. Otevřete `AppDelegate.swift` soubor a přidejte `AppDelegate` do třídy následující řádek:

    ```swift
    var todoTableViewController: ToDoTableViewController?

    func application(_ application: UIApplication, openURL url: NSURL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {
        if url.scheme?.lowercased() == "appname" {
            return (todoTableViewController!.table?.client.resume(with: url as URL))!
        }
        else {
            return false
        }
    }
    ```

    Nahraďte _název aplikace_ hodnotou urlScheme, kterou jste použili v kroku 1.

4. Otevřete `AppName-Info.plist` soubor (nahrazení AppName názvem aplikace) a přidejte následující kód:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Tento kód by měl `<dict>` být umístěn uvnitř prvku.  Nahraďte řetězec _název aplikace_ (v poli pro **CFBundleURLSchemes**) názvem aplikace, který jste zvolili v kroku 1.  Můžete také provést tyto změny v editoru `AppName-Info.plist` plist - klikněte na soubor v XCode pro otevření editoru plist.

    Nahraďte `com.microsoft.azure.zumo` řetězec pro **CFBundleURLName** identifikátorem svazku Apple.

5. Stisknutím *klávesy Spustit* aplikaci spusťte a přihlaste se. Když jste přihlášeni, měli byste být schopni zobrazit seznam úkolů a provést aktualizace.

Ověřování pomocí služby App Service používá meziaplikacovou komunikaci Apples.  Další podrobnosti k tomuto tématu naleznete v [dokumentaci společnosti Apple][2]
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Portál Azure]: https://portal.azure.com

[Rychlý start iOS]: app-service-mobile-ios-get-started.md

