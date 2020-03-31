---
title: Upgrade samostatného clusteru Azure Service Fabric
description: Přečtěte si o upgradu verze nebo konfigurace samostatného clusteru Azure Service Fabric.  T
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 6da9b4c6890895141ecc419382f05f667614fb31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451830"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>Inovace a aktualizace samostatného clusteru Service Fabric

Pro každý moderní systém je návrh upgradability klíčem k dosažení dlouhodobého úspěchu vašeho produktu. Samostatný cluster Azure Service Fabric je prostředek, který vlastníte. Tento článek popisuje, co lze upgradovat nebo aktualizovat.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Řízení verze prostředků infrastruktury, která běží v clusteru
Ujistěte se, že váš cluster vždy běží [podporovanou verzi Service Fabric](service-fabric-versions.md). Když společnost Microsoft oznámí vydání nové verze Service Fabric, předchozí verze je označena pro ukončení podpory po minimálně 60 dnů od data oznámení. Nové verze jsou oznámeny [na blogu týmu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Nová verze je k dispozici na výběr v tomto bodě.

Můžete nastavit cluster přijímat automatické upgrady prostředků infrastruktury, jak jsou vydány společností Microsoft nebo můžete ručně vybrat podporovanou verzi prostředků infrastruktury, kterou chcete, aby byl cluster zapnutý. Další informace naleznete [v části Upgrade verze Service Fabric, která je spuštěna v clusteru](service-fabric-cluster-upgrade-windows-server.md).

## <a name="customize-configuration-settings"></a>Přizpůsobení nastavení konfigurace

V souboru *ClusterConfig.json* lze nastavit mnoho různých [nastavení konfigurace,](service-fabric-cluster-manifest.md) například úroveň spolehlivosti vlastností clusteru a uzlu.  Další informace najdete v části [Upgrade konfigurace samostatného clusteru](service-fabric-cluster-config-upgrade-windows-server.md).  Mnoho dalších, pokročilejší, nastavení lze také přizpůsobit.  Další informace naleznete v [části Nastavení clusteru Service Fabric](service-fabric-cluster-fabric-settings.md).

## <a name="define-node-properties"></a>Definování vlastností uzlu
Někdy můžete chtít zajistit, že určité úlohy spustit pouze na určité typy uzlů v clusteru. Některé úlohy mohou například vyžadovat gpu nebo ssd disy, zatímco jiné nemusí. Pro každý z typů uzlů v clusteru můžete do uzlů clusteru přidat vlastní vlastnosti uzlu. Omezení umístění jsou příkazy připojené k jednotlivým službám, které vybírají pro jeden nebo více vlastností uzlu. Omezení umístění definují, kde by měly být služby spuštěny.

Podrobnosti o použití omezení umístění, vlastnosti uzlu a jejich definování, přečtěte si [vlastnosti uzlu a omezení umístění](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).
 

## <a name="add-capacity-metrics"></a>Přidání metrik kapacity
Pro každý z typů uzlů můžete přidat vlastní metriky kapacity, které chcete použít ve vašich aplikacích k načtení sestavy. Podrobnosti o použití metrik kapacity pro načtení sestavy naleznete v dokumentech Správce prostředků clusteru Service Fabric v [popisu clusteru](service-fabric-cluster-resource-manager-cluster-description.md) a [metriky a načíst](service-fabric-cluster-resource-manager-metrics.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Oprava operačního systému v uzlech clusteru
Aplikace orchestrace oprav (POA) je aplikace Service Fabric, která automatizuje opravy operačního systému v clusteru Service Fabric bez prostojů. [Aplikace Orchestrace oprav pro Windows](service-fabric-patch-orchestration-application.md) lze nasadit do clusteru k instalaci oprav orchestrovaným způsobem při zachování služby k dispozici po celou dobu. 


## <a name="next-steps"></a>Další kroky
* Zjistěte, jak přizpůsobit některá [nastavení clusteru prostředků infrastruktury prostředků služby](service-fabric-cluster-fabric-settings.md)
* Přečtěte si, jak [škálovat cluster y a zmenšování](service-fabric-cluster-scale-up-down.md)
* Informace o [upgradech aplikací](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
