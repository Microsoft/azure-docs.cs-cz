---
title: Ukázkový skript Azure PowerShell – nasazení spravované aplikace
description: Poskytuje vzorový ukázkový skript Azure PowerShell, který nasadí definici spravované aplikace do předplatného.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: a2687e9c943df8454ff42a17f44866dcdb7f4730
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86055881"
---
# <a name="deploy-a-managed-application-for-a-service-catalog-with-powershell"></a>Nasazení spravované aplikace pro katalog služeb pomocí PowerShellu

Tento skript nasadí definici spravované aplikace z katalogu služeb.


[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/create-application/create-application.ps1 "Create application")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k nasazení spravované aplikace používá následující příkaz. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzManagedApplication](/powershell/module/az.resources/new-azmanagedapplication) | Vytvoří spravovanou aplikaci. Zadejte pro šablonu parametry a ID definice. |


## <a name="next-steps"></a>Další kroky

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](../overview.md).
* Další informace o PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/get-started-azureps).
