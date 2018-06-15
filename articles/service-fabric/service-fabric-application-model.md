---
title: Model aplikace Azure Service Fabric | Microsoft Docs
description: Jak modelu a popisují aplikace a služby v Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: ryanwi
ms.openlocfilehash: dd453f393620923041eb8fa07b551f4945f48235
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204630"
---
# <a name="model-an-application-in-service-fabric"></a>Model aplikace v Service Fabric
Tento článek obsahuje přehled aplikačního modelu Azure Service Fabric a jak definovat aplikace a služby pomocí souborů manifestu.

## <a name="understand-the-application-model"></a>Pochopení aplikačního modelu
Aplikace je kolekce základních služeb, které provádějí určité funkce nebo funkce. Služba provede kompletní a samostatné funkce a začít a spustit nezávisle na jiné služby.  Služba se skládá z kódu, konfigurace a data. Pro každou službu kódu se skládá z spustitelné binární soubory, konfigurace se skládá z nastavení služby, které je možné načíst v době běhu a data se skládá z libovolné statických dat, který se má používat služba. Jednotlivé komponenty v tomto modelu hierarchické aplikace může být verzí a upgradovali nezávisle.

![Aplikační model Service Fabric][appmodel-diagram]

Typ aplikace kategorizaci aplikace a skládá se z sady typy služeb. Typ služby je kategorizaci služby. Kategorizaci podle služby může mít různá nastavení a konfigurace, ale základní funkce zůstává stejná. Instance služby jsou variacím konfigurace jinou službu stejného typu služby.  

Třídy (nebo "typů") aplikací a služeb, které jsou popsané prostřednictvím souborů XML (manifestů aplikace a služby manifesty).  Manifesty popisují aplikace a služby a šablony, kterými může být aplikace vytvořena z úložiště bitových kopií clusteru.  Manifesty jsou podrobně popsány v [služby manifestů aplikace a](service-fabric-application-and-service-manifests.md). Definice schématu pro soubor ServiceManifest.xml a ApplicationManifest.xml je nainstalován pomocí Service Fabric SDK a nástroje k *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*. Schéma XML je popsána v [dokumentace schématu ServiceFabricServiceModel.xsd](service-fabric-service-model-schema.md).

Kód pro jinou aplikaci instance spustit jako samostatné procesy, i když jsou hostované ve stejném uzlu Service Fabric. Kromě toho je možné spravovat životní cyklus každá instance aplikace (například upgradovat) nezávisle. Následující diagram znázorňuje, jak typy aplikací se skládají z typů služeb, které pak se skládají z kódu, konfigurace a data balíčky. Pro zjednodušení diagramu, jenom kód/config/data balíčky pro `ServiceType4` se zobrazují, když každý typ služby by mělo zahrnovat některé nebo všechny ty typy balíčků.

![Typy aplikací Service Fabric a typů služeb][cluster-imagestore-apptypes]

Aktivní v clusteru může být jeden nebo více instancí typu služby. Například instance stavové služby nebo repliky, dosáhnout vysokou spolehlivostí nastavením stavu replikace mezi replikami, které jsou umístěné na různých uzlech v clusteru. Replikace v podstatě poskytuje redundanci pro službu být k dispozici i v případě selhání jednoho uzlu v clusteru. A [oddíly služby](service-fabric-concepts-partitioning.md) další rozdělí stavu (a vzory přístupu na tento stav) mezi uzly v clusteru.

Následující diagram znázorňuje vztah mezi aplikací a instance služby, oddíly a repliky.

![Oddíly a repliky v rámci služby][cluster-application-instances]

> [!TIP]
> Rozložení aplikace si můžete prohlédnout v clusteru s podporou pomocí nástroje Service Fabric Explorer k dispozici v http://&lt;yourclusteraddress&gt;: 19080/Explorer. Další informace najdete v tématu [vizualizace vašeho clusteru pomocí Service Fabric Exploreru](service-fabric-visualizing-your-cluster.md).
> 
> 


## <a name="next-steps"></a>Další postup
- Další informace o [škálovatelnost aplikace](service-fabric-concepts-scalability.md).
- Další informace o službě [stavu](service-fabric-concepts-state.md), [dělení](service-fabric-concepts-partitioning.md), a [dostupnosti](service-fabric-availability-services.md).
- Přečtěte si informace o tom, jak jsou definovány aplikací a služeb v [služby manifestů aplikace a](service-fabric-application-and-service-manifests.md).
- [Aplikace hostující modely](service-fabric-hosting-model.md) popisují vztah mezi nasazená služba a služba hostitelský proces repliky (nebo instance).

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


