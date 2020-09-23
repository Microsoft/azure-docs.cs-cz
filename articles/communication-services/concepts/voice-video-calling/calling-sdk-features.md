---
title: Přehled volání klientské knihovny Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Poskytuje přehled o volání klientské knihovny.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 9cc00cfa9f44c69a5880d53c0b7ac623f60be16b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90946967"
---
# <a name="calling-client-library-overview"></a>Přehled volání klientské knihovny

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

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
| Obecné           | Zúčastnit se volání skupiny, které obsahuje až 350 účastníků                                                       |  ✔️  | ✔️            | ✔️    
|                   | Testování mikrofonu, mluvčího a fotoaparátu pomocí služby zvukové testování (k dispozici při volání 8: echo123)                   |  ✔️  | ✔️            | ✔️   

## <a name="calling-client-library-browser-support"></a>Volání podpory prohlížeče klientské knihovny

Následující tabulka představuje sadu podporovaných prohlížečů a verzí, které jsou aktuálně k dispozici.

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    |
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ |
| **Volání klientské knihovny** | Chrome *, nová hrana | Chrome *, Safari** | Chrome  | Chrome | Chrome | Safari * * |


* Všimněte si, že nejnovější verze Chrome je podporovaná Kromě předchozích dvou verzí.<br/>

* * Upozorňujeme, že verze Safari 13.1 + jsou podporované. Odchozí video pro Safari macOS ještě není podporované, ale podporuje se v iOS. Sdílení odchozí obrazovky se podporuje jenom v desktopové iOS.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s voláním](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Další informace najdete v následujících článcích:
- Seznamte se s obecnými [toky volání](../call-flows.md)
- Další informace o [typech volání](../voice-video-calling/about-call-types.md)
- [Plánování řešení veřejné sítě](../telephony-sms/plan-solution.md)
