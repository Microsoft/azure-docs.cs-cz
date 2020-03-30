---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 90dc5a067c05cadb3d5e102435b2e3d3de803e28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334777"
---
| Prostředek | Omezení |
| --- | --- |
| Počet virtuálních počítačů na [předplatné](../articles/billing-buy-sign-up-azure-subscription.md) |25 000<sup>1</sup> na region. |
| Celkový počet jader virtuálního počítače na [předplatné](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> na region. Obraťte se na podporu pro zvýšení limitu. |
| Celkový počet jader virtuálního počítače Azure na [jedno předplatné](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> na region. Obraťte se na podporu pro zvýšení limitu. |
| Virtuální ho virtuálního na řadu, jako jsou Dv2 a F, jádra na [předplatné](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> na region. Obraťte se na podporu pro zvýšení limitu. |
| [Skupiny dostupnosti](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) na jedno předplatné |2000 na region. |
| Virtuální počítače podle skupiny dostupnosti | 200 |
| Certifikáty na předplatné |Neomezený<sup>2</sup> |

<sup>1.</sup> Výchozí limity se liší podle typu kategorie nabídky, například zkušební verze zdarma a průběžných plateb, a podle řad, například Dv2, F a G. Například výchozí pro předplatné smlouvy Enterprise je 350.

<sup>2.</sup> Ve Správci prostředků Azure se certifikáty ukládají v trezoru klíčů Azure. Počet certifikátů je neomezený pro předplatné. Existuje limit 1 MB certifikátů na nasazení, který se skládá z jednoho virtuálního počítače nebo z nastavené dostupnosti.

> [!NOTE]
> Jádra virtuálních strojů mají celkový místní limit. Mají také limit pro regionální řady pro velikost, jako je Dv2 a F. Tyto limity jsou vynuceny samostatně. Představte si například předplatné s omezením celkového počtu 30 jader virtuálního počítače na oblast USA – východ, omezením počtu 30 jader na řadu A a 30 jader na řadu D. Toto předplatné můžete nasadit 30 Virtuální chod A1 nebo 30 D1 virtuálních stránek nebo kombinace těchto dvou nepřekročí celkem 30 jader. Příkladem kombinace je 10 virtuálních disponécí A1 a 20 virtuálních d1.  
> <!-- -->
> 
