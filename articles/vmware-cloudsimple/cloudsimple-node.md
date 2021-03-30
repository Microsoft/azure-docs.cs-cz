---
title: Řešení Azure VMware podle CloudSimple – přehled uzlů
description: Přečtěte si o konceptech CloudSimple, včetně uzlů, zřizovacích uzlů, privátního cloudu a řešení VMware pomocí SKU uzlů CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7de4b5faa997d909089daedab7e48e5d5a6de2e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140832"
---
# <a name="cloudsimple-nodes-overview"></a>CloudSimple uzly – přehled

Uzly jsou stavebními bloky privátního cloudu. Uzel je:

* Vyhrazený výpočetní hostitel na holém počítači, kde je nainstalovaný VMware ESXi hypervisor  
* Jednotka computingu, kterou můžete zřídit nebo rezervovat pro vytváření privátních cloudů
* K dispozici pro zřízení nebo rezervaci v oblasti, kde je dostupná služba CloudSimple

Privátní Cloud můžete vytvořit ze zřízené uzlů. K vytvoření privátního cloudu potřebujete minimálně tři uzly stejné SKU. Pokud chcete rozšířit privátní cloud, přidejte další uzly.  Uzly můžete přidat do existujícího clusteru nebo vytvořit nový cluster pomocí zřízení uzlů v Azure Portal a jejich přidružení ke službě CloudSimple.  Všechny zřízené uzly jsou viditelné v rámci služby CloudSimple.  

## <a name="provisioned-nodes"></a>Zřízené uzly

Zřízené uzly poskytují kapacitu s průběžnými platbami. Zřizování uzlů vám pomůže rychle škálovat cluster VMware na vyžádání. Podle potřeby můžete přidávat uzly nebo odstranit zřízený uzel pro horizontální navýšení kapacity clusteru VMware. Zřízené uzly se účtují měsíčně a účtují se z předplatného, kde se zřídí.

* Pokud platíte za předplatné Azure pomocí platební karty, karta se účtuje okamžitě.
* Pokud se fakturuje podle faktury, účtují se poplatky na vaší další faktuře.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>SKU řešení VMware podle CloudSimple uzlů

Pro zřizování nebo rezervaci jsou k dispozici následující typy uzlů.

| SKU           | CS28 – uzel                 | CS36 – uzel                 | CS36m – uzel                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Oblast        | Východní USA Západní USA            | Východní USA Západní USA            | West Europe                 |
| Procesor           | 2x 2,2 GHz, 28 jader (56 HT) | 2x 2,3 GHz, 36 jader (72 HT) | 2x 2,3 GHz, 36 jader (72 HT) |
| Paměť RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Disk mezipaměti    | 1,6 – TB NVMe                 | 3,2 – TB NVMe                 | 3,2 – TB NVMe                 |
| Disk kapacity | 5,625 TB Raw                | 11,25 TB Raw                | 15,36 TB Raw                |
| Typ úložiště  | Všechny flash                   | Všechny flash                   | Všechny flash                   |

## <a name="limits"></a>Omezení

Následující omezení uzlů platí pro privátní cloudy.

| Prostředek | Omezení |
|----------|-------|
| Minimální počet uzlů pro vytvoření privátního cloudu | 3 |
| Maximální počet uzlů v clusteru v privátním cloudu | 16 |
| Maximální počet uzlů v privátním cloudu | 64 |
| Minimální počet uzlů v novém clusteru | 3 |

## <a name="next-steps"></a>Další kroky

* Naučte se [zřizovat uzly](create-nodes.md) .
* Informace o [privátních cloudech](cloudsimple-private-cloud.md)
