---
title: Přidání ověřování v iOS pomocí Azure Mobile Apps
description: Naučte se používat Azure Mobile Apps k ověřování uživatelů vaší aplikace pro iOS prostřednictvím různých poskytovatelů identit, jako jsou AAD, Google, Facebook, Twitter a Microsoft.
services: app-service\mobile
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 674d5f783f43011ba154b668cea4ec41f6a945f5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025285"
---
# <a name="add-authentication-to-your-ios-app"></a>Přidání ověřování do aplikace pro iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center podporuje vývoj koncových a integrovaných služeb od centrálního vývoje mobilních aplikací. Vývojáři **mohou pomocí sestavování**, **testování** a **distribuce** služeb nastavit kanál průběžné integrace a doručování. Po nasazení aplikace mohou vývojáři sledovat stav a využití své aplikace pomocí **analytických** a **diagnostických** služeb a spolupracovat s uživateli pomocí služby **push** . Vývojáři můžou také využít **ověřování** k ověřování uživatelů a **datových** služeb, aby zachovaly a synchronizovaly data aplikací v cloudu.
> Pokud chcete v mobilní aplikaci integrovat cloudové služby, zaregistrujte se App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) ještě dnes.

V tomto kurzu přidáte do projektu [rychlý Start pro iOS] ověřování pomocí podporovaného zprostředkovatele identity. Tento kurz je založený na [rychlý Start pro iOS] , který musíte nejdřív provést.

## <a name="register"></a>Zaregistrovat aplikaci pro ověřování a nakonfigurovat App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Přidání aplikace do povolených externích adres URL pro přesměrování

Zabezpečené ověřování vyžaduje, abyste pro svou aplikaci nadefinovali nové schéma URL.  To umožňuje, aby systém ověřování po dokončení procesu ověřování přesměroval zpátky do vaší aplikace.  V tomto kurzu používáme _celé rozhraní_ příkazového schématu URL.  Můžete ale použít jakékoli schéma URL, které si zvolíte.  Měl by být jedinečný pro vaši mobilní aplikaci.  Zapnutí přesměrování na straně serveru:

1. V [Azure Portal]vyberte App Service.

2. Klikněte na možnost nabídka **ověřování/autorizace** .

3. V části **Zprostředkovatelé ověřování** klikněte na **Azure Active Directory** .

4. Nastavte **režim správy** na **Upřesnit**.

5. Do pole **povolené externí adresy URL pro přesměrování**zadejte `appname://easyauth.callback`.  _AppName_ v tomto řetězci je schéma adresy URL vaší mobilní aplikace.  Měla by následovat po běžné specifikaci adresy URL protokolu (použijte pouze písmena a čísla a začněte písmenem).  Měli byste si poznamenat řetězec, který si zvolíte, protože budete muset upravit kód mobilní aplikace pomocí schématu adresy URL na několika místech.

6. Klikněte na **OK**.

7. Klikněte na **Uložit**.

## <a name="permissions"></a>Omezení oprávnění pro ověřené uživatele
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

V Xcode spusťte aplikaci stisknutím klávesy **Run** . Vyvolá se výjimka, protože se aplikace pokusí o přístup k back-endu jako neověřený uživatel, ale tabulka *TodoItem* nyní vyžaduje ověření.

## <a name="add-authentication"></a>Přidání ověřování do aplikace
**Cíl-C**:

1. Na Macu otevřete *QSTodoListViewController. m* v Xcode a přidejte následující metodu:

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

    Pokud nepoužíváte Google jako poskytovatele identity, změňte *Google* na *MicrosoftAccount*, *Twitter*, *Facebook*nebo *windowsazureactivedirectory* . Pokud používáte Facebook, musíte v aplikaci povolit [domény Facebooku][1] .

    Nahraďte **urlScheme** jedinečným názvem vaší aplikace.  UrlScheme by měl být stejný jako protokol schématu adresy URL, který jste zadali v poli **povolené adresy URL externích přesměrování** v Azure Portal. UrlScheme používá zpětné volání ověřování k přepínání zpátky do aplikace po dokončení žádosti o ověření.

2. Nahraďte `[self refresh]` v `viewDidLoad` v *QSTodoListViewController. m* následujícím kódem:

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

   Přidejte tento kód přímo před čtením řádku `#pragma mark - Core Data stack`.  Nahraďte _AppName_ hodnotou urlScheme, kterou jste použili v kroku 1.

5. Otevřete soubor `AppName-Info.plist` (nahraďte AppName názvem vaší aplikace) a přidejte následující kód:

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

    Tento kód by měl být umístěn uvnitř elementu `<dict>`.  Nahraďte řetězec _AppName_ (v rámci pole pro **CFBundleURLSchemes**) názvem aplikace, který jste zvolili v kroku 1.  Tyto změny můžete provést také v editoru plist – kliknutím na soubor `AppName-Info.plist` v XCode otevřete Editor plist.

    Nahraďte řetězec `com.microsoft.azure.zumo` pro **CFBundleURLName** pomocí identifikátoru Apple sady prostředků.

6. Stisknutím klávesy *Run* spusťte aplikaci a pak se přihlaste. Když jste přihlášeni, měli byste být schopni zobrazit seznam úkolů a provést aktualizace.

**SWIFT**:

1. Na Macu otevřete *ToDoTableViewController. SWIFT* v Xcode a přidejte následující metodu:

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

    Pokud nepoužíváte Google jako poskytovatele identity, změňte *Google* na *MicrosoftAccount*, *Twitter*, *Facebook*nebo *windowsazureactivedirectory* . Pokud používáte Facebook, musíte v aplikaci povolit [domény Facebooku][1] .

    Nahraďte **urlScheme** jedinečným názvem vaší aplikace.  UrlScheme by měl být stejný jako protokol schématu adresy URL, který jste zadali v poli **povolené adresy URL externích přesměrování** v Azure Portal. UrlScheme používá zpětné volání ověřování k přepínání zpátky do aplikace po dokončení žádosti o ověření.

2. Odebere řádky `self.refreshControl?.beginRefreshing()` a `self.onRefresh(self.refreshControl)` na konci `viewDidLoad()` v *ToDoTableViewController. SWIFT*. Přidejte volání `loginAndGetData()` na svém místě:

    ```swift
    loginAndGetData()
    ```

3. Otevřete soubor `AppDelegate.swift` a přidejte následující řádek do třídy `AppDelegate`:

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

    Nahraďte _AppName_ hodnotou urlScheme, kterou jste použili v kroku 1.

4. Otevřete soubor `AppName-Info.plist` (nahraďte AppName názvem vaší aplikace) a přidejte následující kód:

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

    Tento kód by měl být umístěn uvnitř elementu `<dict>`.  Nahraďte řetězec _AppName_ (v rámci pole pro **CFBundleURLSchemes**) názvem aplikace, který jste zvolili v kroku 1.  Tyto změny můžete provést také v editoru plist – kliknutím na soubor `AppName-Info.plist` v XCode otevřete Editor plist.

    Nahraďte řetězec `com.microsoft.azure.zumo` pro **CFBundleURLName** pomocí identifikátoru Apple sady prostředků.

5. Stisknutím klávesy *Run* spusťte aplikaci a pak se přihlaste. Když jste přihlášeni, měli byste být schopni zobrazit seznam úkolů a provést aktualizace.

Ověřování App Service používá komunikaci mezi aplikacemi jablek.  Další podrobnosti o tomto předmětu najdete v [dokumentaci Apple][2] .
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure Portal]: https://portal.azure.com

[rychlý Start pro iOS]: app-service-mobile-ios-get-started.md

