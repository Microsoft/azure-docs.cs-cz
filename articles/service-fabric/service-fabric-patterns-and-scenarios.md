---
title: Azure Service Fabric vzory a scénáře | Dokumentace Microsoftu
description: Přečtěte si osvědčené postupy a prověřené a opakovaně použitelné modely navrhovat, vyvíjet a provozovat mikroslužby na platformě Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: d5aa75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: 3bd77891cc7508eeb1fee2152d37478c654a7e37
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44294691"
---
# <a name="service-fabric-patterns-and-scenarios"></a>Service Fabric vzory a scénáře
Pokud se díváte na vytváření rozsáhlých mikroslužeb pomocí Azure Service Fabric, Učte se od expertů, kteří navržen a sestaven Tato platforma jako služba (PaaS). Začínáme s architekturou na bázi správné a zjistěte, jak k optimalizaci prostředků pro vaši aplikaci. [Service Fabric vzory a postupy](https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mudwqISGD_6005167344) kurzu odpovědi otázky, nejčastěji skutečných zákazníků o scénáře pro Service Fabric a oblastí aplikace.
 
Prostřednictvím doporučených postupů a prověřených opakovatelně použitelných vzorů se naučíte navrhovat, vyvíjet a provozovat mikroslužby na platformě Service Fabric. Získejte přehled o Service Fabric a ponořte se hlouběji do témata, která pokrývají clusteru optimalizace a zabezpečení, migraci starších aplikací IoT ve velkém měřítku, hosting herní enginy a další. Podívejte se na průběžné doručování pro různé úlohy a dokonce i získat podrobnosti o podpoře pro Linux a kontejnery. 

## <a name="introduction"></a>Úvod
Prozkoumejte osvědčené postupy a další informace o volbě platformy jako služba (PaaS) prostřednictvím infrastruktury jako služby (IaaS). Obsahuje podrobné informace o tyto principy návrhu ověřené aplikací.

<table><tr><th>Video</th><th>Balíček aplikace PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=N2KwbbSGD_6405167344">
<img src="./media/service-fabric-patterns-and-scenarios/intro.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mudwqISGD_6005167344">Úvod do Service Fabric</a></td></tr>
</table>

## <a name="cluster-planning-and-management"></a>Plánování a Správa clusteru
Další informace o plánování kapacity clusteru a optimalizace zabezpečení clusteru v této pohled na Azure Service Fabric.

<table><tr><th>Video</th><th>Balíček aplikace PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=cyDYZcSGD_2805167344">
<img src="./media/service-fabric-patterns-and-scenarios/cluster.png" WIDTH="360" HEIGHT="244">
</a></td><td> <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=E5B3nJSGD_805167344">Plánování clusteru a správy</a></td></tr>
</table>

## <a name="hyper-scale-web"></a>Web velkém měřítku
Seznamte se s koncepty webových velkém měřítku, včetně dostupnost a spolehlivost, vysoce škálovatelné a správu stavu.

<table><tr><th>Video</th><th>Balíček aplikace PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=NgldAdSGD_405167344">
<img src="./media/service-fabric-patterns-and-scenarios/hyperscaleweb.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=CPMLBLSGD_7705167344">Web velkém měřítku</a></td></tr>
</table>

## <a name="iot"></a>IoT
Prozkoumejte Internet of Things (IoT) v rámci Azure Service Fabric, včetně kanálu Azure IoT, více tenantů a IoT ve velkém měřítku.

<table><tr><th>Video</th><th>Balíček aplikace PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=naFUVeSGD_1505167344">
<img src="./media/service-fabric-patterns-and-scenarios/iot.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=kfqFWMSGD_6205167344">IoT</a></td></tr>
</table>

## <a name="gaming"></a>Hraní her
Podívejte se na her, interaktivní hry a hostování existující herní enginy.

<table><tr><th>Video</th><th>Balíček aplikace PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=6wECzeSGD_3805167344">
<img src="./media/service-fabric-patterns-and-scenarios/gaming.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=kfqFWMSGD_6205167344">Hraní her</a></td></tr>
</table>

## <a name="continuous-delivery"></a>Nepřetržité doručování
Seznamte se s koncepty, včetně průběžné integrace a doručování s využitím Azure kanály, pracovního postupu sestavení, balení/publikování, více prostředí a služby/sdílené složky balíčku.

<table><tr><th>Video</th><th>Balíček aplikace PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=78h5ofSGD_305167344">
<img src="./media/service-fabric-patterns-and-scenarios/cd.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=VlENvOSGD_105167344">Průběžné doručování</a></td></tr>
</table>

## <a name="migration"></a>Migrace
Další informace o migraci z cloudové služby, kromě migrace ze starší verze aplikací.

<table><tr><th>Video</th><th>Balíček aplikace PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=hd1cMgSGD_5605167344">
<img src="./media/service-fabric-patterns-and-scenarios/migration.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=GQAq4QSGD_8305167344">Migrace</a></td></tr>
</table>

## <a name="containers-and-linux-support"></a>Kontejnery a podporu Linuxu
Získat odpověď na otázku "Proč kontejnery?" Další informace o verzi preview pro kontejnery Windows, Linux podporuje a Orchestrace kontejnerů Linuxu. Navíc můžete zjistit, jak migrovat aplikace .NET Core pro Linux.

<table><tr><th>Video</th><th>Balíček aplikace PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=V1ERJhSGD_305167344">
<img src="./media/service-fabric-patterns-and-scenarios/containers.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mlYsZRSGD_2105167344">Kontejnery a podporu Linuxu</a></td></tr>
</table>

## <a name="next-steps"></a>Další postup
Teď, když jste se dozvěděli o Service Fabric vzory a scénáře, další informace o tom, jak [vytvořit a spravovat clustery](service-fabric-deploy-anywhere.md), [migrovat aplikace Cloud Services do Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md), [nastavení průběžné doručování](service-fabric-tutorial-deploy-app-with-cicd-vsts.md), a [nasazování kontejnerů](service-fabric-containers-overview.md).
