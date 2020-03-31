---
title: Principy Azure Security Center pro agregaci událostí IoT| Dokumenty společnosti Microsoft
description: Přečtěte si o azure security center pro agregaci událostí IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d40dac0b-abd0-4ff5-82eb-0f511ee13725
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2019
ms.author: mlottner
ms.openlocfilehash: ca1d1a5761e62b2838a474dcb83f450987972998
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73928961"
---
# <a name="azure-security-center-for-iot-event-aggregation"></a>Azure Security Center pro agregaci událostí IoT

Agenti zabezpečení Azure Security Center pro IoT shromažďuje data a systémové události z místního zařízení a odesílají tato data do cloudu Azure pro zpracování a analýzu. Agent zabezpečení shromažďuje mnoho typů událostí zařízení, včetně nového procesu a nové události připojení. Nový proces i nové události připojení se mohou v zařízení během sekundy legitimně vyskytovat často a i když je důležitý pro robustní a komplexní zabezpečení, počet zpráv, které jsou agenti zabezpečení nuceni odesílat, může rychle dosáhnout nebo překročit vaše centrum IoT Hub. kvóta a nákladové limity. Tyto události však obsahují vysoce cenné informace o zabezpečení, které jsou zásadní pro ochranu vašeho zařízení.

Chcete-li snížit další kvóty a náklady při zachování vašich zařízení chráněné, Azure Security Center pro agenty IoT agregovat tyto typy událostí.

Agregace událostí je ve výchozím nastavení **zapnutá** a i když se nedoporučuje, lze ji kdykoli ručně **vypnout.**

Agregace je v současné době k dispozici pro následující typy událostí:
* ProcessCreate
* Vytvoření připojení
* ProcessTerminate (pouze systém Windows)

## <a name="how-does-event-aggregation-work"></a>Jak funguje agregace událostí?
Když je agregace událostí ponechána **na**, Azure Security Center pro agenty IoT agregovat události pro interval období nebo časové okno.
Po uplynutí intervalu agent odešle agregované události do cloudu Azure pro další analýzu.
Agregované události se ukládají v paměti, dokud se neodešlou do cloudu Azure.

Chcete-li snížit nároky na paměť agenta, vždy, když agent shromažďuje identickou událost, která je již uchovávána v paměti, agent zvýší počet přístupů této konkrétní události. Když časové okno agregace projde, agent odešle počet přístupů každého konkrétního typu události, ke které došlo. Agregace událostí je jednoduše agregace počtu přístupů každého shromážděného typu události.

Události jsou považovány za identické pouze v případě, že jsou splněny následující podmínky: 

* ProcessCreate události - když **příkazLine**, **spustitelný soubor**, **uživatelské jméno**a **userid** jsou identické
* ConnectionCreate události - když **commandLine**, **userId**, **směr**, **místní adresa**, **vzdálená adresa**, **protokol a cílový **port** jsou identické
* ProcessTerminate události - když **spustitelný soubor** a **stav ukončení** jsou identické

### <a name="working-with-aggregated-events"></a>Práce s agregovanými událostmi

Během agregace jsou zahozeny vlastnosti událostí, které nejsou agregovány, a zobrazí se v analýze protokolu s hodnotou 0. 
* ProcessCreate události - **processId**a **parentProcessId** jsou nastaveny na 0
* ConnectionCreate události - **processId**a **zdrojový port** jsou nastaveny na 0

## <a name="event-aggregation-based-alerts"></a>Výstrahy založené na agregaci událostí 
Po analýze Azure Security Center pro IoT vytvoří výstrahy zabezpečení pro podezřelé agregované události. Výstrahy vytvořené z agregovaných událostí se zobrazí pouze jednou pro každou agregoci události.

Čas zahájení agregace, čas ukončení a počet přístupů pro každou událost jsou zaznamenány v poli **extradetails** události v rámci analýzy protokolů pro použití během šetření. 

Každá agregovaná událost představuje 24hodinové období shromážděných výstrah. Pomocí nabídky možností události v levém horním rohu každé události můžete každou jednotlivou agregovotou událost **zavřít.**    

## <a name="event-aggregation-twin-configuration"></a>Konfigurace dvojčete agregace událostí
Proveďte změny konfigurace Azure Security Center pro ioT agregace událostí uvnitř [objektu konfigurace agenta](how-to-agent-configuration.md) identity dvojčete modulu **azureiotsecurity.**

| Název konfigurace | Možné hodnoty | Podrobnosti | Poznámky |
|:-----------|:---------------|:--------|:--------|
| agregaceEnabledProcessCreate | Boolean | Povolit nebo zakázat agregaci událostí pro události vytváření procesů |
| agregaceIntervalProcessCreate | Řetězec ISO8601 Timespan | Interval agregace pro události vytvoření procesu |
| agregaceEnabledConnectionCreate | Boolean| Povolit nebo zakázat agregaci událostí pro události vytvoření připojení |
| agregaceIntervalConnectionCreate | Řetězec ISO8601 Timespan | Interval agregace pro události vytvoření připojení |
| aggregaceEnabledProcessTerminate | Boolean | Povolit nebo zakázat agregaci událostí pro události ukončení procesu | Jen ve Windows|
| aggregationIntervalProcessTerminate | Řetězec ISO8601 Timespan | Interval agregace pro události ukončení procesu | Jen ve Windows|
|

## <a name="default-configurations-settings"></a>Výchozí nastavení konfigurací

| Název konfigurace | Výchozí hodnoty |
|:-----------|:---------------|
| agregaceEnabledProcessCreate | true |
| agregaceIntervalProcessCreate | "PT1H"|
| agregaceEnabledConnectionCreate | true |
| agregaceIntervalConnectionCreate | "PT1H"|
| aggregaceEnabledProcessTerminate | true |
| aggregationIntervalProcessTerminate | "PT1H"|
|

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o Azure Security Center pro agregaci agenta zabezpečení IoT a možnosti konfigurace událostí k dispozici.

Pokud chcete pokračovat v začínáme s Azure Security Center pro nasazení IoT, použijte následující články:

- Principy [metod ověřování agenta zabezpečení](concept-security-agent-authentication-methods.md)
- Výběr a nasazení [agenta zabezpečení](how-to-deploy-agent.md)
- Projděte si požadavky Centra zabezpečení Azure pro [Službu](service-prerequisites.md) IoT
- Přečtěte si, jak [povolit službu Azure Security Center for IoT ve vašem Centru IoT Hub](quickstart-onboard-iot-hub.md)
- Další informace o službě v [centru zabezpečení Azure pro ioT nejčastější dotazy](resources-frequently-asked-questions.md)
