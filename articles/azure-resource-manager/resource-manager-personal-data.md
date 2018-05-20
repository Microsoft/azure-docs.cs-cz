---
title: Osobní údaje v Azure Resource Manager | Microsoft Docs
description: Zjistěte, jak spravovat osobní data související s operacemi Azure Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2018
ms.author: tomfitz
ms.openlocfilehash: 5c176dd185a9d2a245045e9c954279506d713e5f
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Spravovat osobní data související s Azure Resource Manager

Aby se zabránilo odhalení citlivých informací, odstraňte všechny osobní údaje, které jste poskytli v nasazení, skupiny prostředků nebo značky. Azure Resource Manager poskytuje operace, které vám umožní spravovat osobní údaje, které jste poskytli v nasazení, skupiny prostředků nebo značky.

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Odstranit osobní data v historii nasazení

Pro nasazení Resource Manager uchovává stavové zprávy v historii nasazení a hodnot parametrů. Tyto hodnoty uchová, dokud odstranit nasazení z historie. Pokud chcete zobrazit, pokud jste zadali osobní data v těchto hodnot, seznam nasazení. Pokud zjistíte, osobní údaje, odstraňte nasazení z historie.

Do seznamu **nasazení** v historii, použijte:

* [Seznam podle skupiny prostředků](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/Get-AzureRmResourceGroupDeployment)
* [seznam az skupiny nasazení](/cli/azure/group/deployment#az-group-deployment-list)

Chcete-li odstranit **nasazení** z historie, použijte:

* [Odstranění](/rest/api/resources/deployments/delete)
* [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/Remove-AzureRmResourceGroupDeployment)
* [Odstranění nasazení skupiny az](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Odstranit osobní data v názvech skupin prostředků

Název skupiny prostředků trvá, dokud neodstraníte skupině prostředků. Pokud chcete zobrazit, pokud jste zadali osobní data v názvech, seznam skupin prostředků. Pokud zjistíte, osobní údaje, [přesunout prostředky](resource-group-move-resources.md) na novou skupinu prostředků a odstranění skupiny prostředků se osobní data v názvu.

Do seznamu **skupiny prostředků**, použijte:

* [seznam](/rest/api/resources/resourcegroups/list)
* [Get-AzureRmResourceGroup](/powershell/module/azurerm.resources/Get-AzureRmResourceGroup)
* [seznam skupiny az](/cli/azure/group#az-group-list)

Chcete-li odstranit **skupiny prostředků**, použijte:

* [Odstranění](/rest/api/resources/resourcegroups/delete)
* [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/Remove-AzureRmResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Odstranit osobní data v značky

Značky názvy a hodnoty uchová, dokud odstranit ani změnit značky. Pokud jste zadali osobních údajů v značek najdete seznam značek. Pokud zjistíte, osobní údaje, odstraňte značek.

Do seznamu **značky**, použijte:

* [seznam](/rest/api/resources/tags/list)
* [Get-AzureRmTag](/powershell/module/azurerm.tags/get-azurermtag)
* [seznam značek az](/cli/azure/tag#az-tag-list)

Chcete-li odstranit **značky**, použijte:

* [Odstranění](/rest/api/resources/tags/delete)
* [Odebrat AzureRmTag](/powershell/module/azurerm.tags/remove-azurermtag)
* [odstranění az značky](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Další postup
* Přehled služby Správce prostředků Azure, najdete [co je Resource Manager?](resource-group-overview.md)