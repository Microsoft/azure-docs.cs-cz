---
title: Azure VMware Solutions (AVS) – přehled uzlů
description: Přečtěte si o uzlech a konceptech AVS.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 08d8fc3e6f1f2f83cf3c4fee3fdafb0bd07e336c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024921"
---
# <a name="avs-nodes-overview"></a>Přehled uzlů pro funkci AVS

Uzly jsou stavebními bloky privátního cloudu služby AVS. Uzel je:

* Vyhrazený výpočetní hostitel na holém počítači, kde je nainstalovaný VMware ESXi hypervisor  
* Jednotka computingu, kterou můžete koupit nebo rezervovat pro vytvoření privátních cloudů pro funkci AVS
* K dispozici pro nákup nebo rezervaci v oblasti, kde je služba AVS k dispozici

Privátní cloud služby AVS můžete vytvořit z nakupovaných uzlů. K vytvoření privátního cloudu služby AVS potřebujete minimálně tři uzly stejné SKU. Pokud chcete rozšířit privátní cloud služby AVS, přidejte další uzly. Uzly můžete přidat do existujícího clusteru nebo vytvořit nový cluster pomocí nákupu uzlů v Azure Portal a jejich přidružení ke službě AVS. Všechny koupené uzly jsou viditelné v rámci služby AVS. 

## <a name="provisioned-nodes"></a>Zřízené uzly

Zřízené uzly poskytují kapacitu s průběžnými platbami. Zřizování uzlů vám pomůže rychle škálovat cluster VMware na vyžádání. Podle potřeby můžete přidávat uzly nebo odstranit zřízený uzel pro horizontální navýšení kapacity clusteru VMware. Zřízené uzly se účtují měsíčně a účtují se z předplatného, kde se zřídí.

* Pokud platíte za předplatné Azure pomocí platební karty, karta se účtuje okamžitě.
* Pokud se fakturuje podle faktury, účtují se poplatky na vaší další faktuře.

## <a name="vmware-solution-by-avs-nodes-sku"></a>Řešení VMware podle SKU uzlů pro funkci AVS

Pro zřizování nebo rezervaci jsou k dispozici následující typy uzlů.

| Skladová položka           | CS28 – uzel                 | CS36 – uzel                 | CS36m – uzel                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Region (Oblast)        | Východní USA Západní USA            | Východní USA Západní USA            | Západní Evropa                 |
| Procesor           | 2x 2,2 GHz, 28 jader (56 HT) | 2x 2,3 GHz, 36 jader (72 HT) | 2x 2,3 GHz, 36 jader (72 HT) |
| Paměť RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Disk mezipaměti    | 1,6 – TB NVMe                 | 3,2 – TB NVMe                 | 3,2 – TB NVMe                 |
| Disk kapacity | 5,625 TB Raw                | 11,25 TB Raw                | 15,36 TB Raw                |
| Typ úložiště  | Všechny flash                   | Všechny flash                   | Všechny flash                   |

## <a name="limits"></a>Omezení

Následující omezení uzlů platí pro privátní cloudy pro funkci AVS.

| Prostředek | škálování |
|----------|-------|
| Minimální počet uzlů pro vytvoření privátního cloudu služby AVS | 3 |
| Maximální počet uzlů v clusteru v privátním cloudu služby AVS | 16 |
| Maximální počet uzlů v privátním cloudu služby AVS | 64 |
| Minimální počet uzlů v novém clusteru | 3 |

## <a name="next-steps"></a>Další kroky

* Informace o [nákupu uzlů](create-nodes.md)
* Informace o [privátních cloudech](cloudsimple-private-cloud.md) pro funkci AVS
* Naučte se [zřizovat uzly](create-nodes.md) .
* Informace o [privátních cloudech](cloudsimple-private-cloud.md)
