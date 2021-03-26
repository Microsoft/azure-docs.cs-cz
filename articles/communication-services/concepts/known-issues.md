---
title: Komunikační služby Azure – známé problémy
description: Další informace o komunikačních službách Azure
author: rinarish
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: aa5530dd279e8b45382fe6841b6f193a652c0ba3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566791"
---
# <a name="known-issues-azure-communication-services-client-libraries"></a>Známé problémy: klientské knihovny služby Azure Communication Services
Tento článek poskytuje informace o omezeních a známých problémech souvisejících s klientskými knihovnami služby Azure Communications Services.

> [!IMPORTANT]
> Existuje několik faktorů, které mohou ovlivnit kvalitu volání. Další informace o konfiguraci sítě komunikačních služeb a testování osvědčených postupů najdete v dokumentaci k **[požadavkům sítě](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements)** .


## <a name="javascript-client-library"></a>Klientská knihovna v JavaScriptu

Tato část poskytuje informace o známých problémech souvisejících s hlasem a voláním klienta JavaScriptu ve službě Azure Communication Services.

### <a name="after-refreshing-the-page-user-is-not-removed-from-the-call-immediately"></a>Po obnovení stránky nebude uživatel z volání okamžitě odebrán. 
Pokud se uživatel ve volání a rozhodne aktualizovat stránku, Klientská knihovna komunikačních služeb nemusí být schopna informovat službu Media Services pro komunikaci, že se chystá odpojit. Služba Media Services pro komunikaci neodebere tohoto uživatele okamžitě z volání, ale počká, až se uživatel znovu připojí za problém s připojením k síti. Uživatel se odebere z volání po vypršení časového limitu služby Media Service.

Doporučujeme vývojářům vytvářet prostředí, která nevyžadují, aby koncoví uživatelé aktualizovali stránku vaší aplikace a účastnili se volání. Pokud uživatel stránku aktualizuje, nejlepším způsobem, jak ho zpracovat pro aplikaci, je použít stejné ID uživatele komunikačních služeb pro uživatele poté, co se po aktualizaci vrátí zpět do aplikace.

V perspektivě dalších účastníků ve volání zůstane tento uživatel ve volání po předem definované množství času (1-2 min). Pokud se uživatel znovu připojí ke stejnému ID uživatele komunikační služby, bude reprezentován jako stejný stávající objekt v `remoteParticipants` kolekci.
Pokud dříve uživatel poslal video, `videoStreams` shromažďování bude uchovávat předchozí informace o datovém proudu, dokud služba neuplyne časovým limitem a nebude ji možné odebrat, v této situaci se může v této aplikaci rozhodnout sledovat všechny nové datové proudy přidané do kolekce a jednu s nejvyšší hodnotou `id` . 


### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>Z více zařízení na webu není možné vykreslovat více náhledů.
Jedná se o známé omezení. Další informace najdete v tématu věnovaném [volání klientské knihovny s přehledem](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features) .

### <a name="enumeration-of-the-microphone-and-speaker-devices-is-not-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>V prohlížeči Safari není možné vyčíslit zařízení mikrofonu a mluvčího, když je aplikace spuštěná v iOS nebo iPadOS. 
Aplikace nemůžou vytvořit výčet/mluvčí zařízení (jako Bluetooth) v Safari iOS/iPadu. Toto je známé omezení operačního systému.

Pokud používáte Safari v macOS, vaše aplikace nebude moct vytvářet výčet a vybírat reproduktory prostřednictvím Správce zařízení komunikačních služeb. V tomto scénáři je potřeba zařízení vybrat přes operační systém. Pokud používáte Chrome v macOS, může aplikace zobrazit nebo vybrat zařízení prostřednictvím komunikačních služeb Správce zařízení.

### <a name="audio-connectivity-is-lost-when-receiving-sms-messages-or-calls-during-an-ongoing-voip-call"></a>Při přijímání zpráv SMS nebo volání během probíhajícího volání VoIP dojde ke ztrátě zvukového připojení.
Mobilní prohlížeče neudržují připojení ve stavu na pozadí. To může vést k degradované možnosti volání, pokud bylo volání VoIP přerušeno pomocí textové zprávy nebo příchozího volání veřejné telefonní sítě, které aplikaci vloží do pozadí.

<br/>Klientská knihovna: volání (JavaScript)
<br/>Prohlížeče: Safari, Chrome
<br/>Operační systém: iOS, Android

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Opakované přepínání grafických zařízení může způsobit, že se streamování videa dočasně zastaví.

