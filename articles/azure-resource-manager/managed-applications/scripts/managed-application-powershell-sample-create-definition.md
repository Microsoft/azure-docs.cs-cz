---
title: Vytvořit definici spravované aplikace – Azure PowerShell
description: Poskytuje ukázkový skript Azure PowerShell, který v předplatném Azure vytvoří definici spravované aplikace.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 09fe1455f0dba57fd23aeeb66acb1f99fc2513d4
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650202"
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
| [New-AzManagedApplicationDefinition](https://docs.microsoft.com/powershell/module/az.resources/new-azmanagedapplicationdefinition) | Vytvoří definici spravované aplikace. Zadejte balíček obsahující požadované soubory. |


## <a name="next-steps"></a>Další kroky

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](../overview.md).
* Další informace o PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/azure/get-started-azureps).
