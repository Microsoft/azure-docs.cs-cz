---
title: Prostředí Azure PowerShell ukázkový skript – získat skupinu spravovaných prostředků a změně velikosti virtuálních počítačů | Microsoft Docs
description: Azure ukázkový skript prostředí PowerShell - získat skupinu spravovaných prostředků a změně velikosti virtuálních počítačů
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: poweshell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: f549f26cb3f9fdb2d805d2efb2c0e1706abe3edb
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2017
ms.locfileid: "23940939"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Získat prostředky ve skupině spravovaných prostředků a změně velikosti virtuálních počítačů pomocí prostředí PowerShell

Tento skript načte zdroje ze skupiny spravovaných prostředků a změní velikost virtuální počítače v příslušné skupině prostředků.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy k nasazení spravované aplikace. Každý příkaz v tabulce odkazy na dokumentaci specifické pro příkaz.

| Příkaz | Poznámky |
|---|---|
| [Get-AzureRmManagedApplication](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermmanagedapplication) | Seznam spravovaných aplikací. Zadejte název skupiny prostředků a zaměřit se výsledky. |
| [Get-AzureRmResource](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresource) | Zobrazí seznam prostředků. Zadejte skupinu prostředků a typ prostředku, který umožňuje zaměřit se výsledek. |
| [Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) | Aktualizujte velikost virtuálního počítače. |


## <a name="next-steps"></a>Další kroky

* Úvod do spravovaných aplikací, najdete v části [Azure spravovaných aplikací – přehled](../overview.md).
* Další informace o prostředí PowerShell najdete v tématu [dokumentace Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
