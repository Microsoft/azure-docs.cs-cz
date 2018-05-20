---
title: Vzdálené monitorování řešení architektury volby - Azure | Microsoft Docs
description: Tento článek popisuje architektury a technické volby provedené v vzdálené monitorování
services: iot-suite
suite: iot-suite
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-suite
ms.date: 04/30/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 607b8aeb2f986eebddf8fe13b88e7f3bc7b4494b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="remote-monitoring-architectural-choices"></a>Vzdálené monitorování architektury volby

Azure IoT vzdálené monitorování (Správce prostředků) je typu open source, MIT licenci, akcelerátoru řešení, která představuje běžné scénáře IoT, jako je připojení zařízení, Správa zařízení a zpracování datového proudu, proto zákazníky urychlit jejich vývoj proces.  RM následuje doporučené referenční architektura Azure IoT publikovaná [zde](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/).  

Tento článek popisuje architektury a technické volby provedené v každém subsystémů pro RM a alternativy, které jsou považovány za.  Je důležité si uvědomit, že technické volby provedené v RM nejsou jediný způsob, jak implementovat řešení vzdáleného sledování IoT.  Technická implementace je směrný plán pro vytváření k úspěšné aplikaci a by měl být upraven podle dovedností, zkušeností a potřebám svislé aplikace pro implementaci řešení zákazníků.

## <a name="architectural-choices"></a>Architektury volby

### <a name="microservices-serverless-and-cloud-native"></a>Mikroslužeb, bez serveru a cloudových nativní

Architektura doporučujeme pro cloud nativní, mikroslužbu, jsou aplikace či aplikace IoT a bez serveru na základě.  Různé subsystémy IoT aplikace by měly být vytvořeny jako samostatné služby, které jsou nasadit nezávisle a škálovat nezávisle.  Tyto atributy povolte větší škálování, větší flexibilitu při aktualizaci jednotlivých subsystémy a poskytují flexibilitu zvolit si odpovídající technologii subsystému za den.  S více technologiemi se dá implementovat Mikroslužeb. Například pomocí bez serveru technologie, jako je například Azure Functions kontejneru technologie, jako je například Docker nebo hostování mikroslužeb v PaaS služby, jako je například Azure App Services.

## <a name="core-subsystem-technology-choices"></a>Výběr technologie subsystému jádra

V této části podrobně technologie volby provedené v RM pro každou subsystémů jádra.

![Diagram jádra](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png) 

### <a name="cloud-gateway"></a>Cloudová brána
Azure IoT Hub se používá jako cloudová brána RM.  IoT Hub nabízí zabezpečené, obousměrná komunikace se zařízeními. Další informace o IoT Hub [zde](https://azure.microsoft.com/services/iot-hub/). Pro připojení zařízení IoT používají .NET Core a sady SDK služby IoT Hub Java.  Sady SDK nabízejí obálky kolem API služby REST centra IoT a popisovač scénáře, jako je opakování 

### <a name="stream-processing"></a>Zpracování streamů
Pro RM zpracování datového proudu používá Azure Stream Analytics pro zpracování komplexní pravidla.  Pro zákazníky, kteří požadují jednodušší pravidla máme také vlastní mikroslužbu s podporou pro zpracování jednoduchých pravidel, i když tato nastavení nejsou součástí mimo nasazení pole. Referenční architektura doporučuje použití Azure Functions pro jednoduché pravidlo zpracování a Azure Stream Analytics (ASA) pro zpracování komplexní pravidla.  

### <a name="storage"></a>Úložiště
Pro úložiště, Cosmos DB se používá pro všechny požadavky na úložiště: studené úložiště, záložním úložiště, pravidla úložiště a výstrahy. Probíhá aktuálně Přesun do Azure blob storage dle doporučení referenční architektura.  Cosmos DB je doporučené úložiště pro obecné účely záložním řešení pro aplikace či aplikace IoT, i když jsou vhodné pro mnoho případy použití řešení například Statistika řady čas Azure a Azure Data Lake.

### <a name="business-integration"></a>Obchodní integraci
Obchodní integraci v RM je omezený na generování výstrahy, které jsou umístěny v záložním úložiště. Integrace obchodních další lze provést integrací řešení službou Azure Logic Apps.

### <a name="user-interface"></a>Uživatelské rozhraní
Webové uživatelské rozhraní je vytvořené s nástroji JavaScript React.  Reagují nabízí rozhraní běžně používané odvětví webového uživatelského rozhraní a je podobná dalších oblíbených rozhraní, jako je například úhlová.  

### <a name="runtime-and-orchestration"></a>Modul runtime a Orchestrace
Doba spuštění aplikace zvolené pro subsystém implementace v RM je Docker kontejnery s Kubernetes (K8s) jako orchestrator pro vodorovné škálování.  Tato architektura umožňuje pro jednotlivé škálování definice za subsystému ale způsobuje náklady DevOps při ochraně virtuálních počítačů a kontejnery aktuální z hlediska zabezpečení.  Alternativy k Docker & K8s zahrnují hostování mikroslužeb v PaaS služby (například Azure App Service) nebo jako produktu orchestrator pomocí Service Fabric, orchestrátoru DC/OS, Swarm atd.

## <a name="next-steps"></a>Další postup
* Nasazení řešení RM [zde](https://www.azureiotsuite.com/).
* Prozkoumejte Githubu kód v [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) a [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Další informace o referenční architektura IoT [zde](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/).
