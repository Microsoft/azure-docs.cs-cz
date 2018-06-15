---
title: Přehled akcelerátorů řešení Azure IoT | Microsoft Docs
description: Popis akcelerátorů řešení Azure IoT a jejich architektury spolu s odkazy na další prostředky
services: iot-suite
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
ms.openlocfilehash: 2dc286dd228b1990e0307476d3623ffc91489f98
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33779918"
---
# <a name="what-are-the-iot-solution-accelerators"></a>Co jsou akcelerátory řešení Azure IoT?

_Akcelerátory řešení_ Azure IoT jsou sady řešení s následujícími charakteristikami:

* Nasadí se v řádu několika minut.
* Pomohou vám rychle začít.
* Můžete je přizpůsobit podle svých specifických požadavků.

Všechny akcelerátory řešení jsou navrženy podle stejných principů a se stejným cílem.

Následující video představuje přehled akcelerátorů řešení vzdáleného monitorování:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Meet-the-new-Remote-Monitoring-accelerator-for-Azure-IoT/Player]

## <a name="solution-accelerators-overview"></a>Přehled akcelerátorů řešení

Akcelerátor řešení je opensourcová implementace běžných vzorů řešení IoT, která můžete nasadit do Azure pomocí svého předplatného. Každý akcelerátor řešení kombinuje vlastní kód a služby Azure a implementuje konkrétní scénář nebo scénáře IoT. Libovolné ze scénářů můžete přizpůsobit podle svých specifických požadavků. Mezi tyto scénáře patří:

* Vizualizace dat na rozšířeném řídicím panelu pro zajištění podrobného přehledu a zjištění stavu řešení
* Konfigurace pravidel a alarmů přes živou telemetrii zařízení IoT
* Plánování úloh správy zařízení, jako jsou aktualizace softwaru a konfigurace
* Zřizování vlastních fyzických nebo simulovaných zařízení
* Řešení potíží a jejich zmírnění v rámci skupin zařízení IoT

Každý akcelerátor řešení je kompletní komplexní implementace, která využívá simulovaná nebo fyzická zařízení ke generování telemetrie. Akcelerátory řešení můžete použít jako akcelerátory řešení pro:

* Zajištění výchozího bodu pro svoje vlastní řešení IoT
* Získání informací o běžných vzorech v designu a vývoji řešení IoT

Tři akcelerátory řešení, které jsou dnes k dispozici:

* [Vzdálené monitorování](iot-suite-remote-monitoring-explore.md)
* [Prediktivní údržba](iot-suite-predictive-overview.md)
* [Připojená továrna](iot-suite-connected-factory-overview.md)

Následující tabulka ukazuje, jak se řešení mapují na určité funkce IoT.

| Řešení | Přijímání dat | Identita zařízení | Správa zařízení | Zpracování hraničních zařízení | Příkazy a ovládání | Pravidla a akce | Prediktivní analýza |
| ------------------------------------------------------------ | -- | -- | -- | -- | -- | -- | -- |
| [Vzdálené monitorování](iot-suite-remote-monitoring-explore.md)  |Ano |Ano |Ano |-   |Ano |Ano |-   |
| [Prediktivní údržba](iot-suite-predictive-overview.md)   |Ano |Ano |-   |-   |Ano |Ano |Ano |
| [Připojená továrna](iot-suite-connected-factory-overview.md) |Ano |- |- |Ano |Ano |Ano |-   |

* *Přijímání dat*: Přijímání škálovaných dat do cloudu.
* *Identita zařízení:* Správa jedinečných identit zařízení a řízení přístupu zařízení k řešení.
* *Správa zařízení:* Správa metadat zařízení a provádění operací, jako například restartování zařízení a upgrady firmwaru.
* *Příkazy a ovládání:* Pokud chcete, aby zařízení provedlo nějakou akci, odešlete do zařízení zprávu z cloudu.
* *Pravidla a akce:* Back-end řešení k reakci na konkrétní data typu zařízení-cloud používá pravidla.
* *Prediktivní analýza:* Back-end řešení analyzuje data typu zařízení-cloud a z nich pak předpovídá, kdy by mělo dojít k určitým událostem. Například může analyzovat telemetrická data motoru letadla a určit, kdy bude zapotřebí provést údržbu.

