---
title: Přehled Service Fabric v Azure | Dokumentace Microsoftu
description: Přehled platformy Service Fabric, kde se aplikace skládají z mnoha mikroslužeb pro zajištění škálování a odolnosti. Service Fabric je platforma distribuovaných systémů, která slouží k sestavování škálovatelných a spolehlivých aplikací se snadnou správou pro cloud.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: masnider
ms.assetid: bbcc652a-a790-4bc4-926b-e8cd966587c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: overview
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: msfussell
ms.custom: mvc
ms.openlocfilehash: fdb710868a75f64c3fe9f3742482c70ff86f4365
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="overview-of-azure-service-fabric"></a>Přehled Azure Service Fabric
Azure Service Fabric je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb a kontejnerů. Service Fabric se také zaměřuje na problematiku vývoje a správy nativních cloudových aplikací. Vývojáři a správci se můžou vyhnout problémům se složitou infrastrukturou a místo toho se soustředit na implementaci zásadních a náročných úloh, které jsou škálovatelné, spolehlivé a spravovatelné. Service Fabric představuje novou generaci platforem pro vytváření a správu těchto podnikových cloudových aplikací 1. úrovně spouštěných v kontejnerech.

Toto krátké video představuje Service Fabric a mikroslužby:
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Service-Fabric/player]

## <a name="applications-composed-of-microservices"></a>Aplikace skládající se z mikroslužeb 
Service Fabric umožňuje vytvářet a spravovat škálovatelné a odolné aplikace skládající se z mikroslužeb, které se spouštějí s vysokou hustotou ve sdíleném fondu počítačů, který se označuje jako cluster. Poskytuje propracovaný a nenáročný modul runtime pro sestavování distribuovaných, škálovatelných, bezstavových a stavových mikroslužeb spouštěných v kontejnerech. Poskytuje také komplexní možnosti správy aplikací umožňující zřizování, nasazování, monitorování, upgradování, opravování a odstraňování nasazených aplikací, včetně kontejnerizovaných služeb.

Service Fabric dnes využívá řada služeb Microsoftu, včetně Azure SQL Database, Azure Cosmos DB, Cortana, Microsoft Power BI, Microsoft Intune, Azure Event Hubs, Azure IoT Hub, Dynamics 365, Skype pro firmy a mnoha dalších základních služeb Azure.

Platforma Service Fabric je přizpůsobená pro vytváření nativních cloudových služeb, které můžou začínat jako malé a podle potřeby se rozrůstat a masivně škálovat na stovky nebo tisíce počítačů.

Dnešní služby v internetovém měřítku se skládají z mikroslužeb. Mezi příklady mikroslužeb patří brány protokolů, profily uživatelů, nákupní košíky, zpracování inventáře, fronty a mezipaměti. Service Fabric je platforma mikroslužeb, která každé mikroslužbě (nebo kontejneru) přiděluje jedinečný název, který může být stavový, nebo bezstavový.

Service Fabric nabízí aplikacím, které se skládají z těchto mikroslužeb, komplexní možnosti správy modulu runtime a životního cyklu. Mikroslužby hostuje uvnitř kontejnerů, které se nasazují a aktivují napříč clusterem Service Fabric. Přesun z virtuálních počítačů na kontejnery umožňuje řádové zvýšení hustoty. Podobně je možné docílit dalšího řádového zvýšeni hustoty přesunem z kontejnerů na mikroslužby v těchto kontejnerech. Například jeden cluster pro službu Azure SQL Database se skládá ze stovek počítačů, na kterých se spouští desítky tisíc kontejnerů, které celkem hostují stovky tisíc databází. Každá databáze je stavovou mikroslužbou Service Fabric. 

Další informace o přístupu založeném na mikroslužbách najdete v tématu [Proč při sestavování aplikací zvolit přístup založený na mikroslužbách?](service-fabric-overview-microservices.md)

