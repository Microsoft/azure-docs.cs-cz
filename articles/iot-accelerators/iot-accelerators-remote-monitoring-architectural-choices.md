---
title: Řešení volby architektury vzdáleného monitorování – Azure | Dokumentace Microsoftu
description: Tento článek popisuje, architektury a technické volby provedené v vzdáleného monitorování
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/12/2018
ms.topic: conceptual
ms.openlocfilehash: 709886e77819adca961a44f65fe6402dd7d20d53
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44716294"
---
# <a name="remote-monitoring-architectural-choices"></a>Volby architektury vzdáleného monitorování

Akcelerátor řešení vzdáleného monitorování Azure IoT je open source, licence MIT, akcelerátor řešení, která představuje běžné scénáře IoT, jako je například připojení k zařízení, zařízení a zpracování datového proudu, takže zákazníci můžou urychlit jejich vývoj proces.  Řešení vzdáleného monitorování následuje doporučené referenční architektura Azure IoT publikované [tady](https://aka.ms/iotrefarchitecture).  

Tento článek popisuje architekturu a technický volby provedené v jednotlivých subsystémů řešení vzdáleného monitorování a popisuje zvažované alternativy.  Je důležité si uvědomit, že technické volby provedené v řešení vzdáleného monitorování nejsou jediný způsob, jak implementovat řešení vzdáleného monitorování IoT.  Technická implementace je směrný plán pro vytváření úspěšné aplikace a měli upravit tak, aby odpovídaly dovednosti, zkušenosti a potřeby svislé aplikace pro zákaznické implementace řešení.

## <a name="architectural-choices"></a>Architektonické volby

### <a name="microservices-serverless-and-cloud-native"></a>Mikroslužby, prostředí bez serveru a nativní pro cloud

Architektura doporučujeme pro cloud nativní, mikroslužby, jsou aplikace IoT a bez serveru založené.  Různé subsystémy aplikace IoT, by měly být sestaveny jako samostatných služeb, které jsou nezávisle nasadit a škálovat nezávisle.  Tyto atributy umožňují větším měřítku, větší flexibilitu při aktualizaci jednotlivých subsystémů a zadejte volnost ve výběru vhodných technologií na základě za subsystému.  Mikroslužby je možné implementovat s více technologiemi. Například technologie kontejnerů, jako je Docker pomocí technologie bez serverů, třeba Azure Functions nebo hostování mikroslužeb na služby modelu PaaS, jako je například Azure App Services.

## <a name="core-subsystem-technology-choices"></a>Základní subsystém technologické volby

Tato část podrobně technologické volby provedené v řešení vzdáleného monitorování pro každou základní subsystémů.

![Základní Diagram](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png) 

### <a name="cloud-gateway"></a>Cloudová brána
Azure IoT Hub se používá jako cloudová brána řešení vzdáleného monitorování.  IoT Hub nabízí zabezpečenou obousměrnou komunikaci mezi zařízeními. Další informace o službě IoT Hub [tady](https://azure.microsoft.com/services/iot-hub/). Připojení zařízení IoT se používají sady SDK služby IoT Hub Java a .NET Core.  Sady SDK nabízejí obálky kolem rozhraní REST API centra IoT a zpracování scénářů, jako je například opakovaných pokusů.

### <a name="stream-processing"></a>Zpracování streamů
Pro datový proud používá řešení vzdáleného monitorování pro zpracování Azure Stream Analytics pro zpracování složitých pravidla.  Pro zákazníky, kteří jednodušší pravidla máme i vlastní mikroslužeb s podporou pro zpracování jednoduchých pravidel, i když tato nastavení nejsou součástí vzdáleného nasazení pole. Referenční architektura doporučuje použití Azure Functions pro zpracování jednoduché pravidlo a Azure Stream Analytics (ASA) pro zpracování složitých pravidla.  

### <a name="storage"></a>Úložiště
Akcelerátor řešení vzdálené monitorování pro úložiště, používá Azure Time Series Insights a Azure Cosmos DB. Azure Time Series Insights ukládá zprávy přicházející z připojených zařízení prostřednictvím služby IoT Hub. Akcelerátor řešení využívá Azure Cosmos DB pro všechna úložiště, jako je například studeného úložiště, definice pravidla, alarmy a nastavení konfigurace. Azure Cosmos DB je doporučené úložiště pro obecné účely teplé řešení pro aplikace IoT, i když jsou vhodné pro mnoho případy použití řešení, jako je Azure Time Series Insights a Azure Data Lake. S Azure Time Series Insights můžete získat podrobnější přehled o datech časových řad ze zjišťování trendy a anomálie, které umožňuje provádět analýzy původních příčin a vyhnout se nákladným prostojům. 

> [!NOTE]
> Azure Time Series Insights pro akcelerátor řešení vzdálené monitorování je aktuálně ve verzi preview a je dostupná jenom [vybrat oblasti](https://azure.microsoft.com/global-infrastructure/services/). Pokud provádíte nasazení akcelerátoru řešení vzdáleného monitorování mimo tyto oblasti, Cosmos DB je výchozí možnost úložiště.

### <a name="business-integration"></a>Obchodní integrace
Integrace obchodních v řešení vzdáleného monitorování je omezená na generování poplachů, které jsou umístěny v záložním úložišti. Další obchodní integrace lze provést integrací řešení s Azure Logic Apps.

### <a name="user-interface"></a>Uživatelské rozhraní
Webové uživatelské rozhraní je sestavené s JavaScript React.  React nabízí architekturu webového uživatelského rozhraní běžně používaných odvětví a je podobný dalších oblíbených architektur, jako jsou Angular.  

### <a name="runtime-and-orchestration"></a>Modul runtime a Orchestrace
Doba spuštění aplikace, které jste zvolili pro subsystém implementace v řešení vzdáleného monitorování kontejnerů Dockeru pomocí Kubernetes je jako orchestrátor pro horizontální škálování.  Tato architektura umožňuje pro definici jednotlivých škálování za subsystému ale způsobuje náklady na DevOps v průběžná virtuálních počítačů a kontejnerů z hlediska zabezpečení.  Alternativy k Dockeru a Kubernetes zahrnují hostování mikroslužeb na služby modelu PaaS (například Azure App Service) nebo jako orchestrátor pomocí Service Fabric, DC/OS, Swarm atd.

## <a name="next-steps"></a>Další postup
* Nasazení řešení vzdáleného monitorování [tady](https://www.azureiotsolutions.com/).
* Zkoumání kódu Githubu v [jazyka C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) a [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Další informace o referenční architektura IoT [tady](https://aka.ms/iotrefarchitecture).
