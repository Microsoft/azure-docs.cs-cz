---
title: Přehled předkonfigurovaných řešení Azure IoT Suite | Dokumentace Microsoftu
description: Popis předkonfigurovaných řešení Azure IoT Suite a jejich architektury spolu s odkazy na další prostředky
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
ms.date: 17/01/2018
ms.author: dobett
ms.openlocfilehash: b7b9f61a8fb46d5d591b317049cfd60b723a7e77
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-azure-iot-suite"></a>Co je Azure IoT Suite?

Azure IoT Suite je sada *předkonfigurovaných řešení* s těmito vlastnostmi:

* Nasadí se v řádu několika minut.
* Pomohou vám rychle začít.
* Můžete je přizpůsobit podle svých specifických požadavků.

Všechna předkonfigurovaná řešení *IoT Suite* jsou navržena podle stejných principů a se stejným cílem.

Následující video představuje přehled předkonfigurovaného řešení vzdáleného monitorování:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Meet-the-new-Remote-Monitoring-accelerator-for-Azure-IoT/Player]

## <a name="preconfigured-solutions-overview"></a>Přehled předkonfigurovaných řešení

Předkonfigurované řešení je opensourcová implementace běžných vzorů řešení IoT, která můžete nasadit do Azure pomocí svého předplatného. Každé předkonfigurované řešení kombinuje vlastní kód a služby Azure a implementuje konkrétní scénář nebo scénáře IoT. Libovolné ze scénářů můžete přizpůsobit podle svých specifických požadavků. Mezi tyto scénáře patří:

* Vizualizace dat na rozšířeném řídicím panelu pro zajištění podrobného přehledu a zjištění stavu řešení
* Konfigurace pravidel a alarmů přes živou telemetrii zařízení IoT
* Plánování úloh správy zařízení, jako jsou aktualizace softwaru a konfigurace
* Zřizování vlastních fyzických nebo simulovaných zařízení
* Řešení potíží a jejich zmírnění v rámci skupin zařízení IoT

Každé předkonfigurované řešení je kompletní komplexní implementace, která využívá simulovaná nebo fyzická zařízení ke generování telemetrie. Předkonfigurovaná řešení můžete použít jako akcelerátory řešení pro:

* Zajištění výchozího bodu pro svoje vlastní řešení IoT
* Získání informací o běžných vzorech v designu a vývoji řešení IoT

Momentálně jsou k dispozici tři předkonfigurovaná řešení:

* [Vzdálené monitorování](iot-suite-remote-monitoring-explore.md)
* [Prediktivní údržba](iot-suite-predictive-overview.md)
* [Propojená továrna](iot-suite-connected-factory-overview.md)

Následující tabulka ukazuje, jak se řešení mapují na určité funkce IoT.

| Řešení | Přijímání dat | Identita zařízení | Správa zařízení | Zpracování hraničních zařízení | Příkazy a ovládání | Pravidla a akce | Prediktivní analýza |
| ------------------------------------------------------------ | -- | -- | -- | -- | -- | -- | -- |
| [Vzdálené monitorování](iot-suite-remote-monitoring-explore.md)  |Ano |Ano |Ano |-   |Ano |Ano |-   |
| [Prediktivní údržba](iot-suite-predictive-overview.md)   |Ano |Ano |-   |-   |Ano |Ano |Ano |
| [Propojená továrna](iot-suite-connected-factory-overview.md) |Ano |- |- |Ano |Ano |Ano |-   |

* *Přijímání dat*: Přijímání škálovaných dat do cloudu.
* *Identita zařízení:* Správa jedinečných identit zařízení a řízení přístupu zařízení k řešení.
* *Správa zařízení:* Správa metadat zařízení a provádění operací, jako například restartování zařízení a upgrady firmwaru.
* *Příkazy a ovládání:* Pokud chcete, aby zařízení provedlo nějakou akci, odešlete do zařízení zprávu z cloudu.
* *Pravidla a akce:* Back-end řešení k reakci na konkrétní data typu zařízení-cloud používá pravidla.
* *Prediktivní analýza:* Back-end řešení analyzuje data typu zařízení-cloud a z nich pak předpovídá, kdy by mělo dojít k určitým událostem. Například může analyzovat telemetrická data motoru letadla a určit, kdy bude zapotřebí provést údržbu.

