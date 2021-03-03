---
title: Přehled volání klientské knihovny Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Poskytuje přehled o volání klientské knihovny.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: ed56d0cf69710173d41c5262e7a3121a0cad1304
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660136"
---
# <a name="calling-client-library-overview"></a>Přehled klientských knihoven pro hovory

Existují dvě samostatné rodiny volání klientských knihoven pro *klienty* a *služby.* Aktuálně dostupné klientské knihovny jsou určené pro prostředí koncových uživatelů: websites a Native Apps.

Knihovny klienta služby ještě nejsou k dispozici a poskytují přístup k nezpracovaným datovým a obrazovým rovinám, které jsou vhodné pro integraci s roboty a dalšími službami.

## <a name="calling-client-library-capabilities"></a>Volání funkcí klientské knihovny

Následující seznam obsahuje sadu funkcí, které jsou aktuálně k dispozici ve komunikačních službách Azure, které volají klientské knihovny.

| Skupina funkcí | Schopnost                                                                                                          | JS  | Java (Android) | Objective-C (iOS)
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Základní funkce | Jedno volání 1:1 mezi dva uživatele                                                                           | ✔️   | ✔️            | ✔️
|                   | Umístit skupinové volání s více než dvěma uživateli (až 350 uživatelů)                                                       | ✔️   | ✔️            | ✔️
|                   | Zvýšení úrovně volání 1:1 se dvěma uživateli na skupinové volání s více než dvěma uživateli                                 | ✔️   | ✔️            | ✔️
|                   | Připojit se k volání skupiny po jeho spuštění                                                                              | ✔️   | ✔️            | ✔️
|                   | Pozvat dalšího účastníka VoIP, aby se připojil k probíhajícímu volání skupiny                                                       | ✔️   | ✔️            | ✔️
|                   | Zapnout/vypnout video                                                         | ✔️   | ✔️            | ✔️
|                   | Ztlumení a ztlumení mikrofonu                                                                                                     | ✔️   | ✔️            | ✔️
|                   | Přepínání mezi fotoaparáty                                                                                              | ✔️   | ✔️            | ✔️
|                   | Místní blokování/zrušení blokování                                                                                                  | ✔️   | ✔️            | ✔️
|                   | Aktivní mluvčí                                                                                                      | ✔️   | ✔️            | ✔️
|                   | Zvolit mluvčí pro volání                                                                                            | ✔️   | ✔️            | ✔️
|                   | Volba mikrofonu pro volání                                                                                         | ✔️   | ✔️            | ✔️
|                   | Zobrazit stav účastníka<br/>*Nečinné, předčasné médium, připojení, připojení, blokováno, v předsálí, odpojeno*         | ✔️   | ✔️            | ✔️
|                   | Zobrazit stav volání<br/>*Předčasné médium, příchozí, připojení, vyzvánění, připojení, blokování, odpojování, odpojení* | ✔️   | ✔️            | ✔️
|                   | Zobrazit, zda je účastník ztlumený                                                                                      | ✔️   | ✔️            | ✔️
|                   | Zobrazit důvod, proč účastník opustí volání                                                                       | ✔️   | ✔️            | ✔️
| Sdílení obrazovky    | Sdílet celou obrazovku v rámci aplikace                                                                 | ✔️   | ❌            | ❌
|                   | Sdílet konkrétní aplikaci (ze seznamu spuštěných aplikací)                                                | ✔️   | ❌            | ❌
|                   | Sdílení karty webového prohlížeče ze seznamu otevřených karet                                                                  | ✔️   | ❌            | ❌
|                   | Účastník může zobrazit sdílenou složku na vzdálené obrazovce.                                                                            | ✔️   | ✔️            | ✔️
| Soupis            | Seznam účastníků                                                                                                   | ✔️   | ✔️            | ✔️
|                   | Odebrat účastníka                                                                                                | ✔️   | ✔️            | ✔️
| Veřejná              | Vložení volání 1:1 s účastníkem veřejné sítě                                                                     | ✔️   | ✔️            | ✔️
|                   | Umístěte volání skupiny do účastníků veřejné telefonní sítě.                                                                           | ✔️   | ✔️            | ✔️
|                   | Zvýšení úrovně volání 1:1 s účastníkem veřejné sítě na volání skupiny                                                 | ✔️   | ✔️            | ✔️
|                   | Telefonické připojení z volání skupiny jako účastník veřejné sítě                                                                    | ✔️   | ✔️            | ✔️
| Obecné           | Testování mikrofonu, mluvčího a fotoaparátu pomocí služby zvukové testování (k dispozici při volání 8: echo123)                   |  ✔️  | ✔️            | ✔️

