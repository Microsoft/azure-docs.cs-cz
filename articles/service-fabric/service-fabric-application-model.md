---
title: Model aplikace Azure Service Fabric
description: Jak modelovat a popisovat aplikace a služby v Azure Service Fabric pomocí souborů manifestu aplikací a služeb.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 7179686b7d4ef2df267cb95ece8f83d5fb7682b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551875"
---
# <a name="model-an-application-in-service-fabric"></a>Modelování aplikace v service fabric
Tento článek obsahuje přehled aplikačního modelu Azure Service Fabric a jak definovat aplikaci a službu prostřednictvím souborů manifestu.

## <a name="understand-the-application-model"></a>Principy aplikačního modelu
Aplikace je kolekce základních služeb, které vykonávají určitou funkci nebo funkce. Služba provádí úplnou a samostatnou funkci a může být spuštěna a spuštěna nezávisle na jiných službách.  Služba se skládá z kódu, konfigurace a dat. Pro každou službu se kód skládá z spustitelných binárních souborů, konfigurace se skládá z nastavení služby, které lze načíst za běhu, a data se skládají z libovolných statických dat, která má služba spotřebovávat. Každá součást v tomto hierarchickém aplikačním modelu může být verzí a upgradována nezávisle.

![Model aplikace Service Fabric][appmodel-diagram]

Typ aplikace je kategorizace aplikace a skládá se ze sady typů služeb. Typ služby je kategorizace služby. Kategorizace může mít různá nastavení a konfigurace, ale základní funkce zůstává stejná. Instance služby jsou různé varianty konfigurace služby stejného typu služby.  

Třídy (nebo "typy") aplikací a služeb jsou popsány prostřednictvím souborů XML (manifesty aplikací a manifesty služeb).  Manifesty popisují aplikace a služby a jsou šablony, proti kterým lze vytvořit instanci aplikací z úložiště bitových obrázků clusteru.  Manifesty jsou podrobně popsány v [manifestech aplikace a služby](service-fabric-application-and-service-manifests.md). Definice schématu pro soubor ServiceManifest.xml a ApplicationManifest.xml je nainstalována se sadou Service Fabric SDK a nástroji na *C:\Program Files\Microsoft SDKs\Service Fabric\Schemas\ServiceFabricServiceModel.xsd*. Schéma XML je popsáno v [dokumentaci ke schématu ServiceFabricServiceModel.xsd](service-fabric-service-model-schema.md).

Kód pro různé instance aplikace spustit jako samostatné procesy i v případě, že hostované stejným uzlink service fabric. Kromě toho životní cyklus každé instance aplikace lze spravovat (například upgradovat) nezávisle. Následující diagram znázorňuje, jak se typy aplikací skládají z typů služeb, které se zase skládají z balíčků kódu, konfigurace a dat. Pro zjednodušení diagramu `ServiceType4` jsou zobrazeny pouze balíčky kódu/konfigurace/dat, ačkoli každý typ služby by zahrnoval některé nebo všechny tyto typy balíčků.

![Typy aplikací Service Fabric a typy služeb][cluster-imagestore-apptypes]

V clusteru může být aktivní jedna nebo více instancí typu služby. Například instance stavové služby nebo repliky dosahují vysoké spolehlivosti replikací stavu mezi replikami umístěnými na různých uzlech v clusteru. Replikace v podstatě poskytuje redundanci pro službu, která má být k dispozici i v případě, že jeden uzel v clusteru selže. [Dělená služba](service-fabric-concepts-partitioning.md) dále rozděluje svůj stav (a vzory přístupu k tomuto stavu) mezi uzly v clusteru.

Následující diagram znázorňuje vztah mezi aplikacemi a instancemi služby, oddíly a replikami.

![Oddíly a repliky v rámci služby][cluster-application-instances]

> [!TIP]
> Rozložení aplikací v clusteru můžete zobrazit pomocí nástroje Service&lt;Fabric Explorer, který je k dispozici na adrese http:// adresu&gt;clusteru :19080/Explorer. Další informace naleznete v [tématu Vizualizace clusteru pomocí aplikace Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
> 
> 


## <a name="next-steps"></a>Další kroky
- Informace o [škálovatelnosti aplikací](service-fabric-concepts-scalability.md).
- Informace o [stavu](service-fabric-concepts-state.md)služby , [dělení](service-fabric-concepts-partitioning.md)a [dostupnosti](service-fabric-availability-services.md).
- Přečtěte si o tom, jak jsou definovány aplikace a služby v [manifestech aplikací a služeb](service-fabric-application-and-service-manifests.md).
- [Modely hostování aplikací](service-fabric-hosting-model.md) popisují vztah mezi replikami (nebo instancemi) nasazené služby a procesem hostitele služby.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