## <a name="container-deployment-and-orchestration"></a>Nasazení a orchestrace kontejnerů
Service Fabric je [orchestrátor kontejnerů](service-fabric-cluster-resource-manager-introduction.md) od Microsoftu, který nasazuje mikroslužby napříč clusterem počítačů. Mikroslužby je možné vyvíjet mnoha způsoby, od použití [programovacích modelů Service Fabric](service-fabric-choose-framework.md) přes [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) až po nasazení [libovolného vlastního kódu](service-fabric-guest-executables-introduction.md). Důležité je, že ve stejné aplikaci můžete kombinovat služby v procesech i služby v kontejnerech. Pokud chcete pouze [nasazovat a spravovat kontejnery](service-fabric-containers-overview.md), Service Fabric je jako orchestrátor kontejnerů ideální volbou.

## <a name="any-os-any-cloud"></a>Jakýkoli operační systém a cloud
Service Fabric funguje všude. Clustery pro Service Fabric můžete vytvářet v mnoha prostředích, včetně Azure, místního prostředí, Windows Serveru nebo Linuxu. Dokonce můžete vytvářet clustery v jiných veřejných cloudech. Kromě toho je vývojové prostředí v sadě SDK **identické** s produkčním prostředím, a to bez použití emulátorů. Jinými slovy, cokoli běží v místním vývojovém clusteru se nasadí i do clusterů v jiných prostředích.

![Platforma Service Fabric][Image1]

Pro účely vývoje ve Windows je v sadě Visual Studio a v PowerShellu integrovaná sada Service Fabric .NET SDK. Viz [Příprava vývojového prostředí ve Windows](service-fabric-get-started.md). Pro účely vývoje v Linuxu je v Eclipse integrovaná sada Service Fabric Java SDK a ke generování šablon pro Javu, .NET Core a aplikace typu kontejner se používá Yeoman. Viz [Příprava vývojového prostředí v Linuxu](service-fabric-get-started.md).

Další informace o vytváření clusterů najdete v tématu popisujícím [vytvoření clusteru ve Windows Serveru nebo v Linuxu](service-fabric-deploy-anywhere.md) nebo v tématu popisujícím vytvoření clusteru v Azure [prostřednictvím webu Azure Portal](service-fabric-cluster-creation-via-portal.md).

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Bezstavové a stavové mikroslužby pro Service Fabric
Service Fabric umožňuje sestavovat aplikace, které se skládají z mikroslužeb nebo kontejnerů. Bezstavové mikroslužby (například brány protokolů a webové proxy) si mimo požadavek a odpověď ze služby neudržují měnitelný stav. Role pracovních procesů služby Azure Cloud Services jsou příkladem stavové služby. Stavové mikroslužby (například uživatelské účty, databáze, zařízení, nákupní košíky a fronty) si udržují měnitelný a autoritativní stav i mimo požadavek a odpověď. Dnešní aplikace v internetovém měřítku se skládají z kombinace bezstavových a stavových mikroslužeb. 

Service Fabric se odlišuje hlavně silným zaměřením na sestavování stavových služeb, a to pomocí [integrovaných programovacích modelů](service-fabric-choose-framework.md) nebo kontejnerizovaných stavových služeb. [Scénáře aplikací](service-fabric-application-scenarios.md) popisují scénáře, ve kterých se používají stavové služby.


## <a name="application-lifecycle-management"></a>Správa životního cyklu aplikací
Service Fabric poskytuje podporu pro celý životní cyklus aplikací a CI/CD cloudových aplikací včetně kontejnerů. Tento životní cyklus zahrnuje vývoj až po nasazení, každodenní správu a údržbu až po případné vyřazení z provozu.

Možnosti správy životního cyklu aplikací Service Fabric umožňují správcům aplikací a IT operátorům používat ke zřizování, nasazování, opravování a monitorování aplikací jednoduché pracovní postupy, které téměř nevyžadují úpravy. Tyto integrované pracovní postupy výrazně snižují zátěž kladenou na IT operátory kvůli udržování neustále dostupných aplikací.

