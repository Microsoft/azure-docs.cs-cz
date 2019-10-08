---
title: Přidání ověřování u Apache Cordova pomocí Mobile Apps | Microsoft Docs
description: Naučte se používat Mobile Apps v Azure App Service k ověřování uživatelů vaší Apache Cordova aplikace prostřednictvím různých poskytovatelů identit, jako jsou Google, Facebook, Twitter a Microsoft.
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: d5e124c6f73285efdaef515deba5816093a27e98
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027562"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Přidání ověřování do aplikace Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center podporuje vývoj koncových a integrovaných služeb od centrálního vývoje mobilních aplikací. Vývojáři **mohou pomocí sestavování**, **testování** a **distribuce** služeb nastavit kanál průběžné integrace a doručování. Po nasazení aplikace mohou vývojáři sledovat stav a využití své aplikace pomocí **analytických** a **diagnostických** služeb a spolupracovat s uživateli pomocí služby **push** . Vývojáři můžou také využít **ověřování** k ověřování uživatelů a **datových** služeb, aby zachovaly a synchronizovaly data aplikací v cloudu.
> Pokud chcete v mobilní aplikaci integrovat cloudové služby, zaregistrujte se App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) ještě dnes.

## <a name="summary"></a>Souhrn
V tomto kurzu přidáte ověřování do projektu ToDoList pro rychlé zprovoznění na Apache Cordova s použitím podporovaného zprostředkovatele identity. Tento kurz je založený na kurzu [Začínáme s Mobile Apps] , který musíte nejdřív provést.

## <a name="register"></a>Zaregistrovat aplikaci pro ověřování a nakonfigurovat App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Podívejte se na video zobrazující podobný postup.](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>Omezení oprávnění pro ověřené uživatele
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Nyní můžete ověřit, zda byl zakázán anonymní přístup k back-endu. V aplikaci Visual Studio:

* Otevřete projekt, který jste vytvořili po dokončení kurzu [Začínáme s Mobile Apps].
* Spusťte aplikaci na webu **Google Android Emulator**.
* Ověřte, že po spuštění aplikace se zobrazí neočekávaná chyba připojení.

Dále aktualizujte aplikaci tak, aby ověřovala uživatele před vyžádáním prostředků z back-endu mobilní aplikace.

## <a name="add-authentication"></a>Přidání ověřování do aplikace
1. Otevřete projekt v **aplikaci Visual Studio**a pak otevřete soubor `www/index.html` pro úpravy.
2. V části Head Najděte značku meta `Content-Security-Policy`.  Přidejte hostitele OAuth do seznamu povolených zdrojů.

   | Poskytovatel | Název poskytovatele sady SDK | Hostitel OAuth |
   |:--- |:--- |:--- |
   | Azure Active Directory | poplašné | https://login.microsoftonline.com |
   | Facebook | přes | https://www.facebook.com |
   | Google | internetového | https://accounts.google.com |
   | Microsoft | microsoftaccount | https://login.live.com |
   | Twitter | Službě | https://api.twitter.com |

    Příkladem obsahu – zásada zabezpečení (implementovaná pro Azure Active Directory) je následující:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Nahraďte `https://login.microsoftonline.com` hostitelem OAuth z předchozí tabulky.  Další informace o značce metadata Content-Security-Policy Policy najdete v [Obsah-dokumentace k zásadám zabezpečení].

    Někteří poskytovatelé ověřování nevyžadují změny zásad zabezpečení obsahu při použití na příslušných mobilních zařízeních.  Například při použití ověřování Google na zařízení s Androidem se nevyžadují žádné změny v zásadách zabezpečení obsahu.

3. Otevřete soubor `www/js/index.js` pro úpravy, vyhledejte metodu `onDeviceReady()` a v části kód pro vytvoření klienta přidejte následující kód:

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

    Tento kód nahradí existující kód, který vytvoří odkaz na tabulku a aktualizuje uživatelské rozhraní.

    Metoda Login () spustí ověřování u poskytovatele. Metoda Login () je asynchronní funkce, která vrací příslib JavaScriptu.  Zbytek inicializace je umístěn v rámci reakce na příslib, takže nebude proveden až do dokončení metody Login ().

4. V kódu, který jste právě přidali, nahraďte `SDK_Provider_Name` názvem vašeho poskytovatele přihlášení. Například pro Azure Active Directory použijte `client.login('aad')`.
5. Spusťte projekt.  Po dokončení inicializace projektu vaše aplikace zobrazí přihlašovací stránku OAuth pro vybraného zprostředkovatele ověřování.

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace [o ověřování] pomocí Azure App Service.
* Pokračujte v kurzu přidáváním [Nabízená oznámení] do aplikace Apache Cordova.

Zjistěte, jak používat sady SDK.

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- URLs. -->
[Začínáme s Mobile Apps]: app-service-mobile-cordova-get-started.md
[Obsah-dokumentace k zásadám zabezpečení]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Nabízená oznámení]: app-service-mobile-cordova-get-started-push.md
[O ověřování]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
