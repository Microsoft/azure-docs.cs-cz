---
title: Přehled služby Azure Communication Services pro volání sady SDK
titleSuffix: An Azure Communication Services concept document
description: Poskytuje přehled o volání sady SDK.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 39331cb83b233f6b91344e6e299ed81f187fe9d9
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108383"
---
# <a name="calling-sdk-overview"></a>Přehled volání sady SDK

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


Existují dvě samostatné rodiny volání sad SDK pro *klienty* a *služby.* Aktuálně dostupné sady SDK jsou určené pro prostředí koncových uživatelů: websites a nativní aplikace.

Sady SDK služby ještě nejsou k dispozici a poskytují přístup k nezpracovaným datovým a obrazovým rovinám, které jsou vhodné pro integraci s roboty a dalšími službami.

## <a name="calling-sdk-capabilities"></a>Volání funkcí sady SDK

Následující seznam obsahuje sadu funkcí, které jsou aktuálně k dispozici ve komunikačních službách Azure, které volají sady SDK.

| Skupina funkcí | Schopnost                                                                                                          | JS  | Java (Android) | Objective-C (iOS)
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Základní funkce | Jedno volání 1:1 mezi dva uživatele                                                                           | ✔️   | ✔️            | ✔️
|                   | Umístit skupinové volání s více než dvěma uživateli (až 350 uživatelů)                                                       | ✔️   | ✔️            | ✔️
|                   | Zvýšení úrovně volání 1:1 se dvěma uživateli na skupinové volání s více než dvěma uživateli                                 | ✔️   | ✔️            | ✔️
|                   | Připojit se k volání skupiny po jeho spuštění                                                                              | ✔️   | ✔️            | ✔️
|                   | Pozvat dalšího účastníka VoIP, aby se připojil k probíhajícímu volání skupiny                                                       | ✔️   | ✔️            | ✔️
|  Střední řízení volání | Zapnout/vypnout video                                                                                              | ✔️   | ✔️            | ✔️ 
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
| Obecné           | Testování mikrofonu, mluvčího a fotoaparátu pomocí služby zvukové testování (k dispozici při volání 8: echo123)                   | ✔️   | ✔️            | ✔️ 
| Správa zařízení | Požádat o oprávnění používat zvuk a video                                                                       | ✔️   | ✔️            | ✔️
|                   | Získat seznam fotoaparátů                                                                                                     | ✔️   | ✔️            | ✔️ 
|                   | Nastavit kameru                                                                                                          | ✔️   | ✔️            | ✔️
|                   | Získat vybranou kameru                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Získat seznam mikrofonů                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Nastavení mikrofonu                                                                                                      | ✔️   | ✔️            | ✔️
|                   | Získat vybraný mikrofon                                                                                             | ✔️   | ✔️            | ✔️
|                   | Získat seznam mluvčích                                                                                                   | ✔️   | ✔️            | ✔️
|                   | Nastavit mluvčí                                                                                                         | ✔️   | ✔️            | ✔️
|                   | Získat vybraného mluvčího                                                                                                | ✔️   | ✔️            | ✔️
| Vykreslování videa   | Vykreslování jednoho videa na mnoha místech (místní fotoaparát nebo vzdálený datový proud)                                                  | ✔️   | ✔️            | ✔️
|                   | Nastavit nebo aktualizovat režim škálování                                                                                           | ✔️   | ✔️            | ✔️ 
|                   | Vykreslit vzdálený video stream                                                                                          | ✔️   | ✔️            | ✔️



## <a name="javascript-calling-sdk-support-by-os-and-browser"></a>JavaScript – volání sady SDK pomocí operačního systému a prohlížeče

Následující tabulka představuje sadu podporovaných prohlížečů, které jsou aktuálně k dispozici. Pokud není uvedeno jinak, podporujeme nejnovější tři verze prohlížeče.

|                                  | Chrome | Prohlížeče  | Hrana (chrom) | 
| -------------------------------- | -------| ------  | --------------  |
| Android                          |  ✔️    | ❌     | ❌             |
| iOS                              |  ❌    | ✔️**** | ❌             |
| macOS * * *                         |  ✔️    | ✔️**   | ❌             |
| Windows * * *                       |  ✔️    | ❌     | ✔️             |
| Ubuntu/Linux                     |  ✔️    | ❌     | ❌             |

* Podporované verze prohlížeče Safari 13.1 +, volání 1:1 nejsou v Safari podporována. 

* * Prohlížeč Safari 14 +/macOS 11 + potřebný pro podporu odchozích videí. 

Sdílení odchozí obrazovky se podporuje jenom na platformách desktopu (Windows, macOS a Linux) bez ohledu na verzi prohlížeče a nepodporuje se na žádné mobilní platformě (Android, iOS, iPad a tablety).

Aplikace pro iOS v Safari nemůže zobrazit výčet a vybrat zařízení s mikrofonem a mluvčím (třeba Bluetooth). Toto je omezení operačního systému a existuje vždy pouze jedno zařízení.


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

## <a name="calling-sdk-streaming-support"></a>Volání podpory streamování SDK
Komunikační služby, které volají sadu SDK, podporují následující konfigurace streamování:

|           |Web | Android/iOS|
|-----------|----|------------|
|**počet odchozích streamů, které se dají poslat současně** |1 sdílení obrazovky nebo 1 | 1 sdílení obrazovky a 1 video + 1|
|**počet příchozích streamů, které se dají vykreslovat současně** |1 sdílení obrazovky nebo 1| 6 sdílení obrazovky a videa 1 |


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s voláním](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Další informace najdete v následujících článcích:
- Seznamte se s obecnými [toky volání](../call-flows.md)
- Další informace o [typech volání](../voice-video-calling/about-call-types.md)
- [Plánování řešení veřejné sítě](../telephony-sms/plan-solution.md)
