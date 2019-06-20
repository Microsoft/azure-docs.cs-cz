---
title: Přehled uzly pro řešení VMware podle CloudSimple – Azure
description: Další informace o konceptech a CloudSimple uzly.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fb82e31d58d9955efc3b147eccf2b82b8768aeee
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165798"
---
# <a name="cloudsimple-nodes-overview"></a>Přehled CloudSimple uzly

Uzel je:

* Vyhrazené holé počítače compute hostitele, kde je nainstalován hypervisor VMware ESXi  
* Jednotka, kterou můžete zřizovat výpočetní nebo rezervy pro vytváření privátních cloudů  
* K dispozici pro zřizování, nebo aby byly v oblasti, kde je k dispozici služba CloudSimple

Uzly jsou stavební bloky privátního cloudu.  K vytvoření privátního cloudu, budete potřebovat minimálně tři uzly se stejným SKU.  Rozbalte privátního cloudu, přidáte další uzly.  Přidat uzly do existujícího clusteru. Nebo můžete vytvořit nový cluster díky zřizování uzly na webu Azure Portal a jejich přidružení k CloudSimple služby.  Všechny uzly, které jsou zřízené jsou viditelné v rámci služby CloudSimple.  Vytvoření privátního cloudu z zřízené uzlů na CloudSimple portálu.

## <a name="provisioned-nodes"></a>Zřízené uzly

Zřízené uzly poskytují kapacitu s průběžnými platbami. Zřizování uzly umožňuje rychle škálovat cluster VMware na vyžádání. Můžete podle potřeby přidejte uzly nebo odstranit zřízené uzel vertikálně snížit kapacitu vašeho clusteru VMware. zřízené uzly se účtuje každý měsíc a zpoplatněné nad rámec předplatného, ve kterém máte zřízené:

* Pokud platíte kreditní kartou pro vaše předplatné Azure, je karta účtovat okamžitě.
* Pokud bude se vám účtovat pomocí faktury, poplatky se zobrazí na další faktuře.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>Řešení VMware CloudSimple uzly SKU

Následující typy uzly jsou k dispozici pro zřizování nebo rezervace.

| Skladová jednotka (SKU) | CS28 - Node | CS36 - Node |
|-----|-------------|-------------|
| Procesor | 2x2.2 GHz, 28 jader (56 HT) | 2x2.3 GHz, 36 jader (72 HT) |
| Paměť RAM | 256 GB | 512 GB |
| Cache Disk |  NVMe 1.6 TB | NVMe 3.2 TB |
| Kapacita disku | 5.625 nezpracovaná TB | 11,25 nezpracovaná TB |
| Typ úložiště | Všechny Flash | Všechny Flash |

## <a name="limits"></a>Limits

Následující uzel omezení platí do privátních cloudů.

| Resource | Omezení |
|----------|-------|
| Minimální počet uzlů pro vytvoření privátního cloudu | 3 |
| Maximální počet uzlů v clusteru v privátním cloudu | 16 |
| Maximální počet uzlů v privátním cloudu | 64 |
| Minimální počet uzlů v novém clusteru | 3 |

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [zřízení uzlů](create-nodes.md)
* Další informace o [privátního cloudu](cloudsimple-private-cloud.md)