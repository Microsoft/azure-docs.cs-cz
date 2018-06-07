---
title: Přehled akcelerátorů řešení Azure IoT | Microsoft Docs
description: Popis akcelerátorů řešení Azure IoT a jejich architektury spolu s odkazy na další prostředky
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: dobett
ms.openlocfilehash: b961217317cea65b05cd58d66e64f0e4f012131e
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808549"
---
# <a name="what-are-the-iot-solution-accelerators"></a>Co jsou akcelerátory řešení Azure IoT?

_Akcelerátory řešení_ Azure IoT jsou sady řešení s následujícími charakteristikami:

* Nasadí se v řádu několika minut.
* Pomohou vám rychle začít.
* Můžete je přizpůsobit podle svých specifických požadavků.

Všechny akcelerátory řešení jsou navrženy podle stejných principů a se stejným cílem.

Následující video obsahuje přehled nástroje akcelerátoru řešení vzdáleného monitorování:

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

* [Vzdálené monitorování](iot-accelerators-remote-monitoring-explore.md)
* [Prediktivní údržba](iot-accelerators-predictive-overview.md)
* [Připojená továrna](iot-accelerators-connected-factory-overview.md)

Následující tabulka ukazuje, jak se řešení mapují na určité funkce IoT.

| Řešení | Přijímání dat | Identita zařízení | Správa zařízení | Zpracování hraničních zařízení | Příkazy a ovládání | Pravidla a akce | Prediktivní analýza |
| ------------------------------------------------------------ | -- | -- | -- | -- | -- | -- | -- |
| [Vzdálené monitorování](iot-accelerators-remote-monitoring-explore.md)  |Ano |Ano |Ano |-   |Ano |Ano |-   |
| [Prediktivní údržba](iot-accelerators-predictive-overview.md)   |Ano |Ano |-   |-   |Ano |Ano |Ano |
| [Připojená továrna](iot-accelerators-connected-factory-overview.md) |Ano |- |- |Ano |Ano |Ano |-   |

* *Přijímání dat*: Přijímání škálovaných dat do cloudu.
* *Identita zařízení:* Správa jedinečných identit zařízení a řízení přístupu zařízení k řešení.
* *Správa zařízení:* Správa metadat zařízení a provádění operací, jako například restartování zařízení a upgrady firmwaru.
* *Příkazy a ovládání:* Pokud chcete, aby zařízení provedlo nějakou akci, odešlete do zařízení zprávu z cloudu.
* *Pravidla a akce:* Back-end řešení k reakci na konkrétní data typu zařízení-cloud používá pravidla.
* *Prediktivní analýza:* Back-end řešení analyzuje data typu zařízení-cloud a z nich pak předpovídá, kdy by mělo dojít k určitým událostem. Například může analyzovat telemetrická data motoru letadla a určit, kdy bude zapotřebí provést údržbu.

