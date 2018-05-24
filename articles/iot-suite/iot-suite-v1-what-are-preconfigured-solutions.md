---
title: Začínáme s předkonfigurovanými řešeními Azure IoT | Dokumentace Microsoftu
description: Popis předkonfigurovaných řešení Azure IoT a jejich architektury spolu s odkazy na další prostředky
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 59009f37-9ba0-4e17-a189-7ea354a858a2
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: d860c768a73737e6c8c52a8652d6b43434a3a07d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361984"
---
# <a name="what-are-the-azure-iot-suite-preconfigured-solutions"></a>Co jsou předkonfigurovaná řešení Azure IoT Suite?

Předkonfigurovaná řešení Azure IoT Suite jsou implementací běžných vzorů řešení IoT, která můžete nasadit v Azure pomocí svého předplatného. Předkonfigurovaná řešení můžete použít následovně:

* Jako výchozí bod pro své vlastní řešení IoT.
* K získání informací o běžných vzorech v designu a vývoji řešení IoT.

Každé předkonfigurované řešení je kompletní a komplexní implementace, která využívá telemetrická simulovaná zařízení ke generování telemetrie.

Můžete si stáhnout kompletní zdrojový kód a upravit a rozšířit řešení tak, aby odpovídalo vašim specifickým požadavkům na IoT.

> [!NOTE]
> Pokud chcete nasadit některé z předkonfigurovaných řešení, navštivte web [Microsoft Azure IoT Suite][lnk-azureiotsuite]. Další informace o nasazení a spuštění jednoho z řešení najdete v článku [Začínáme s předkonfigurovanými řešeními IoT][lnk-getstarted-preconfigured].

Následující tabulka ukazuje, jak se řešení mapují na určité funkce IoT.

| Řešení | Přijímání dat | Identita zařízení | Správa zařízení | Příkazy a ovládání | Pravidla a akce | Prediktivní analýza |
| --- | --- | --- | --- | --- | --- | --- |
| [Vzdálené monitorování][lnk-getstarted-preconfigured] |Ano |Ano |Ano |Ano |Ano |- |
| [Prediktivní údržba][lnk-predictive-maintenance] |Ano |Ano |- |Ano |Ano |Ano |
| [Propojená továrna][lnk-getstarted-factory] |Ano |Ano |Ano |Ano |Ano |- |

* *Přijímání dat*: Přijímání škálovaných dat do cloudu.
* *Identita zařízení:* Správa jedinečných identit zařízení a řízení přístupu zařízení k řešení.
* *Správa zařízení:* Správa metadat zařízení a provádění operací, jako například restartování zařízení a upgrady firmwaru.
* *Příkazy a ovládání:* Pokud chcete, aby zařízení provedlo nějakou akci, odešlete do zařízení zprávu z cloudu.
* *Pravidla a akce:* Back-end řešení k reakci na konkrétní data typu zařízení-cloud používá pravidla.
* *Prediktivní analýza:* Back-end řešení analyzuje data typu zařízení-cloud a z nich pak předpovídá, kdy by mělo dojít k určitým událostem. Například může analyzovat telemetrická data motoru letadla a určit, kdy bude zapotřebí provést údržbu.

## <a name="remote-monitoring-preconfigured-solution-overview"></a>Přehled předkonfigurovaného řešení vzdáleného monitorování

V tomto článku se budeme zabývat předkonfigurovaným řešením vzdáleného monitorování, protože ilustruje mnoho běžných prvků návrhu, které sdílejí i jiná řešení.

Následující diagram ilustruje klíčové prvky řešení vzdáleného monitorování. Následující části poskytují další informace o těchto prvcích.

![Architektura předkonfigurovaného řešení vzdáleného monitorování][img-remote-monitoring-arch]

## <a name="devices"></a>Zařízení

Když nasadíte předkonfigurované řešení vzdáleného monitorování, bude obsahovat čtyři předem zřízená simulovaná zařízení, simulující chladicí zařízení. Tato simulovaná zařízení mají předdefinovaný model teploty a vlhkosti, který vysílá telemetrická data. Tato simulovaná zařízení jsou zahrnuta, protože:

- Znázorňují tok dat řešením od začátku do konce.
- Poskytují praktický zdroj telemetrie.
- Můžou být cílem pro metody nebo příkazy, pokud jste vývojář back-endu a používáte řešení jako výchozí bod pro vlastní implementaci.

Simulovaná zařízení v tomto řešení můžou reagovat na následující komunikace typu cloud-zařízení:

- *Metody ([přímé metody][lnk-direct-methods]):* Způsob obousměrné komunikace, u kterého se očekává, že připojené zařízení bude reagovat okamžitě.
- *Příkazy (zprávy typu cloud-zařízení):* Způsob jednosměrné komunikace, při které zařízení načítá příkazy z trvalé fronty.

