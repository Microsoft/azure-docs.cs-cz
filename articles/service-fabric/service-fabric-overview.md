---
title: Přehled Azure Service Fabric
description: Service Fabric je platforma distribuovaných systémů pro vytváření škálovatelných, spolehlivých a snadno spravovaných mikroslužeb.
ms.topic: overview
ms.date: 09/22/2020
ms.custom: contentperfq1
ms.openlocfilehash: 3c282178decc1a07b2c0acc102b279688c42d52e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91300645"
---
# <a name="overview-of-azure-service-fabric"></a>Přehled Azure Service Fabric

Azure Service Fabric je [platforma distribuovaných systémů](#container-orchestration) usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb a kontejnerů. Service Fabric také řeší významné problémy při [vývoji a správě](#application-lifecycle-management) cloudových nativních aplikací.

Klíčovým rozlišením Service Fabric je jeho silný fokus při vytváření stavových služeb. Můžete použít [programovací model](#stateless-and-stateful-microservices) Service Fabric nebo spustit kontejnerové služby, které jsou napsané v libovolném jazyce nebo kódu. Kromě Azure můžete vytvářet [Service Fabric clustery kdekoli](#any-os-any-cloud), včetně Windows serveru a Linux místních i dalších veřejných cloudů.

![Service Fabric platforma poskytuje správu životního cyklu, dostupnost, orchestraci, programovací modely, stav a monitorování, nástroje pro vývoj a operace a automatické škálování – v Azure, místně, v jiných cloudech a na vašem vývojovém počítači.][Image1]

Service Fabric dnes využívá řada služeb Microsoftu, včetně Azure SQL Database, Azure Cosmos DB, Cortana, Microsoft Power BI, Microsoft Intune, Azure Event Hubs, Azure IoT Hub, Dynamics 365, Skype pro firmy a mnoha dalších základních služeb Azure.

## <a name="container-orchestration"></a>Orchestrace kontejnerů

Service Fabric je produkt Microsoft [Container Orchestrator](service-fabric-cluster-resource-manager-introduction.md) pro nasazování a správu mikroslužeb napříč clusterem počítačů a přináší vám z lekcí zkušeností s provozem služeb Microsoftu v rozsáhlém měřítku. Service Fabric můžou nasazovat aplikace v řádu sekund s vysokou hustotou se stovkami nebo tisíci aplikacemi nebo kontejnery na jeden počítač. Pomocí Service Fabric můžete kombinovat obě služby v procesech a službách v kontejnerech ve stejné aplikaci.

[Přečtěte si další informace o Service Fabric](service-fabric-content-roadmap.md) základních konceptech, programovacích modelech, životním cyklu aplikací, testování, clusterech a monitorování stavu.

## <a name="stateless-and-stateful-microservices"></a>Bezstavové a stavové mikroslužby

Service Fabric poskytuje propracovaný a lehký modul runtime, který podporuje bezstavové a stavové mikroslužby. Klíčovým rozdílem Service Fabric je jeho robustní podpora pro vytváření stavových služeb, a to buď pomocí Service Fabric [integrovaných programovacích modelů](service-fabric-choose-framework.md) nebo kontejnerových stavových služeb.

Přečtěte si další informace o [scénářích aplikací](service-fabric-application-scenarios.md) , které využívají Service Fabric stavové služby.

## <a name="application-lifecycle-management"></a>Správa životního cyklu aplikací

Service Fabric poskytuje podporu pro úplný životní cyklus aplikací a CI/CD cloudových aplikací, včetně kontejnerů: vývoj prostřednictvím nasazení, každodenní monitorování, správy a údržby, aby bylo možné provést vyřazení z provozu. Platforma Service Fabric je integrovaná s nástroji CI/CD, jako jsou [Azure Pipelines](https://www.visualstudio.com/team-services/), [Jenkins](https://jenkins.io/index.html) a [Octopus Deploy](https://octopus.com/), a můžete ji používat s jakýmkoli jiným oblíbeným nástrojem CI/CD.

Další informace o správě životního cyklu aplikací najdete v tématu [Životní cyklus aplikace](service-fabric-application-lifecycle.md). Informace o nasazení stávajících aplikací do Service Fabric najdete v tématu [nasazení hostovaného spustitelného souboru](service-fabric-deploy-existing-app.md).

## <a name="any-os-any-cloud"></a>Jakýkoli operační systém a cloud

Můžete vytvářet clustery pro Service Fabric v mnoha prostředích, včetně [Azure nebo místního](service-fabric-deploy-anywhere.md)prostředí, v [systému Windows Server nebo Linux](service-fabric-linux-windows-differences.md). Dokonce můžete vytvářet clustery v jiných veřejných cloudech. Vývojové prostředí v sadě Service Fabric SDK se shoduje s provozním prostředím bez zapojení emulátorů. Jinými slovy, co se spouští na místním vývojovém clusteru, je to, co se nasazuje do vašich clusterů v jiných prostředích.

Pro [vývoj pro Windows](service-fabric-get-started.md)je Service Fabric .NET SDK integrovaná v sadě Visual Studio a prostředí PowerShell. Pro [vývoj pro Linux](service-fabric-get-started-linux.md)se Service Fabric Java SDK integruje s zatmění a Yeoman se používá ke generování šablon pro aplikace Java, .NET Core a kontejner.

## <a name="compliance"></a>Dodržování předpisů

Poskytovatel prostředků Azure Service Fabric je k dispozici ve všech oblastech Azure a je kompatibilní se všemi certifikacemi dodržování předpisů Azure, včetně: SOC, ISO, PCI DSS, HIPAA a GDPR. Úplný seznam najdete v tématu nabídky týkající se [dodržování předpisů Microsoftu](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="next-steps"></a>Další kroky

Vytvořte a nasaďte svoji první aplikaci v Azure Service Fabric:

> [!div class="nextstepaction"]
> [Rychlý Start Service Fabric][sf-quickstart]

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
[sf-quickstart]: ./service-fabric-quickstart-dotnet.md
