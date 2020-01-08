---
title: Získání spravované skupiny prostředků & změně velikosti virtuálních počítačů – Azure PowerShell
description: Poskytuje Azure PowerShell ukázkový skript, který načte spravovanou skupinu prostředků pro spravovanou aplikaci Azure. Skript změní velikost virtuálních počítačů.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 695023f4700370cbe5e9b345f513e38d1cf1fc0c
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650181"
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
| [Get-AzManagedApplication](https://docs.microsoft.com/powershell/module/az.resources/get-azmanagedapplication) | Vypíše spravované aplikace. Zadáním názvu skupiny prostředků se zaměřte na výsledky. |
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | Vypíše prostředky. Zadejte skupinu prostředků a typ prostředku pro zaměření na výsledek. |
| [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) | Aktualizuje velikost virtuálního počítače. |


## <a name="next-steps"></a>Další kroky

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](../overview.md).
* Další informace o PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/azure/get-started-azureps).
