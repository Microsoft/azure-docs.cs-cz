---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 651027ffd63a376ff0b8595636ece4c8f39c86e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82616055"
---
| Prostředek | Omezení |
| --- | --- |
| Počet virtuálních počítačů na [předplatné](../articles/billing-buy-sign-up-azure-subscription.md) |25 000<sup>1</sup> na oblast |
| Celkový počet jader virtuálního počítače na [předplatné](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> na oblast. Pokud chcete zvýšit limit, obraťte se na podporu. |
| Celkový počet jader virtuálních počítačů Azure na jedno [předplatné](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> na oblast. Pokud chcete zvýšit limit, obraťte se na podporu. |
| Virtuální počítač na řadu, například Dv2 a F, jader na [předplatné](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> na oblast. Pokud chcete zvýšit limit, obraťte se na podporu. |
| [Skupiny dostupnosti](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) na předplatné |2 500 na oblast |
| Virtuální počítače na skupinu dostupnosti | 200 |
| Certifikáty na předplatné |Neomezeno<sup>2</sup> |

<sup>1</sup> Výchozí limity se liší podle typu kategorie nabídky, jako je bezplatná zkušební verze a průběžné platby a podle řad, například Dv2, F a G. Například výchozí hodnota pro smlouva Enterprise předplatná je 350.

<sup>2</sup> . Při Azure Resource Manager se certifikáty ukládají do Azure Key Vault. Počet certifikátů je pro předplatné neomezený. Pro každé nasazení je povolený limit 1 MB, který se skládá z jednoho virtuálního počítače nebo skupiny dostupnosti.

> [!NOTE]
> Jádra virtuálních počítačů mají celkový limit na úrovni. Mají také omezení pro místní řady jednotlivých velikostí, například Dv2 a F. Tato omezení se vynutila samostatně. Představte si například předplatné s omezením celkového počtu 30 jader virtuálního počítače na oblast USA – východ, omezením počtu 30 jader na řadu A a 30 jader na řadu D. Toto předplatné může nasadit virtuální počítače s 30 a1 nebo 30 virtuálních počítačů D1 nebo kombinaci těchto dvou počítačů, které nepřesahují celkem 30 jader. Příkladem kombinace je 10 virtuálních počítačů a 20 virtuálních počítačů D1.  
> <!-- -->
> 
