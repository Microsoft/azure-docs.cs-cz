---
title: Řešení Azure VMware podle CloudSimple – přehled uzlů
description: Přečtěte si o CloudSimplech uzlech a konceptech.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 729b832c068dcd401fa6e9d2f4af9193d3859a83
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71845549"
---
# <a name="cloudsimple-nodes-overview"></a>CloudSimple uzly – přehled

Uzly jsou stavebními bloky privátního cloudu. Uzel je:

* Vyhrazený výpočetní hostitel na holém počítači, kde je nainstalovaný VMware ESXi hypervisor  
* Jednotka computingu, kterou můžete koupit nebo rezervovat pro vytváření privátních cloudů
* K dispozici pro nákup nebo rezervaci v oblasti, kde je k dispozici služba CloudSimple

Privátní cloud vytvoříte z nakupovaných uzlů. K vytvoření privátního cloudu potřebujete minimálně tři uzly stejné SKU. Pokud chcete rozšířit privátní cloud, přidejte další uzly.  Uzly můžete přidat do existujícího clusteru nebo vytvořit nový cluster pomocí nákupu uzlů v Azure Portal a jejich přidružení ke službě CloudSimple.  Všechny koupené uzly jsou viditelné v rámci služby CloudSimple.  

## <a name="purchased-nodes"></a>Koupené uzly

Koupené uzly poskytují kapacitu s průběžnými platbami. Nákupy uzlů vám pomůžou rychle škálovat cluster VMware na vyžádání. Můžete přidat uzly podle potřeby nebo odstranit koupený uzel pro horizontální navýšení kapacity clusteru VMware. Koupené uzly se účtují měsíčně a účtují se do zakoupeného předplatného.

* Pokud platíte za předplatné Azure pomocí platební karty, karta se účtuje okamžitě.
* Pokud se fakturuje podle faktury, účtují se poplatky na vaší další faktuře.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>SKU řešení VMware podle CloudSimple uzlů

Pro nákup nebo rezervaci jsou k dispozici následující typy uzlů.

| Skladová položka           | CS28 – uzel                 | CS36 – uzel                 | CS36m – uzel                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Oblast        | Východní USA Západní USA            | Východní USA Západní USA            | Západní Evropa                 |
| Procesor           | 2x 2,2 GHz, 28 jader (56 HT) | 2x 2,3 GHz, 36 jader (72 HT) | 2x 2,3 GHz, 36 jader (72 HT) |
| Paměť RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Disk mezipaměti    | 1,6 – TB NVMe                 | 3,2 – TB NVMe                 | 3,2 – TB NVMe                 |
| Disk kapacity | 5,625 TB Raw                | 11,25 TB Raw                | 15,36 TB Raw                |
| Typ úložiště  | Všechny flash                   | Všechny flash                   | Všechny flash                   |

## <a name="limits"></a>Omezení

Následující omezení uzlů platí pro privátní cloudy.

| Prostředek | škálování |
|----------|-------|
| Minimální počet uzlů pro vytvoření privátního cloudu | 3 |
| Maximální počet uzlů v clusteru v privátním cloudu | 16 |
| Maximální počet uzlů v privátním cloudu | 64 |
| Minimální počet uzlů v novém clusteru | 3 |

## <a name="next-steps"></a>Další kroky

* Informace o [nákupu uzlů](create-nodes.md)
* Informace o [privátních cloudech](cloudsimple-private-cloud.md)
