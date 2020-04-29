---
title: Agregace událostí
description: Přečtěte si o Azure Security Center pro agregaci událostí IoT.
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
ms.openlocfilehash: f72ef8cc5161bd6f885249e7d39344a57fa2368e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311419"
---
# <a name="azure-security-center-for-iot-event-aggregation"></a>Azure Security Center pro agregaci událostí IoT

Azure Security Center pro agenty zabezpečení IoT shromažďují data a systémové události z místního zařízení a odesílají tato data do cloudu Azure pro zpracování a analýzu. Agent zabezpečení shromažďuje mnoho typů událostí zařízení, včetně nového procesu a událostí nového připojení. Události nového procesu a nového připojení se můžou na zařízení v druhé době často vyskytnout a i když jsou důležité pro robustní a komplexní zabezpečení, může se stát, že počet agentů zabezpečení zprávy bude nuceně zasílat nebo překročit vaši kvótu IoT Hub a omezení nákladů. Tyto události však obsahují vysoce cenné informace o zabezpečení, které jsou zásadní pro ochranu zařízení.

Pokud chcete snížit další kvótu a náklady a zachovat chráněná zařízení, Azure Security Center pro agenty IoT agregovat tyto typy událostí.

Agregace událostí je ve výchozím nastavení **zapnutá** a i když se nedoporučuje, dá se v jakémkoli okamžiku ručně **vypnout** .

Agregace je aktuálně k dispozici pro následující typy událostí:

* ProcessCreate
* ConnectionCreate
* ProcessTerminate (jenom Windows)

## <a name="how-does-event-aggregation-work"></a>Jak funguje agregace událostí?

Když je agregace **událostí ponechána**, Azure Security Center pro agenty IoT agregovat události pro období intervalu nebo časový interval.
Po uplynutí časového intervalu pošle agent agregované události do cloudu Azure pro další analýzu.
Agregované události jsou uloženy v paměti, dokud nejsou odesílány do cloudu Azure.

Aby se snížilo nároky na paměť agenta, pokaždé, když agent shromáždí identickou událost k tomu, že se už uchovává v paměti, agent zvyšuje počet volání této konkrétní události. Po úspěšném časovém intervalu agregace pošle agentovi počet přístupů každého konkrétního typu události, ke které došlo. Agregace událostí je jednoduše agregace počtu přístupů každého shromážděného typu události.

Události se považují za identické jenom v případě, že jsou splněné následující podmínky:

* ProcessCreate události – když je **příkazový řádek**, **spustitelný soubor**, **username**a **UserID** , je stejný
* ConnectionCreate události – když se příkaz **CommandLine**, **userId**, **směr**, **místní adresa**, **Vzdálená adresa**, * * protokol a **cílový port** shodují
* ProcessTerminate události – když je stav **spustitelného souboru** a **ukončení** shodný

### <a name="working-with-aggregated-events"></a>Práce s agregovanými událostmi

Během agregace se vlastnosti události, které nejsou agregované, zahodí a zobrazují se v Log Analytics s hodnotou 0.

* Události ProcessCreate- **ProcessID**a **parentProcessId** jsou nastaveny na 0.
* Události ConnectionCreate- **ProcessID**a **zdrojový port** jsou nastaveny na 0.

## <a name="event-aggregation-based-alerts"></a>Výstrahy založené na agregaci událostí

Po analýze Azure Security Center pro IoT vytvoří výstrahy zabezpečení pro podezřelé agregované události. Výstrahy vytvořené z agregovaných událostí se u každé agregované události zobrazují jenom jednou.

Čas zahájení agregace, koncový čas a počet průchodů každé události jsou protokolovány v poli Event **ExtraDetails** v rámci Log Analytics pro použití během vyšetřování.

Každá agregovaná událost představuje 24hodinové období shromážděných výstrah. Pomocí nabídky možnosti události v levém horním rohu každé události můžete **Zavřít** každou jednotlivou agregovanou událost.

## <a name="event-aggregation-twin-configuration"></a>Konfigurace zdvojené agregace událostí

Proveďte změny v konfiguraci Azure Security Center pro agregaci událostí IoT uvnitř [objektu konfigurace agenta](how-to-agent-configuration.md) s doplněním identity modulu **azureiotsecurity** .

| Název konfigurace | Možné hodnoty | Podrobnosti | Poznámky |
|:-----------|:---------------|:--------|:--------|
| aggregationEnabledProcessCreate | Boolean | Povolit/zakázat agregaci události pro události vytvoření procesu |
| aggregationIntervalProcessCreate | Řetězec ISO8601 TimeSpan | Interval agregace pro události vytvoření procesu |
| aggregationEnabledConnectionCreate | Boolean| Povolit/zakázat agregaci události pro události vytvoření připojení |
| aggregationIntervalConnectionCreate | Řetězec ISO8601 TimeSpan | Interval agregace pro události vytvoření připojení |
| aggregationEnabledProcessTerminate | Boolean | Povolit/zakázat agregaci události pro události ukončení procesu | Jen ve Windows|
| aggregationIntervalProcessTerminate | Řetězec ISO8601 TimeSpan | Interval agregace pro události ukončení procesu | Jen ve Windows|
|

## <a name="default-configurations-settings"></a>Výchozí nastavení konfigurace

| Název konfigurace | Výchozí hodnoty |
|:-----------|:---------------|
| aggregationEnabledProcessCreate | true |
| aggregationIntervalProcessCreate | PT1H|
| aggregationEnabledConnectionCreate | true |
| aggregationIntervalConnectionCreate | PT1H|
| aggregationEnabledProcessTerminate | true |
| aggregationIntervalProcessTerminate | PT1H|
|

## <a name="next-steps"></a>Další kroky

V tomto článku jste se seznámili s Azure Security Center pro agregaci agenta zabezpečení IoT a dostupných možností konfigurace událostí.

Pokud chcete pokračovat v seznámení s Azure Security Center pro nasazení IoT, postupujte podle následujících článků:

- Porozumění [metodám ověřování agenta zabezpečení](concept-security-agent-authentication-methods.md)
- Výběr a nasazení [agenta zabezpečení](how-to-deploy-agent.md)
- Kontrola [požadovaných součástí Azure Security Center služby](service-prerequisites.md) IoT
- Naučte se, jak [povolit Azure Security Center pro službu IoT v IoT Hub](quickstart-onboard-iot-hub.md)
- Další informace o službě najdete v tématu [Nejčastější dotazy k Azure Security Center pro IoT](resources-frequently-asked-questions.md)
