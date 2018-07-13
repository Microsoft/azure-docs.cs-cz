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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38467882"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Jak používat klientskou knihovnu pro Apache Cordova pro Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Tento průvodce vás naučí, provádět běžné scénáře pomocí nejnovější [modul plug-in Apache Cordova pro Azure Mobile Apps]. Pokud jste ještě na službu Azure Mobile Apps, nejprve dokončit [Azure Mobile Apps rychlým startem] Pokud chcete vytvořit back-endu, vytvořte tabulku a stáhněte si předem sestavených projektů Apache Cordova. V této příručce se zaměříme na modul plug-in na straně klienta pro Apache Cordova.

## <a name="supported-platforms"></a>Podporované platformy
Tato sada SDK podporuje v6.0.0 Apache Cordova a později na iOS, Android a Windows zařízení.  Podpora platformy je následujícím způsobem:

* Rozhraní Android API 19-24 (KitKat prostřednictvím verzi Nougat).
* iOS verze 8.0 a novější.
* Windows Phone 8.1.
* Universal Windows Platform.

## <a name="Setup"></a>Instalace a požadavky
Tato příručka předpokládá, že vytvoříte back-end s tabulkou. Tento průvodce to předpokládá, že tabulka má stejné schéma jako tabulky v těchto kurzech. Tento průvodce to předpokládá také, že jste přidali modul plug-in Apache Cordova do vašeho kódu.  Pokud jste neudělali, můžete přidat modul plug-in Apache Cordova do vašeho projektu na příkazovém řádku:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Další informace o vytváření [vaše první aplikace Apache Cordova], najdete v jejich dokumentaci.

## <a name="ionic"></a>Nastavení aplikace Ionic v2

O správné konfiguraci projektu aplikace Ionic v2, nejprve vytvořit základní aplikaci a přidejte modul plug-in Cordova:

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

Modul plug-in Azure Mobile Apps Cordova podporuje obě Ionic aplikace v1 a v2.  Jen aplikace Ionic v2 vyžadují další deklarace `WindowsAzure` objektu.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Postupy: ověřování uživatelů
Azure App Service podporuje ověřování a autorizace uživatelů aplikací pomocí různých externích zprostředkovatelů identity: Facebook, Google, Account Microsoft a Twitter. Můžete nastavit oprávnění pro tabulky, pokud chcete omezit přístup pro určité operace pouze ověřeným uživatelům. Identity ověřeného uživatele můžete také použít k implementaci autorizační pravidla v serverových skriptů. Další informace najdete v tématu [Začínáme s ověřováním] kurzu.

Pokud používáte ověřování v aplikaci Apache Cordova, musí být k dispozici následující moduly plug-in Cordova:

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

Jsou podporovány dvě toky ověřování: serveru a klienta tok.  Tok server poskytuje nejjednodušší prostředí pro ověřování, spoléhá na zprostředkovatele webového ověření rozhraní. Tok klienta umožňuje hlubší integraci s funkcemi konkrétní zařízení, jako single-sign-on jako spoléhá na sady SDK pro konkrétní zařízení specifickým pro zprostředkovatele.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Postupy: Konfigurace služby Mobile App pro adresy URL pro externí přesměrování.
Několik typů aplikací Apache Cordova pomocí funkce zpětné smyčky pro zpracování toky OAuth uživatelského rozhraní.  Toky OAuth uživatelského rozhraní na místním hostiteli způsobit problémy vzhledem k tomu, že ověřovací službu pouze ví, jak využít vaše služba ve výchozím nastavení.  Příklady problematické toky OAuth uživatelského rozhraní:

* Emulátor Ripple.
* Živé opětovné načtení s využitím nástroje Ionic.
* Místně spuštěná mobilním back-endu
* Mobilní back-end používané jiné službě Azure App Service než jeden poskytuje ověřování.

Postupujte podle těchto pokynů a přidejte svá místní nastavení konfigurace:

1. Přihlaste se k portálu [Azure Portal].
2. Vyberte **všechny prostředky** nebo **App Services** pak klikněte na název vaší mobilní aplikace.
3. Klikněte na tlačítko **nástroje**
4. Klikněte na tlačítko **Průzkumníka prostředků** v nabídce dodržovat klikněte **Přejít**.  Otevře se nové okno nebo kartu.
5. Rozbalte **config**, **authsettings** uzly, aby se váš web v levém navigačním panelu.
6. Klikněte na tlačítko **upravit**
7. Vyhledejte element "allowedExternalRedirectUrls".  Může být nastaven na hodnotu null nebo pole hodnot.  Změňte hodnotu na následující hodnotu:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Nahraďte adresy URL adresy URL vaší služby.  Mezi příklady patří "http://localhost:3000" (pro službu ukázky Node.js), nebo "http://localhost:4400" (pro službu Ripple).  Ale tyto adresy URL jsou příklady - vaší situaci, včetně služby uvedené v příkladech se může lišit.
8. Klikněte na tlačítko **r/w** tlačítko v pravém horním rohu obrazovky.
9. Klikněte na zelené **UMÍSTIT** tlačítko.

Nastavení se ukládají v tomto okamžiku.  Nezavírejte okno prohlížeče, dokud dokončení nastavení uložení.
Tyto adresy URL zpětné smyčky je také možné přidáte do nastavení CORS pro službu App Service:

1. Přihlaste se k portálu [Azure Portal].
2. Vyberte **všechny prostředky** nebo **App Services** pak klikněte na název vaší mobilní aplikace.
3. Automaticky se otevře okno nastavení.  Pokud tomu tak není, klikněte na tlačítko **všechna nastavení**.
4. Klikněte na tlačítko **CORS** nabídce rozhraní API.
5. Zadejte adresu URL, kterou chcete přidat do pole k dispozici a stiskněte klávesu Enter.
6. Další adresy URL zadejte podle potřeby.
7. Kliknutím na **Uložit** nastavení uložte.

Trvá přibližně 10 – 15 sekund nové nastavení projevilo.

## <a name="register-for-push"></a>Postupy: registrace pro nabízená oznámení
Nainstalujte [nabízené oznámení phonegap plugin] pro zpracování nabízených oznámení.  Tento modul plug-in můžete snadno přidat pomocí `cordova plugin add` příkazu na příkazovém řádku, nebo prostřednictvím instalačního programu modulu plug-in Git v sadě Visual Studio.  Následující kód do aplikace Apache Cordova zaregistruje zařízení pro nabízená oznámení:

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

Použijte sadu SDK služby Notification Hubs k odesílání nabízených oznámení ze serveru.  Nikdy odesílání nabízených oznámení přímo z klientů. To může použít k aktivaci služby útoku proti Notification Hubs nebo systém oznámení platformy.  Systém oznámení platformy může zakázat provoz v důsledku takové útoky.

## <a name="more-information"></a>Další informace

Můžete najít podrobné podrobnosti o rozhraní API v naší [dokumentace k rozhraní API](http://azure.github.io/azure-mobile-apps-js-client/).

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com
[Azure Mobile Apps rychlým startem]: app-service-mobile-cordova-get-started.md
[Začínáme s ověřováním]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Modul plug-in Apache Cordova pro Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[vaše první aplikace Apache Cordova]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[nabízené oznámení phonegap plugin]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
