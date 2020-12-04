---
title: Přehled programovacího modelu služby Service Fabric
description: 'Service Fabric nabízí dvě architektury pro vytváření služeb: rozhraní actor Framework a rozhraní služeb. Nabízejí v jednoduchosti a řízení různé kompromisy.'
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: 523c9e0064d8b78698f1a0dd3544bda58436800c
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575716"
---
# <a name="service-fabric-programming-model-overview"></a>Přehled programovacího modelu služby Service Fabric

Service Fabric nabízí několik způsobů, jak psát a spravovat vaše služby. Služby mohou používat rozhraní Service Fabric API k plnému využití funkcí platformy a architektur aplikací. Služby může být také spustitelný spustitelný program napsaný v jakémkoli jazyce nebo kódu spuštěném v kontejneru hostovaném na Service Fabricm clusteru.

## <a name="guest-executables"></a>Spustitelné soubory typu Host

[Spustitelný soubor hosta](service-fabric-guest-executables-introduction.md) je existující libovolný spustitelný soubor (napsaný v jakémkoli jazyce), který je možné spustit jako službu ve vaší aplikaci. Spustitelné soubory hosta přímo nevolají rozhraní API sady Service Fabric SDK. Stále však využívají funkce, které nabízí platforma, jako je například zjistitelnost služeb, vlastní stav a generování sestav voláním rozhraní REST API vystavených Service Fabric. Mají také úplnou podporu životního cyklu aplikací.

Začněte se spustitelnými soubory hosta nasazením první [spustitelné aplikace hosta](service-fabric-deploy-existing-app.md).

## <a name="containers"></a>Containers

Ve výchozím nastavení Service Fabric nasadí a aktivuje služby jako procesy. Service Fabric mohou také nasazovat služby v [kontejnerech](service-fabric-containers-overview.md). Service Fabric podporuje nasazení kontejnerů Linux a kontejnerů Windows ve Windows serveru 2016 a novějších verzích. Image kontejnerů se dají z libovolného úložiště kontejnerů načíst a nasadit do počítače. Stávající aplikace můžete nasadit jako spustitelné soubory typu Host, Service Fabric spolehlivé nebo stavové služby nebo Reliable Actors v kontejnerech a můžete kombinovat služby v procesech a službách v kontejnerech ve stejné aplikaci.

[Další informace o uzavření vašich služeb v systému Windows nebo Linux](./service-fabric-get-started-containers.md)

## <a name="reliable-services"></a>Reliable Services

Reliable Services je nevýznamová architektura pro psaní služeb, které se integrují s Service Fabricou platformou a využívají kompletní sadu funkcí platformy. Reliable Services poskytují minimální sadu rozhraní API, která umožňuje modulu runtime Service Fabric spravovat životní cyklus vašich služeb a umožňuje vašim službám pracovat s modulem runtime. Aplikační rozhraní je minimální a poskytuje plnou kontrolu nad návrhem a implementacemi a lze ji použít k hostování libovolné jiné aplikační architektury, například ASP.NET Core.

Reliable Services může být Bezstavová, podobně jako u většiny platforem služeb, jako jsou například webové servery, ve kterých je každá instance služby vytvořena stejně a stav je trvalý v externím řešení, jako je například Azure DB nebo Azure Table Storage.

Ve výhradním Service Fabric lze také Reliable Services stav, kde je stav uložen přímo ve službě samotné pomocí spolehlivých kolekcí. Stav je vysoce dostupný prostřednictvím replikace a distribuuje se prostřednictvím dělení, a to vše spravované automaticky pomocí Service Fabric.

[Přečtěte si další informace o Reliable Services](service-fabric-reliable-services-introduction.md) nebo Začněte vytvořením [první spolehlivé služby](service-fabric-reliable-services-quick-start.md).

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core je open source architektura pro různé platformy pro vytváření moderních cloudových aplikací připojených k Internetu, jako jsou webové aplikace, aplikace IoT a mobilní back-endy. Service Fabric se integruje s ASP.NET Core, takže můžete napsat bezstavové a stavové ASP.NET Core aplikace, které využívají spolehlivé kolekce a možnosti pokročilé orchestrace Service Fabric.

[Přečtěte si další informace o ASP.NET Core v Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) nebo začněte tím, že [napíšete první ASP.NET Core Service Fabric aplikaci](service-fabric-tutorial-create-dotnet-app.md).

## <a name="reliable-actors"></a>Reliable Actors

Technologie Reliable Actors, která je postavená na Reliable Services, je aplikační architektura, která implementuje vzor [virtuálního objektu actor](https://research.microsoft.com/en-us/projects/orleans/) na základě modelu výpočtu objektu [actor](https://en.wikipedia.org/wiki/Actor_model). Rozhraní Reliable actor používá nezávislé jednotky COMPUTE a State s jedním vláknovým spouštěním nazvaným *Actors*. Rozhraní Reliable Actors poskytuje vestavěnou komunikaci pro objekty actor a nastavení trvalosti a trvalého stavu a škálování na více systémů.

Vzhledem k tomu, že Reliable Actors je aplikační architektura postavená na Reliable Services, je plně integrovaná s platformou Service Fabric a výhodami z kompletní sady funkcí nabízených platformou.

[Přečtěte si další informace o Reliable Actors](service-fabric-reliable-actors-introduction.md) nebo Začněte vytvořením [první služby Reliable actor](service-fabric-reliable-actors-get-started.md) .

[Sestavení front-endové služby pomocí ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>Další kroky

[Přehled Service Fabric a kontejnerů](service-fabric-containers-overview.md)

[Přehled Reliable Services](service-fabric-reliable-services-introduction.md)

[Přehled Reliable Actors](service-fabric-reliable-actors-introduction.md)

[Service Fabric a ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)
