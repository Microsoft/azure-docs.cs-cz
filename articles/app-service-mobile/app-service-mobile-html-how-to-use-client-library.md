---
title: Jak pomocí sady JavaScript SDK pro Azure Mobile Apps
description: Jak používat technologie v Azure Mobile Apps
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38488888"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Použití knihovny JavaScript klienta pro Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Tento průvodce vás naučí, provádět běžné scénáře pomocí nejnovější [Sada JavaScript SDK pro Azure Mobile Apps]. Pokud jste ještě na službu Azure Mobile Apps, nejprve dokončit [Azure Mobile Apps rychlým startem] vytvoření back-endu a vytvořte tabulku. V této příručce se zaměříme na používání mobilní back-end v HTML/JavaScript webových aplikací.

## <a name="supported-platforms"></a>Podporované platformy
Omezená podpora prohlížečů na aktuální a poslední verze nejpoužívanějších prohlížečích: Google Chrome, Microsoft Edge, Microsoft Internet Explorer a Mozilla Firefox.  Očekáváme, že sada SDK pro funkci v libovolném relativně moderním prohlížeči.

Balíček je distribuován jako univerzální modul JavaScript, tak podporuje globals, AMD, a CommonJS formáty.

## <a name="Setup"></a>Instalace a požadavky
Tato příručka předpokládá, že vytvoříte back-end s tabulkou. Tento průvodce to předpokládá, že tabulka má stejné schéma jako tabulky v těchto kurzech.

Instalace sady Azure Mobile Apps JavaScript SDK, můžete to udělat pomocí `npm` příkaz:

```
npm install azure-mobile-apps-client --save
```

Knihovny můžete použít také jako modul ES2015 v rámci CommonJS prostředí, jako je například Browserify a Webpacku a jako knihovny AMD.  Příklad:

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Předem připravené verzi sady SDK můžete také stáhnout přímo z naší síti CDN:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Postupy: ověřování uživatelů
Azure App Service podporuje ověřování a autorizace uživatelů aplikací pomocí různých externích zprostředkovatelů identity: Facebook, Google, Account Microsoft a Twitter. Můžete nastavit oprávnění pro tabulky, pokud chcete omezit přístup pro určité operace pouze ověřeným uživatelům. Identity ověřeného uživatele můžete také použít k implementaci autorizační pravidla v serverových skriptů. Další informace najdete v tématu [Začínáme s ověřováním] kurzu.

Jsou podporovány dvě toky ověřování: serveru a klienta tok.  Tok server poskytuje nejjednodušší prostředí pro ověřování, spoléhá na zprostředkovatele webového ověření rozhraní. Tok klienta umožňuje hlubší integraci s funkcemi konkrétní zařízení, jako single-sign-on jako spoléhá na SDK specifickým pro zprostředkovatele.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Postupy: Konfigurace služby Mobile App pro adresy URL pro externí přesměrování.
Několik typů aplikací jazyka JavaScript pomocí funkce zpětné smyčky pro zpracování toky OAuth uživatelského rozhraní.  Mezi tyto možnosti patří:

* Vaše služba spuštěná místně
* Živé opětovné načtení pomocí rozhraní Ionic
* Přesměrování do služby App Service pro ověřování.

Místně spuštěná může způsobit potíže, protože ve výchozím nastavení, ověřování pomocí služby App Service je pouze nakonfigurována pro povolení přístupu z back-endu mobilní aplikace. Chcete-li změnit nastavení služby App Service umožňuje ověřování při místním spuštění serveru použijte následující kroky:

1. Přihlaste se k portálu [Azure Portal].
2. Přejděte do back-endu mobilní aplikace.
3. Vyberte **Průzkumníka prostředků** v **nástroje pro vývoj** nabídky.
4. Klikněte na tlačítko **Přejít** otevřete Průzkumníka prostředků pro back-endu mobilní aplikace v nové kartě nebo v okně.
5. Rozbalte **config** > **authsettings** uzel pro vaši aplikaci.
6. Klikněte na tlačítko **upravit** tlačítko, aby se povolily úpravy prostředku.
7. Najít **allowedExternalRedirectUrls** element, který by měl mít hodnotu null. Přidání vaší adresy URL v poli:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Nahraďte adresy URL do pole adresy URL vaší služby, který v tomto příkladu je `http://localhost:3000` pro místní služby pro Node.js vzorku. Můžete také použít `http://localhost:4400` pro službu Ripple nebo některé jiné adresy URL, v závislosti na tom, jak vaše aplikace je nakonfigurovaná.
8. V horní části stránky klikněte na tlačítko **r/w**, pak klikněte na tlačítko **UMÍSTIT** uložte provedené změny.

Také je potřeba přidat stejné adresy URL zpětné smyčky do seznamu povolených nastavení CORS:

1. Přejděte zpět [Azure Portal].
2. Přejděte do back-endu mobilní aplikace.
3. Klikněte na tlačítko **CORS** v **API** nabídky.
4. Zadejte všechny adresy URL v prázdném **povolené zdroje** textového pole.  Vytvoří se nové textové pole.
5. Klikněte na tlačítko **uložit**

Po dokončení aktualizace back-endem bude moct pomocí nové adresy URL zpětné smyčky ve vaší aplikaci.

<!-- URLs. -->
[Azure Mobile Apps rychlým startem]: app-service-mobile-cordova-get-started.md
[Začínáme s ověřováním]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure Portal]: https://portal.azure.com/
[Sada JavaScript SDK pro Azure Mobile Apps]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
