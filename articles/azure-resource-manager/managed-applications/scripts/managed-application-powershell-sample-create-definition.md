---
title: Vytvořit definici spravované aplikace – Azure PowerShell
description: Poskytuje ukázkový skript Azure PowerShell, který v předplatném Azure vytvoří definici spravované aplikace.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: b605d63db09b81bfe73eca8f4abd7a38d2325004
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86056017"
---
# <a name="create-a-managed-application-definition-with-powershell"></a>Vytvoření definice spravované aplikace pomocí PowerShellu

Tento skript publikuje definici spravované aplikace do katalogu služeb.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/create-definition/create-definition.ps1 "Create definition")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření definice spravované aplikace používá následující příkaz. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzManagedApplicationDefinition](/powershell/module/az.resources/new-azmanagedapplicationdefinition) | Vytvoří definici spravované aplikace. Zadejte balíček obsahující požadované soubory. |


## <a name="next-steps"></a>Další kroky

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](../overview.md).
* Další informace o PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/get-started-azureps).
