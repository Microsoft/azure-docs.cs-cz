---
title: Přidání ověřování v systému iOS pomocí Azure Mobile Apps
description: Zjistěte, jak používat Azure Mobile Apps k ověřování uživatelů vaší aplikace pro iOS prostřednictvím různých poskytovatelů identit, včetně AAD, Google, Facebook, Twitter a Microsoft.
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
ms.openlocfilehash: 8c1c52790065015977add7e32a06063057b24dad
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128145"
---
# <a name="add-authentication-to-your-ios-app"></a>Přidání ověřování do aplikace pro iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

V tomto kurzu, přidat ověřování [Rychlý start pro iOS] projektu pomocí zprostředkovatele identity podporovaná. Tento kurz je založený na [Rychlý start pro iOS] kurzu, který je třeba nejprve provést.

## <a name="register"></a>Registrace aplikace pro ověřování a konfigurace služby App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Přidání aplikace do adresy URL pro povolené externího přesměrování

Zabezpečené ověřování, musíte definovat nové schéma adresy URL pro vaši aplikaci.  To umožňuje ověřování systému přesměrovat zpět do aplikace po dokončení procesu ověřování.  V tomto kurzu používáme schéma adresy URL _appname_ v průběhu.  Můžete ale použít jakékoli schéma adresy URL, kterou zvolíte.  Musí být jedinečné pro vaši mobilní aplikaci.  Pokud chcete povolit přesměrování na straně serveru th:

1. V [Azure Portal], vyberte službu App Service.

2. Klikněte na tlačítko **ověřování / autorizace** nabídky.

3. Klikněte na tlačítko **Azure Active Directory** pod **zprostředkovatelé ověřování** oddílu.

4. Nastavte **režim správy** k **Upřesnit**.

5. V **povolené externí adresy URL pro přesměrování**, zadejte `appname://easyauth.callback`.  _Appname_ v tomto řetězci je schéma adresy URL pro vaši mobilní aplikaci.  Měla by odpovídat specifikaci normální adresu URL pro určitý protokol (použití písmena a čísla jenom a začíná písmenem).  By měl poznamenejte řetězce, který zvolíte, jako je třeba upravit kód mobilní aplikace s schéma adresy URL na několika místech.

6. Klikněte na **OK**.

7. Klikněte na **Uložit**.

## <a name="permissions"></a>Omezit oprávnění k ověření uživatelé
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

V Xcode, stiskněte klávesu **spustit** a spusťte aplikaci. Je vyvolána výjimka, protože se aplikace pokusí o přístup k back-end jako neověřený uživatel, ale *TodoItem* tabulka nyní vyžaduje ověřování.

## <a name="add-authentication"></a>Přidání ověřování do aplikace
**Objective-C**:

1. Na počítači Mac otevřete *QSTodoListViewController.m* v prostředí Xcode a přidejte následující metodu:

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

    Změna *google* k *microsoftaccount*, *twitter*, *facebook*, nebo *windowsazureactivedirectory* Pokud nepoužíváte Google jako zprostředkovatele identity. Pokud používáte sítě Facebook, je nutné [seznamu povolených IP adres služby Facebook domén] [ 1] ve vaší aplikaci.

    Nahradit **urlScheme** s jedinečným názvem aplikace.  UrlScheme by měl být stejný jako schéma adresy URL protokolu, který jste zadali v **povolené externí adresy URL pro přesměrování** pole na webu Azure Portal. UrlScheme používá zpětné volání pro ověřování Pokud chcete přepnout zpět do aplikace po dokončení žádosti o ověření.

2. Nahraďte `[self refresh]` v `viewDidLoad` v *QSTodoListViewController.m* následujícím kódem:

    ```Objective-C
    [self loginAndGetData];
    ```

3. Otevřít `QSAppDelegate.h` a přidejte následující kód:

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Otevřít `QSAppDelegate.m` a přidejte následující kód:

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

   Přidejte tento kód bezprostředně před čtení řádku `#pragma mark - Core Data stack`.  Nahradit _appname_ urlScheme hodnotou, kterou jste použili v kroku 1.

5. Otevřít `AppName-Info.plist` (nahrazení AppName s názvem vaší aplikace) a přidejte následující kód:

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

    Tento kód by měl být umístěn uvnitř `<dict>` elementu.  Nahradit _appname_ řetězce (v rámci pole pro **CFBundleURLSchemes**) s názvem aplikace, kterou jste zvolili v kroku 1.  Můžete provést také tyto změny v souboru plist editor – kliknutím na `AppName-Info.plist` souborů v XCode otevřete plist editor.

    Nahradit `com.microsoft.azure.zumo` řetězce pro **CFBundleURLName** s vaší společnosti Apple identifikátor sady prostředků.

6. Stisknutím klávesy *spustit* spusťte aplikaci a pak se přihlásit. Pokud jste přihlášeni, je třeba zobrazit seznam úkolů a aktualizace.

**Swift**:

1. Na počítači Mac otevřete *ToDoTableViewController.swift* v prostředí Xcode a přidejte následující metodu:

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

    Změna *google* k *microsoftaccount*, *twitter*, *facebook*, nebo *windowsazureactivedirectory* Pokud nepoužíváte Google jako zprostředkovatele identity. Pokud používáte sítě Facebook, je nutné [seznamu povolených IP adres služby Facebook domén] [ 1] ve vaší aplikaci.

    Nahradit **urlScheme** s jedinečným názvem aplikace.  UrlScheme by měl být stejný jako schéma adresy URL protokolu, který jste zadali v **povolené externí adresy URL pro přesměrování** pole na webu Azure Portal. UrlScheme používá zpětné volání pro ověřování Pokud chcete přepnout zpět do aplikace po dokončení žádosti o ověření.

2. Odebrat řádky `self.refreshControl?.beginRefreshing()` a `self.onRefresh(self.refreshControl)` na konci `viewDidLoad()` v *ToDoTableViewController.swift*. Přidejte volání do `loginAndGetData()` místo nich:

    ```swift
    loginAndGetData()
    ```

3. Otevřít `AppDelegate.swift` a přidejte následující řádek, který `AppDelegate` třídy:

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

    Nahradit _appname_ urlScheme hodnotou, kterou jste použili v kroku 1.

4. Otevřít `AppName-Info.plist` (nahrazení AppName s názvem vaší aplikace) a přidejte následující kód:

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

    Tento kód by měl být umístěn uvnitř `<dict>` elementu.  Nahradit _appname_ řetězce (v rámci pole pro **CFBundleURLSchemes**) s názvem aplikace, kterou jste zvolili v kroku 1.  Můžete provést také tyto změny v souboru plist editor – kliknutím na `AppName-Info.plist` souborů v XCode otevřete plist editor.

    Nahradit `com.microsoft.azure.zumo` řetězce pro **CFBundleURLName** s vaší společnosti Apple identifikátor sady prostředků.

5. Stisknutím klávesy *spustit* spusťte aplikaci a pak se přihlásit. Pokud jste přihlášeni, je třeba zobrazit seznam úkolů a aktualizace.

Ověřování pomocí služby App Service používá jablka komunikaci mezi aplikacemi.  Další podrobnosti k tomuto tématu najdete [dokumentaci společnosti Apple][2]
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure Portal]: https://portal.azure.com

[Rychlý start pro iOS]: app-service-mobile-ios-get-started.md

