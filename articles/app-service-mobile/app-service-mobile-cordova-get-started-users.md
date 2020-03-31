---
title: Přidání ověřování na Apache Cordova
description: Přečtěte si, jak používat mobilní aplikace ve službě Azure App Service k ověřování uživatelů vaší aplikace Apache Cordova pomocí poskytovatelů identit, jako jsou Google, Facebook, Twitter a Microsoft.
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 3714ce2a8098608851991115aa82afdc00d08a47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459384"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Přidání ověřování do aplikace Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Souhrn
V tomto kurzu přidáte ověřování do projektu todolista rychlého startu na Apache Cordova pomocí podporovaného poskytovatele identity. Tento kurz je založen na kurzu [Začínáme s mobilními aplikacemi,] který musíte dokončit jako první.

## <a name="register-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Registrace aplikace pro ověřování a konfigurace služby App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Podívejte se na video zobrazující podobný postup.](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Omezení oprávnění pro ověřené uživatele
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Nyní můžete ověřit, že anonymní přístup k back-endu byl zakázán. V sadě Visual Studio:

* Otevřete projekt, který jste vytvořili po dokončení kurzu [Začínáme s mobilními aplikacemi].
* Spusťte aplikaci v **emulátoru Google Android**.
* Ověřte, zda se po spuštění aplikace zobrazí neočekávaná chyba připojení.

Dále aktualizujte aplikaci tak, aby ověřovala uživatele před vyžádáním prostředků z back-endu mobilní aplikace.

## <a name="add-authentication-to-the-app"></a><a name="add-authentication"></a>Přidání ověřování do aplikace
1. Otevřete projekt v sadě Visual `www/index.html` **Studio**a otevřete soubor pro úpravy.
2. Vyhledejte `Content-Security-Policy` metaznačku v části hlavy.  Přidejte hostitele OAuth do seznamu povolených zdrojů.

   | Poskytovatel | Název zprostředkovatele sady SDK | Hostitel OAuth |
   |:--- |:--- |:--- |
   | Azure Active Directory | aad | https://login.microsoftonline.com |
   | Facebook | Facebook | https://www.facebook.com |
   | Google | Google | https://accounts.google.com |
   | Microsoft | účet microsoftaccount | https://login.live.com |
   | Twitter | Twitter | https://api.twitter.com |

    Příklad zásady zabezpečení obsahu (implementované pro službu Azure Active Directory) je následující:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Nahraďte `https://login.microsoftonline.com` hostitelem OAuth z předchozí tabulky.  Další informace o metaznačce zásad zabezpečení obsahu naleznete v [dokumentaci k zásadám zabezpečení obsahu].

    Někteří poskytovatelé ověřování nevyžadují změny zásad zabezpečení obsahu při použití na vhodných mobilních zařízeních.  Při používání ověřování Google v zařízení Android například nejsou vyžadovány žádné změny zásad zabezpečení obsahu.

3. Otevřete `www/js/index.js` soubor pro úpravy, vyhledejte metodu `onDeviceReady()` a pod kódem vytvoření klienta přidejte následující kód:

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

    Tento kód nahradí existující kód, který vytvoří odkaz na tabulku a aktualizuje uI.

    Metoda login() spustí ověřování u zprostředkovatele. Metoda login() je asynchronní funkce, která vrací JavaScript Promise.  Zbytek inicializace je umístěn uvnitř odpověď slib tak, aby není proveden, dokud login() metoda dokončí.

4. V kódu, který jste `SDK_Provider_Name` právě přidali, nahraďte jménem svého poskytovatele přihlášení. Například pro službu Azure `client.login('aad')`Active Directory použijte .
5. Spusťte projekt.  Po dokončení inicializace projektu aplikace zobrazí přihlašovací stránku OAuth pro vybraného zprostředkovatele ověřování.

## <a name="next-steps"></a><a name="next-steps"></a>Další kroky
* Přečtěte si další informace [o ověřování] pomocí služby Azure App Service.
* Pokračujte v kurzu přidáním [nabízených oznámení] do aplikace Apache Cordova.

Zjistěte, jak používat sady SDK.

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- URLs. -->
[Začínáme s mobilními aplikacemi]: app-service-mobile-cordova-get-started.md
[Dokumentace k zásadám zabezpečení obsahu]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Nabízená oznámení]: app-service-mobile-cordova-get-started-push.md
[O ověřování]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
