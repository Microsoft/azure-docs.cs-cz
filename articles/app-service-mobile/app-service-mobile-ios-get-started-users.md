---
title: Přidání ověřování v systému iOS pomocí Azure Mobile Apps
description: Naučte se používat Azure Mobile Apps ověřovat uživatele vaší aplikace pro iOS prostřednictvím řady různých zprostředkovatelů identity, včetně AAD, Google, Facebook, Twitter a Microsoft.
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: crdun
ms.openlocfilehash: e0eeee05ebad2e8148752f988bbbc2f6a0d7c296
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2018
ms.locfileid: "27592690"
---
# <a name="add-authentication-to-your-ios-app"></a>Přidání ověřování do vaší aplikace pro iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

V tomto kurzu přidáte ověřování [iOS rychlý start] projektu pomocí zprostředkovatele identity podporované. V tomto kurzu vychází z [iOS rychlý start] kurz, který je třeba nejprve provést.

## <a name="register"></a>Registrace aplikace pro ověřování a nakonfigurovat App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Přidání aplikace do adresy URL pro povolené externí přesměrování

Zabezpečené ověřování vyžaduje, můžete definovat nové schéma adresy URL pro vaši aplikaci.  To umožňuje ověřování systému přesměrovat zpět do aplikace po dokončení procesu ověřování.  V tomto kurzu používáme schématu adresy URL _appname_ v průběhu.  Můžete však použít žádné schéma adresy URL, které zvolíte.  Musí být jedinečné pro mobilní aplikace.  Chcete povolit přesměrování na straně serveru tý:

1. V [portál Azure], vyberte svoji službu aplikace.

2. Klikněte **ověřování / autorizace** možnost nabídky.

3. Klikněte na tlačítko **Azure Active Directory** pod **zprostředkovatele ověřování** části.

4. Nastavte **režim správy** k **Upřesnit**.

5. V **povoleno externí adres URL pro přesměrování**, zadejte `appname://easyauth.callback`.  _Appname_ v tento řetězec je schéma adresy URL pro mobilní aplikace.  Měl by splňovat specifikaci normální adresu URL pro určitý protokol (používejte písmena a čísla pouze a začněte s písmenem).  Měli byste si poznamenat řetězce, který zvolíte, jako je třeba upravit kód mobilní aplikace s schéma adresy URL na několika místech.

6. Klikněte na **OK**.

7. Klikněte na **Uložit**.

## <a name="permissions"></a>Omezit oprávnění k ověření uživatelé
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

V Xcode, stiskněte klávesu **spustit** a spusťte aplikaci. Je vyvolána výjimka, protože se aplikace pokusí o přístup k back-end jako neověřený uživatel, ale *TodoItem* tabulka nyní vyžaduje ověření.

## <a name="add-authentication"></a>Přidejte ověřování do aplikace
**Jazyka Objective-C**:

1. Na počítači Mac otevřete *QSTodoListViewController.m* v Xcode a přidejte následující metodu:

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

    Změna *google* k *microsoftaccount*, *twitter*, *facebook*, nebo *windowsazureactivedirectory* Pokud nepoužíváte Google jako zprostředkovatele identity. Pokud používáte sítě Facebook, musíte [povolených domén Facebook] [ 1] ve vaší aplikaci.

    Nahraďte **urlScheme** s jedinečným názvem pro vaši aplikaci.  UrlScheme by měla být stejná jako schéma adresy URL protokolu, který jste zadali v **povoleno externí adres URL pro přesměrování** pole na portálu Azure. UrlScheme je používán zpětné volání ověřování přepnout zpět do aplikace po dokončení žádosti o ověření.

2. Nahraďte `[self refresh]` v `viewDidLoad` v *QSTodoListViewController.m* následujícím kódem:

    ```Objective-C
    [self loginAndGetData];
    ```

3. Otevřete `QSAppDelegate.h` souboru a přidejte následující kód:

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Otevřete `QSAppDelegate.m` souboru a přidejte následující kód:

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

   Přidejte tento kód přímo před při čtení řádku `#pragma mark - Core Data stack`.  Nahraďte _appname_ wih urlScheme hodnotu, která jste použili v kroku 1.

5. Otevřete `AppName-Info.plist` souboru (nahraďte AppName s názvem vaší aplikace) a přidejte následující kód:

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

    Tento kód by měl být umístěn uvnitř `<dict>` elementu.  Nahraďte _appname_ řetězec (v rámci pole pro **CFBundleURLSchemes**) s názvem aplikace, který jste zvolili v kroku 1.  Můžete provést také tyto změny v plist. editoru – kliknutím na tlačítko `AppName-Info.plist` souboru v XCode otevřete plist editor.

    Nahraďte `com.microsoft.azure.zumo` řetězec **CFBundleURLName** identifikátor sady vaší společnosti Apple.

6. Stiskněte klávesu *spustit* a spusťte aplikaci a znovu přihlásili. Pokud jste se přihlásili, byste měli moct zobrazit seznam úkolů a provádět aktualizace.

**Kód SWIFT**:

1. Na počítači Mac otevřete *ToDoTableViewController.swift* v Xcode a přidejte následující metodu:

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

    Změna *google* k *microsoftaccount*, *twitter*, *facebook*, nebo *windowsazureactivedirectory* Pokud nepoužíváte Google jako zprostředkovatele identity. Pokud používáte sítě Facebook, musíte [povolených domén Facebook] [ 1] ve vaší aplikaci.

    Nahraďte **urlScheme** s jedinečným názvem pro vaši aplikaci.  UrlScheme by měla být stejná jako schéma adresy URL protokolu, který jste zadali v **povoleno externí adres URL pro přesměrování** pole na portálu Azure. UrlScheme je používán zpětné volání ověřování přepnout zpět do aplikace po dokončení žádosti o ověření.

2. Odstranit řádky `self.refreshControl?.beginRefreshing()` a `self.onRefresh(self.refreshControl)` na konci `viewDidLoad()` v *ToDoTableViewController.swift*. Přidejte volání `loginAndGetData()` místo nich:

    ```swift
    loginAndGetData()
    ```

3. Otevřete `AppDelegate.swift` souboru a přidejte následující řádek do `AppDelegate` třídy:

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

    Nahraďte _appname_ wih urlScheme hodnotu, která jste použili v kroku 1.

4. Otevřete `AppName-Info.plist` souboru (nahraďte AppName s názvem vaší aplikace) a přidejte následující kód:

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

    Tento kód by měl být umístěn uvnitř `<dict>` elementu.  Nahraďte _appname_ řetězec (v rámci pole pro **CFBundleURLSchemes**) s názvem aplikace, který jste zvolili v kroku 1.  Můžete provést také tyto změny v plist. editoru – kliknutím na tlačítko `AppName-Info.plist` souboru v XCode otevřete plist editor.

    Nahraďte `com.microsoft.azure.zumo` řetězec **CFBundleURLName** identifikátor sady vaší společnosti Apple.

5. Stiskněte klávesu *spustit* a spusťte aplikaci a znovu přihlásili. Pokud jste se přihlásili, byste měli moct zobrazit seznam úkolů a provádět aktualizace.

Ověřování služby App Service používá jablka Inter aplikace komunikace.  Další podrobnosti o této problematice naleznete [dokumentaci od společnosti Apple][2]
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[portál Azure]: https://portal.azure.com

[iOS rychlý start]: app-service-mobile-ios-get-started.md

