---
title: Použití sady JavaScript SDK
description: Jak používat v pro mobilní aplikace Azure
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 029b01f3aacc928ebdae0e8fe90871437afccea5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461517"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Jak používat klientskou knihovnu JavaScriptu pro mobilní aplikace Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Přehled
Tato příručka vás naučí provádět běžné scénáře pomocí nejnovější [sady JavaScript SDK pro mobilní aplikace Azure]. Pokud s Mobilními aplikacemi Azure teprve začínáte, nejprve dokončete [úvodní obrazovku s Azure Mobile Apps] a vytvořte back-end a tabulku. V této příručce se zaměřujeme na používání mobilního back-endu ve webových aplikacích HTML/JavaScript.

## <a name="supported-platforms"></a>Podporované platformy
Podporu prohlížeče omezujeme na aktuální a poslední verze hlavních prohlížečů: Google Chrome, Microsoft Edge, Microsoft Internet Explorer a Mozilla Firefox.  Očekáváme, že sada SDK bude fungovat s jakýmkoli relativně moderním prohlížečem.

Balíček je distribuován jako univerzální javascriptový modul, takže podporuje globální formáty, formáty AMD a CommonJS.

## <a name="setup-and-prerequisites"></a><a name="Setup"></a>Nastavení a požadavky
Tato příručka předpokládá, že jste vytvořili back-end s tabulkou. Tato příručka předpokládá, že tabulka má stejné schéma jako tabulky v těchto kurzech.

Instalaci sady JavaScript SDK Azure Mobile `npm` Apps lze provést pomocí příkazu:

```
npm install azure-mobile-apps-client --save
```

Knihovnu lze také použít jako modul ES2015, v rámci prostředí CommonJS, jako je Browserify a Webpack a jako knihovna AMD.  Například:

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Můžete také použít předem vytvořenou verzi sady SDK stažením přímo z naší cdn:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="how-to-authenticate-users"></a><a name="auth"></a>Postup: Ověření uživatelů
Služba Azure App Service podporuje ověřování a autorizaci uživatelů aplikací pomocí různých poskytovatelů externích identit: Facebook, Google, Účet Microsoft a Twitter. Můžete nastavit oprávnění pro tabulky, která omezí přístup pro určité operace pouze na ověřené uživatele. Identitu ověřených uživatelů můžete také použít k implementaci autorizačních pravidel v serverových skriptech. Další informace najdete v tématu [Začínáme s ověřováním] kurzu.

Podporovány jsou dva toky ověřování: tok serveru a tok klienta.  Tok serveru poskytuje nejjednodušší možnosti ověřování, protože závisí na webovém ověřovacím rozhraní zprostředkovatele. Tok klienta umožňuje hlubší integraci s funkcemi specifickými pro zařízení, jako je jednotné přihlašování, protože závisí na sadách SDK specifických pro zprostředkovatele.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="how-to-configure-your-mobile-app-service-for-external-redirect-urls"></a><a name="configure-external-redirect-urls"></a>Postup: Konfigurace služby mobilních aplikací pro externí adresy URL přesměrování.
Několik typů aplikací JavaScript upoužití funkce zpětné smyčky pro zpracování toků oauth ui.  Mezi tyto možnosti patří:

* Místní spuštění služby
* Použití živého nabíjení s iontovým rámcem
* Přesměrování na službu App Service pro ověřování.

Místní spuštění může způsobit problémy, protože ve výchozím nastavení je ověřování služby App Service nakonfigurováno pouze tak, aby umožňovalo přístup z back-endu mobilní aplikace. Pomocí následujících kroků změňte nastavení služby App Service a povolte ověřování při místním spuštění serveru:

1. Přihlášení na [portál Azure]
2. Přejděte do back-endu mobilní aplikace.
3. V nabídce **NÁSTROJE PRO VÝVOJ** vyberte **Průzkumník prostředků.**
4. Kliknutím na **Přejít** otevřete průzkumník prostředků pro back-end mobilní aplikace na nové kartě nebo v okně.
5. Rozbalte uzel **config** > **authsettings** pro vaši aplikaci.
6. Klepnutím na tlačítko **Upravit** povolíte úpravy zdroje.
7. Najít **allowedExternalRedirectUrls** element, který by měl být null. Přidejte adresy URL do pole:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Nahraďte adresy URL v poli adresy URL vaší služby, `http://localhost:3000` které jsou v tomto příkladu pro místní ukázkovou službu Node.js. Můžete také `http://localhost:4400` použít pro službu Ripple nebo jinou adresu URL v závislosti na konfiguraci aplikace.
8. V horní části stránky klikněte na **Číst nebo zapisovat**a potom kliknutím na **PUT** uložte aktualizace.

Musíte také přidat stejné adresy URL zpětné smyčky do nastavení seznamu povolených adres CORS:

1. Přejděte zpět na [portál Azure].
2. Přejděte do back-endu mobilní aplikace.
3. V nabídce **rozhraní API** klikněte na **CORS.**
4. Zadejte každou adresu URL do prázdného textového pole **Povolený původ.**  Vytvoří se nové textové pole.
5. Klikněte na **ULOŽIT.**

Po aktualizacích back-endu budete moct ve své aplikaci používat nové adresy URL zpětné smyčky.

<!-- URLs. -->
[Rychlý start mobilních aplikací Azure]: app-service-mobile-cordova-get-started.md
[Začínáme s ověřováním]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Portál Azure]: https://portal.azure.com/
[Sada JavaScript SDK pro mobilní aplikace Azure]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
