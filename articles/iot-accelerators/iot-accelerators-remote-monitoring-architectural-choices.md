---
title: Možnosti architektury řešení vzdáleného monitorování – Azure | Microsoft Docs
description: Tento článek popisuje strukturální a technické volby provedené při vzdáleném monitorování.
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 27a65d8a499f6eba130dc9537de3cb2b3dc3abe8
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446760"
---
# <a name="remote-monitoring-architectural-choices"></a>Volby architektury vzdáleného monitorování

Akcelerátor řešení vzdáleného monitorování Azure IoT je open source s licencí MIT a akcelerátor řešení. Abychom vám pomohli urychlit proces vývoje IoT, zobrazují se běžné scénáře IoT, jako například:

- Připojení zařízení
- Správa zařízení
- Zpracování streamů

Řešení vzdáleného monitorování se řídí doporučenou [referenční architekturou Azure IoT](/azure/architecture/reference-architectures/iot).

Tento článek popisuje klíčovou architekturu a technické možnosti provedené v jednotlivých subsystémech vzdáleného monitorování. Technické možnosti, které Microsoft provedl v řešení vzdáleného monitorování, ale nejsou jediným způsobem implementace řešení pro vzdálené monitorování IoT. Tuto technickou implementaci byste měli brát v úvahu jako základ pro sestavení úspěšné aplikace a měli byste ji upravit na:

- Využijte možnosti dostupných dovedností a zkušeností ve vaší organizaci.
- Vyhovět vašim vertikálním potřebám aplikací.

## <a name="architectural-choices"></a>Architektonické volby

Architektura, kterou Microsoft doporučuje pro aplikaci IoT, je cloudová nativní, mikroslužba a založená na architektuře bez serveru. Měli byste sestavit různé subsystémy aplikace IoT jako diskrétní služby, které můžete nasazovat a škálovat nezávisle. Tyto atributy umožňují větší škálování, větší flexibilitu při aktualizaci jednotlivých subsystémů a poskytují flexibilitu pro výběr vhodné technologie pro každý subsystém.

Mikroslužby můžete implementovat pomocí více než jedné technologie. Můžete například zvolit jednu z následujících možností pro implementaci mikroslužby:

- Použijte technologii kontejneru, jako je Docker s technologií bez serveru, jako je například Azure Functions.
- Hostování mikroslužeb v PaaS službách, jako je Azure App Services.

## <a name="technology-choices"></a>Technologické volby

Tato část podrobně popisuje možnosti technologie provedené v řešení vzdáleného monitorování pro všechny základní subsystémy.

![Základní diagram](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>Cloudová brána

Azure IoT Hub se používá jako cloudová brána řešení vzdáleného monitorování. [IoT Hub](https://azure.microsoft.com/services/iot-hub/) nabízí zabezpečenou obousměrnou komunikaci se zařízeními.

V případě připojení zařízení IoT můžete použít:

- Sady [SDK pro zařízení IoT Hub](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) pro implementaci nativní klientské aplikace pro vaše zařízení. Sady SDK nabízejí obálky kolem IoT Hub REST API a zpracovávají scénáře, jako jsou třeba opakované pokusy.
- Integrace s Azure IoT Edge pro nasazení a správu vlastních modulů spuštěných v kontejnerech na vašich zařízeních.
- Integrace s automatickou správou zařízení v IoT Hub pro hromadnou správu připojených zařízení.

### <a name="stream-processing"></a>Zpracování streamů

Pro zpracování streamu používá řešení vzdáleného monitorování Azure Stream Analytics pro zpracování složitých pravidel. Pokud chcete použít jednodušší pravidla, je k dispozici vlastní mikroslužba s podporou pro zpracování jednoduchých pravidel, i když tato nastavení nepatří do předem připraveného nasazení. Referenční architektura doporučuje Azure Functions pro zpracování jednoduchých pravidel a Azure Stream Analytics pro zpracování složitých pravidel.

### <a name="storage"></a>Storage

V případě úložiště používá akcelerátor řešení vzdáleného monitorování Azure Time Series Insights i Azure Cosmos DB. Azure Time Series Insights ukládá zprávy přicházející do IoT Hub z připojených zařízení. Akcelerátor řešení používá Azure Cosmos DB pro všechna ostatní úložiště, jako je například studené úložiště, definice pravidel, výstrahy a nastavení konfigurace.

Azure Cosmos DB je doporučené řešení teplého úložiště pro obecné účely pro aplikace IoT. Řešení, jako je Azure Time Series Insights a Azure Data Lake, jsou však vhodná pro mnoho případů použití. Pomocí Azure Time Series Insights můžete získat hlubší přehledy o datech snímačů časových řad tím, že hledání trendy a anomálie. Tato funkce umožňuje provádět analýzy hlavní příčiny a vyhnout se nákladným prostojům.

> [!NOTE]
> Time Series Insights není aktuálně k dispozici v cloudu Azure Čína. Nové nasazení akcelerátoru řešení vzdáleného monitorování v cloudu Azure Čína používá Cosmos DB pro všechna úložiště.

### <a name="business-integration"></a>Podniková integrace

Podniková integrace v řešení vzdáleného monitorování je omezená na generování výstrah, které jsou umístěné v teplém úložišti. Připojte řešení pomocí Azure Logic Apps pro implementaci hlubších scénářů pro podnikovou integraci.

### <a name="user-interface"></a>Uživatelské rozhraní

Webové uživatelské rozhraní je sestaveno s reakci JavaScriptu. Reakce nabízí běžně používané prostředí pro prostředí Web UI a je podobné jiným oblíbeným architekturám, jako je například úhlové.

### <a name="runtime-and-orchestration"></a>Modul runtime a orchestrace

Řešení vzdáleného monitorování používá kontejnery Docker ke spouštění subsystémů s Kubernetes jako se nástrojem Orchestrator pro horizontální škálování. Tato architektura umožňuje individuální definice škálování pro každý subsystém. Tato architektura ale účtuje DevOps náklady, aby byly virtuální počítače a kontejnery v aktuálním stavu a zabezpečené.

Mezi alternativy k Docker patří hostování mikroslužeb v PaaS službách, jako je například Azure App Service. Mezi alternativy k Kubernetes patří orchestrace, jako Service Fabric, DC/OS nebo Swarm.

## <a name="next-steps"></a>Další kroky

* [Sem](https://www.azureiotsolutions.com/)nasaďte řešení vzdáleného monitorování.
* Prozkoumejte kód GitHubu v [jazycích C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) a [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Další informace o referenční architektuře IoT [najdete tady](/azure/architecture/reference-architectures/iot).