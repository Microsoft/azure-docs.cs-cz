---
title: Model aplikace Azure Service Fabric | Dokumentace Microsoftu
description: Jak pro modelování a popisují aplikace a služby v Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: 750970233cbcb14d901dbb5fa94f649f6ff8ae6c
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666269"
---
# <a name="model-an-application-in-service-fabric"></a>Modelování aplikace v Service Fabric
Tento článek obsahuje přehled aplikačního modelu Azure Service Fabric a jak definovat aplikace a služby přes soubory manifestu.

## <a name="understand-the-application-model"></a>Pochopení aplikačního modelu
Aplikace je kolekce základních služeb, které provádějí určité funkce nebo funkce. Služba provádí kompletní a samostatné funkce a můžete spustit a spustit nezávisle na ostatních službách.  Služba se skládá z kódu, konfigurace a data. Pro každou službu kód se skládá ze spustitelného souboru binární soubory, konfigurace se skládá z nastavení služby, které načtené být za běhu a data se skládají z libovolného statických dat, který se má používat služba. Jednotlivé komponenty v tomto modelu hierarchické aplikace může být označovat verzí a upgradovat nezávisle na sobě.

![Aplikační model Service Fabric][appmodel-diagram]

Typ aplikace se kategorizace aplikace a se skládá ze sady typů služeb. Typ služby je kategorizace služby. Kategorii mohou mít různá nastavení a konfigurace, ale základní funkce zůstala stejná. Instance služby jsou jiné služby variacím konfigurace stejného typu služby.  

Třídy (nebo "typy") aplikací a služeb, které jsou popsány pomocí souborů XML (manifesty aplikace a manifesty služby).  Manifesty popisují aplikací a služeb a jsou šablony, u kterých aplikací může být vytvořena z úložiště imagí clusteru.  Manifesty je podrobně popsána v [manifestů služby a aplikace](service-fabric-application-and-service-manifests.md). Definice schématu pro soubor ServiceManifest.xml a ApplicationManifest.xml se instaluje se sadou Service Fabric SDK a nástrojů k *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*. Schéma XML je popsána v [dokumentace schématu ServiceFabricServiceModel.xsd](service-fabric-service-model-schema.md).

Kód pro různé instance aplikace spouštět jako samostatné procesy, i když jsou hostované ve stejném uzlu Service Fabric. Kromě toho je možné spravovat životní cyklus jednotlivých instancí aplikací (například upgradovat) nezávisle na sobě. Následující diagram znázorňuje, jak typy aplikací se skládají z typů služby, které zase se skládají z kódu, konfigurace a balíčky data. Pro zjednodušení diagramu, pouze kód/config/data balíčky `ServiceType4` jsou zobrazeny, i když každý typ služby bude zahrnovat některé nebo všechny tyto typy balíčků.

![Typy aplikací Service Fabric a typy služeb][cluster-imagestore-apptypes]

Aktivní v clusteru může být jeden nebo víc instancí typu služby. Například stavové služby instancí nebo replik, dosáhnout tím, že stav replikace replik, které jsou umístěné na různých uzlech v clusteru vysokou spolehlivostí. Replikace v podstatě zajišťuje redundanci pro službu k dispozici i v případě, že selže jeden uzel v clusteru. A [rozdělit na oddíly služby](service-fabric-concepts-partitioning.md) další rozděluje jeho stav (a vzory přístupu k tomuto státu) napříč uzly v clusteru.

Následující diagram znázorňuje vztah mezi aplikacemi a instancemi služby, oddíly a repliky.

![Oddílů a replik v rámci služby][cluster-application-instances]

> [!TIP]
> Zobrazí se rozložení aplikace v clusteru pomocí nástroje Service Fabric Explorer k dispozici na http://&lt;yourclusteraddress&gt;: 19080/Explorer. Další informace najdete v tématu [vizualizace vašeho clusteru pomocí Service Fabric Exploreru](service-fabric-visualizing-your-cluster.md).
> 
> 


## <a name="next-steps"></a>Další postup
- Další informace o [aplikace škálovatelnost](service-fabric-concepts-scalability.md).
- Další informace o službě [stavu](service-fabric-concepts-state.md), [dělení](service-fabric-concepts-partitioning.md), a [dostupnosti](service-fabric-availability-services.md).
- Přečtěte si informace o tom, jak aplikace a služby jsou definovány v [manifestů služby a aplikace](service-fabric-application-and-service-manifests.md).
- [Modely pro hostování aplikací](service-fabric-hosting-model.md) popisují vztah mezi repliky (nebo instance) nasazená služba a služba hostitelský proces.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