Porovnání těchto rozdílných přístupů najdete v [doprovodných materiálech ke komunikaci typu cloud-zařízení][lnk-c2d-guidance].

Když se zařízení poprvé připojí ke službě IoT Hub v rámci předkonfigurovaného řešení, odešle do služby zprávu s informacemi o zařízení. V této zprávě bude uveden seznam metod, na které zařízení může reagovat. V předkonfigurovaném řešení vzdáleného monitorování podporují simulovaná zařízení tyto metody:

* *Initiate Firmware Update* (Zahájit aktualizaci firmwaru): Tato metoda v zařízení zahájí asynchronní úlohu, která provede aktualizaci firmwaru. Asynchronní úloha pomocí ohlášených vlastností doručuje aktualizace stavu do řídicího panelu služby.
* *Reboot* (Restartovat): Tato metoda způsobí, že se simulované zařízení restartuje.
* *FactoryReset* (Obnovení do výrobního nastavení): Tato metoda v simulovaném zařízení aktivuje obnovení do výrobního nastavení.

Když se zařízení poprvé připojí ke službě IoT Hub v rámci předkonfigurovaného řešení, odešle do služby zprávu s informacemi o zařízení. V této zprávě bude uveden seznam příkazů, na které zařízení může reagovat. V předkonfigurovaném řešení vzdáleného monitorování podporují simulovaná zařízení tyto příkazy:

* *Ping Device* (Odeslat do zařízení příkaz ping): Zařízení na tento příkaz odpoví potvrzením. Tímto příkazem snadno zjistíte, zda je zařízení stále aktivní a naslouchá.
* *Start Telemetry* (Spustit telemetrii): Dá zařízení pokyn, aby začalo odesílat telemetrii.
* *Stop Telemetry* (Zastavit telemetrii): Dá zařízení pokyn, aby přestalo odesílat telemetrii.
* *Change Set Point Temperature* (Změnit nastavenou teplotu): Ovládá hodnoty simulované teplotní telemetrie, které zařízení odesílá. Tímto příkazem snadno otestujete logiku back-endu.
* *Diagnostic Telemetry* (Diagnostická telemetrie): Ovládá, zda má zařízení odesílat externí teplotu jako telemetrický údaj.
* *Change Device State* (Změnit stav zařízení): Nastaví vlastnosti metadat o stavu zařízení, která zařízení odesílá. Tímto příkazem snadno otestujete logiku back-endu.

Do řešení můžete přidat další simulovaná zařízení, která vysílají stejnou telemetrii a reagují na stejné metody a příkazy.

Řešení kromě reagování na příkazy a metody využívá i [dvojčata zařízení][lnk-device-twin]. Zařízení pomocí dvojčat zařízení hlásí hodnoty vlastností do back-endu řešení. Řídicí panel řešení používá dvojčata zařízení k nastavení nových hodnot požadovaných vlastností v zařízeních. Například během procesu aktualizace firmwaru simulované zařízení pomocí ohlášených vlastností hlásí stav aktualizace.

## <a name="iot-hub"></a>IoT Hub

V tomto předkonfigurovaném řešení odpovídá instance služby IoT Hub *cloudové bráně* v typické [architektuře řešení IoT][lnk-what-is-azure-iot].

Centrum IoT získává telemetrická data ze zařízení v jednom koncovém bodě. IoT Hub také udržuje pro každé zařízení specifické koncové body, odkud zařízení získávají příkazy, které do nich byly zaslány.

Centrum IoT zpřístupňuje získaná telemetrická data přes koncový bod čtení telemetrických dat na straně služby.

Možnost správy zařízení služby IoT Hub umožňuje spravovat vlastnosti zařízení z portálu řešení a plánovat úlohy provádějící operace, jako třeba:

- Restartování zařízení
- Změna stavu zařízení
- Aktualizace firmwaru

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

Předkonfigurované řešení používá tři úlohy [Azure Stream Analytics][lnk-asa] (ASA), jejichž pomocí filtruje datový proud telemetrických dat ze zařízení:

