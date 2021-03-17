---
title: Aplikační model Azure Service Fabric
description: Jak modelovat a popsat aplikace a služby v Azure Service Fabric pomocí souborů manifestu aplikace a služby.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 84e6b2309fdb206771d4ea01aa03c7f355d6ff19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85963729"
---
# <a name="model-an-application-in-service-fabric"></a>Modelování aplikace v Service Fabric
Tento článek poskytuje přehled aplikačního modelu Azure Service Fabric a definování aplikace a služby prostřednictvím souborů manifestu.

## <a name="understand-the-application-model"></a>Pochopení aplikačního modelu
Aplikace je kolekce základních služeb, které provádějí určitou funkci nebo funkce. Služba provádí úplnou a samostatnou funkci a může spustit a spustit nezávisle na jiných službách.  Služba se skládá z kódu, konfigurace a dat. U každé služby se kód skládá ze spustitelných binárních souborů, konfigurace se skládá z nastavení služby, které lze načíst za běhu, a data se skládají z libovolných statických dat, která má služba spotřebovat. Jednotlivé komponenty v tomto modelu hierarchické aplikace lze zavažovat a upgradovat nezávisle.

![Model aplikace Service Fabric][appmodel-diagram]

Typ aplikace je kategorizace aplikace a sestává ze sady typů služeb. Typ služby je kategorizace služby. Kategorizace může mít různá nastavení a konfigurace, ale základní funkce zůstanou stejné. Instance služby jsou odlišné varianty konfigurace služby pro stejný typ služby.  

Třídy (nebo "typy") aplikací a služeb jsou popsány prostřednictvím souborů XML (manifesty aplikací a manifesty služeb).  Manifesty popisují aplikace a služby a jsou šablony, u kterých je možné vytvářet instance aplikací z úložiště imagí clusteru.  Manifesty jsou podrobně popsány v [manifestech aplikací a služeb](service-fabric-application-and-service-manifests.md). Definice schématu pro ServiceManifest.xml a soubor ApplicationManifest.xml se instaluje s Service Fabric SDK a nástroji do složky *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*. Schéma XML je dokumentováno v [dokumentaci ke schématu ServiceFabricServiceModel. xsd](service-fabric-service-model-schema.md).

Kód pro různé instance aplikace běží jako samostatné procesy, i když hostuje stejný Service Fabric uzel. Kromě toho je možné nezávisle na životní cyklus každé instance aplikace spravovat (například upgradovat). Následující diagram znázorňuje, jak se typy aplikací skládají z typů služby, které jsou zase tvořeny kódem, konfigurací a balíčky dat. Pro zjednodušení diagramu se zobrazí jenom balíčky Code/config/data pro `ServiceType4` , i když každý typ služby obsahuje některé nebo všechny tyto typy balíčků.

![Service Fabric typů aplikací a typů služeb][cluster-imagestore-apptypes]

V clusteru může být aktivní jedna nebo víc instancí typu služby. Například stavové instance služby nebo repliky, které se replikují do stavu replikace mezi replikami umístěnými na různých uzlech v clusteru, dosahují vysoké spolehlivosti. Replikace v podstatě zajišťuje redundanci služby, která bude dostupná i v případě, že jeden uzel v clusteru selhává. [Dělená služba](service-fabric-concepts-partitioning.md) dále rozděluje svůj stav (a přistupuje ke vzorům tohoto stavu) napříč uzly v clusteru.

Následující diagram znázorňuje vztah mezi aplikacemi a instancemi služby, oddíly a replikami.

![Oddíly a repliky v rámci služby][cluster-application-instances]

> [!TIP]
> Rozložení aplikací v clusteru můžete zobrazit pomocí nástroje Service Fabric Explorer, který je k dispozici na adrese http:// &lt; yourclusteraddress &gt; : 19080/Explorer. Další informace najdete v tématu [Vizualizace clusteru pomocí Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
> 
> 


## <a name="next-steps"></a>Další kroky
- Přečtěte si o [škálovatelnosti aplikací](service-fabric-concepts-scalability.md).
- Seznamte se s informacemi o [stavu](service-fabric-concepts-state.md)služby, [dělení](service-fabric-concepts-partitioning.md)a [dostupnosti](service-fabric-availability-services.md).
- Přečtěte si o tom, jak jsou aplikace a služby definované v [manifestech aplikací a služeb](service-fabric-application-and-service-manifests.md).
- [Modely hostování aplikací](service-fabric-hosting-model.md) popisují vztah mezi replikami (nebo instancemi) nasazené služby a procesu hostitele služby.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


