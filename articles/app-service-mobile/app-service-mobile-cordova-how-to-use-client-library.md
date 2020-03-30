---
title: Jak používat Apache Cordova Plugin
description: Jak používat apache cordova plugin pro mobilní aplikace Azure
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: cafeea8afe571fc81548833952eee72a695fed41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459357"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Jak používat klientskou knihovnu Apache Cordova pro mobilní aplikace Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Přehled
Tato příručka vás naučí provádět běžné scénáře pomocí nejnovějšího [pluginu Apache Cordova pro mobilní aplikace Azure]. Pokud s Mobilními aplikacemi Azure teprve začínáte, nejprve dokončete [rychlý start pro mobilní aplikace Azure] a vytvořte back-end, vytvořte tabulku a stáhněte si předem sestavený projekt Apache Cordova. V této příručce se zaměřujeme na klienta-side Apache Cordova Plugin.

## <a name="supported-platforms"></a>Podporované platformy
Tato sada SDK podporuje Apache Cordova v6.0.0 a novější na zařízeních se systémy iOS, Android a Windows.  Podpora platformy je následující:

* Android API 19-24 (KitKat přes Nougat).
* verze iOS 8.0 a novější.
* Windows Phone 8.1.
* Univerzální platforma Windows.

## <a name="setup-and-prerequisites"></a><a name="Setup"></a>Nastavení a požadavky
Tato příručka předpokládá, že jste vytvořili back-end s tabulkou. Tato příručka předpokládá, že tabulka má stejné schéma jako tabulky v těchto kurzech. Tato příručka také předpokládá, že jste do svého kódu přidali plugin Apache Cordova.  Pokud jste tak neučinili, můžete přidat plugin Apache Cordova do svého projektu na příkazovém řádku:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Další informace o vytvoření [první aplikace Apache Cordova]najdete v jejich dokumentaci.

## <a name="setting-up-an-ionic-v2-app"></a><a name="ionic"></a>Nastavení aplikace Ionic v2

Chcete-li správně nakonfigurovat projekt Ionic v2, nejprve vytvořte základní aplikaci a přidejte plugin Cordova:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Chcete-li vytvořit `app.component.ts` objekt klienta, přidejte následující řádky:

```typescript
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Nyní můžete vytvořit a spustit projekt v prohlížeči:

```
ionic platform add browser
ionic run browser
```

Modul plug-in Azure Mobile Apps Cordova podporuje aplikace Ionic v1 i v2.  Pouze aplikace Ionic v2 vyžadují další `WindowsAzure` deklaraci pro objekt.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="how-to-authenticate-users"></a><a name="auth"></a>Postup: Ověření uživatelů
Služba Azure App Service podporuje ověřování a autorizaci uživatelů aplikací pomocí různých poskytovatelů externích identit: Facebook, Google, Účet Microsoft a Twitter. Můžete nastavit oprávnění pro tabulky, která omezí přístup pro určité operace pouze na ověřené uživatele. Identitu ověřených uživatelů můžete také použít k implementaci autorizačních pravidel v serverových skriptech. Další informace najdete v tématu [Začínáme s ověřováním] kurzu.

Při použití ověřování v aplikaci Apache Cordova musí být k dispozici následující pluginy Cordova:

* [cordova-plugin-zařízení]
* [cordova-plugin-inappbrowser]

Podporovány jsou dva toky ověřování: tok serveru a tok klienta.  Tok serveru poskytuje nejjednodušší možnosti ověřování, protože závisí na webovém ověřovacím rozhraní zprostředkovatele. Tok klienta umožňuje hlubší integraci s funkcemi specifickými pro zařízení, jako je jednotné přihlašování, protože závisí na sadách SDK specifických pro zprostředkovatele.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="how-to-configure-your-mobile-app-service-for-external-redirect-urls"></a><a name="configure-external-redirect-urls"></a>Postup: Konfigurace služby mobilních aplikací pro externí adresy URL přesměrování.
Několik typů aplikací Apache Cordova používá funkci zpětné smyčky ke zpracování toků ui OAuth.  OAuth UI toky na localhost způsobit problémy, protože ověřovací služba ví pouze, jak využít službu ve výchozím nastavení.  Příklady problematických toků operačního procesu OAuth zahrnují:

* Vlnitý emulátor.
* Live Reload s Ionic.
* Spuštění mobilního back-endu místně
* Spuštění mobilního back-endu v jiné službě Azure App Service než v té, která poskytuje ověřování.

Podle těchto pokynů přidejte místní nastavení do konfigurace:

1. Přihlášení na [portál Azure]
2. Vyberte **Všechny prostředky** nebo **Služby aplikací** a klikněte na název mobilní aplikace.
3. Klikněte na **Nástroje.**
4. V nabídce OBSERVE klepněte na **Průzkumník prostředků** a potom klepněte na **tlačítko Přejít**.  Otevře se nové okno nebo karta.
5. Rozbalte **config**, **authsettings** uzly pro váš web v levé navigaci.
6. Klikněte na **Upravit.**
7. Vyhledejte element "allowedExternalRedirectUrls".  Může být nastavena na hodnotu null nebo pole hodnot.  Změňte hodnotu na následující hodnotu:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Nahraďte adresy URL adresy URL vaší služby.  Příklady `http://localhost:3000` zahrnují (pro ukázkovou službu Node.js) nebo `http://localhost:4400` (pro službu Ripple).  Tyto adresy URL jsou však příklady - vaše situace, včetně služeb uvedených v příkladech, se může lišit.
8. Klikněte na tlačítko **Číst/zapisovat** v pravém horním rohu obrazovky.
9. Klikněte na zelené tlačítko **PUT.**

