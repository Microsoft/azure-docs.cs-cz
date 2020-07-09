---
title: Azure PowerShell – přihlášení k odběru skupiny prostředků
description: Tento článek obsahuje ukázkový skript Azure PowerShell, který ukazuje, jak se přihlásit k odběru Event Grid událostí pro skupinu prostředků a filtrovat prostředek.
ms.devlang: powershell
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: ebb9b1ce729d5a2690492401bc4fd351e86f3462
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171206"
---
# <a name="subscribe-to-events-for-a-resource-group-and-filter-for-a-resource-with-powershell"></a>Přihlášení k odběru událostí skupiny prostředků a vyfiltrování prostředku pomocí PowerShellu

Tento skript vytvoří odběr Event Gridu pro události skupiny prostředků. Používá filtr k získání událostí pouze pro konkrétní prostředek ve skupině prostředků.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Ukázkový skript – stabilní

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/filter-events/filter-events.ps1 "Filter events")]

## <a name="sample-script---preview-module"></a>Vzorový modul Script-Preview

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Vzorový skript ve verzi Preview vyžaduje modul Event Grid. Chcete-li nainstalovat, spusťte příkaz`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

[!code-powershell[main](../../../powershell_scripts/event-grid/filter-events-preview/filter-events-preview.ps1 "Filter events")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření odběru událostí používá následující příkaz. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | Vytvoří odběr Event Gridu. |

## <a name="next-steps"></a>Další kroky

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](../overview.md).
* Další informace o PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/azure/get-started-azureps).
