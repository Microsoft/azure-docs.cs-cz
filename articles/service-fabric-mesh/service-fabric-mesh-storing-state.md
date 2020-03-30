---
title: Možnosti úložiště stavu v síti Azure Service Fabric Mesh
description: Další informace o spolehlivém ukládání stavu v aplikacích Service Fabric Mesh spuštěných v síti Azure Service Fabric.
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: 0b0bd611fa86d155bb5bf2e07808e7365e28871c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259094"
---
# <a name="state-management-with-service-fabric"></a>Správa stavu pomocí service fabricu

Service Fabric podporuje mnoho různých možností pro úložiště stavu. Koncepční přehled vzorů správy stavu a service fabric naleznete v tématu [Service Fabric Concepts: State](/azure/service-fabric/service-fabric-concepts-state). Všechny tyto stejné koncepty platí bez ohledu na to, zda vaše služby běží uvnitř nebo vně sítě Service Fabric. 

Pomocí service fabric mesh můžete snadno nasadit novou aplikaci a připojit ji k existujícímu úložišti dat hostovanému v Azure. Kromě použití jakékoli vzdálené databáze existuje několik možností pro ukládání dat v závislosti na tom, zda služba touží po místním nebo vzdáleném úložišti. 

## <a name="volumes"></a>Svazky

Kontejnery často využívají dočasné disky. Dočasné disky jsou však dočasné, takže získáte nový dočasný disk a ztratíte informace při havárii kontejneru. Je také obtížné sdílet informace o dočasných discích s jinými kontejnery. Svazky jsou adresáře, které se připevní uvnitř instancí kontejneru, které můžete použít k zachování stavu. Svazky umožňují ukládání souborů pro obecné účely a umožňují čtení a zápis souborů pomocí běžných souborů V/O disku. Prostředek svazku popisuje, jak připojit adresář a které záložní úložiště použít. Můžete zvolit úložiště souborů Azure nebo service fabric svazek disk pro ukládání dat.

![Svazky][image3]

### <a name="service-fabric-reliable-volume"></a>Spolehlivý svazek service fabric

Service Fabric Reliable Volume je ovladač svazku Dockeru, který se používá k připojení místního svazku do kontejneru. Čtení a zápisy jsou místní operace a rychlé. Data jsou replikována do sekundárních uzlů, takže jsou vysoce dostupná. Převzetí služeb při selhání je také rychlé. Při chybě kontejneru převzetí služby při selhání do uzlu, který již obsahuje kopii dat. Například najdete v [tématu Jak nasadit aplikaci pomocí spolehlivého svazku Service Fabric](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md).

### <a name="azure-files-volume"></a>Svazek souborů Azure

Svazek souborů Azure je ovladač svazku Dockeru, který se používá k připojení sdílené složky Souborů Azure do kontejneru. Úložiště Souborů Azure používá síťové úložiště, takže čtení a zápisy probíhají v síti. Ve srovnání se spolehlivým svazkem Service Fabric je úložiště souborů Azure méně výkonné, ale poskytuje levnější a plně spolehlivou možnost dat. Například najdete v [tématu Jak nasadit aplikaci pomocí svazku souborů Azure](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).

## <a name="next-steps"></a>Další kroky

Informace o modelu aplikace naleznete v tématu [Service Fabric prostředky](service-fabric-mesh-service-fabric-resources.md)

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