Nastavení jsou uložena v tomto okamžiku.  Nezavírejte okno prohlížeče, dokud nastavení nedokončí ukládání.
Také přidejte tyto adresy URL zpětné smyčky do nastavení CORS pro vaši službu App Service:

1. Přihlášení na [portál Azure]
2. Vyberte **Všechny prostředky** nebo **Služby aplikací** a klikněte na název mobilní aplikace.
3. Okno Nastavení se automaticky otevře.  Pokud tomu tak není, klepněte na **položku Všechna nastavení**.
4. V nabídce ROZHRANÍ API klikněte na **CORS.**
5. Do dokončovaného pole zadejte adresu URL, kterou chcete přidat, a stiskněte Enter.
6. Podle potřeby zadejte další adresy URL.
7. Kliknutím na **Uložit** nastavení uložte.

Nové nastavení se projeví přibližně 10–15 sekund.

## <a name="how-to-register-for-push-notifications"></a><a name="register-for-push"></a>Postup: Registrace nabízených oznámení
Nainstalujte [phonegap-plugin-push] pro zpracování nabízených oznámení.  Tento plugin lze snadno `cordova plugin add` přidat pomocí příkazu na příkazovém řádku nebo prostřednictvím instalačního programu pluginu Git v rámci sady Visual Studio.  Následující kód v aplikaci Apache Cordova registruje vaše zařízení pro nabízená oznámení:

```javascript
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

Pomocí sady SDK centra oznámení odesílejte nabízená oznámení ze serveru.  Nikdy neodesílejte nabízená oznámení přímo od klientů. To by mohlo být použito k aktivaci útoku odmítnutí služby proti oznamovacím centrům nebo službě PNS.  PNS může zakázat provoz v důsledku těchto útoků.

## <a name="more-information"></a>Další informace

Podrobné informace o ROZHRANÍ API naleznete v naší [dokumentaci k rozhraní API](https://azure.github.io/azure-mobile-apps-js-client/).

<!-- URLs. -->
[Portál Azure]: https://portal.azure.com
[Rychlý start mobilních aplikací Azure]: app-service-mobile-cordova-get-started.md
[Začínáme s ověřováním]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova Plugin pro mobilní aplikace Azure]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[vaše první aplikace Apache Cordova]: https://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-zařízení]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