## <a name="javascript-calling-client-library-support-by-os-and-browser"></a>JavaScript – volání klientské podpory pomocí operačního systému a prohlížeče

Následující tabulka představuje sadu podporovaných prohlížečů a verzí, které jsou aktuálně k dispozici.

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | operační systém iPad|
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ | -------|
| **Volání klientské knihovny** | Chrome *, nová hrana | Chrome *, Safari** | Chrome  | Chrome | Chrome | Safari * * | Safari * * |


* Všimněte si, že nejnovější verze Chrome je podporovaná Kromě předchozích dvou verzí.<br/>

* * Upozorňujeme, že verze Safari 13.1 + jsou podporované. Odchozí video pro Safari macOS ještě není podporované, ale podporuje se v iOS. Sdílení odchozí obrazovky se podporuje jenom v desktopové iOS. 1:1 a volání skupin aktuálně nejsou k dispozici v Safari.

## <a name="calling-client---browser-security-model"></a>Volání modelu zabezpečení prohlížeče klienta

### <a name="user-webrtc-over-https"></a>Uživatel WebRTC přes HTTPS

Rozhraní API WebRTC jako `getUserMedia` vyžadují, aby aplikace, která volá tato rozhraní API, byla obsluhována prostřednictvím protokolu HTTPS.

Pro místní vývoj můžete použít `http://localhost` .

### <a name="embed-the-communication-services-calling-sdk-in-an-iframe"></a>Vložení komunikačních služeb pro volání sady SDK v prvku IFRAME

Nové [zásady oprávnění (označované taky jako zásady funkcí)](https://www.w3.org/TR/permissions-policy-1/#iframe-allow-attribute) se přijímají různými prohlížeči. Tato zásada má vliv na volání scénářů tím, že řídí, jak můžou aplikace přistupovat ke kameře a mikrofonu zařízení prostřednictvím elementu IFRAME s více zdroji.

Pokud chcete použít prvek IFRAME k hostování části aplikace z jiné domény, je nutné přidat `allow` atribut se správnou hodnotou do prvku IFRAME.

Tento prvek IFRAME například umožňuje přístup ke kameře a mikrofonu:

```html
<iframe allow="camera *; microphone *">
```

## <a name="calling-client-library-streaming-support"></a>Volání podpory streamování klientské knihovny
Komunikační služby volající klientské knihovny podporují následující konfigurace streamování:

|           |Web | Android/iOS|
|-----------|----|------------|
|**počet odchozích streamů, které se dají poslat současně** |1 audio/video nebo 1 sdílení zvuku/obrazovky | 1 zvuk/video |
|**počet příchozích streamů, které se dají vykreslovat současně** |1 audio/video nebo 1 sdílení zvuku/obrazovky| 6 audio/video nebo 1 sdílení obrazovky |

Všimněte si, že ve scénářích skupiny je jeden smíšený zvukový stream použit k podpoře všech účastníků zvuku.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s voláním](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Další informace najdete v následujících článcích:
- Seznamte se s obecnými [toky volání](../call-flows.md)
- Další informace o [typech volání](../voice-video-calling/about-call-types.md)
- Další informace o [typech telefonních čísel](../telephony-sms/plan-solution.md)