> [!NOTE]
> Pokud chcete nasadit předkonfigurované řešení a získat další informace o tom, jak je přizpůsobit, navštivte [Microsoft Azure IoT Suite](https://www.azureiotsuite.com/).

## <a name="azure-services"></a>Služby Azure

Když nasadíte předkonfigurované řešení, proces zřizování konfiguruje celou řadu služeb Azure. Následující tabulka obsahuje seznam služeb používaných v předkonfigurovaných řešeních:

|                      | Vzdálené monitorování  | Prediktivní údržba | Propojená továrna |
| -------------------- | ------------------ | ---------------------- | ----------------- |
| IoT Hub              | Ano                | Ano                    | Ano               |
| Event Hubs           |                    | Ano                    |                   |
| Time Series Insights |                    |                        | Ano               |
| Služba kontejneru   | Ano                |                        |                   |
| Stream Analytics     |                    | Ano                    |                   |
| Web Apps             | Ano                | Ano                    | Ano               |
| Databáze Cosmos            | Ano                | Ano                    |                    |
| Azure Storage         |                    | Ano                    | Ano               |

> [!NOTE]
> Další informace o prostředcích nasazených v předkonfigurovaném řešení vzdáleného monitorování najdete v tomto [článku](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/blob/master/README.md#basic-vs-standard-deployments) na GitHubu.

* [Azure IoT Hub](../iot-hub/index.md). Tato služba zajišťuje obousměrnou výměnu zpráv mezi zařízením a cloudem a funguje jako brána ke cloudu a ostatním klíčovým službám IoT Suite. Umožňuje příjem zpráv z vašich zařízení a odesílání příkazů do zařízení. Tato služba také umožňuje [spravovat vaše zařízení](../iot-hub/iot-hub-device-management-overview.md). Můžete třeba nakonfigurovat, restartovat nebo obnovit tovární nastavení na jednom nebo několika zařízeních připojených k rozbočovači.
* [Azure Event Hubs](../event-hubs/index.md). Tato služba poskytuje ingestování velkého objemu událostí do cloudu. Další informace najdete v tématu věnovaném [porovnání Azure IoT Hub a Azure Event Hubs](../iot-hub/iot-hub-compare-event-hubs.md).
* [Azure Time Series Insights](../time-series-insights/index.yml). Předkonfigurovaná řešení používají tuto službu k analýze a zobrazování telemetrických dat z vašich zařízení.
* [Azure Container Service](../container-service/index.yml). Tato služba v předkonfigurovaných řešeních hostuje a spravuje mikroslužby.
* [Azure Cosmos DB](../cosmos-db/index.yml) a [Azure Storage](../storage/index.yml) pro ukládání dat.
* [Azure Stream Analytics](../stream-analytics/index.yml). Předkonfigurované řešení prediktivní údržby využívá tuto službu ke zpracování příchozích telemetrických dat, vytváření agregací a zjišťování událostí. Toto předkonfigurované řešení dále s využitím analýzy datových proudů zpracovává informační zprávy, které obsahují například metadata nebo odezvy zařízení na příkazy.
* [Azure Web Apps](../app-service/index.yml). Tato služba v předkonfigurovaných řešeních hostuje vlastní kód aplikací.

Informace o tom, jak vypadá architektura typického řešení IoT, najdete v tématu [Microsoft Azure a Internet věcí (IoT)](iot-suite-what-is-azure-iot.md).

## <a name="whats-new-in-preconfigured-solutions"></a>Co je nového u předkonfigurovaných řešení?

Společnost Microsoft aktualizuje předkonfigurovaná řešení, aby využívala novou architekturu založenou na mikroslužbách. Následující tabulka ukazuje aktuální stav předkonfigurovaný řešení:

| Předkonfigurované řešení | Architektura  | Jazyky     |
| ---------------------- | ------------- | ------------- |
| Vzdálené monitorování      | Mikroslužby | Java a .NET |
| Prediktivní údržba | MVC           | .NET          |
| Propojená továrna      | MVC           | .NET          |

Následující části popisují, co je u předkonfigurovaných řešení založených na mikroslužbách nového:

### <a name="microservices"></a>Mikroslužby

Nové verze předkonfigurovaného řešení vzdáleného monitorování využívá architekturu mikroslužeb. Toto předkonfigurované řešení se skládá z několika mikroslužeb, jako je *správce služby IoT Hub* a *správce služby Storage*. Verze jednotlivých mikroslužeb v Javě i v .NET jsou k dispozici ke stažení, společně se související dokumentací pro vývojáře. Další informace o mikroslužbách najdete v tématu věnovaném [architektuře vzdáleného monitorování](iot-suite-remote-monitoring-sample-walkthrough.md).

Tato architektura mikroslužeb představuje ověřený vzor pro cloudová řešení, který:

* Je škálovatelný.
* Umožňuje rozšíření.
* Je snadno pochopitelný.
* Umožňuje výměnu jednotlivých služeb za alternativní služby.

> [!TIP]
> Další informace o architekturách mikroslužeb najdete v tématech věnovaných [architektuře aplikací .NET](https://www.microsoft.com/net/learn/architecture) a [mikroslužbám jako revoluci v aplikacích, kterou umožnil cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

Když nasadíte novou verzi vzdáleného monitorování, musíte vybrat jednu z následujících možností nasazení:

* **Basic:** Verze s nižšími náklady pro ukázku nebo otestování nasazení. Všechny mikroslužby se nasazují do jednoho virtuálního počítače Azure.
* **Standard:** Nasazení rozšířené infrastruktury pro vývoj produkčního nasazení. Azure Container Service nasazuje mikroslužby do několika virtuálních počítačů Azure. Kubernetes orchestruje kontejnery Dockeru, které jsou hostiteli jednotlivých mikroslužeb.

### <a name="language-choices-java-and-net"></a>Volba jazyka: Java a .NET

Implementace jednotlivých mikroslužeb jsou dostupné v Javě i v .NET. Stejně jako kód v .NET je zdrojový kód v Javě opensourcový a umožňuje přizpůsobení vašim konkrétním požadavkům:

* [Úložiště GitHub pro vzdálené monitorování ve verzi .NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Úložiště GitHub pro vzdálené monitorování ve verzi Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

Pokud byste chtěli implementace v jiných jazycích, zadejte svůj požadavek na [Azure IoT User Voice](https://feedback.azure.com/forums/321918-azure-iot).

### <a name="react-user-interface-framework"></a>Architektura uživatelského rozhraní React

Uživatelské rozhraní je sestavené s využitím javascriptové knihovny [React](https://facebook.github.io/react/). Zdrojový kód je opensourcový a můžete si ho stáhnout a upravit.

## <a name="next-steps"></a>Další kroky

Seznámili jste se s přehledem předkonfigurovaných řešení IoT Suite a tady jsou navrhované další kroky pro jednotlivá předkonfigurovaná řešení:

* [Prozkoumání modelu nasazení Resource Manager pro řešení vzdáleného monitorování Azure IoT Suite](iot-suite-remote-monitoring-explore.md)
* [Přehled předkonfigurovaných řešení prediktivní údržby](iot-suite-predictive-overview.md)
* [Začínáme s předkonfigurovaným řešením propojené továrny](iot-suite-connected-factory-overview.md)

Další informace o architekturách řešení IoT najdete v tématu [Služby Microsoft Azure IoT: Referenční architektura](http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf).
