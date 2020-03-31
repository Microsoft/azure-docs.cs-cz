---
title: Jak migrovat Azure API Management napříč oblastmi
description: Zjistěte, jak migrovat instanci správy rozhraní API z jedné oblasti do druhé.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 3294a7b2112e9527041ef343f4452aedb7a2a272
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073467"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>Jak migrovat Azure API Management napříč oblastmi
Chcete-li migrovat instance správy rozhraní API z jedné oblasti Azure do jiné, můžete použít funkci [zálohování a obnovení.](api-management-howto-disaster-recovery-backup-restore.md) Měli byste zvolit stejnou cenovou úroveň správy rozhraní API ve zdrojových a cílových oblastech. 

> [!NOTE]
> Zálohování a obnovení nebude fungovat při migraci mezi různými typy cloudu. Za tímto účelem budete muset exportovat prostředek [jako šablonu](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates). Potom přizpůsobte exportovnou šablonu pro cílovou oblast Azure a znovu vytvořte prostředek. 

## <a name="option-1-use-a-different-api-management-instance-name"></a>Možnost 1: Použití jiného názvu instance správy rozhraní API

1. V cílové oblasti vytvořte novou instanci správy rozhraní API se stejnou cenovou vrstvou jako instance source API Management. Nová instance by měla mít jiný název. 
1. Zálohování existující instance správy rozhraní API na účet úložiště.
1. Obnovte zálohu vytvořenou v kroku 2 do nové instance správy rozhraní API vytvořené v nové oblasti v kroku 1.
1. Pokud máte vlastní doménu, která ukazuje na instanci správy rozhraní API zdrojové oblasti, aktualizujte vlastní doménu CNAME tak, aby ukazovala na novou instanci správy rozhraní API. 


## <a name="option-2-use-the-same-api-management-instance-name"></a>Možnost 2: Použití stejného názvu instance správy rozhraní API

> [!NOTE]
> Tato možnost bude mít za následek prostoje během migrace.

1. Zálohujte instanci správy rozhraní API ve zdrojové oblasti na účet úložiště.
1. Odstraňte správu rozhraní API ve zdrojové oblasti. 
1. Vytvořte novou instanci správy rozhraní API v cílové oblasti se stejným názvem jako ve zdrojové oblasti.
1. Obnovte zálohu vytvořenou v kroku 1 do nové instance správy rozhraní API v cílové oblasti.  


## <a name="next-steps"></a><a name="next-steps"> </a>Další kroky
* Další informace o funkci zálohování a obnovení naleznete v tématu [implementace zotavení po havárii](api-management-howto-disaster-recovery-backup-restore.md).
* Informace o migraci prostředků Azure najdete v [tématu Pokyny k migraci mezi oblastmi Azure](https://github.com/Azure/Azure-Migration-Guidance).