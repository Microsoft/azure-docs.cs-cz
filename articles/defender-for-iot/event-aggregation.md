---
title: Defender – agregace událostí v programu IoT-Micro-Agent
description: Přečtěte si o programu Defender pro agregaci událostí IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/20/2021
ms.author: shhazam
ms.openlocfilehash: 01333da3041cc93154c46fb45a39bb209fd30f91
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493065"
---
# <a name="defender-iot-micro-agent-classic-event-aggregation"></a>Defender – agregace událostí v programu IoT-Micro-Agent

Defender pro agenty zabezpečení IoT shromažďuje data a systémové události z místního zařízení a odesílá tato data do cloudu Azure pro zpracování a analýzu. Agent zabezpečení shromažďuje mnoho typů událostí zařízení, včetně nového procesu a událostí nového připojení. Události nového procesu a nového připojení se můžou na zařízení v druhé době často vyskytnout a i když jsou důležité pro robustní a komplexní zabezpečení, může se stát, že počet agentů zabezpečení zprávy bude nuceně zasílat nebo překročit vaši kvótu IoT Hub a omezení nákladů. Tyto události však obsahují vysoce cenné informace o zabezpečení, které jsou zásadní pro ochranu zařízení.

Aby se snížila další kvóta a náklady při zachování chráněných zařízení, Defender pro agenty IoT agreguje tyto typy událostí.

Agregace událostí je ve výchozím nastavení **zapnutá** a i když se nedoporučuje, dá se v jakémkoli okamžiku ručně **vypnout** .

Agregace je aktuálně k dispozici pro následující typy událostí:

* ProcessCreate
* ConnectionCreate
* ProcessTerminate (jenom Windows)

## <a name="how-does-event-aggregation-work"></a>Jak funguje agregace událostí?

V případě, že je agregace událostí ponechána **v**, Defender pro agenty IoT agreguje události pro období intervalu nebo časové okno.
Po uplynutí časového intervalu pošle agent agregované události do cloudu Azure pro další analýzu.
Agregované události jsou uloženy v paměti, dokud nejsou odesílány do cloudu Azure.

Aby se snížilo nároky na paměť agenta, pokaždé, když agent shromáždí identickou událost k tomu, že se už uchovává v paměti, agent zvyšuje počet volání této konkrétní události. Po úspěšném časovém intervalu agregace pošle agentovi počet přístupů každého konkrétního typu události, ke které došlo. Agregace událostí je jednoduše agregace počtu přístupů každého shromážděného typu události.

Události se považují za identické jenom v případě, že jsou splněné následující podmínky:

* ProcessCreate události – když je **příkazový řádek**, **spustitelný soubor**, **username** a **UserID** , je stejný
* ConnectionCreate události – když se příkaz **CommandLine**, **userId**, **směr**, **místní adresa**, **Vzdálená adresa**, **protokol** a **cílový port** shodují.
* ProcessTerminate události – když je stav **spustitelného souboru** a **ukončení** shodný

### <a name="working-with-aggregated-events"></a>Práce s agregovanými událostmi

Během agregace se vlastnosti události, které nejsou agregované, zahodí a zobrazují se v Log Analytics s hodnotou 0.

* Události ProcessCreate- **ProcessID** a **parentProcessId** jsou nastaveny na 0.
* Události ConnectionCreate- **ProcessID** a **zdrojový port** jsou nastaveny na 0.

## <a name="event-aggregation-based-alerts"></a>Výstrahy založené na agregaci událostí

Po analýze program Defender pro IoT vytvoří výstrahy zabezpečení pro podezřelé agregované události. Výstrahy vytvořené z agregovaných událostí se u každé agregované události zobrazují jenom jednou.

Čas zahájení agregace, koncový čas a počet průchodů každé události jsou protokolovány v poli Event **ExtraDetails** v rámci Log Analytics pro použití během vyšetřování.

Každá agregovaná událost představuje 24hodinové období shromážděných výstrah. Pomocí nabídky možnosti události v levém horním rohu každé události můžete **Zavřít** každou jednotlivou agregovanou událost.

## <a name="event-aggregation-twin-configuration"></a>Konfigurace zdvojené agregace událostí

Proveďte změny v konfiguraci programu Defender pro agregaci událostí IoT uvnitř [objektu konfigurace agenta](how-to-agent-configuration.md) , který je v modulu s dvojitou identitou modulu **azureiotsecurity** .

| Název konfigurace | Možné hodnoty | Podrobnosti | Poznámky |
|:-----------|:---------------|:--------|:--------|
| aggregationEnabledProcessCreate | boolean | Povolit/zakázat agregaci události pro události vytvoření procesu |
| aggregationIntervalProcessCreate | Řetězec ISO8601 TimeSpan | Interval agregace pro proces vytváří události |
| aggregationEnabledConnectionCreate | boolean| Povolit/zakázat agregaci události pro události vytvoření připojení |
| aggregationIntervalConnectionCreate | Řetězec ISO8601 TimeSpan | Interval agregace pro připojení vytváří události. |
| aggregationEnabledProcessTerminate | boolean | Povolit/zakázat agregaci události pro události ukončení procesu | Jen ve Windows|
| aggregationIntervalProcessTerminate | Řetězec ISO8601 TimeSpan | Interval agregace pro proces ukončení událostí | Jen ve Windows|
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

V tomto článku jste se dozvěděli o programu Defender pro agregaci zabezpečení služby IoT a dostupných možnostech konfigurace událostí.

Pokud chcete pokračovat v seznámení s Defenderem pro nasazení IoT, postupujte podle následujících článků:

- Porozumění [metodám ověřování agenta zabezpečení](concept-security-agent-authentication-methods.md)
- Výběr a nasazení [agenta zabezpečení](how-to-deploy-agent.md)
- Kontrola [požadavků na systém](quickstart-system-prerequisites.md) IoT v Defenderu
- Naučte se, jak [ve IoT Hub povolit Defender pro službu IoT](quickstart-onboard-iot-hub.md) .
- Další informace o službě najdete v tématu [Nejčastější dotazy k programu Defender pro IoT](resources-frequently-asked-questions.md) .
