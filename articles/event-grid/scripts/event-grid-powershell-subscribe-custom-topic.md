---
title: Ukázkový skript Azure PowerShellu – Přihlášení k odběru vlastního tématu | Microsoft Docs
description: Tento článek obsahuje ukázkový skript Azure PowerShell, který ukazuje, jak se přihlásit k odběru Event Grid událostí pro vlastní téma.
ms.devlang: powershell
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: f37a90b84e5e55298efc0b3cd53812db95e4d86d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87460724"
---
# <a name="subscribe-to-events-for-a-custom-topic-with-powershell"></a>Přihlášení k odběru událostí vlastního tématu pomocí PowerShellu

Tento skript vytvoří odběr Event Gridu pro události vlastního tématu.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Vzorový skript ve verzi Preview vyžaduje modul Event Grid. Chcete-li nainstalovat, spusťte příkaz `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

## <a name="sample-script---stable"></a>Ukázkový skript – stabilní

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-custom-topic/subscribe-to-custom-topic.ps1 "Subscribe to custom topic")]

## <a name="sample-script---preview-module"></a>Vzorový modul Script-Preview

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-custom-topic-preview/subscribe-to-custom-topic-preview.ps1 "Subscribe to custom topic")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření odběru událostí používá následující příkaz. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) | Vytvoří odběr Event Gridu. |

## <a name="next-steps"></a>Další kroky

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](../overview.md).
* Další informace o PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/get-started-azureps).
