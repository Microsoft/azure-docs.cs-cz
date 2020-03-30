---
title: Přehled programovacího modelu služby Service Fabric
description: 'Service Fabric nabízí dva rámce pro vytváření služeb: objekt actor framework a rozhraní služby. Nabízejí odlišné kompromisy v jednoduchosti a kontrole.'
author: vturecek
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: 11e32c9d1290227e638a314ed8417b1bed906842
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749530"
---
# <a name="service-fabric-programming-model-overview"></a>Přehled programovacího modelu služby Service Fabric

Service Fabric nabízí několik způsobů, jak psát a spravovat své služby. Služby můžete použít rozhraní API Service Fabric plně využít funkce platformy a rozhraní aplikace. Služby mohou být také libovolný kompilovaný spustitelný program napsaný v libovolném jazyce nebo kód spuštěný v kontejneru hostovaném v clusteru Service Fabric.

## <a name="guest-executables"></a>Spustitelné soubory typu Host

[Spustitelný soubor hosta](service-fabric-guest-executables-introduction.md) je existující, libovolný spustitelný soubor (napsaný v libovolném jazyce), který lze spustit jako službu ve vaší aplikaci. Spustitelné soubory hosta nevolají přímo na api sady Service Fabric SDK. Ale stále těžit z funkcí platformy nabízí, jako je například služby zjistitelnost, vlastní stav a vykazování zatížení voláním REST API vystavené Service Fabric. Mají také plnou podporu životního cyklu aplikace.

S spustitelnými soubory hosta můžete začít nasazením první [spustitelné aplikace hosta](service-fabric-deploy-existing-app.md).

## <a name="containers"></a>Kontejnery

Service Fabric ve výchozím nastavení nasazuje a aktivuje služby jako procesy. Service Fabric můžete také nasadit služby v [kontejnerech](service-fabric-containers-overview.md). Service Fabric podporuje nasazení linuxových kontejnerů a kontejnerů Windows na Windows Server 2016 a novějších. Image kontejneru lze navádět z libovolného úložiště kontejnerů a nasadit do počítače. Můžete nasadit existující aplikace jako host spustitelné soubory, Service Fabric bezstavové nebo stavové spolehlivé služby nebo spolehlivé objekty actor v kontejnerech a můžete kombinovat služby v procesech a službách v kontejnerech ve stejné aplikaci.

[Další informace o kontejnerování služeb ve Windows nebo Linuxu](service-fabric-deploy-container.md)

## <a name="reliable-services"></a>Reliable Services

Spolehlivé služby je lehký rámec pro psaní služeb, které se integrují s platformou Service Fabric a těží z celé sady funkcí platformy. Spolehlivé služby poskytují minimální sadu api, které umožňují service fabric runtime spravovat životní cyklus vašich služeb a které umožňují vaše služby pro interakci s runtime. Aplikační rámec je minimální, což vám dává plnou kontrolu nad návrhem a implementací a lze jej použít k hostování jakéhokoli jiného rozhraní aplikace, například ASP.NET Core.

Spolehlivé služby mohou být bezstavové, podobně jako většina platforem služeb, jako jsou webové servery, ve kterých je každá instance služby vytvořena stejně a stav je trvalý v externím řešení, jako je Azure DB nebo Azure Table Storage.

Výhradní service fabric, spolehlivé služby může být také stavové, kde stav je trvalý přímo v samotné službě pomocí spolehlivé kolekce. Stav je vysoce dostupný prostřednictvím replikace a distribuován prostřednictvím dělení, vše automaticky spravované service fabric.

[Přečtěte si další informace o spolehlivých službách](service-fabric-reliable-services-introduction.md) nebo můžete začít [napíšete svou první spolehlivou službu](service-fabric-reliable-services-quick-start.md).

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core je open source, multiplatformní framework pro vytváření moderních cloudových aplikací připojených k internetu, jako jsou webové aplikace, aplikace IoT a mobilní back-endy. Service Fabric integruje s ASP.NET Core, takže můžete psát bezstavové a stavové aplikace ASP.NET core, které využívají spolehlivé kolekce a service fabric pokročilé možnosti orchestrace.

[Další informace o ASP.NET Core v Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) nebo začít [písemně první ASP.NET aplikace Core Service Fabric](service-fabric-tutorial-create-dotnet-app.md).

## <a name="reliable-actors"></a>Reliable Actors

Postaveno na nad rámec spolehlivé služby, architektura reliable actor je rozhraní aplikace, které implementuje virtuální [objekt actor](https://research.microsoft.com/en-us/projects/orleans/) vzor, na základě výpočetní objekt [actor modelu](https://en.wikipedia.org/wiki/Actor_model). Architektura Reliable Actor používá nezávislé jednotky výpočetních prostředků a stavu s jednovláknovým spuštěním nazývaným *objekty actor*. Architektura reliable actor poskytuje integrovanou komunikaci pro objekty actor a přednastavené konfigurace trvalosti stavu a horizontálního navýšení kapacity.

Vzhledem k tomu, že reliable actors je aplikační architektura založená na spolehlivých službách, je plně integrována s platformou Service Fabric a těží z celé sady funkcí nabízených platformou.

[Další informace o spolehlivých aktérech](service-fabric-reliable-actors-introduction.md) nebo můžete začít [psaním své první služby Reliable Actor](service-fabric-reliable-actors-get-started.md)

[Vytvoření front-endové služby pomocí ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>Další kroky

[Přehled servisních prostředků a kontejnerů](service-fabric-containers-overview.md)

[Přehled Reliable Services](service-fabric-reliable-services-introduction.md)

[Přehled Reliable Actors](service-fabric-reliable-actors-introduction.md)

[Service Fabric a ASP.NET jádro](service-fabric-reliable-services-communication-aspnetcore.md)
