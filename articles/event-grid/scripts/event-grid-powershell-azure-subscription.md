---
title: Azure PowerShell – přihlášení k odběru předplatného Azure
description: Tento článek obsahuje ukázkový skript Azure PowerShell, který ukazuje, jak se přihlásit k odběru Event Grid událostí pro předplatné Azure.
ms.devlang: powershell
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: 0aae1d64b789cedab6420ac1f323a2379bff17be
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87460809"
---
# <a name="subscribe-to-events-for-an-azure-subscription-with-powershell"></a>Přihlášení k odběru událostí předplatného Azure pomocí PowerShellu

Tento skript vytvoří odběr Event Gridu pro události předplatného Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Ukázkový skript – stabilní

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-azure-subscription/subscribe-to-azure-subscription.ps1 "Subscribe to Azure subscription")]

## <a name="sample-script---preview-module"></a>Vzorový modul Script-Preview

Tento ukázkový skript ve verzi Preview vyžaduje modul Event Grid. Chcete-li nainstalovat, spusťte příkaz `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-azure-subscription-preview/subscribe-to-azure-subscription-preview.ps1 "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření odběru událostí používá následující příkaz. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) | Vytvoří odběr Event Gridu. |

## <a name="next-steps"></a>Další kroky

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](../overview.md).
* Další informace o PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/get-started-azureps).