Přepínání mezi grafickými zařízeními může způsobit, že se datový proud videa během získání datového proudu z vybraného zařízení zastaví.

#### <a name="possible-causes"></a>Možné příčiny
Streamování mezi mediálními zařízeními a přepínání mezi nimi je výpočetní výkon. Přepínání často může způsobit snížení výkonu. Vývojářům Doporučujeme zastavit jeden datový proud zařízení před tím, než začnete s jiným.

### <a name="bluetooth-headset-microphone-is-not-detected-therefore-is-not-audible-during-the-call-on-safari-on-ios"></a>Nepovedlo se zjistit mikrofon sluchátek s mikrofonem Bluetooth. při volání Safari v iOS se nejedná o zvuk.
Prohlížeč Safari v iOS nepodporuje sluchátka s mikrofonem Bluetooth. Vaše zařízení Bluetooth se nebude zobrazovat v dostupných možnostech mikrofonu a ostatní účastníci vás nebudou moci slyšet, pokud se pokusíte použít Bluetooth přes Safari.

#### <a name="possible-causes"></a>Možné příčiny
Jedná se o známé omezení operačního systému macOS/iOS/iPadOS. 

Pomocí prohlížeče Safari v **MacOS** a **iOS/iPadOS** není možné vytvořit výčet a výběr zařízení mluvčích prostřednictvím komunikačních služeb Správce zařízení protože aplikace Safari nepodporuje výčet a výběr mluvčích. V tomto scénáři by se Váš výběr zařízení měl aktualizovat přes operační systém.

### <a name="rotation-of-a-device-can-create-poor-video-quality"></a>Rotace zařízení může vytvořit špatnou kvalitu videa.
Když se zařízení otáčí, můžou uživatelé zaznamenat zhoršení kvality videa.

<br/>Ovlivněná zařízení: Google pixel 5, Google pixel 3a, Apple iPad 8 a Apple iPad X
<br/>Klientská knihovna: volání (JavaScript)
<br/>Prohlížeče: Safari, Chrome
<br/>Operační systém: iOS, Android


### <a name="camera-switching-makes-the-screen-freeze"></a>Přepínání fotoaparátů umožňuje zablokovat obrazovku 
Když uživatel komunikačních služeb připojí volání pomocí JavaScriptu pro volání klientské knihovny a pak narazí na přepínač kamery, uživatelské rozhraní může zcela nereagovat, dokud se aplikace neaktualizuje nebo dokud se prohlížeč neodešle na pozadí uživatelem.

<br/>Ovlivněná zařízení: Google pixel 4a
<br/>Klientská knihovna: volání (JavaScript)
<br/>Prohlížeče: Chrome
<br/>Operační systém: iOS, Android


#### <a name="possible-causes"></a>Možné příčiny
V rámci šetření.

### <a name="if-the-video-signal-was-stopped-while-the-call-is-in-connecting-state-the-video-will-not-be-sent-after-the-call-started"></a>Pokud se signál videa zastavil v době, kdy je hovor ve stavu "připojování", video se po zahájení hovoru nepošle. 
Pokud se uživatelé rozhodnou rychle zapnout nebo vypnout video, zatímco volání je ve `Connecting` stavu, může to vést k potížím s datovým proudem získaným pro volání. Doporučujeme vývojářům sestavovat své aplikace tak, aby nevyžadovaly zapnutí nebo vypnutí videa, zatímco volání je ve `Connecting` stavu. Tento problém může způsobit snížení výkonu videa v následujících scénářích:

 - Pokud uživatel začne používat zvuk a pak zahájí a zastaví video, když je hovor ve `Connecting` stavu.
 - Pokud uživatel začne používat zvuk a pak zahájí a zastaví video, když je hovor ve `Lobby` stavu.


#### <a name="possible-causes"></a>Možné příčiny
V rámci šetření.

###  <a name="sometimes-it-takes-a-long-time-to-render-remote-participant-videos"></a>Vykreslování videí vzdáleného účastníka může někdy trvat dlouhou dobu.
Během probíhajícího volání skupiny _uživatel a_ pošle video a pak se _uživateli B_ připojí volání. V některých případech se uživateli B nezobrazuje video od uživatele A, nebo uživatel A video začíná vykreslování po dlouhém zpoždění. Příčinou může být síťové prostředí, které vyžaduje další konfiguraci. Pokyny k konfiguraci sítě najdete v dokumentaci k [požadavkům na síť](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements) .
