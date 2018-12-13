---
title: Ukázkový skript Azure PowerShellu – Přihlášení k odběru účtu úložiště objektů blob | Microsoft Docs
description: Ukázkový skript Azure PowerShellu – Přihlášení k odběru účtu úložiště objektů blob
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
ms.openlocfilehash: bc8726499f900602bf1dbfc58a7ea2574a483245
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273897"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-powershell"></a>Přihlášení k odběru událostí účtu úložiště objektů blob pomocí PowerShellu

Tento skript vytvoří odběr Event Gridu pro události účtu úložiště objektů blob.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Ukázkový skript – stabilní

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.ps1 "Subscribe to Blob storage")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření odběru událostí používá následující příkaz. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzureRmEventGridSubscription](https://docs.microsoft.com/powershell/module/azurerm.eventgrid/new-azurermeventgridsubscription) | Vytvoří odběr Event Gridu. |

## <a name="next-steps"></a>Další postup

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](../overview.md).
* Další informace o PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/azure/get-started-azureps).
