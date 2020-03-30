---
title: Osobní údaje
description: Zjistěte, jak spravovat osobní data spojená s operacemi Azure Resource Manageru.
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 22cfc1b6096980f3d10db404a1c4e02f2de355d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485257"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Správa osobních dat spojených se Správcem prostředků Azure

Chcete-li se vyhnout odhalení citlivých informací, odstraňte všechny osobní informace, které jste poskytli v nasazeních, skupinách prostředků nebo značkách. Azure Resource Manager poskytuje operace, které vám umožní spravovat osobní data, které jste poskytli v nasazeních, skupinách prostředků nebo značkách.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Odstranění osobních údajů v historii nasazení

Pro nasazení správce prostředků zachová hodnoty parametrů a stavové zprávy v historii nasazení. Tyto hodnoty přetrvávají, dokud neodstraníte nasazení z historie. Chcete-li zjistit, zda jste v těchto hodnotách poskytli osobní údaje, uveďte seznam nasazení. Pokud najdete osobní údaje, odstraňte nasazení z historie.

Chcete-li **vypsat nasazení** v historii, použijte:

* [Seznam podle skupiny prostředků](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [seznam nasazení skupiny az](/cli/azure/group/deployment#az-group-deployment-list)

Chcete-li odstranit **nasazení** z historie, použijte:

* [Odstranit](/rest/api/resources/deployments/delete)
* [Odebrat azResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [odstranění nasazení skupiny az](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Odstranění osobních údajů v názvech skupin prostředků

Název skupiny prostředků přetrvává, dokud skupinu prostředků neodstraníte. Chcete-li zjistit, zda jste v názvech zadali osobní údaje, uveďte skupiny prostředků. Pokud najdete osobní údaje, [přesuňte prostředky](move-resource-group-and-subscription.md) do nové skupiny prostředků a odstraňte skupinu prostředků s osobními údaji v názvu.

Chcete-li vypsat **skupiny prostředků**, použijte:

* [Seznamu](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [seznam skupin az](/cli/azure/group#az-group-list)

Chcete-li odstranit **skupiny prostředků**, použijte:

* [Odstranit](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Odstranění osobních údajů ve značkách

Názvy a hodnoty značek zůstanou zachovány, dokud značku neodstraníte nebo nezměníte. Chcete-li zjistit, zda jste ve značkách zadali osobní údaje, uveďte je. Pokud najdete osobní údaje, odstraňte značky.

Chcete-li **seznam značek**, použijte:

* [Seznamu](/rest/api/resources/tags/list)
* [Získat-AzTag](/powershell/module/az.resources/Get-AzTag)
* [az seznam značek](/cli/azure/tag#az-tag-list)

Chcete-li odstranit **značky**, použijte:

* [Odstranit](/rest/api/resources/tags/delete)
* [Odebrat-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [odstranit značku az](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Další kroky
* Přehled Správce prostředků Azure najdete v tématu [Co je Správce prostředků?](overview.md)