---
title: zahrnout soubor
description: zahrnout soubor
services: billing
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 07/22/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: d94937a738034904413eac8b256121f14221d1ac
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846000"
---
| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Počet virtuálních počítačů na [předplatné](../articles/billing-buy-sign-up-azure-subscription.md) |25 000<sup>1</sup> na oblast |25 000 na oblast |
| Celkový počet jader virtuálního počítače na [předplatné](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> na oblast. | Obraťte se na podporu. |
| Celkový počet jader virtuálních počítačů Azure na jedno [předplatné](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> na oblast. | Obraťte se na podporu. |
| Virtuální počítač na řadu, například Dv2 a F, jader na [předplatné](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> na oblast. | Obraťte se na podporu. |
| [Účty úložiště](../articles/storage/common/storage-account-create.md) pro jednotlivé oblasti a předplatné |250 |250 |
| [Skupiny dostupnosti](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) na předplatné |2 000 na oblast |2 000 na oblast |
| [Skupiny vztahů](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) na předplatné |Není k dispozici<sup>3</sup> |Není k dispozici<sup>3</sup> |
| [Cloudové služby](../articles/cloud-services/cloud-services-choose-me.md) na předplatné |Není k dispozici<sup>3</sup> |Není k dispozici<sup>3</sup> |
| [Skupiny prostředků](../articles/azure-resource-manager/management/overview.md) na předplatné |980 |980 |
| Velikost požadavku rozhraní API pro Azure Resource Manager |4 194 304 bajtů. |4 194 304 bajtů. |
| Značky na předplatné<sup>2</sup> |Počet. |Počet. |
| Jedinečné výpočty značek na předplatné<sup>2</sup> | 10 000 | 10 000 |
| [Nasazení na úrovni předplatného](../articles/azure-resource-manager/templates/deploy-to-subscription.md) na umístění | 800<sup>4</sup> | 800 |
| Předplatná na tenanta Azure Active Directory | Počet. | Počet. |
| [Spolusprávci](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) na předplatné |Počet. |Počet. |

<sup>1</sup> Výchozí limity se liší podle typu kategorie nabídky, jako je bezplatná zkušební verze a průběžné platby a podle řad, například Dv2, F a G. Například výchozí hodnota pro smlouva Enterprise předplatná je 350.

<sup>2</sup> . Na jedno předplatné můžete použít neomezený počet značek. Počet značek na prostředek nebo skupinu prostředků je omezený na 50. Správce prostředků vrátí [seznam jedinečných názvů a hodnot značek](/rest/api/resources/tags) v rámci předplatného pouze v případě, že je počet značek 10 000 nebo méně. I když číslo překračuje 10 000, můžete pořád najít prostředek podle značky.  

<sup>3</sup> . Tyto funkce se už nevyžadují u skupin prostředků Azure a Správce prostředků.

<sup>4</sup> . Pokud dosáhnete limitu nasazení 800, odstraňte nasazení z historie, která už nepotřebujete. Pokud chcete odstranit nasazení na úrovni předplatného, použijte [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) nebo [AZ Deployment Delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).

> [!NOTE]
> Jádra virtuálních počítačů mají celkový limit na úrovni. Mají také omezení pro místní řady jednotlivých velikostí, například Dv2 a F. Tato omezení se vynutila samostatně. Představte si například předplatné s omezením celkového počtu 30 jader virtuálního počítače na oblast USA – východ, omezením počtu 30 jader na řadu A a 30 jader na řadu D. Toto předplatné může nasadit virtuální počítače s 30 a1 nebo 30 virtuálních počítačů D1 nebo kombinaci těchto dvou počítačů, které nepřesahují celkem 30 jader. Příkladem kombinace je 10 virtuálních počítačů a 20 virtuálních počítačů D1.  
> <!-- -->
> 
> 