> [!NOTE]
> Pokud chcete nasadit akcelerátor řešení a získat další informace o tom, jak ho přizpůsobit, navštivte stránku [Akcelerátory řešení Microsoft Azure IoT](https://www.azureiotsuite.com/).

## <a name="azure-services"></a>Služby Azure

Když nasadíte akcelerátor řešení, proces zřizování konfiguruje celou řadu služeb Azure. Následující tabulka obsahuje seznam služeb používaných v akcelerátorech řešeních:

|                      | Vzdálené monitorování  | Prediktivní údržba | Připojená továrna |
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
> Další informace o prostředcích nasazených v akcelerátoru řešení vzdáleného monitorování najdete v tomto [článku](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/blob/master/README.md#basic-vs-standard-deployments) na GitHubu.

* [Azure IoT Hub](../iot-hub/index.yml). Tato služba zajišťuje obousměrnou výměnu zpráv mezi zařízením a cloudem a funguje jako brána ke cloudu a ostatním klíčovým službám akcelerátorů řešení. Umožňuje příjem zpráv z vašich zařízení a odesílání příkazů do zařízení. Tato služba také umožňuje [spravovat vaše zařízení](../iot-hub/iot-hub-device-management-overview.md). Můžete třeba nakonfigurovat, restartovat nebo obnovit tovární nastavení na jednom nebo několika zařízeních připojených k rozbočovači.
* [Azure Event Hubs](../event-hubs/index.md). Tato služba poskytuje ingestování velkého objemu událostí do cloudu. Další informace najdete v tématu věnovaném [porovnání Azure IoT Hub a Azure Event Hubs](../iot-hub/iot-hub-compare-event-hubs.md).
* [Azure Time Series Insights](../time-series-insights/index.yml). Akcelerátory řešení používají tuto službu k analýze a zobrazování telemetrických dat z vašich zařízení.
* [Azure Container Service](../container-service/index.yml). Tato služba v akcelerátorech řešení hostuje a spravuje mikroslužby.
* [Azure Cosmos DB](../cosmos-db/index.yml) a [Azure Storage](../storage/index.yml) pro ukládání dat.
* [Azure Stream Analytics](../stream-analytics/index.yml). Předkonfigurované řešení prediktivní údržby využívá tuto službu ke zpracování příchozích telemetrických dat, vytváření agregací a zjišťování událostí. Toto předkonfigurované řešení dále s využitím analýzy datových proudů zpracovává informační zprávy, které obsahují například metadata nebo odezvy zařízení na příkazy.
* [Azure Web Apps](../app-service/index.yml). Tato služba v předkonfigurovaných řešeních hostuje vlastní kód aplikací.

Informace o tom, jak vypadá architektura typického řešení IoT, najdete v tématu [Microsoft Azure a Internet věcí (IoT)](iot-suite-what-is-azure-iot.md).

## <a name="whats-new-in-solution-accelerators"></a>Co je nového v akcelerátorech řešení?

Společnost Microsoft aktualizuje akcelerátory řešení, aby využívaly novou architekturu založenou na mikroslužbách. Následující tabulka ukazuje aktuální stav akcelerátorů řešení:

| Akcelerátor řešení | Architektura  | Jazyky     |
| ---------------------- | ------------- | ------------- |
| Vzdálené monitorování      | Mikroslužby | Java a .NET |
| Prediktivní údržba | MVC           | .NET          |
| Připojená továrna      | MVC           | .NET          |

Následující části popisují, co je u akcelerátorů řešení založených na mikroslužbách nového:

### <a name="microservices"></a>Mikroslužby

Nová verze akcelerátoru řešení vzdáleného monitorování využívá architekturu mikroslužeb. Tento akcelerátor řešení se skládá z několika mikroslužeb, jako je *správce služby IoT Hub* a *správce služby Storage*. Verze jednotlivých mikroslužeb v Javě i v .NET jsou k dispozici ke stažení, společně se související dokumentací pro vývojáře. Další informace o mikroslužbách najdete v tématu věnovaném [architektuře vzdáleného monitorování](iot-suite-remote-monitoring-sample-walkthrough.md).

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

Seznámili jste se s přehledem akcelerátorů řešení IoT a tady jsou navrhované další kroky pro jednotlivé akcelerátory řešení:

* [Seznámení s řešením vzdáleného monitorování](iot-suite-remote-monitoring-explore.md)
* [Přehled akcelerátorů řešení prediktivní údržby](iot-suite-predictive-overview.md)
* [Začínáme s akcelerátorem řešení připojené továrny](iot-suite-connected-factory-overview.md)

Další informace o architekturách řešení IoT najdete v tématu [Služby Microsoft Azure IoT: Referenční architektura](http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf).
