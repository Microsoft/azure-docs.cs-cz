---
title: Ukázkový skript Azure PowerShellu – Přihlášení k odběru vlastního tématu | Microsoft Docs
description: Ukázkový skript Azure PowerShellu – Přihlášení k odběru vlastního tématu
services: event-grid
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2018
ms.author: tomfitz
ms.openlocfilehash: cddf60c3e9356b9c18ab60adbea8eb5af768ad90
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269246"
---
# <a name="subscribe-to-events-for-a-custom-topic-with-powershell"></a>Přihlášení k odběru událostí vlastního tématu pomocí PowerShellu

Tento skript vytvoří odběr Event Gridu pro události vlastního tématu.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Ukázkový skript ve verzi preview vyžaduje modul služby Event Grid. Pokud chcete nainstalovat, spusťte `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

## <a name="sample-script---stable"></a>Ukázkový skript – stabilní

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-custom-topic/subscribe-to-custom-topic.ps1 "Subscribe to custom topic")]

## <a name="sample-script---preview-module"></a>Ukázkový skript – modul ve verzi preview

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-custom-topic-preview/subscribe-to-custom-topic-preview.ps1 "Subscribe to custom topic")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření odběru událostí používá následující příkaz. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzureRmEventGridSubscription](https://docs.microsoft.com/powershell/module/azurerm.eventgrid/new-azurermeventgridsubscription) | Vytvoří odběr Event Gridu. |

## <a name="next-steps"></a>Další postup

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](../overview.md).
* Další informace o PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/azure/get-started-azureps).
