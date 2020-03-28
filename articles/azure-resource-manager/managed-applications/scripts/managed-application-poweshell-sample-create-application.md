---
title: Ukázka skriptu Azure PowerShellu – nasazení spravované aplikace
description: Poskytuje ukázkový skript Prostředí Azure PowerShell, který nasadí definici spravované aplikace do předplatného.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: e076128a5be453028635b1657bf3f90980863148
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75650167"
---
# <a name="deploy-a-managed-application-for-a-service-catalog-with-powershell"></a>Nasazení spravované aplikace pro katalog služeb pomocí Prostředí PowerShell

Tento skript nasadí definici spravované aplikace z katalogu služeb.


[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/create-application/create-application.ps1 "Create application")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k nasazení spravované aplikace používá následující příkaz. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Nová aplikace AzManaged](https://docs.microsoft.com/powershell/module/az.resources/new-azmanagedapplication) | Vytvoří spravovanou aplikaci. Zadejte pro šablonu parametry a ID definice. |


## <a name="next-steps"></a>Další kroky

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](../overview.md).
* Další informace o PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/azure/get-started-azureps).
