---
title: Azure skript prostředí PowerShell ukázkový – nasadit spravované aplikace | Microsoft Docs
description: Azure skript prostředí PowerShell ukázkový – nasazení definici spravované aplikace
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
ms.openlocfilehash: 2429d561beffed5bc171b9dbc2c2c9c88eba3313
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2017
ms.locfileid: "23941247"
---
# <a name="deploy-a-managed-application-for-a-service-catalog-with-powershell"></a>Nasadit spravované aplikace pro katalog služeb v prostředí PowerShell

Tento skript nasadí definice spravované aplikace z katalogu služeb.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/managed-applications/create-application/create-application.ps1 "Create application")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkaz k nasazení spravované aplikace. Každý příkaz v tabulce odkazy na dokumentaci specifické pro příkaz.

| Příkaz | Poznámky |
|---|---|
| [Nové AzureRmManagedApplication](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermmanagedapplication) | Vytvoření spravované aplikace. Zadejte ID definice a parametry šablony. |


## <a name="next-steps"></a>Další kroky

* Úvod do spravovaných aplikací, najdete v části [Azure spravovaných aplikací – přehled](../overview.md).
* Další informace o prostředí PowerShell najdete v tématu [dokumentace Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
