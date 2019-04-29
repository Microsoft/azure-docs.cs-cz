---
title: Stav možnosti úložiště v Azure Service Fabric mřížky | Dokumentace Microsoftu
description: Přečtěte si o spolehlivé ukládání stavu v Service Fabric mřížky aplikace běžící na Azure Service Fabric mřížky.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: ef51040d1bad74ee74d5901d1f5acbe875c02a07
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810542"
---
# <a name="state-management-with-service-fabric"></a>Správa stavu s využitím Service Fabric

Service Fabric podporuje spoustu různých možností pro úložiště stavu. Koncepční přehled vzorce správy stavu a Service Fabric najdete v části [koncepty služby Service Fabric: Stav](/azure/service-fabric/service-fabric-concepts-state). Všechny tyto stejné koncepty platí, jestli vaše služby běžet uvnitř nebo vně sítě pro Service Fabric. 

Pomocí Service Fabric sítě můžete snadno nasadit novou aplikaci a připojte ho k existujícímu úložišti dat hostovaném v Azure. Kromě použití libovolné vzdálené databázi, existuje několik možností pro ukládání dat, v závislosti na tom, jestli služba chce místní nebo vzdálené úložiště. 

## <a name="volumes"></a>Svazky

Často musíte dělat kontejnery využívání dočasné disky. Dočasné disky jsou dočasné, ale tak získat nový dočasný disk a ke ztrátě informací, pokud dojde k chybě kontejneru. Je taky obtížné sdílet informace o dočasné disky s jiných kontejnerů. Svazky jsou adresáře, které se připojit k uvnitř instancí kontejneru, které můžete použít k uložení stavu. Svazky poskytují úložiště pro obecné účely souborů a umožňují čtení a zápis souborů pomocí rozhraní API normální disku vstupně-výstupní operace souboru. Prostředku svazku popisuje postup připojení adresáře a které pomocného úložiště používat. Azure File storage nebo Service Fabric svazku disku k uložení dat můžete.

![Svazky][image3]

### <a name="service-fabric-reliable-volume"></a>Service Fabric Reliable svazku

Service Fabric Reliable svazek je svazek ovladač Dockeru použili k připojení místní svazek do kontejneru. Čtení a zápisu jsou místní provoz a rychlé. Data se replikují do sekundární uzly, zajištění vysoké dostupnosti. Je také rychlé převzetí služeb při selhání. Pokud dojde k chybě kontejner, ji převezme služby při selhání na uzel, který již obsahuje kopii vaše data. Příklad najdete v tématu [postup nasazení aplikace pomocí Service Fabric Reliable svazku](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md).

### <a name="azure-files-volume"></a>Služba soubory Azure svazku

Azure soubory svazek je svazek ovladač Dockeru pro připojení sdílené složky služby soubory Azure do kontejneru. Soubory Azure storage, používá síť úložiště, takže čte a zapisuje probíhat přes síť. Porovnání se Service Fabric Reliable svazku, Azure Files storage je méně výkonných ale poskytuje možnost levnější a plně spolehlivé data. Příklad najdete v tématu [postup nasazení aplikace pomocí služby Azure souborů svazku](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).

## <a name="next-steps"></a>Další postup

Informace o modelu aplikací najdete v tématu [prostředky Service Fabric](service-fabric-mesh-service-fabric-resources.md)

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
