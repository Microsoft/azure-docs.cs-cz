---
title: Ukázka skriptu Azure PowerShell – získání spravované skupiny prostředků a změna velikosti virtuálních počítačů | Microsoft Docs
description: Poskytuje Azure PowerShell ukázkový skript, který načte spravovanou skupinu prostředků pro spravovanou aplikaci Azure. Skript změní velikost virtuálních počítačů.
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
ms.openlocfilehash: f36ed42e0b7f8e4d1e5b092d2a14fd0cd8178ee0
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330123"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Získání prostředků ve spravované skupině prostředků a změna velikosti virtuálních počítačů pomocí PowerShellu

Tento skript načte prostředky ze spravované skupiny prostředků a změní velikost virtuálních počítačů v této skupině prostředků.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k nasazení spravované aplikace používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzManagedApplication](https://docs.microsoft.com/powershell/module/az.resources/get-azmanagedapplication) | Vypíše spravované aplikace. Zadáním názvu skupiny prostředků se zaměřte na výsledky. |
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | Vypíše prostředky. Zadejte skupinu prostředků a typ prostředku pro zaměření na výsledek. |
| [Update – AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) | Aktualizuje velikost virtuálního počítače. |


## <a name="next-steps"></a>Další kroky

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](../overview.md).
* Další informace o PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/azure/get-started-azureps).
