---
title: Získání spravované skupiny prostředků & změně velikosti virtuálních počítačů – Azure PowerShell
description: Poskytuje Azure PowerShell ukázkový skript, který načte spravovanou skupinu prostředků pro spravovanou aplikaci Azure. Skript změní velikost virtuálních počítačů.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 602aaeb67ca081ebac71ca1d6d24a2de3c020603
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86055983"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Získání prostředků ve spravované skupině prostředků a změna velikosti virtuálních počítačů pomocí PowerShellu

Tento skript načte prostředky ze spravované skupiny prostředků a změní velikost virtuálních počítačů v této skupině prostředků.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k nasazení spravované aplikace používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzManagedApplication](/powershell/module/az.resources/get-azmanagedapplication) | Vypíše spravované aplikace. Zadáním názvu skupiny prostředků se zaměřte na výsledky. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Vypíše prostředky. Zadejte skupinu prostředků a typ prostředku pro zaměření na výsledek. |
| [Update-AzVM](/powershell/module/az.compute/update-azvm) | Aktualizuje velikost virtuálního počítače. |


## <a name="next-steps"></a>Další kroky

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](../overview.md).
* Další informace o PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/get-started-azureps).
