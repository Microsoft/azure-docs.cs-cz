---
title: zahrnout soubor
description: zahrnout soubor
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 04/02/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: d490cab4d437c30fdb211ea27397777afc27e72e
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58890908"
---
| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Počet virtuálních počítačů na [předplatné](../articles/billing-buy-sign-up-azure-subscription.md) |25 000<sup>1</sup> v jedné oblasti. |25 000 na oblast. |
| Celkový počet jader virtuálního počítače na [předplatné](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> v jedné oblasti. | Kontaktujte podporu. |
| Virtuální počítač na řadu, jako je Dv2 a F, jader za [předplatného](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> v jedné oblasti. | Kontaktujte podporu. |
| [Spolusprávci](../articles/billing-add-change-azure-subscription-administrator.md) na předplatné |Neomezený počet. |Neomezený počet. |
| [Účty úložiště](../articles/storage/common/storage-quickstart-create-account.md) na oblast a předplatné |250 |250 |
| [Skupiny prostředků](../articles/azure-resource-manager/resource-group-overview.md) na předplatné |980 |980 |
| [Skupiny dostupnosti](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) na předplatné |2 000 na oblast. |2 000 na oblast. |
| Azure velikost požadavku rozhraní API Resource Manageru |4,194,304 bajtů. |4,194,304 bajtů. |
| Značek na předplatné<sup>2</sup> |Neomezený počet. |Neomezený počet. |
| Jedinečné výpočty značek na předplatné<sup>2</sup> | 10 000 | 10 000 |
| [Cloudové služby](../articles/cloud-services/cloud-services-choose-me.md) na předplatné |NENÍ K DISPOZICI<sup>3</sup> |NENÍ K DISPOZICI<sup>3</sup> |
| [Skupiny vztahů](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) na předplatné |NENÍ K DISPOZICI<sup>3</sup> |NENÍ K DISPOZICI<sup>3</sup> |
| [Nasazení na úrovni předplatného](../articles/azure-resource-manager/deploy-to-subscription.md) podle umístění | 800 | 800 |

<sup>1</sup>výchozí omezení se liší podle typu kategorie nabídky, jako jsou bezplatné zkušební verze a průběžné platby a řadě, jako je Dv2, F a G.

<sup>2</sup>můžete použít neomezený počet značek na předplatné. Počet značek na prostředek nebo skupinu prostředků je omezený na 15. Vrátí Resource Manageru [seznamu jedinečný název značky a hodnoty](/rest/api/resources/tags) v předplatném, pouze pokud je počet značek 10 000 nebo méně. Stále můžete najít prostředek podle značky Pokud počet překročí 10 000.  

<sup>3</sup>tyto funkce se už nevyžadují se skupinami prostředků Azure a Resource Manageru.

> [!NOTE]
> Počet jader virtuálního počítače mají regionální celkový limit. Mají také omezení pro místní za velikost series, jako je Dv2 a F. Tato omezení se vynucují samostatně. Představte si například předplatné s omezením celkového počtu 30 jader virtuálního počítače na oblast USA – východ, omezením počtu 30 jader na řadu A a 30 jader na řadu D. Toto předplatné můžete nasadit 30 virtuálních počítačů řady A1, nebo 30 virtuálních počítačů řady D1 nebo kombinaci obou, která nepřekročí celkový počet 30 jader. Příklad kombinace je 10 virtuálních počítačů řady A1 a 20 virtuálních počítačů řady D1.  
> <!-- -->
> 
> 