* *Úloha DeviceInfo* (Informace o zařízení) – Odesílá výstupní data do centra událostí, které do registru zařízení pro řešení směruje zprávy specifické pro registraci zařízení. Tento registr zařízení je databáze Azure Cosmos DB. Tyto zprávy se odešlou ve chvíli, kdy se zařízení poprvé připojí, nebo v reakci na příkaz **Change Device State** (Změnit stav zařízení).
* *Úloha Telemetry (Telemetrie)* – Odesílá veškerá nezpracovaná telemetrická data do služby Azure Blob Storage, uloží málo používaná data a vypočítá ze získaných telemetrických dat agregované hodnoty, které se zobrazují na řídicím panelu řešení.
* *Úloha Rules (Pravidla)* – Filtruje datový proud telemetrie podle hodnot, které přesahují stanovené mezní hodnoty pravidel. Výstupní data odesílá do centra událostí. Jakmile se spustí pravidlo, na řídicím panelu portálu řešení se tato událost objeví jako nový řádek v tabulce historie alarmů. Tato pravidla můžou také aktivovat akce v závislosti na nastaveních definovaných v zobrazeních **Pravidla** a **Akce** na portálu řešení.

V tomto předkonfigurovaném řešení jsou úlohy ASA součástí **back-endu řešení IoT** v typické [architektuře řešení IoT][lnk-what-is-azure-iot].

## <a name="event-processor"></a>Procesor událostí

V tomto předkonfigurovaném řešení je procesor událostí součástí **back-endu řešení IoT** v typické [architektuře řešení IoT][lnk-what-is-azure-iot].

Úlohy ASA **DeviceInfo** a **Rules** odesílají výstup do center událostí, odkud se přeposílají do dalších služeb back-endu. Toto řešení využívá instanci třídy [EventPocessorHost][lnk-event-processor] spuštěnou v rámci [webové úlohy][lnk-web-job] ke čtení zpráv z těchto center událostí. Instance **EventProcessorHost** využívá:
- Data úlohy **DeviceInfo** k aktualizaci dat zařízení v databázi Cosmos DB.
- Data úlohy **Rules** k vyvolání aplikace logiky a aktualizaci zobrazování upozornění na portálu řešení.

## <a name="device-identity-registry-device-twin-and-cosmos-db"></a>Registr identit zařízení, dvojče zařízení a služba Cosmos DB

Každá služba IoT Hub obsahuje [registr identit zařízení][lnk-identity-registry], který ukládá klíče zařízení. IoT Hub používá tuto informaci k ověřování zařízení – pokud se má zařízení připojit k centru, musí být registrováno a mít platný klíč.

[Dvojče zařízení][lnk-device-twin] je dokument JSON spravovaný službou IoT Hub. Dvojče každého zařízení obsahuje:

- Ohlášené vlastnosti, které zařízení odeslalo do služby IoT Hub. Tyto vlastnosti můžete zobrazit na portálu řešení.
- Požadované vlastnosti, které chcete odeslat do zařízení. Tyto vlastnosti můžete nastavit na portálu řešení.
- Značky, které existují pouze ve dvojčeti zařízení a nikoli v samotném zařízení. Pomocí těchto značek můžete na portálu řešení filtrovat seznamy zařízení.

Toto řešení využívá dvojčata zařízení ke správě metadat zařízení. Řešení také využívá databázi Cosmos DB, ve které ukládá další data zařízení specifická pro řešení, jako jsou příkazy podporované jednotlivými zařízeními nebo historie příkazů.

Řešení také musí stále udržovat synchronizované informace v registru identit zařízení s obsahem databáze Cosmos DB. Příkaz **EventProcessorHost** používá k řízení synchronizace data z úlohy analýzy datového proudu **DeviceInfo**.

## <a name="solution-portal"></a>Portál řešení

![portál řešení][img-dashboard]

Portál řešení je uživatelské rozhraní, které je založené na webu a nasazené v cloudu jako součást předkonfigurovaného řešení. Umožňuje:

* Zobrazovat historii telemetrie a alarmů na řídicím panelu.
* Zřizovat nová zařízení.
* Spravovat a sledovat zařízení.
* Odesílat příkazy na konkrétní zařízení.
* Vyvolávat metody v konkrétních zařízeních.
* Upravovat pravidla a akce.
* Plánovat spouštění úloh v jednom nebo více zařízeních.

V tomto předkonfigurovaném řešení je portál řešení součástí **back-endu řešení IoT** a součástí **připojení pro zpracování a obchodní logiku** v typické [architektuře řešení IoT][lnk-what-is-azure-iot].

## <a name="next-steps"></a>Další kroky

Další informace o architekturách řešení IoT najdete v tématu [Služby Microsoft Azure IoT: Referenční architektura][lnk-refarch].

Nyní víte, co je to předem nakonfigurované řešení a můžete začít nasazením předkonfigurovaného řešení *vzdáleného monitorování*: [Začínáme s předkonfigurovanými řešeními][lnk-getstarted-preconfigured].

[img-remote-monitoring-arch]: ./media/iot-suite-v1-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-v1-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-v1-getstarted-preconfigured-solutions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-getstarted-factory]:../iot-accelerators/iot-accelerators-connected-factory-overview.md
