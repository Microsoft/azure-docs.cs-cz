---
title: Přehled programovacího modelu Service Fabric | Dokumentace Microsoftu
description: 'Service Fabric nabízí dvě architektury pro vytváření služeb: rozhraní objektu actor a rozhraní služby. Nabízejí různé kompromisy v jednoduchost a řízení.'
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: vturecek
ms.assetid: 974b2614-014e-4587-a947-28fcef28b382
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: f37e1ed2c7dd720e4a77076c8587fdf540b29ca5
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713948"
---
# <a name="service-fabric-programming-model-overview"></a>Přehled programovacího modelu Service Fabric
Service Fabric nabízí několik způsobů, jak zapisovat a spravovat vaše služby. Služby můžete zvolit použití rozhraní API služby Service Fabric plně využít funkce platformy a aplikace rozhraní. Služby mohou být také všechny zkompilovaný spustitelný program napsané v libovolném jazyce nebo kód spuštěný v kontejneru hostovaná v clusteru Service Fabric.

## <a name="guest-executables"></a>Spustitelné soubory typu Host
A [spustitelný soubor typu Host](service-fabric-guest-executables-introduction.md) je existující libovolný spustitelný (napsané v libovolném jazyce), který může běžet jako služba ve vaší aplikaci. Spustitelné soubory typu Host přímo volat rozhraní API služby Service Fabric SDK. Ale jsou stále využívat funkce, které platforma nabízí, jako je například služba zjistitelnost, vlastních stavových a načíst reporting voláním rozhraní API REST vystavené Service Fabric. Mají také podporu životního cyklu celou aplikaci.

Začínáme s spustitelné soubory hosta nasazením první [aplikace spustitelná hostem](service-fabric-deploy-existing-app.md).

## <a name="containers"></a>Containers
Ve výchozím nastavení Service Fabric nasadí a aktivuje služby jako procesy. Service Fabric dokáže nasadit také služby v [kontejnery](service-fabric-containers-overview.md). Service Fabric podporuje nasazování kontejnerů Linuxu a kontejnery Windows ve Windows serveru 2016. Image kontejneru můžete získaných z jakékoli úložiště kontejnerů a nasadili do počítače. Stávající aplikace můžete nasadit jako spustitelných souborů hosta a Bezstavová nebo stavová spolehlivých služeb Service Fabric Reliable Actors v kontejnerech a je možné kombinovat služby v procesech a služby v kontejnerech ve stejné aplikaci.

[Další informace o uzavření do kontejneru služby ve Windows nebo Linux](service-fabric-deploy-container.md)

## <a name="reliable-services"></a>Reliable Services
Reliable Services je architektura nižšími nároky pro zápis služby, které integrace s platformou Service Fabric a využívat výhod celé sady funkcí platformy. Reliable Services poskytují minimální sadu rozhraní API, která umožňují modulu runtime Service Fabric pro správu životního cyklu služeb a, která umožňují vašich služeb k interakci s modulem runtime. Aplikační framework je minimální, získáte plnou kontrolu nad možností návrhu a implementace a je možné k hostování všechny ostatní aplikační platformy, jako je ASP.NET Core.

Reliable Services může být bezstavové, podobně jako na většině platforem služby, jako jsou třeba webové servery, ve kterých každá instance služby je vytvářeny na stejné úrovni a stát se ukládají v externí řešení, jako je Azure DB nebo Azure Table Storage.

Reliable Services může být také stavové, výhradně pro Service Fabric, ve kterém je trvalý stav přímo v službu samotnou použitím spolehlivých kolekcí. Stav vytvoření vysoce dostupné prostřednictvím replikace a distribuovaných přes dělení, všechny spravované automaticky pomocí Service Fabric.

[Další informace o modelu Reliable Services](service-fabric-reliable-services-introduction.md) nebo začít tím, že [zápis první spolehlivé služby](service-fabric-reliable-services-quick-start.md).

## <a name="aspnet-core"></a>Jádro ASP.NET
ASP.NET Core je nové open-source a multiplatformní rozhraní pro vytváření moderních cloudových připojeného k Internetu aplikací, jako jsou webové aplikace, aplikace IoT a mobilních back-endů. Service Fabric se integruje s ASP.NET Core, ASP.NET Core bezstavových a stavových můžete psát aplikace, které budou využívat Reliable Collections a možnosti pokročilé Orchestrace Service Fabric.

[Další informace o ASP.NET Core v Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) nebo začít tím, že [zápis svou první aplikaci ASP.NET Core Service Fabric](service-fabric-tutorial-create-dotnet-app.md).

## <a name="reliable-actors"></a>Reliable Actors
Staví na modelu Reliable Services, Reliable Actors rozhraní framework je aplikační rozhraní, která implementuje vzoru virtuálního objektu Actor, podle návrhu vzor objektu actor. Reliable Actors rozhraní framework používá nezávislých jednotek výpočetního výkonu a stavu s názvem actors spuštění s jedním vláknem. Reliable Actors rozhraní poskytuje integrované komunikace pro objekty actor a přednastaveným stavu konfigurace trvalosti a horizontální navýšení kapacity.

Protože Reliable Actors je aplikační rozhraní založené na modelu Reliable Services, je plně integrovaná s platformou Service Fabric a výhody úplnou sadu funkcí nabízenými touto platformou.

[Další informace o Reliable Actors](service-fabric-reliable-actors-introduction.md) nebo začít tím, že [zápis první služby Reliable Actors](service-fabric-reliable-actors-get-started.md)


[Vytvoření front-endové služby pomocí ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>Další postup
[Přehled Service Fabric a kontejnery](service-fabric-containers-overview.md)

[Přehled Reliable Services](service-fabric-reliable-services-introduction.md)

[Přehled Reliable Actors](service-fabric-reliable-actors-introduction.md)

[Service Fabric a ASP.NET Core ](service-fabric-reliable-services-communication-aspnetcore.md)




