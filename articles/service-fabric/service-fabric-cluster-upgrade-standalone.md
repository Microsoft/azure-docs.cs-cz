---
title: Upgrade samostatného clusteru Azure Service Fabric | Dokumentace Microsoftu
description: Další informace o upgradu na verzi nebo konfiguraci samostatného clusteru Azure Service Fabric.  T
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: aljo
ms.openlocfilehash: 6f0ffac9ecf4d0c8f6c3dc7c57670b168417cd3a
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51855137"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>Upgrade a aktualizuje samostatného clusteru Service Fabric

Návrh pro zdokonalovány moderního systému, je klíčem k dosažení dlouhodobý úspěch vašeho produktu. Samostatný cluster Azure Service Fabric je prostředek, který vlastníte. Tento článek popisuje, co lze upgradovat nebo aktualizovat.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Řízení verze prostředků infrastruktury, na kterém běží v clusteru
Ujistěte se, že cluster běží vždy podporovanou verzi Service Fabric. Když Microsoft oznamuje vydání nové verze Service Fabric, předchozí verze budou označena k ukončení podpory po minimálně 60 dní od data oznámení. Oznámení nových vydaných verzích [na blog týmu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Si můžete vybrat v tomto okamžiku je nová verze.

Můžete nastavit pro příjem upgradů automatické prostředků infrastruktury, jako jsou vydané společností Microsoft nebo po ručním výběru verze podporovaných fabric, že chcete, aby váš cluster na clusteru. Další informace najdete v článku [Upgrade, na kterém běží ve vašem clusteru Service Fabric verze](service-fabric-cluster-upgrade-windows-server.md).

## <a name="customize-configuration-settings"></a>Přizpůsobení nastavení konfigurace

Mnoho různých [nastavení konfigurace](service-fabric-cluster-manifest.md) je možné nastavit v *ClusterConfig.json* souboru, například úroveň spolehlivosti clusteru a uzlů vlastností.  Další informace najdete v článku [upgradovat konfiguraci samostatného clusteru](service-fabric-cluster-config-upgrade-windows-server.md).  Mnoho dalších, rozšířené, nastavení se taky dají upravit.  Další informace najdete v článku [nastavení prostředků infrastruktury pro cluster Service Fabric](service-fabric-cluster-fabric-settings.md).

## <a name="define-node-properties"></a>Definovat vlastnosti uzlu
Někdy můžete chtít Ujistěte se, že určité úlohy spouštěny pouze v určitých typů uzlů v clusteru. Pro některé úlohy může například vyžadovat grafickými procesory nebo disky SSD a jiné ne. Pro každý typ uzlu v clusteru můžete přidat vlastní uzel vlastnosti na uzlech clusteru. Omezení umístění jsou příkazy připojených služeb, které vyberte jeden nebo více vlastností uzlu. Omezení umístění definovat, ve kterém by měly běžet služby.

Podrobnosti o použití omezení umístění, vlastnosti uzlu a postupy jejich definování najdete [vlastnosti uzlu a omezení umístění](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).
 

## <a name="add-capacity-metrics"></a>Přidání metriky kapacity
Pro každý typ uzlu můžete přidat vlastní metriku kapacity, kterou chcete použít ve svých aplikacích k načtení sestavy. Podrobnosti týkající se použití metriky kapacity pro načtení sestavy, najdete v Service Fabric Cluster Resource Manager dokumenty na [popisující svůj Cluster](service-fabric-cluster-resource-manager-cluster-description.md) a [metriky a zatížení](service-fabric-cluster-resource-manager-metrics.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Opravy operačního systému v uzlech clusteru
Aplikace orchestraci oprav (POA) je aplikace Service Fabric, který automatizuje operačního systému, použití dílčích oprav v clusteru Service Fabric bez jakýchkoli prostojů. [Opravy Orchestrace aplikace pro Windows](service-fabric-patch-orchestration-application.md) je možné nasadit v clusteru pro instalaci oprav iniciovat organizovaně, což způsobem při zachování služby dostupné neustále. 


## <a name="next-steps"></a>Další postup
* Zjistěte, jak přizpůsobit některé z [service fabric nastavení prostředků infrastruktury clusteru](service-fabric-cluster-fabric-settings.md)
* Zjistěte, jak [dovnitř a ven škálování clusteru](service-fabric-cluster-scale-up-down.md)
* Další informace o [upgrady aplikací](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
