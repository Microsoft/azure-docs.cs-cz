---
title: Jak používat modul plug-in Apache Cordova pro Azure Mobile Apps
description: Jak používat modul plug-in Apache Cordova pro Azure Mobile Apps
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 6fb8be96c9793e96f1f7d2ad8e212d056d7e9ba5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32152582"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Používání klientské knihovny pro Apache Cordova pro Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Tato příručka je určena můžete provádět běžné scénáře s využitím nejnovější [modul plug-in pro Apache Cordova pro Azure Mobile Apps]. Pokud jste ještě Azure Mobile Apps, nejprve dokončit [Azure Mobile Apps rychlý Start] vytvořte back-end, vytvořit tabulku a stáhněte si předem připravené projekt Apache Cordova. V této příručce se zaměříme na modulu plug-in na straně klienta Apache Cordova.

## <a name="supported-platforms"></a>Podporované platformy
Tato sada SDK podporuje v6.0.0 Apache Cordova a později na iOS, Android a Windows zařízení.  Podpora platformy je následující:

* Rozhraní API systému Android 19 – 24 (KitKat prostřednictvím cukrovinkách typu nugát).
* iOS verze 8.0 a novější.
* Windows Phone 8.1.
* Univerzální platformy Windows.

## <a name="Setup"></a>Instalační program a požadavky
Tato příručka předpokládá, že jste vytvořili back-end s tabulkou. Tato příručka předpokládá, že tabulka má stejné schéma jako tabulky v těchto kurzech. Tato příručka také předpokládá, že jste přidali Cordovu Apache do vašeho kódu.  Pokud jste tak dosud neučinili, můžete přidat modul plug-in Apache Cordova na projekt na příkazovém řádku:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Další informace o vytváření [vaší první aplikace Apache Cordova], najdete v jejich dokumentaci.

## <a name="ionic"></a>Nastavení aplikace iontových v2

O správné konfiguraci projektu iontových v2, nejprve vytvořit základní aplikaci a přidejte Cordova plugin:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Přidejte následující řádky do `app.component.ts` k vytvoření objektu klienta:

```
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Teď můžete sestavit a spustit projekt v prohlížeči:

```
ionic platform add browser
ionic run browser
```

Modul plug-in Azure Mobile Apps Cordova podporuje obě iontových v1 a v2 aplikace.  Jenom aplikace iontových v2 vyžadovat další deklarace pro `WindowsAzure` objektu.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Postupy: ověřování uživatelů
Azure App Service podporuje ověřování a autorizaci uživatelů aplikace pomocí různých zprostředkovatelů externí identity: Facebook, Google, Microsoft Account a Twitter. Můžete nastavit oprávnění pro tabulky, pokud chcete omezit přístup pro určité operace pouze ověřené uživatele. Můžete také použít identitu ověřeného uživatele k implementaci autorizační pravidla v skripty serveru. Další informace najdete v tématu [Začínáme s ověřováním] kurzu.

Při použití ověřování v aplikaci Apache Cordova, musí být k dispozici následující moduly plug-in Cordova:

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

Jsou podporovány dva ověřování toky: serveru a klienta tok.  Vývojový server poskytuje nejjednodušší zkušeností ověřování, jako je závislé na poskytovatele webové ověřování rozhraní. Tok klienta umožňuje hlubší integrace s funkcemi konkrétní zařízení, jako-jednotné přihlášení jako přitom spoléhá na konkrétní zařízení specifické pro poskytovatele sady SDK.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Postupy: Konfigurace služby Mobile App pro adresy URL pro externí přesměrování.
Několik typů aplikací Apache Cordova pomocí funkce zpětné smyčky pro zpracování uživatelského rozhraní OAuth toky.  Toky OAuth uživatelského rozhraní na místním hostiteli způsobit problémy, protože ověřovací služby pouze věděl, kam může využívat vaše služba ve výchozím nastavení.  Příklady problematické toky OAuth uživatelského rozhraní:

* Ripple emulatoru.
* Živé opětovného načtení s Ionic.
* Místní spuštění mobilního back-endu
* Spuštění mobilní back-end v různých App Service Azure než jeden poskytnete ověřování.

Postupujte podle těchto pokynů můžete přidat místní nastavení konfigurace:

1. Přihlaste se k portálu [Azure Portal].
2. Vyberte **všechny prostředky** nebo **App Services** pak klikněte na název vaší mobilní aplikace.
3. Klikněte na tlačítko **nástroje**
4. Klikněte na tlačítko **Průzkumníka prostředků** v nabídce dodržovat klikněte **přejděte**.  Otevře se nové okno nebo kartu.
5. Rozbalte **konfigurace**, **authsettings** uzly pro svůj web v levém navigačním panelu.
6. Klikněte na tlačítko **upravit**
7. Vyhledejte element "allowedExternalRedirectUrls".  Může být nastaven na hodnotu null nebo pole hodnot.  Změňte hodnotu na následující hodnotu:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Nahraďte adresy URL adresy URL služby.  Mezi příklady patří "http://localhost:3000" (pro službu ukázka Node.js), nebo "http://localhost:4400" (pro službu Ripple).  Ale tyto adresy URL jsou příklady - vaší situaci, včetně služeb uvedených v příkladech se můžou lišit.
8. Klikněte **pro čtení a zápis** tlačítko v pravém horním rohu obrazovky.
9. Klikněte na tlačítko se zeleným **PUT** tlačítko.

Nastavení se ukládají v tomto okamžiku.  Nezavírejte okno prohlížeče, dokud nebudou dokončeny nastavení ukládání.
Tyto adresy URL zpětné smyčky je také možné přidáte do nastavení CORS pro App Service:

1. Přihlaste se k portálu [Azure Portal].
2. Vyberte **všechny prostředky** nebo **App Services** pak klikněte na název vaší mobilní aplikace.
3. Automaticky se otevře v okně nastavení.  Pokud není, klikněte na tlačítko **všechna nastavení**.
4. Klikněte na tlačítko **CORS** v nabídce rozhraní API.
5. Zadejte adresu URL, kterou chcete přidat do pole zadat a stiskněte klávesu Enter.
6. Zadejte další adresy URL, podle potřeby.
7. Kliknutím na **Uložit** nastavení uložte.

Jak dlouho trvá přibližně 10 až 15 sekund pro nová nastavení vstoupila v platnost.

## <a name="register-for-push"></a>Postupy: zaregistrovat pro nabízená oznámení
Nainstalujte [phonegap-plugin nabízené] ke zpracování nabízených oznámení.  Tento modul plug-in lze snadno přidat pomocí `cordova plugin add` příkazu na příkazovém řádku nebo prostřednictvím Git instalační program modulu plug-in v sadě Visual Studio.  Následující kód do vaší aplikace Apache Cordova registruje zařízení pro nabízená oznámení:

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Pomocí sady SDK centra oznámení k odesílání nabízených oznámení ze serveru.  Nikdy odeslat nabízená oznámení přímo z klientů. Díky tomu mohou využít ke spuštění útoku DoS proti centra oznámení nebo systém PNS.  Systém PNS může zakázat provozu v důsledku takové útoky.

## <a name="more-information"></a>Další informace

Můžete najít podrobnosti podrobné rozhraní API v našem [dokumentaci k rozhraní API](http://azure.github.io/azure-mobile-apps-js-client/).

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com
[Azure Mobile Apps rychlý Start]: app-service-mobile-cordova-get-started.md
[Začínáme s ověřováním]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[modul plug-in pro Apache Cordova pro Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[vaší první aplikace Apache Cordova]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin nabízené]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
