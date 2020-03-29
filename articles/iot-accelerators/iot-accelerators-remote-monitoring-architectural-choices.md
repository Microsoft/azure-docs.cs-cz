---
title: Možnosti architektury řešení vzdáleného monitorování – Azure | Dokumenty společnosti Microsoft
description: Tento článek popisuje architektonické a technické volby provedené v programu Vzdálené monitorování
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 1bd08596a30db7322a72b4269fddfe0b9df19119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447165"
---
# <a name="remote-monitoring-architectural-choices"></a>Volby architektury vzdáleného monitorování

Akcelerátor řešení vzdáleného monitorování Azure IoT je open source, mit licencovaný akcelerátor řešení. Abychom vám pomohli urychlit proces vývoje IoT, zobrazuje běžné scénáře IoT, jako jsou:

- Připojení zařízení
- Správa zařízení
- Zpracování streamů

Řešení vzdáleného monitorování se řídí doporučenou [referenční architekturou Azure IoT](https://aka.ms/iotrefarchitecture).

Tento článek popisuje klíčové architektonické a technické volby provedené v každém subsystému vzdáleného monitorování. Technické volby, které společnost Microsoft provedla v řešení vzdáleného monitorování, však nejsou jediným způsobem, jak implementovat řešení IoT vzdáleného monitorování. Technickou implementaci byste měli považovat za základní směr pro vytváření úspěšné aplikace a měli byste ji upravit takto:

- Přizpůsobí se dostupným dovednostem a zkušenostem ve vaší organizaci.
- Splňte své vertikální potřeby aplikací.

## <a name="architectural-choices"></a>Architektonické volby

Architektura, kterou Microsoft doporučuje pro aplikaci IoT, je založená na cloudu, mikroslužeb a bez serveru. Měli byste vytvořit různé subsystémy aplikace IoT jako samostatné služby, které můžete nasadit a škálovat nezávisle. Tyto atributy umožňují větší rozsah, větší flexibilitu při aktualizaci jednotlivých subsystémů a poskytují flexibilitu při výběru vhodné technologie pro každý subsystém.

Můžete implementovat mikroslužeb pomocí více než jednu technologii. Můžete například zvolit některou z následujících možností implementace mikroslužby:

- Použijte kontejnerovou technologii, jako je Docker s technologií bez serveru, jako jsou funkce Azure.
- Hostujte své mikroslužby ve službách PaaS, jako jsou služby Azure App Services.

## <a name="technology-choices"></a>Technologické volby

Tato část podrobně popisuje volby technologií provedené v řešení vzdáleného monitorování pro každý z hlavních subsystémů.

![Základní diagram](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>Cloudová brána

Azure IoT Hub se používá jako cloudová brána řešení vzdáleného monitorování. [IoT Hub](https://azure.microsoft.com/services/iot-hub/) nabízí bezpečnou obousměrnou komunikaci se zařízeními.

Pro připojení zařízení IoT můžete použít:

- Sady [SDK zařízení IoT Hub](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) k implementaci nativní klientské aplikace pro vaše zařízení. Sady SDK nabízejí obálky kolem rozhraní REST ROZHRANÍ API služby IoT Hub a zpracovávají scénáře, jako jsou opakování.
- Integrace s Azure IoT Edge pro nasazení a správu vlastních modulů spuštěných v kontejnerech na vašich zařízeních.
- Integrace s automatickou správou zařízení v ioT hubu pro hromadnou hromadnou správu připojených zařízení.

### <a name="stream-processing"></a>Zpracování streamů

Pro zpracování datového proudu používá řešení vzdáleného monitorování Azure Stream Analytics pro komplexní zpracování pravidel. Pokud chcete použít jednodušší pravidla, je vlastní mikroslužbu s podporou pro jednoduché zpracování pravidel, i když toto nastavení není součástí out-of-the-box nasazení. Referenční architektura doporučuje Funkce Azure pro jednoduché zpracování pravidel a Azure Stream Analytics pro komplexní zpracování pravidel.

### <a name="storage"></a>Úložiště

Pro úložiště akcelerátor řešení vzdáleného monitorování používá Azure Time Series Insights i Azure Cosmos DB. Azure Time Series Insights ukládá zprávy přicházející přes IoT Hub z připojených zařízení. Akcelerátor řešení používá Azure Cosmos DB pro všechna ostatní úložiště, jako je například úložiště, definice pravidel, výstrahy a nastavení konfigurace.

Azure Cosmos DB je doporučené řešení pro obecné úložiště teplého úložiště pro aplikace IoT. Řešení, jako je Azure Time Series Insights a Azure Data Lake jsou vhodná pro mnoho případů použití. S Azure Time Series Insights můžete získat hlubší přehled o datech senzorů časových řad tím, že zjišní trendy a anomálie. Tato funkce umožňuje provádět analýzy hlavních příčin a vyhnout se nákladným prostojům.

> [!NOTE]
> Time Series Insights není momentálně dostupná v cloudu Azure China. Nová nasazení akcelerátoru řešení vzdáleného monitorování v cloudu Azure China používají Cosmos DB pro všechna úložiště.

### <a name="business-integration"></a>Obchodní integrace

Podniková integrace do řešení vzdáleného monitorování je omezena na generování výstrah, které jsou umístěny v teplém úložišti. Připojte řešení s Azure Logic Apps a implementujte hlubší scénáře obchodní integrace.

### <a name="user-interface"></a>Uživatelské rozhraní

Webové uživatelské uzdy je vytvořeno s JavaScriptreact. React nabízí běžně používaný oborový rámec uživatelského rozhraní a je podobný jiným oblíbeným rámcům, jako je Angular.

### <a name="runtime-and-orchestration"></a>Doba běhu a orchestrace

Řešení vzdáleného monitorování používá kontejnery Dockeru ke spuštění subsystémů s Kubernetes jako orchestratorem pro horizontální škálování. Tato architektura umožňuje jednotlivé definice škálování pro každý subsystém. Tato architektura však vynaloží náklady na devOps, aby virtuální počítače a kontejnery byly aktuální a zabezpečené.

Alternativy k Dockeru zahrnují hostování mikroslužeb ve službách PaaS, jako je azure app service. Alternativy k Kubernetes patří orchestrators jako Service Fabric, DC/OS nebo Roj.

## <a name="next-steps"></a>Další kroky

* Nasazení řešení vzdáleného monitorování [zde](https://www.azureiotsolutions.com/).
* Prozkoumejte kód GitHub u [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) a [Javy](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Další informace o referenční architektuře IoT [najdete tady](https://aka.ms/iotrefarchitecture).