> [!NOTE]
> Pokud chcete nasadit akcelerátor řešení a získat další informace o tom, jak ho přizpůsobit, navštivte stránku [Akcelerátory řešení Microsoft Azure IoT](https://www.azureiotsolutions.com/).

## <a name="azure-services"></a>Služby Azure

Když nasadíte akcelerátor řešení, proces zřizování konfiguruje celou řadu služeb Azure. Následující tabulka obsahuje seznam služeb používaných v akcelerátorech řešeních:

|                      | Vzdálené monitorování  | Prediktivní údržba | Připojená továrna |
| -------------------- | ------------------ | ---------------------- | ----------------- |
| IoT Hub              | Ano                | Ano                    | Ano               |
| Event Hubs           |                    | Ano                    |                   |
| Time Series Insights |                    |                        | Ano               |
| Služba kontejneru   | Ano                |                        |                   |
| Stream Analytics     | Ano                | Ano                    |                   |
| Web Apps             | Ano                | Ano                    | Ano               |
| Databáze Cosmos            | Ano                | Ano                    |                    |
| Azure Storage         |                    | Ano                    | Ano               |

> [!NOTE]
> Další informace o prostředky nasazené v akcelerátoru řešení vzdáleného monitorování najdete v tématu [vzdálené monitorování architektury volby](iot-accelerators-remote-monitoring-architectural-choices.md).

* [Azure IoT Hub](../iot-hub/index.yml). Tato služba zajišťuje obousměrnou výměnu zpráv mezi zařízením a cloudem a funguje jako brána ke cloudu a ostatním klíčovým službám akcelerátorů řešení. Umožňuje příjem zpráv z vašich zařízení a odesílání příkazů do zařízení. Tato služba také umožňuje [spravovat vaše zařízení](../iot-hub/iot-hub-device-management-overview.md). Můžete třeba nakonfigurovat, restartovat nebo obnovit tovární nastavení na jednom nebo několika zařízeních připojených k rozbočovači.
* [Azure Event Hubs](../active-directory-domain-services/index.yml). Tato služba poskytuje ingestování velkého objemu událostí do cloudu. Další informace najdete v tématu věnovaném [porovnání Azure IoT Hub a Azure Event Hubs](../iot-hub/iot-hub-compare-event-hubs.md).
* [Azure Time Series Insights](../time-series-insights/index.yml). Akcelerátory řešení používají tuto službu k analýze a zobrazování telemetrických dat z vašich zařízení.
* [Azure Container Service](../container-service/index.yml). Tato služba v akcelerátorech řešení hostuje a spravuje mikroslužby.
* [Azure Cosmos DB](../cosmos-db/index.yml) a [Azure Storage](../storage/index.yml) pro ukládání dat.
* [Azure Stream Analytics](../stream-analytics/index.yml). Akcelerátoru řešení prediktivní údržby tato služba používá pro zpracování příchozích telemetrických dat, vytváření agregací a zjišťování událostí. Toto předkonfigurované řešení dále s využitím analýzy datových proudů zpracovává informační zprávy, které obsahují například metadata nebo odezvy zařízení na příkazy.
* [Azure Web Apps](../app-service/index.yml). Tato služba v předkonfigurovaných řešeních hostuje vlastní kód aplikací.

Informace o tom, jak vypadá architektura typického řešení IoT, najdete v tématu [Microsoft Azure a Internet věcí (IoT)](iot-accelerators-what-is-azure-iot.md).

## <a name="whats-new-in-solution-accelerators"></a>Co je nového v akcelerátorech řešení?

Společnost Microsoft aktualizuje akcelerátory řešení, aby využívaly novou architekturu založenou na mikroslužbách. Následující tabulka ukazuje aktuální stav akcelerátorů řešení:

| Akcelerátor řešení | Architektura  | Jazyky     |
| ---------------------- | ------------- | ------------- |
| Vzdálené monitorování      | Mikroslužby | Java a .NET |
| Prediktivní údržba | MVC           | .NET          |
| Připojená továrna      | MVC           | .NET          |

Následující části popisují, co je u akcelerátorů řešení založených na mikroslužbách nového:

### <a name="microservices"></a>Mikroslužby

Nové verze akcelerátoru řešení vzdáleného monitorování používá architekturu mikroslužeb. Tento akcelerátor řešení se skládá z několika mikroslužeb, jako je *správce služby IoT Hub* a *správce služby Storage*. Verze jednotlivých mikroslužeb v Javě i v .NET jsou k dispozici ke stažení, společně se související dokumentací pro vývojáře. Další informace o mikroslužbách najdete v tématu věnovaném [architektuře vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md).

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

## <a name="next-steps"></a>Další postup

Seznámili jste se s přehledem akcelerátorů řešení IoT a tady jsou navrhované další kroky pro jednotlivé akcelerátory řešení:

* [Seznámení s řešením vzdáleného monitorování](iot-accelerators-remote-monitoring-explore.md)
* [Přehled akcelerátorů řešení prediktivní údržby](iot-accelerators-predictive-overview.md)
* [Začínáme s akcelerátorem řešení připojené továrny](iot-accelerators-connected-factory-overview.md)

Další informace o architekturách řešení IoT najdete v tématu [Služby Microsoft Azure IoT: Referenční architektura](https://aka.ms/iotrefarchitecture).
