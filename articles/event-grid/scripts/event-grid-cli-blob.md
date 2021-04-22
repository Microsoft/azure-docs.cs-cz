---
title: Ukázkový skript Azure CLI – Přihlášení k odběru účtu úložiště objektů blob | Microsoft Docs
description: Tento článek obsahuje ukázkový skript Azure CLI, který ukazuje, jak se přihlásit k odběru událostí pro účet Azure Blob Storage.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1bc1f67277741dfa12209451375301cec7a63e9f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871401"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-azure-cli"></a>Přihlášení k odběru událostí účtu úložiště objektů blob pomocí Azure CLI

Tento skript vytvoří odběr Event Gridu pro události účtu úložiště objektů blob. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.sh "Subscribe to Blob storage")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření odběru událostí používá následující příkaz. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) | Vytvoří odběr Event Gridu. |
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) – verze rozšíření | Vytvoří odběr Event Gridu. |

## <a name="next-steps"></a>Další kroky

* Informace o dotazování předplatných najdete v tématu [Dotazování odběrů Event Gridu](../query-event-subscriptions.md).
* Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).
