---
title: Postup migrace API Management Azure napříč oblastmi
description: Naučte se migrovat instanci API Management z jedné oblasti do druhé.
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
ms.openlocfilehash: 0eed2328aca78402c5f4691bb9b3d07d4f36472e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86250222"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>Postup migrace API Management Azure napříč oblastmi
Pokud chcete migrovat API Management instance z jedné oblasti Azure do jiné, můžete použít funkci [zálohování a obnovení](api-management-howto-disaster-recovery-backup-restore.md) . Měli byste zvolit stejný API Management cenové úrovně ve zdrojové a cílové oblasti. 

> [!NOTE]
> Zálohování a obnovení nebude při migraci mezi různými typy cloudu fungovat. V takovém případě budete muset prostředek exportovat [jako šablonu](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates). Potom Přizpůsobte exportovanou šablonu pro cílovou oblast Azure a znovu vytvořte prostředek. 

## <a name="option-1-use-a-different-api-management-instance-name"></a>Možnost 1: použijte jiný název instance API Management

1. V cílové oblasti vytvořte novou instanci API Management se stejnou cenovou úrovní jako instance zdrojového API Management. Nová instance by měla mít jiný název. 
1. Zálohuje stávající instanci API Management na účet úložiště.
1. Obnovte zálohu vytvořenou v kroku 2 na novou instanci API Management vytvořenou v nové oblasti v kroku 1.
1. Pokud máte vlastní doménu, která odkazuje na API Management instanci zdrojové oblasti, aktualizujte záznam CNAME vlastní domény tak, aby odkazoval na novou instanci API Management. 


## <a name="option-2-use-the-same-api-management-instance-name"></a>Možnost 2: použijte stejný název instance API Management

> [!NOTE]
> Tato možnost způsobí výpadky během migrace.

1. Vytvořte zálohu instance API Management ve zdrojové oblasti do účtu úložiště.
1. Odstraňte API Management ve zdrojové oblasti. 
1. Vytvoří novou instanci API Management v cílové oblasti se stejným názvem jako má ve zdrojové oblasti.
1. Obnovte zálohu vytvořenou v kroku 1 na novou instanci API Management v cílové oblasti.  


## <a name="next-steps"></a><a name="next-steps"> </a>Další kroky
* Další informace o funkci zálohování a obnovení najdete v tématu [implementace zotavení po havárii](api-management-howto-disaster-recovery-backup-restore.md).
* Informace o prostředcích migrace Azure najdete v tématu [pokyny k migraci mezi oblastmi Azure](https://github.com/Azure/Azure-Migration-Guidance).
* [Optimalizujte a Šetřete své výdaje na Cloud](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
