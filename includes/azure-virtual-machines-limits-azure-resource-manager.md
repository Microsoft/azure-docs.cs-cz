---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 397dd3d16fa994df29a08ff9095b4c7c6c4af815
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510895"
---
| Prostředek | Omezení |
| --- | --- |
| Počet virtuálních počítačů na [předplatné](https://azure.microsoft.com/pricing/) |25 000<sup>1</sup> na oblast |
| Celkový počet jader virtuálního počítače na [předplatné](https://azure.microsoft.com/pricing/) |20<sup>1</sup> na oblast. Pokud chcete zvýšit limit, obraťte se na podporu. |
| Celkový počet jader virtuálních počítačů Azure na jedno [předplatné](https://azure.microsoft.com/pricing/) |20<sup>1</sup> na oblast. Pokud chcete zvýšit limit, obraťte se na podporu. |
| Virtuální počítač na řadu, například Dv2 a F, jader na [předplatné](https://azure.microsoft.com/pricing/) |20<sup>1</sup> na oblast. Pokud chcete zvýšit limit, obraťte se na podporu. |
| [Skupiny dostupnosti](../articles/virtual-machines/availability-set-overview.md) na předplatné |2 500 na oblast |
| Virtuální počítače na skupinu dostupnosti | 200 |
| [Skupiny umístění blízkosti](../articles/virtual-machines/windows/proximity-placement-groups-portal.md) na [skupinu prostředků](../articles/azure-resource-manager/management/overview.md#resource-groups) | 800 | 
| Počet certifikátů na skupinu dostupnosti | 199<sup>2</sup> |
| Certifikáty na předplatné |Neomezeno<sup>3</sup> |

<sup>1</sup> výchozí omezení se liší podle typu kategorie nabídky, jako je bezplatná zkušební verze a průběžné platby a podle řad, například Dv2, F a G. Například výchozí hodnota pro smlouva Enterprise předplatná je 350.

<sup>2</sup> vlastnosti, jako jsou například veřejné klíče SSH, jsou také vloženy jako certifikáty a počítají se k tomuto limitu. Pokud chcete tento limit obejít, nainstalujte certifikáty pomocí [rozšíření Azure Key Vault pro Windows](../articles/virtual-machines/extensions/key-vault-windows.md) nebo [rozšíření Azure Key Vault pro Linux](../articles/virtual-machines/extensions/key-vault-linux.md) .

<sup>3</sup> s Azure Resource Manager jsou certifikáty uložené v Azure Key Vault. Počet certifikátů je pro předplatné neomezený. Pro každé nasazení je povolený limit 1 MB, který se skládá z jednoho virtuálního počítače nebo skupiny dostupnosti.



> [!NOTE]
> Jádra virtuálních počítačů mají celkový limit na úrovni. Mají také omezení pro místní řady jednotlivých velikostí, například Dv2 a F. Tato omezení se vynutila samostatně. Představte si například předplatné s omezením celkového počtu 30 jader virtuálního počítače na oblast USA – východ, omezením počtu 30 jader na řadu A a 30 jader na řadu D. Toto předplatné může nasadit virtuální počítače s 30 a1 nebo 30 virtuálních počítačů D1 nebo kombinaci těchto dvou počítačů, které nepřesahují celkem 30 jader. Příkladem kombinace je 10 virtuálních počítačů a 20 virtuálních počítačů D1.  
> <!-- -->
>