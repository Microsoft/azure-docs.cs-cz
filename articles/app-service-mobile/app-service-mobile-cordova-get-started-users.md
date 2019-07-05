---
title: Přidání ověřování na Apache Cordova s funkcí Mobile Apps | Dokumentace Microsoftu
description: Naučte se využívat Mobile Apps ve službě Azure App Service k ověřování uživatelů vaší aplikace Apache Cordova prostřednictvím různých poskytovatelů identit, včetně Google, Facebook, Twitter a Microsoft.
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
ms.openlocfilehash: b0634038dbf5771ac1aa0bc00d007e758171b238
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443512"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Přidání ověřování do aplikace Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center investuje do nové a integrované služby, které jsou centrální při vývoji mobilních aplikací. Vývojáři mohou použít **sestavení**, **testovací** a **rozmístit** služby vytvořit kanál pro průběžnou integraci a doručování. Po nasazení aplikace se můžou vývojáři monitorovat stav a využití své aplikace pomocí **Analytics** a **diagnostiky** služeb a Zaujměte uživatele, kteří používají **Push** Služba. Vývojáři mohou využít i **Auth** k ověření uživatelů a **Data** službu zachovat, synchronizaci dat aplikací v cloudu. Podívejte se na [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-cordova-get-started-users) ještě dnes.
>

## <a name="summary"></a>Souhrn
V tomto kurzu přidáte do seznamu úkolů projektu na Apache Cordova pomocí zprostředkovatele identity podporované ověřování. Tento kurz je založený na [Začínáme s Mobile Apps] kurzu, který je třeba nejprve provést.

## <a name="register"></a>Registrace aplikace pro ověřování a konfigurace služby App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Podívejte se na video zobrazující podobný postup.](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>Omezit oprávnění k ověření uživatelé
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Teď můžete ověřit, že byl zakázán anonymní přístup k back-endu. V sadě Visual Studio:

* Otevřete projekt, který jste vytvořili po dokončení tohoto kurzu [Začínáme s Mobile Apps].
* Spusťte aplikaci **emulátor Google Android**.
* Ověřte, že k neočekávanému selhání připojení se zobrazí po spuštění aplikace.

V dalším kroku aktualizace aplikace k ověření uživatelů před požadováním prostředky z back-endu mobilní aplikace.

## <a name="add-authentication"></a>Přidání ověřování do aplikace
1. Otevřete projekt v **sady Visual Studio**a potom otevřete `www/index.html` soubor pro úpravy.
2. Vyhledejte `Content-Security-Policy` metaznačku v hlavní části.  Přidejte hostitele OAuth na seznam povolených zdrojů.

   | Poskytovatel | Název poskytovatele sady SDK | OAuth Host |
   |:--- |:--- |:--- |
   | Azure Active Directory | aad | https://login.microsoftonline.com |
   | Facebook | facebook | https://www.facebook.com |
   | Google | Google | https://accounts.google.com |
   | Microsoft | microsoftaccount | https://login.live.com |
   | Twitter | twitter | https://api.twitter.com |

    Příklad obsahu-Security-Policy (implementována pro Azure Active Directory) je následujícím způsobem:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Nahraďte `https://login.microsoftonline.com` s hostitelem OAuth v předchozí tabulce.  Další informace o metaznačku zásady zabezpečení obsahu, najdete v článku [zásady zabezpečení obsahu dokumentace].

    Někteří poskytovatelé ověřování nevyžadují změny zásad zabezpečení obsahu při použití na odpovídající mobilní zařízení.  Například nejsou potřeba žádné změny zásad zabezpečení obsahu, pokud používáte ověřování Google na zařízeních s Androidem.

3. Otevřít `www/js/index.js` soubor pro úpravy, vyhledejte `onDeviceReady()` metoda, a v části Vytvoření klienta kódu přidejte následující kód:

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

    Metoda login() začíná zprostředkovatele ověřování. Metoda login() je asynchronní funkci, která vrací Promise jazyka JavaScript.  Zbývající inicializace je umístěn uvnitř odpovědi promise tak, že není spuštěn, dokud se nedokončí login() metoda.

4. V kódu, který jste právě přidali, nahraďte `SDK_Provider_Name` s názvem vašeho zprostředkovatele přihlášení. Například pro Azure Active Directory, použijte `client.login('aad')`.
5. Spuštění projektu.  Po dokončení inicializace projektu aplikace zobrazuje stránku pro přihlášení OAuth pro zprostředkovatele zvolené ověřování.

## <a name="next-steps"></a>Další kroky
* Další informace [Informace o ověřování] pomocí služby Azure App Service.
* Pokračovat v kurzu přidáním [nabízená oznámení] do aplikace Apache Cordova.

Zjistěte, jak používat sady SDK.

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- URLs. -->
[Začínáme s Mobile Apps]: app-service-mobile-cordova-get-started.md
[Zásady zabezpečení obsahu dokumentace]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Nabízená oznámení]: app-service-mobile-cordova-get-started-push.md
[Informace o ověřování]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
