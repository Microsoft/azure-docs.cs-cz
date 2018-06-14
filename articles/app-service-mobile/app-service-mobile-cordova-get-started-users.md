---
title: Přidání ověřování na Apache Cordova s Mobile Apps | Microsoft Docs
description: Naučte se používat Mobile Apps v Azure App Service ověřovat uživatele vaší aplikace Apache Cordova prostřednictvím řady různých zprostředkovatelů identity, včetně Google, Facebook, Twitter a společnosti Microsoft.
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: b5cce832ae7ae83552c2a5ded2f5f5bda0ac76bf
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2018
ms.locfileid: "27591942"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Přidání ověřování do vaší aplikace Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Souhrn
V tomto kurzu přidání ověřování do seznamu úkolů projektu pro rychlý start na Apache Cordova pomocí zprostředkovatele identity podporované. V tomto kurzu vychází z [Začínáme s Mobile Apps] kurz, který je třeba nejprve provést.

## <a name="register"></a>Registrace aplikace pro ověřování a nakonfigurovat App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Podívejte se na video zobrazující podobný postup.](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>Omezit oprávnění k ověření uživatelé
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Nyní můžete ověřit, že byl zakázán anonymní přístup k vaší back-end. V sadě Visual Studio:

* Otevřete projekt, který jste vytvořili, když jste dokončili kurz [Začínáme s Mobile Apps].
* Spusťte aplikaci **emulátor Google Android**.
* Ověřte, že k neočekávané chybě připojení se zobrazí po spuštění aplikace.

Potom aktualizujte aplikace k ověření uživatelů před vyžádáním prostředky z back-end mobilní aplikace.

## <a name="add-authentication"></a>Přidání ověřování do aplikace
1. Otevřete projekt v **Visual Studio**, pak otevřete `www/index.html` soubor pro úpravy.
2. Vyhledejte `Content-Security-Policy` značka meta v části head.  Přidejte hostitele OAuth do seznamu povolených zdrojů.

   | Poskytovatel | Název poskytovatele sady SDK | OAuth hostitele |
   |:--- |:--- |:--- |
   | Azure Active Directory | aad | https://login.microsoftonline.com |
   | Facebook | Facebook | https://www.Facebook.com |
   | Google | Google | https://accounts.Google.com |
   | Microsoft | MicrosoftAccount | https://Login.live.com |
   | Twitter | Služby Twitter. | https://API.Twitter.com |

    Příklad obsah-Security-Policy (implementována pro Azure Active Directory) je následující:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Nahraďte `https://login.microsoftonline.com` s hostitelem OAuth z výše uvedené tabulce.  Další informace o metaznačku zásady zabezpečení obsahu najdete v tématu [zásady zabezpečení obsahu dokumentaci].

    Někteří poskytovatelé ověřování nevyžadují změny zásad zabezpečení obsahu při použití na příslušné mobilních zařízeních.  Například žádné změny zásad zabezpečení obsahu jsou požadovaná při použití ověřování Google na zařízení s Androidem.

3. Otevřete `www/js/index.js` souboru pro úpravy, vyhledejte `onDeviceReady()` metoda, a v části Vytvoření klienta kód přidejte následující kód:

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Tento kód nahradí stávající kód, který vytvoří odkaz na tabulku a aktualizuje uživatelské rozhraní.

    Metoda login() začíná zprostředkovatele ověřování. Metoda login() je asynchronní funkce vracející JavaScript Promise.  Zbytek inicializace je umístěn uvnitř promise odpověď, a tedy není spuštěn až do dokončení metodu login().

4. V kódu, který jste právě přidali, nahraďte `SDK_Provider_Name` s názvem vaší zprostředkovatele přihlášení. Například pro Azure Active Directory, použijte `client.login('aad')`.
5. Spusťte projekt.  Po dokončení inicializace projekt aplikace zobrazuje stránku pro přihlášení OAuth zprostředkovatele zvolené ověřování.

## <a name="next-steps"></a>Další kroky
* Další informace [o ověřování] službou Azure App Service.
* Pokračovat v kurzu přidáním [nabízená oznámení] do vaší aplikace Apache Cordova.

Zjistěte, jak používat sady SDK.

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- URLs. -->
[Začínáme s Mobile Apps]: app-service-mobile-cordova-get-started.md
[zásady zabezpečení obsahu dokumentaci]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[nabízená oznámení]: app-service-mobile-cordova-get-started-push.md
[o ověřování]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
