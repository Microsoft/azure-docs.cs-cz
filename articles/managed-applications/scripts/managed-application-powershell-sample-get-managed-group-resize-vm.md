---
title: Ukázkový skript Azure Powershellu – získání spravované skupiny prostředků a změna velikosti virtuálních počítačů | Dokumentace Microsoftu
description: Ukázkový skript Azure Powershellu – získání spravované skupiny prostředků a změna velikosti virtuálních počítačů
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: baad12379c01ac955f1b0e036398e5889f3681b2
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203194"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Získání prostředků ve spravované skupiny prostředků a změna velikosti virtuálních počítačů pomocí Powershellu

Tento skript načte prostředky ze spravované skupiny prostředků a změní velikost virtuálních počítačů v této skupině prostředků.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k nasazení spravované aplikace používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzureRmManagedApplication](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermmanagedapplication) | Vypíše spravované aplikace. Zadejte název skupiny prostředků pro zúžení výsledků. |
| [Get-AzureRmResource](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresource) | Vypíše prostředky. Zadejte skupinu prostředků a typ prostředku pro zúžení výsledků. |
| [Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) | Aktualizuje velikost virtuálního počítače. |


## <a name="next-steps"></a>Další postup

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](../overview.md).
* Další informace o PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/azure/get-started-azureps).