Většina aplikací se skládá z kombinace bezstavových a stavových mikroslužeb, kontejnerů a dalších spustitelných souborů, které se nasazují společně. Díky vynucování silných typů aplikací umožňuje Service Fabric nasazení více instancí aplikace. Každá instance se spravuje a upgraduje samostatně. Důležité je, že Service Fabric dokáže nasadit kontejnery nebo jakékoli spustitelné soubory a zajistit jejich spolehlivost. Service Fabric dokáže nasadit například kód v .NET, ASP.NET Core nebo Node.js, kontejnery Windows, kontejnery Linuxu, virtuální počítače Java, skripty, kód Angular nebo prakticky cokoli, z čeho se vaše aplikace skládá.

Platforma Service Fabric je integrovaná v nástrojích CI/CD, jako jsou [Visual Studio Team Services](https://www.visualstudio.com/team-services/), [Jenkins](https://jenkins.io/index.html) a [Octopus Deploy](https://octopus.com/), a můžete ji používat s jakýmkoli jiným oblíbeným nástrojem CI/CD.

Další informace o správě životního cyklu aplikací najdete v tématu [Životní cyklus aplikace](service-fabric-application-lifecycle.md). Další informace o nasazení jakéhokoli kódu najdete v tématu popisujícím [nasazení hostujícího spustitelného souboru](service-fabric-deploy-existing-app.md).

## <a name="key-capabilities"></a>Klíčové funkce
Pomocí Service Fabric můžete:

* Nasazovat do Azure nebo místních datacenter s Windows nebo Linuxem beze změn kódu. Kód můžete napsat jednou a pak ho nasadit kamkoli a do jakéhokoli clusteru Service Fabric.
* Vyvíjet škálovatelné aplikace, které se skládají z mikroslužeb, pomocí programovacích modelů Service Fabric, kontejnerů nebo jakéhokoli kódu.
* Vyvíjet vysoce spolehlivé bezstavové a stavové mikroslužby. Zjednodušit návrh aplikace s použitím stavových mikroslužeb. 
* Používat programovací model Reliable Actors k vytváření cloudových objektů s uzavřeným kódem a stavem.
* Nasazovat a orchestrovat kontejnery, což zahrnuje kontejnery Windows a kontejnery Linuxu. Service Fabric je stavový orchestrátor kontejnerů, který je schopný rozpoznávat data.
* Během několika sekund nasazovat aplikace s vysokou hustotou stovek nebo tisíců aplikací nebo kontejnerů na počítač.
* Nasazovat různé verze stejné aplikace vedle sebe a upgradovat jednotlivé aplikace nezávisle na ostatních.
* Spravovat životní cyklus vašich aplikací bez jakýchkoli prostojů, včetně zásadních i méně zásadních upgradů.
* Horizontálně navýšit nebo snížit kapacitu počtu uzlů v clusteru. Při škálování uzlů se automaticky škálují i vaše aplikace.
* Monitorovat a diagnostikovat stav vašich aplikací a nastavit zásady pro provádění automatických oprav.
* Sledovat, jak nástroj pro vyrovnávání prostředků orchestruje přerozdělování aplikací napříč clusterem. Service Fabric se v případě selhání zotaví a optimalizuje distribuci zatížení na základě dostupných prostředků.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další kroky
* Další informace najdete tady:
  * [Proč při sestavování aplikací zvolit přístup založený na mikroslužbách?](service-fabric-overview-microservices.md)
  * [Přehled terminologie](service-fabric-technical-overview.md)
* Nastavení [vývojového prostředí ve Windows](service-fabric-get-started.md)  
* Nastavení [vývojového prostředí v Linuxu](service-fabric-get-started-linux.md)
* Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
