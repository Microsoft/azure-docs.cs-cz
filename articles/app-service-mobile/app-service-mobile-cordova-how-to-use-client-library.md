---
title: Jak používat modul plug-in Apache Cordova
description: Jak používat modul plug-in Apache Cordova pro Azure Mobile Apps
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: ecca8f719a01abe68b368987fce4ea883193e844
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668503"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Jak používat klientskou knihovnu Apache Cordova pro Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Visual Studio App Center podporuje vývoj kompletních integrovaných služeb, které jsou důležité pro vývoj mobilních aplikací. Vývojáři mohou využít služby pro **sestavování**, **testování** a **distribuci** a nastavit kanál pro průběžnou integraci a doručování. Jakmile je aplikace nasazená, mohou vývojáři monitorovat její stav a využití pomocí **analytických** a **diagnostických** služeb a spolupracovat s uživateli pomocí služby **Push**. Vývojáři mohou také využít **Auth** k ověřování svých uživatelů a službu and **Data** k uchování dat aplikace a jejich synchronizaci v cloudu.
>
> Pokud chcete do vaší mobilní aplikace integrovat cloudové služby, ještě dnes se zaregistrujte do služeb [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

## <a name="overview"></a>Přehled
Tato příručka vás seznámí s provedením běžných scénářů pomocí nejnovějšího [Modul plug-in Apache Cordova pro Azure Mobile Apps]. Pokud s Azure Mobile Apps začínáte, nejdřív dokončete [rychlé zprovoznění Mobile Apps Azure] a vytvořte back-end, vytvořte tabulku a stáhněte předem sestavený projekt Apache Cordova. V tomto průvodci se zaměříme na Apache Cordova modul plug-in na straně klienta.

## <a name="supported-platforms"></a>Podporované platformy
Tato sada SDK podporuje Apache Cordova v 6.0.0 a novějších v zařízeních s iOS, Androidem a Windows.  Podpora platforem je následující:

* Rozhraní Android API 19-24 (KitKat až nougat).
* verze iOS 8,0 a novější.
* Windows Phone 8,1.
* Univerzální platforma Windows.

## <a name="Setup"></a>Nastavení a předpoklady
V tomto průvodci se předpokládá, že jste vytvořili back-end s tabulkou. V tomto průvodci se předpokládá, že tabulka má stejné schéma jako tabulky v těchto kurzech. Tato příručka také předpokládá, že jste přidali modul plug-in Apache Cordova do svého kódu.  Pokud jste to ještě neudělali, můžete přidat modul plug-in Apache Cordova do svého projektu na příkazovém řádku:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Další informace o vytvoření [Vaše první aplikace Apache Cordova]najdete v dokumentaci.

## <a name="ionic"></a>Nastavení iontové aplikace V2

Aby bylo možné správně nakonfigurovat projekt iontu v2, vytvořte nejprve základní aplikaci a přidejte modul plug-in Cordova:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Přidejte následující řádky, které `app.component.ts` k vytvoření objektu klienta:

```typescript
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Nyní můžete sestavit a spustit projekt v prohlížeči:

```
ionic platform add browser
ionic run browser
```

Modul plug-in Azure Mobile Apps Cordova podporuje aplikace s ionty V1 a v2.  Pouze aplikace iont v2 vyžadují další deklaraci pro objekt `WindowsAzure`.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Postupy: ověřování uživatelů
Azure App Service podporuje ověřování a autorizaci uživatelů aplikací pomocí různých externích zprostředkovatelů identity: Facebook, Google, účet Microsoft a Twitter. Můžete nastavit oprávnění pro tabulky a omezit tak přístup pro konkrétní operace jenom na ověřené uživatele. Identitu ověřených uživatelů můžete použít taky k implementaci autorizačních pravidel ve skriptech serveru. Další informace najdete v kurzu Začínáme [Začínáme s ověřováním] .

Při použití ověřování v aplikaci Apache Cordova musí být k dispozici následující moduly plug-in Cordova:

* [Cordova – modul plug-in zařízení]
* [Cordova – modul plug-in – do pluginu inappbrowser]

Podporují se dva toky ověřování: tok serveru a tok klienta.  Tok serveru poskytuje nejjednodušší možnosti ověřování, protože spoléhá na rozhraní webového ověřování poskytovatele. Tok klienta umožňuje hlubší integraci s funkcemi specifickými pro zařízení, jako je jednotné přihlašování, protože spoléhá na sady SDK specifické pro konkrétní zařízení.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Postupy: konfigurace mobilních App Service pro externí adresy URL pro přesměrování
Několik typů Apache Cordova aplikací používá ke zpracování toků uživatelského rozhraní OAuth schopnost zpětné smyčky.  Toky uživatelského rozhraní OAuth na localhostu způsobují problémy, protože služba ověřování ví, že ve výchozím nastavení využívá službu.  Příklady problematických toků v uživatelském rozhraní OAuth zahrnují:

* Emulátor Ripple.
* V reálném čase se znovu načte s iontem.
* Místní spuštění mobilního back-endu
* Provozování mobilního back-endu v jiném Azure App Service, než je ten, který poskytuje ověřování.

Podle těchto pokynů přidejte do konfigurace místní nastavení:

1. Přihlaste se k portálu [Azure Portal].
2. Vyberte **všechny prostředky** nebo **App Services** klikněte na název mobilní aplikace.
3. Klikněte na **nástroje** .
4. V nabídce sledování klikněte na **Průzkumník prostředků** a potom klikněte na **Přejít**.  Otevře se nové okno nebo karta.
5. V levém navigačním panelu rozbalte uzly **config**, **authsettings** pro vaši lokalitu.
6. Klikněte na **Upravit** .
7. Vyhledejte element "allowedExternalRedirectUrls".  Může být nastaven na hodnotu null nebo pole hodnot.  Změňte hodnotu na následující hodnotu:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Nahraďte adresy URL adresami URL vaší služby.  Příklady zahrnují `http://localhost:3000` (pro ukázkovou službu Node. js) nebo `http://localhost:4400` (pro službu Ripple).  Tyto adresy URL jsou ale příklady – vaše situace, včetně služeb uvedených v ukázkách, se můžou lišit.
8. Klikněte na tlačítko **pro čtení a zápis** v pravém horním rohu obrazovky.
9. Klikněte na zelené tlačítko pro **vložení** .

Nastavení jsou uložena v tomto okamžiku.  Nezavírejte okno prohlížeče, dokud se nedokončí ukládání nastavení.
Přidejte taky tyto adresy URL zpětné smyčky do nastavení CORS pro vaše App Service:

1. Přihlaste se k portálu [Azure Portal].
2. Vyberte **všechny prostředky** nebo **App Services** klikněte na název mobilní aplikace.
3. Automaticky se otevře okno nastavení.  Pokud ne, klikněte na **všechna nastavení**.
4. V nabídce rozhraní API klikněte na **CORS** .
5. Zadejte adresu URL, kterou chcete přidat, do zadaného pole a stiskněte klávesu ENTER.
6. Podle potřeby zadejte další adresy URL.
7. Kliknutím na **Uložit** nastavení uložte.

Aby se nové nastavení projevilo, trvá přibližně 10-15 sekund.

## <a name="register-for-push"></a>Postupy: registrace nabízených oznámení
Pro zpracování nabízených oznámení nainstalujte [PhoneGap – plugin – nabízení] .  Tento modul plug-in se dá snadno přidat pomocí příkazu `cordova plugin add` v příkazovém řádku nebo prostřednictvím instalačního programu modulu plug-in Git v rámci sady Visual Studio.  Následující kód v aplikaci Apache Cordova zaregistruje vaše zařízení pro nabízená oznámení:

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

Pro posílání nabízených oznámení ze serveru použijte sadu SDK Notification Hubs.  Nikdy Neodesílat nabízená oznámení přímo z klientů. V takovém případě se dá použít k aktivaci útoku DOS proti Notification Hubs nebo PNS.  PNS by mohl zakázat provoz v důsledku takových útoků.

## <a name="more-information"></a>Další informace

Podrobné informace o rozhraní API najdete v [dokumentaci k rozhraní API](https://azure.github.io/azure-mobile-apps-js-client/).

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com
[rychlé zprovoznění Mobile Apps Azure]: app-service-mobile-cordova-get-started.md
[Začínáme s ověřováním]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Modul plug-in Apache Cordova pro Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[Vaše první aplikace Apache Cordova]: https://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[PhoneGap – plugin – nabízení]: https://www.npmjs.com/package/phonegap-plugin-push
[Cordova – modul plug-in zařízení]: https://www.npmjs.com/package/cordova-plugin-device
[Cordova – modul plug-in – do pluginu inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
