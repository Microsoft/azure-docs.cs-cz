---
title: Azure VMware Solution by CloudSimple – přehled uzlů
description: Další informace o cloudových jednoduchých uzlech a konceptech.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65afe26a98a53b00b72a1ea2b49799db2049b727
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024921"
---
# <a name="cloudsimple-nodes-overview"></a>Přehled uzlů CloudSimple

Uzly jsou stavební kameny privátního cloudu. Uzel je:

* Vyhrazený holý kovový výpočetní hostitel, kde je nainstalován hypervisor VMware ESXi  
* Výpočetní jednotka, kterou můžete zřídit nebo rezervovat pro vytvoření privátních cloudů
* K dispozici pro poskytování nebo rezervaci v oblasti, kde je služba CloudSimple k dispozici

Privátní cloud vytvoříte z zřízených uzlů. Chcete-li vytvořit privátní cloud, potřebujete minimálně tři uzly stejné skladové položky. Chcete-li rozšířit privátní cloud, přidejte další uzly.  Uzly můžete přidat do existujícího clusteru nebo vytvořit nový cluster zřizováním uzlů na portálu Azure a jejich přidruženou službou CloudSimple.  Všechny zřízené uzly jsou viditelné v rámci služby CloudSimple.  

## <a name="provisioned-nodes"></a>Zřízené uzly

Zřízené uzly poskytují průběžnou kapacitu. Zřizování uzlů vám pomůže rychle škálovat cluster VMware na vyžádání. Můžete přidat uzly podle potřeby nebo odstranit zřízený uzel pro škálování clusteru VMware. Zřízené uzly se účtují měsíčně a účtují se do předplatného, kde jsou zřízeny.

* Pokud platíte za předplatné Azure platební kartou, bude se karta účtovat okamžitě.
* Pokud vám fakturuje faktura, poplatky se zobrazí na další faktuře.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>Řešení VMware podle soupoložky uzlů CloudSimple

Následující typy uzlů jsou k dispozici pro zřizování nebo rezervace.

| Skladová jednotka (SKU)           | CS28 - Uzel                 | CS36 - Uzel                 | CS36m - Uzel                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Region (Oblast)        | Východní USA, Západní USA            | Východní USA, Západní USA            | Západní Evropa                 |
| Procesor           | 2x2,2 GHz, 28 jader (56 HT) | 2x2,3 GHz, 36 jader (72 HT) | 2x2,3 GHz, 36 jader (72 HT) |
| Paměť RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Disk mezipaměti    | 1,6 TB NVMe                 | 3.2-TB NVMe                 | 3.2-TB NVMe                 |
| Disk kapacity | 5.625 TB raw                | 11.25 TB raw                | 15,36 TB raw                |
| Typ úložiště  | Všechny Flash                   | Všechny Flash                   | Všechny Flash                   |

## <a name="limits"></a>Omezení

Následující omezení uzlů platí pro privátní cloudy.

| Prostředek | Omezení |
|----------|-------|
| Minimální počet uzlů pro vytvoření privátního cloudu | 3 |
| Maximální počet uzlů v clusteru v privátním cloudu | 16 |
| Maximální počet uzlů v privátním cloudu | 64 |
| Minimální počet uzlů v novém clusteru | 3 |

## <a name="next-steps"></a>Další kroky

* Naučte se [zřazovat uzly](create-nodes.md)
* Další informace o [privátních cloudech](cloudsimple-private-cloud.md)
