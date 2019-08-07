---
title: Přehled uzlů pro řešení VMware podle CloudSimple – Azure
description: Přečtěte si o CloudSimplech uzlech a konceptech.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 346bd046810ebae5142bc23400419857000d0c8e
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812574"
---
# <a name="cloudsimple-nodes-overview"></a>CloudSimple uzly – přehled

Uzel je:

* Vyhrazený výpočetní hostitel na holém počítači, kde je nainstalovaný VMware ESXi hypervisor  
* Jednotka computingu, kterou můžete zřídit nebo rezervovat pro vytváření privátních cloudů  
* K dispozici pro zřízení nebo rezervaci v oblasti, kde je dostupná služba CloudSimple

Uzly jsou stavebními bloky privátního cloudu.  K vytvoření privátního cloudu potřebujete minimálně tři uzly stejné SKU.  Pokud chcete rozšířit privátní cloud, přidejte další uzly.  Uzly můžete přidat do existujícího clusteru. Nebo můžete vytvořit nový cluster pomocí zřízení uzlů v Azure Portal a jejich přidružení ke službě CloudSimple.  V rámci služby CloudSimple se zobrazí všechny zřízené uzly.  Privátní Cloud můžete vytvořit ze zřízené uzlů na portálu CloudSimple.

## <a name="provisioned-nodes"></a>Zřízené uzly

Zřízené uzly poskytují kapacitu s průběžnými platbami. Zřizování uzlů vám pomůže rychle škálovat cluster VMware na vyžádání. Podle potřeby můžete přidávat uzly nebo odstranit zřízený uzel pro horizontální navýšení kapacity clusteru VMware. zřízené uzly se účtují měsíčně a účtují se z předplatného, kde jsou zřízené:

* Pokud platíte za předplatné Azure pomocí platební karty, karta se účtuje okamžitě.
* Pokud se fakturuje podle faktury, účtují se poplatky na vaší další faktuře.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>SKU řešení VMware podle CloudSimple uzlů

Následující typy uzlů jsou k dispozici pro zřizování nebo rezervaci.

| SKU | CS28 – uzel | CS36 – uzel |
|-----|-------------|-------------|
| Procesor | 2x 2,2 GHz, 28 jader (56 HT) | 2x 2,3 GHz, 36 jader (72 HT) |
| Paměť RAM | 256 GB | 512 GB |
| Disk mezipaměti |  1,6 – TB NVMe | 3,2 – TB NVMe |
| Disk kapacity | 5,625 TB Raw | 11,25 TB Raw |
| Typ úložiště | Všechny flash | Všechny flash |

## <a name="limits"></a>Omezení

Následující omezení uzlů platí pro privátní cloudy.

| Resource | Omezení |
|----------|-------|
| Minimální počet uzlů pro vytvoření privátního cloudu | 3 |
| Maximální počet uzlů v clusteru v privátním cloudu | 16 |
| Maximální počet uzlů v privátním cloudu | 64 |
| Minimální počet uzlů v novém clusteru | 3 |

## <a name="next-steps"></a>Další postup

* Naučte se [zřizovat uzly](create-nodes.md) .
* Informace o [privátním cloudu](cloudsimple-private-cloud.md)