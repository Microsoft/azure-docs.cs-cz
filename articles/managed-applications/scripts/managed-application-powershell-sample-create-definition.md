---
title: Azure skript prostředí PowerShell ukázkový – vytvoření definice spravované aplikace | Microsoft Docs
description: Azure skript prostředí PowerShell ukázkový – vytvoření definice spravované aplikace
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
ms.openlocfilehash: ee721b7c68924f5e05fbdc4bc4b919837d4cc05b
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2017
ms.locfileid: "23940953"
---
# <a name="create-a-managed-application-definition-with-powershell"></a>Vytvoření definice spravovaných aplikací pomocí prostředí PowerShell

Tento skript publikuje definici spravované aplikace do katalogu služeb.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/managed-applications/create-definition/create-definition.ps1 "Create definition")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkaz k vytvoření definice spravovaných aplikací. Každý příkaz v tabulce odkazy na dokumentaci specifické pro příkaz.

| Příkaz | Poznámky |
|---|---|
| [Nové AzureRmManagedApplicationDefinition](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermmanagedapplicationdefinition) | Vytvořte definici spravované aplikace. Zadejte balíček, který obsahuje požadované soubory. |


## <a name="next-steps"></a>Další kroky

* Úvod do spravovaných aplikací, najdete v části [Azure spravovaných aplikací – přehled](../overview.md).
* Další informace o prostředí PowerShell najdete v tématu [dokumentace Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
