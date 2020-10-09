---
title: Azure PowerShell – přihlášení k odběru účtu úložiště objektů BLOB
description: Tento článek obsahuje ukázkový skript Azure PowerShell, který ukazuje, jak se přihlásit k odběru Event Grid událostí pro účet Blob Storage.
ms.devlang: powershell
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: 15daa9843eb721ebf9caa2ae49e75fadbd0a4be8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87460792"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-powershell"></a>Přihlášení k odběru událostí účtu úložiště objektů blob pomocí PowerShellu

Tento skript vytvoří odběr Event Gridu pro události účtu úložiště objektů blob.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Ukázkový skript – stabilní

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.ps1 "Subscribe to Blob storage")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření odběru událostí používá následující příkaz. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) | Vytvoří odběr Event Gridu. |

## <a name="next-steps"></a>Další kroky

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](../overview.md).
* Další informace o PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/get-started-azureps).
