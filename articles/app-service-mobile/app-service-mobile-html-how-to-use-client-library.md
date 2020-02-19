---
title: Použití sady JavaScript SDK
description: Jak používat v pro Azure Mobile Apps
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 029b01f3aacc928ebdae0e8fe90871437afccea5
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461517"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Jak používat klientskou knihovnu JavaScriptu pro Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Přehled
Tato příručka vás seznámí s provedením běžných scénářů pomocí nejnovější [JavaScript SDK pro Azure Mobile Apps]. Pokud s Azure Mobile Apps začínáte, nejdřív dokončete [rychlé zprovoznění Mobile Apps Azure] a vytvořte back-end a vytvořte tabulku. V tomto průvodci se zaměříme na použití mobilního back-endu ve webových aplikacích HTML/JavaScript.

## <a name="supported-platforms"></a>Podporované platformy
Podporujeme podporu prohlížeče pro aktuální a poslední verze hlavních prohlížečů: Google Chrome, Microsoft Edge, Microsoft Internet Explorer a Mozilla Firefox.  Očekáváme, že sada SDK bude fungovat s případným poměrně moderním prohlížečem.

Balíček se distribuuje jako univerzální modul JavaScriptu, takže podporuje i formáty Globals, AMD a CommonJS.

## <a name="Setup"></a>Nastavení a předpoklady
V tomto průvodci se předpokládá, že jste vytvořili back-end s tabulkou. V tomto průvodci se předpokládá, že tabulka má stejné schéma jako tabulky v těchto kurzech.

Instalace sady Azure Mobile Apps JavaScript SDK se dá provést pomocí příkazu `npm`:

```
npm install azure-mobile-apps-client --save
```

Knihovnu je také možné použít jako modul ES2015 v prostředích CommonJS, jako jsou Browserify a Webpack, a jako knihovna AMD.  Například:

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Můžete také použít předem vytvořenou verzi sady SDK stažením přímo z našeho CDN:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Postupy: ověřování uživatelů
Azure App Service podporuje ověřování a autorizaci uživatelů aplikací pomocí různých externích zprostředkovatelů identity: Facebook, Google, účet Microsoft a Twitter. Můžete nastavit oprávnění pro tabulky a omezit tak přístup pro konkrétní operace jenom na ověřené uživatele. Identitu ověřených uživatelů můžete použít taky k implementaci autorizačních pravidel ve skriptech serveru. Další informace najdete v kurzu Začínáme [Začínáme s ověřováním] .

Podporují se dva toky ověřování: tok serveru a tok klienta.  Tok serveru poskytuje nejjednodušší možnosti ověřování, protože spoléhá na rozhraní webového ověřování poskytovatele. Tok klienta umožňuje hlubší integraci s funkcemi specifickými pro zařízení, jako je jednotné přihlašování, protože spoléhá na sady SDK pro konkrétní poskytovatele.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Postupy: konfigurace mobilních App Service pro externí adresy URL pro přesměrování
Několik typů aplikací JavaScriptu používá ke zpracování toků uživatelského rozhraní OAuth schopnost zpětné smyčky.  Mezi tyto možnosti patří:

* Místní spuštění služby
* Použití živého opětovného načtení s iontovou architekturou
* Přesměrování na App Service pro ověřování.

Spouštění v místním prostředí může způsobovat problémy, protože ve výchozím nastavení App Service ověřování je nakonfigurované jenom tak, aby povolovalo přístup z back-endu mobilní aplikace. Pomocí následujícího postupu změníte nastavení App Service tak, aby se povolilo ověřování při místním spuštění serveru:

1. Přihlaste se k portálu [Azure Portal].
2. Přejděte na back-end mobilní aplikace.
3. V nabídce **vývojové nástroje** vyberte **Průzkumník prostředků** .
4. Kliknutím na **Přejít** otevřete Průzkumníka prostředků pro back-end mobilní aplikace na nové kartě nebo v okně.
5. Rozbalte uzel **konfigurace** > **authsettings** pro vaši aplikaci.
6. Kliknutím na tlačítko **Upravit** povolte úpravy prostředku.
7. Vyhledejte element **allowedExternalRedirectUrls** , který by měl mít hodnotu null. Přidejte adresy URL do pole:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Nahraďte adresy URL v poli adresami URL vaší služby, které jsou v tomto příkladu `http://localhost:3000` pro ukázkovou službu Node. js v místním prostředí. V závislosti na tom, jak je vaše aplikace nakonfigurovaná, můžete také použít `http://localhost:4400` pro službu Ripple nebo jinou adresu URL.
8. V horní části stránky klikněte na **čtení/zápis**a pak **klikněte na Uložit. tím uložíte** aktualizace.

Do nastavení seznamu povolených rozhraní CORS taky musíte přidat stejné adresy URL zpětné smyčky:

1. Přejděte zpět na [Azure Portal].
2. Přejděte na back-end mobilní aplikace.
3. V nabídce **rozhraní API** klikněte na **CORS** .
4. Zadejte každou adresu URL do textového pole prázdné **Povolené zdroje** .  Vytvoří se nové textové pole.
5. Klikněte na **Uložit** .

Po aktualizaci back-endu budete moci v aplikaci používat nové adresy URL zpětné smyčky.

<!-- URLs. -->
[rychlé zprovoznění Mobile Apps Azure]: app-service-mobile-cordova-get-started.md
[Začínáme s ověřováním]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure Portal]: https://portal.azure.com/
[JavaScript SDK pro Azure Mobile Apps]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
