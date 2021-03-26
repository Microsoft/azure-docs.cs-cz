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
ms.openlocfilehash: 7be40ac5f6cda7a81d68ca0b17f377891dd58480
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606041"
---
# <a name="known-issues-azure-communication-services-sdks"></a>Známé problémy: sady SDK služby Azure Communication Services
Tento článek poskytuje informace o omezeních a známých problémech týkajících se sad Azure Communication Services SDK.

> [!IMPORTANT]
> Existuje několik faktorů, které mohou ovlivnit kvalitu volání. Další informace o konfiguraci sítě komunikačních služeb a testování osvědčených postupů najdete v dokumentaci k **[požadavkům sítě](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements)** .


## <a name="javascript-sdk"></a>JavaScript SDK

Tato část poskytuje informace o známých problémech spojených s komunikačními službami JavaScript a voláním sady SDK pro komunikaci s Azure Communications.

### <a name="refreshing-a-page-doesnt-immediately-remove-the-user-from-their-call"></a>Aktualizace stránky uživateli okamžitě neodebere uživatele z jeho volání.

Pokud se uživatel ve volání a rozhodne aktualizovat stránku, služba Media Services Media Service neodebere tohoto uživatele okamžitě z tohoto volání. Bude počkat na opětovné připojení uživatele. Uživatel se odebere ze volání po vypršení časového limitu služby Media Service.

Nejlepší je vytvořit uživatelské prostředí, které nevyžadují, aby koncoví uživatelé aktualizovali stránku aplikace při volání. Pokud uživatel aktualizuje stránku, po návratu zpět do aplikace znovu použije stejné ID uživatele komunikační služby.

Z perspektivy dalších účastníků ve volání zůstane uživatel ve volání po dobu trvání (1-2 minut). Pokud se uživatel znovu připojí ke stejnému ID uživatele komunikačních služeb, bude reprezentován jako stejný existující objekt v `remoteParticipants` kolekci.

Pokud uživatel odeslal video před aktualizací, `videoStreams` kolekce uchová předchozí informace o datovém proudu, dokud nevyprší časový limit služby a neodebere je. V tomto scénáři se aplikace může rozhodnout sledovat všechny nové datové proudy přidané do kolekce a jednu z nich vykreslit nejvyšší `id` . 


### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>Z více zařízení na webu není možné vykreslovat více náhledů.
Jedná se o známé omezení. Další informace najdete v tématu [Přehled volání sady SDK](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features).

### <a name="enumerating-devices-isnt-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>Vytváření výčtu zařízení není možné v Safari, pokud je aplikace spuštěná v iOS nebo iPadOS.

Aplikace nemůžou vytvořit výčet/mluvčí zařízení (jako Bluetooth) v Safari iOS/iPadu. Toto je známé omezení operačního systému.

Pokud používáte Safari v macOS, vaše aplikace nebude moct vytvářet výčet a vybírat reproduktory prostřednictvím Správce zařízení komunikačních služeb. V tomto scénáři je potřeba zařízení vybrat přes operační systém. Pokud používáte Chrome v macOS, může aplikace zobrazit nebo vybrat zařízení prostřednictvím komunikačních služeb Správce zařízení.

### <a name="audio-connectivity-is-lost-when-receiving-sms-messages-or-calls-during-an-ongoing-voip-call"></a>Při přijímání zpráv SMS nebo volání během probíhajícího volání VoIP dojde ke ztrátě zvukového připojení.
Mobilní prohlížeče neudržují připojení ve stavu na pozadí. To může vést k degradované možnosti volání, pokud bylo volání VoIP přerušeno událostí, která aplikaci vloží do pozadí.

<br/>Klientská knihovna: volání (JavaScript)
<br/>Prohlížeče: Safari, Chrome
<br/>Operační systém: iOS, Android

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Opakované přepínání grafických zařízení může způsobit, že se streamování videa dočasně zastaví.

Přepínání mezi grafickými zařízeními může způsobit, že se datový proud videa během získání datového proudu z vybraného zařízení zastaví.

#### <a name="possible-causes"></a>Možné příčiny
Přepínání mezi zařízeními často může způsobit snížení výkonu. Vývojářům Doporučujeme zastavit jeden datový proud zařízení před tím, než začnete s jiným.

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
Když uživatel komunikačních služeb připojí volání pomocí JavaScriptu pro volání sady JavaScript a potom narazí na tlačítko vypínače fotoaparátu, může uživatelské rozhraní přestat reagovat, dokud nebude aplikace obnovena nebo dokud nebude prohlížeč na pozadí vložen uživatelem.

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
Během probíhajícího volání skupiny _uživatel a_ pošle video a pak se _uživateli B_ připojí volání. V některých případech se uživateli B nezobrazuje video od uživatele A, nebo uživatel A video začíná vykreslování po dlouhém zpoždění. K tomuto problému může dojít v důsledku síťového prostředí, které vyžaduje další konfiguraci. Pokyny k konfiguraci sítě najdete v dokumentaci k [požadavkům na síť](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements) .
