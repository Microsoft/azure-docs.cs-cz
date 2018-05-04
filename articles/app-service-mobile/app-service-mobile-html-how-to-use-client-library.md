---
title: Jak používat JavaScript SDK pro Azure Mobile Apps
description: Postup použití v Azure Mobile Apps
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: ba2eb5a7f888e4cffcd798259afa8194b4021025
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Používání knihovny JavaScript klienta pro Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Tato příručka je určena můžete provádět běžné scénáře s využitím nejnovější [JavaScript SDK pro Azure Mobile Apps]. Pokud jste ještě Azure Mobile Apps, nejprve dokončit [Azure Mobile Apps rychlý Start] vytvoření back-end a vytvořte tabulku. V této příručce se zaměříme na používání mobilního back-end v HTML/JavaScript webových aplikací.

## <a name="supported-platforms"></a>Podporované platformy
Omezená podpora prohlížečů na aktuální a poslední verze hlavní prohlížeče: Google Chrome, Microsoft Edge, Microsoft Internet Explorer a Mozilla Firefox.  Očekáváme, že sada SDK pro funkci v libovolného relativně moderní prohlížeče.

Balíček je distribuován jako univerzální modul JavaScript, tak, aby podporuje globals, AMD, a formátuje CommonJS.

## <a name="Setup"></a>Instalační program a požadavky
Tato příručka předpokládá, že jste vytvořili back-end s tabulkou. Tato příručka předpokládá, že tabulka má stejné schéma jako tabulky v těchto kurzech.

Instalace Azure Mobile Apps JavaScript SDK, můžete to udělat pomocí `npm` příkaz:

```
npm install azure-mobile-apps-client --save
```

Knihovny můžete použít také jako modul ES2015 v rámci CommonJS prostředí, například Browserify a Webpack a jako knihovnu AMD.  Příklad:

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Předem připravené verzi sady SDK můžete také stáhnout přímo z našich CDN:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Postupy: ověřování uživatelů
Azure App Service podporuje ověřování a autorizaci uživatelů aplikace pomocí různých zprostředkovatelů externí identity: Facebook, Google, Microsoft Account a Twitter. Můžete nastavit oprávnění pro tabulky, pokud chcete omezit přístup pro určité operace pouze ověřené uživatele. Můžete také použít identitu ověřeného uživatele k implementaci autorizační pravidla v skripty serveru. Další informace najdete v tématu [Začínáme s ověřováním] kurzu.

Jsou podporovány dva ověřování toky: serveru a klienta tok.  Vývojový server poskytuje nejjednodušší zkušeností ověřování, jako je závislé na poskytovatele webové ověřování rozhraní. Tok klienta umožňuje hlubší integrace s funkcemi konkrétní zařízení, jako-jednotné přihlášení jako přitom spoléhá na specifický pro zprostředkovatele sady SDK.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Postupy: Konfigurace služby Mobile App pro adresy URL pro externí přesměrování.
Několik typů aplikací JavaScript pomocí funkce zpětné smyčky pro zpracování uživatelského rozhraní OAuth toky.  Tyto funkce patří:

* Spuštění služby místně
* Pomocí rozhraní iontových načtěte za provozu
* Přesměrování na službu App Service pro ověřování.

Spuštěn místně může způsobit problémy, protože ve výchozím nastavení, ověřování služby App Service je nakonfigurovaná pouze pro povolení přístupu z váš back-end mobilní aplikace. Chcete-li změnit nastavení služby App Service povolení ověřování při místním spuštění serveru použijte následující kroky:

1. Přihlaste se k portálu [portál Azure].
2. Přejděte na váš back-end mobilní aplikace.
3. Vyberte **Průzkumníka prostředků** v **nástroje pro vývoj** nabídky.
4. Klikněte na tlačítko **přejděte** otevřete Průzkumníka prostředků pro váš back-end mobilní aplikace v nové záložky nebo okno.
5. Rozbalte **konfigurace** > **authsettings** uzel pro vaši aplikaci.
6. Klikněte **upravit** tlačítko povolte úpravy prostředku.
7. Najít **allowedExternalRedirectUrls** element, který by měl mít hodnotu null. Přidejte vaší adresy URL do pole:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Nahraďte adresy URL v pole adresy URL služby, který v tomto příkladu je `http://localhost:3000` pro místní službu ukázka Node.js. Můžete také použít `http://localhost:4400` pro službu Ripple nebo některé jiné adresy URL, v závislosti na konfiguraci aplikace.
8. V horní části stránky klikněte na tlačítko **pro čtení a zápis**, pak klikněte na tlačítko **PUT** uložte provedené změny.

Musíte taky přidat stejné adresy URL zpětné smyčky do nastavení CORS seznamu povolených IP adres:

1. Přejděte zpět [portál Azure].
2. Přejděte na váš back-end mobilní aplikace.
3. Klikněte na tlačítko **CORS** v **rozhraní API** nabídky.
4. Zadejte jednotlivé adresy URL v prázdném **povolené zdroje** textové pole.  Vytvoří se nové textové pole.
5. Klikněte na tlačítko **uložit**

Po aktualizaci back-end, bude moci použít nové adresy URL zpětné smyčky ve vaší aplikaci.

<!-- URLs. -->
[Azure Mobile Apps rychlý Start]: app-service-mobile-cordova-get-started.md
[Začínáme s ověřováním]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[portál Azure]: https://portal.azure.com/
[JavaScript SDK pro Azure Mobile Apps]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
