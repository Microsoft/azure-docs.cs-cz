---
title: Řešení volby architektury vzdáleného monitorování – Azure | Dokumentace Microsoftu
description: Tento článek popisuje, architektury a technické volby provedené v vzdáleného monitorování
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 20af014e5a59cb526d5b96e543b10d5b2b6d6937
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52679592"
---
# <a name="remote-monitoring-architectural-choices"></a>Volby architektury vzdáleného monitorování

Akcelerátor řešení vzdáleného monitorování Azure IoT je open source, MIT licenci, akcelerátor řešení. Ke zrychlení procesu vývoje IoT, zobrazuje běžným scénářům IoT, jako:

- Připojení zařízení
- Správa zařízení
- Zpracování streamů

Následující doporučené řešení vzdáleného monitorování [referenční architektura Azure IoT](https://aka.ms/iotrefarchitecture).

Tento článek popisuje klíčové architektury a technické volby provedené v každém subsystémy vzdálené monitorování. Technické možnosti Microsoft vytvořil v řešení vzdáleného monitorování však nejsou jediný způsob, jak implementovat řešení vzdáleného monitorování IoT. Technická implementace měli považovat za směrný plán pro vytváření úspěšné aplikace a byste měli upravit na:

- Přizpůsobit k dispozici znalosti a zkušenosti ve vaší organizaci.
- Splňte své vertikální aplikace.

## <a name="architectural-choices"></a>Architektonické volby

Architektura, která společnost Microsoft doporučuje pro aplikace IoT je cloud určený pro nativní, mikroslužby, a bez serveru založené. Měli byste vytvořit různé subsystémy aplikace IoT jako samostatných služeb, které můžete nasadit a škálování nezávisle na sobě. Tyto atributy umožňují větším měřítku, větší flexibilitu při aktualizaci jednotlivých subsystémů a poskytují volnost ve výběru vhodných technologií pro každý subsystém.

Můžete implementovat mikroslužby pomocí více než jedné technologie. Například může zvolit některý z následujících možností implementovat mikroslužby:

- Technologie kontejnerů, jako je Docker pomocí technologie bez serverů, třeba Azure Functions.
- Hostování mikroslužby na služby modelu PaaS, jako je například Azure App Services.

## <a name="technology-choices"></a>Technologické volby

Tato část podrobně technologické volby provedené v řešení vzdáleného monitorování pro každou základní subsystémů.

![Základní Diagram](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>Cloudová brána

Azure IoT Hub se používá jako cloudová brána řešení vzdáleného monitorování. [IoT Hub](https://azure.microsoft.com/services/iot-hub/) nabízí zabezpečenou obousměrnou komunikaci mezi zařízeními.

Připojení zařízení IoT můžete použít:

- [Sady SDK pro zařízení služby IoT Hub](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-device-sdks) implementovat nativní klientské aplikace pro vaše zařízení. Sady SDK nabízejí obálky kolem rozhraní REST API centra IoT a zpracování scénářů, jako je například opakovaných pokusů.
- Integrace s Azure IoT Edge můžete nasadit a spravovat vlastní moduly spouštěných v kontejnerech na vašich zařízeních.
- Integrace se správu automatické zařízení ve službě IoT Hub ke správě připojených zařízení hromadně.

### <a name="stream-processing"></a>Zpracování streamů

Pro zpracování datového proudu, používá řešení vzdáleného monitorování Azure Stream Analytics pro zpracování složitých pravidla. Pokud chcete použít jednodušší pravidla, je vlastní mikroslužeb s podporou pro zpracování jednoduché pravidlo, i když toto nastavení není součástí nasazení out-of-the-box. Referenční architektura doporučuje Azure Functions pro zpracování jednoduché pravidlo a Azure Stream Analytics pro zpracování složitých pravidla.

### <a name="storage"></a>Úložiště

Akcelerátor řešení vzdálené monitorování pro úložiště, používá Azure Time Series Insights a Azure Cosmos DB. Azure Time Series Insights ukládá zprávy přicházející z připojených zařízení prostřednictvím služby IoT Hub. Akcelerátor řešení využívá Azure Cosmos DB pro všechna úložiště, jako je například studeného úložiště, definice pravidla, alarmy a nastavení konfigurace.

Azure Cosmos DB je doporučené úložiště pro obecné účely teplé řešení pro IoT aplikace. Řešení, jako je Azure Time Series Insights a Azure Data Lake jsou však vhodné pro mnoho případy použití. S Azure Time Series Insights můžete získat podrobnější přehled o datech časových řad ze zjistíte trendy a anomálie. Tato funkce umožňuje provádět analýzy původních příčin a vyhnout se nákladným prostojům.

> [!NOTE]
> Time Series Insights není aktuálně k dispozici v cloud Azure China. Nové vzdálené monitorování akcelerátoru nasazení řešení v cloudu Azure China pomocí služby Cosmos DB pro všechna úložiště.

### <a name="business-integration"></a>Obchodní integrace

Integrace obchodních v řešení vzdáleného monitorování je omezená na generování alarmy, které jsou umístěny v záložním úložišti. Připojte řešení pomocí Azure Logic Apps provádět lepší obchodní scénáře integrace.

### <a name="user-interface"></a>Uživatelské rozhraní

Webové uživatelské rozhraní je sestavené s JavaScript React. React nabízí architekturu webového uživatelského rozhraní běžně používaných odvětví a je podobný dalších oblíbených architektur, jako jsou Angular.

### <a name="runtime-and-orchestration"></a>Modul runtime a Orchestrace

Řešení vzdáleného monitorování využívá kontejnery Dockeru spouštění subsystémů s využitím Kubernetes jako orchestrator pro horizontální škálování. Tato architektura umožňuje škálování jednotlivých definic pro každý subsystém. Tato architektura však nesnižuje DevOps náklady a zachovat virtuální počítače a kontejnery, aktuálnosti a zabezpečení.

Alternativy k Dockeru zahrnují hostování mikroslužeb na služby modelu PaaS, jako je Azure App Service. Alternativy k Kubernetes zahrnout orchestrátorů, jako je Service Fabric, DC/OS nebo Swarm.

## <a name="next-steps"></a>Další postup

* Nasazení řešení vzdáleného monitorování [tady](https://www.azureiotsolutions.com/).
* Zkoumání kódu Githubu v [jazyka C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) a [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Další informace o referenční architektura IoT [tady](https://aka.ms/iotrefarchitecture).
