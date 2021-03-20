---
title: Upgrade samostatného clusteru Azure Service Fabric
description: Přečtěte si informace o upgradu verze nebo konfigurace samostatného clusteru Azure Service Fabric.  T
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 519a9163d16fda2dd9fcf49cf22fe4ad4a272e09
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260983"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>Upgrade a aktualizace samostatného clusteru Service Fabric

Pro jakýkoliv moderní systém je pro zajištění dlouhodobé úspěšnosti produktu navržený návrh pro zajištění náročnosti. Samostatný cluster Azure Service Fabric je prostředek, který vlastníte. Tento článek popisuje, co je možné upgradovat nebo aktualizovat.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Řízení verze prostředků infrastruktury, která běží na vašem clusteru
Ujistěte se, že cluster vždy používá [podporovanou verzi Service Fabric](service-fabric-versions.md). Když společnost Microsoft oznamuje vydání nové verze Service Fabric, bude předchozí verze označena pro konec podpory po nejméně 60 dní od data oznámení. Nové verze jsou oznámeny [na blogu týmu Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric). Nová verze je dostupná k výběru v tomto okamžiku.

Cluster můžete nastavit tak, aby přijímal automatické upgrady prostředků infrastruktury, když jsou vydané společností Microsoft, nebo můžete ručně vybrat podporovanou verzi prostředků infrastruktury, na které má být cluster zapnutý. Další informace najdete v článku [Upgrade verze Service Fabric, která běží na vašem clusteru](service-fabric-cluster-upgrade-windows-server.md).

## <a name="customize-configuration-settings"></a>Přizpůsobení nastavení konfigurace

V souboru *ClusterConfig.js* se dá nastavit mnoho různých [nastavení konfigurace](service-fabric-cluster-manifest.md) , jako je například úroveň spolehlivosti clusteru a vlastností uzlu.  Pokud se chcete dozvědět víc, přečtěte si téma [Upgrade konfigurace samostatného clusteru](service-fabric-cluster-config-upgrade-windows-server.md).  Mnoho dalších, pokročilejších nastavení, je také možné přizpůsobit.  Další informace najdete v tématu [Service Fabric nastavení prostředků infrastruktury clusteru](service-fabric-cluster-fabric-settings.md).

## <a name="define-node-properties"></a>Definovat vlastnosti uzlu
Někdy možná budete chtít zajistit, aby některé úlohy běžely jenom na určitých typech uzlů v clusteru. Některé úlohy můžou například vyžadovat GPU nebo SSD, zatímco jiné nemusí. Pro každý typ uzlu v clusteru můžete přidat vlastní vlastnosti uzlu do uzlů clusteru. Omezení umístění jsou příkazy připojené k jednotlivým službám, které jsou vybrané pro jednu nebo více vlastností uzlu. Omezení umístění definují, kde by měly služby běžet.

Podrobnosti o použití omezení umístění, vlastnostech uzlů a způsobu jejich definování, čtení [vlastností uzlu a omezení umístění](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).
 

## <a name="add-capacity-metrics"></a>Přidat metriky kapacity
Pro každý typ uzlu můžete přidat vlastní metriky kapacity, které chcete použít ve svých aplikacích, abyste nahlásili zatížení. Podrobnosti o použití metriky kapacity k načtení sestav najdete v tématu Service Fabric clusteru Správce prostředků dokumentu [popisujícího cluster](service-fabric-cluster-resource-manager-cluster-description.md) a [metriky a zatížení](service-fabric-cluster-resource-manager-metrics.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Oprava operačního systému v uzlech clusteru
Aplikace orchestrace oprav (POA) je Service Fabric aplikace, která automatizuje opravy operačního systému v clusteru Service Fabric bez výpadků. [Aplikace orchestrace oprav pro Windows](service-fabric-patch-orchestration-application.md) se dá nasadit do clusteru a instalovat opravy do Orchestrace a zároveň přitom zachovat dostupné služby. 


## <a name="next-steps"></a>Další kroky
* Informace o tom, jak přizpůsobit některá [nastavení prostředků infrastruktury Service Fabric pro clustery](service-fabric-cluster-fabric-settings.md)
* Přečtěte si, jak [škálovat cluster na úrovni a ven](service-fabric-cluster-scale-in-out.md) .
* Další informace o [upgradech aplikací](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
