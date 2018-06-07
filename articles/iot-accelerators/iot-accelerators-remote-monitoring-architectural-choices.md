---
title: Vzdálené monitorování řešení architektury volby - Azure | Microsoft Docs
description: Tento článek popisuje architektury a technické volby provedené v vzdálené monitorování
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/30/2018
ms.topic: conceptual
ms.openlocfilehash: 6c4bf0e4bf0a6c1a791cf762ec9bb44ed5c0b1bd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627684"
---
# <a name="remote-monitoring-architectural-choices"></a>Vzdálené monitorování architektury volby

Accelerator řešení Azure IoT vzdálené monitorování je typu open source, MIT licenci, akcelerátoru řešení, která představuje běžné scénáře IoT, jako je připojení zařízení, Správa zařízení a zpracování datového proudu, aby zákazníci urychlit jejich vývoj proces.  Řešení vzdáleného monitorování následuje doporučené referenční architektura Azure IoT publikovaná [zde](https://aka.ms/iotrefarchitecture).  

Tento článek popisuje architektury a technické volby provedené v každém subsystémů pro řešení vzdáleného monitorování a alternativy, které jsou považovány za.  Je důležité si uvědomit, že technické volby provedené v řešení vzdáleného monitorování nejsou jediný způsob, jak implementovat řešení vzdáleného sledování IoT.  Technická implementace je směrný plán pro vytváření k úspěšné aplikaci a by měl být upraven podle dovedností, zkušeností a potřebám svislé aplikace pro implementaci řešení zákazníků.

## <a name="architectural-choices"></a>Architektury volby

### <a name="microservices-serverless-and-cloud-native"></a>Mikroslužeb, bez serveru a cloudových nativní

Architektura doporučujeme pro cloud nativní, mikroslužbu, jsou aplikace či aplikace IoT a bez serveru na základě.  Různé subsystémy IoT aplikace by měly být vytvořeny jako samostatné služby, které jsou nasadit nezávisle a škálovat nezávisle.  Tyto atributy povolte větší škálování, větší flexibilitu při aktualizaci jednotlivých subsystémy a poskytují flexibilitu zvolit si odpovídající technologii subsystému za den.  S více technologiemi se dá implementovat Mikroslužeb. Například pomocí bez serveru technologie, jako je například Azure Functions kontejneru technologie, jako je například Docker nebo hostování mikroslužeb v PaaS služby, jako je například Azure App Services.

## <a name="core-subsystem-technology-choices"></a>Výběr technologie subsystému jádra

V této části podrobně technologie volby provedené v řešení vzdáleného monitorování pro každou subsystémů jádra.

![Diagram jádra](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png) 

### <a name="cloud-gateway"></a>Cloudová brána
Azure IoT Hub se používá jako cloudová brána řešení vzdáleného monitorování.  IoT Hub nabízí zabezpečené, obousměrná komunikace se zařízeními. Další informace o IoT Hub [zde](https://azure.microsoft.com/services/iot-hub/). Pro připojení zařízení IoT používají .NET Core a sady SDK služby IoT Hub Java.  Sady SDK nabízejí obálky kolem rozhraní API REST centra IoT a zpracovávat scénáře, jako je opakování.

### <a name="stream-processing"></a>Zpracování streamů
Pro datový proud zpracování řešení vzdáleného monitorování používá Azure Stream Analytics ke zpracování komplexní pravidlo.  Pro zákazníky, kteří požadují jednodušší pravidla máme také vlastní mikroslužbu s podporou pro zpracování jednoduchých pravidel, i když tato nastavení nejsou součástí mimo nasazení pole. Referenční architektura doporučuje použití Azure Functions pro jednoduché pravidlo zpracování a Azure Stream Analytics (ASA) pro zpracování komplexní pravidla.  

### <a name="storage"></a>Úložiště
Pro úložiště Azure Cosmos DB se používá pro všechny požadavky na úložiště: studené úložiště, záložním úložiště, pravidla úložiště a výstrahy. Probíhá aktuálně Přesun do Azure blob storage dle doporučení referenční architektura.  Azure Cosmos DB je doporučené úložiště pro obecné účely záložním řešení pro aplikace či aplikace IoT, i když jsou vhodné pro mnoho případy použití řešení například Statistika řady čas Azure a Azure Data Lake.

### <a name="business-integration"></a>Obchodní integraci
Obchodní integraci do řešení pro vzdálené monitorování je omezený na generování výstrahy, které jsou umístěny v záložním úložiště. Integrace obchodních další lze provést integrací řešení službou Azure Logic Apps.

### <a name="user-interface"></a>Uživatelské rozhraní
Webové uživatelské rozhraní je vytvořené s nástroji JavaScript React.  Reagují nabízí rozhraní běžně používané odvětví webového uživatelského rozhraní a je podobná dalších oblíbených rozhraní, jako je například úhlová.  

### <a name="runtime-and-orchestration"></a>Modul runtime a Orchestrace
Doba spuštění aplikace zvolené pro subsystém implementace v řešení vzdáleného monitorování je Docker kontejnery s Kubernetes jako orchestrator pro vodorovné škálování.  Tato architektura umožňuje pro jednotlivé škálování definice za subsystému ale způsobuje náklady DevOps při ochraně virtuálních počítačů a kontejnery aktuální z hlediska zabezpečení.  Alternativy k Docker a Kubernetes zahrnují hostování mikroslužeb v PaaS služby (například Azure App Service) nebo jako produktu orchestrator pomocí Service Fabric, orchestrátoru DC/OS, Swarm atd.

## <a name="next-steps"></a>Další postup
* Nasazení řešení vzdáleného monitorování [zde](https://www.azureiotsolutions.com/).
* Prozkoumejte Githubu kód v [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) a [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Další informace o referenční architektura IoT [zde](https://aka.ms/iotrefarchitecture).
