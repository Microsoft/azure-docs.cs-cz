---
title: Ukázkový skript Azure CLI – Přihlášení k odběru skupiny prostředků | Microsoft Docs
description: Tento článek obsahuje ukázkový skript Azure CLI, který ukazuje, jak se přihlásit k odběru Azure Event Gridch událostí pro skupinu prostředků.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: f655ed3f2b6a3cef89c493137bd34732a60e414a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87494090"
---
# <a name="subscribe-to-events-for-a-resource-group-with-azure-cli"></a>Přihlášení k odběru událostí skupiny prostředků pomocí Azure CLI

Tento skript vytvoří odběr Event Gridu pro události skupiny prostředků. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Ukázkový skript ve verzi Preview vyžaduje rozšíření Event Grid. Pokud ho chcete nainstalovat, spusťte příkaz `az extension add --name eventgrid`.

## <a name="sample-script---stable"></a>Ukázkový skript – stabilní

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-resource-group/subscribe-to-resource-group.sh "Subscribe to resource group")]

## <a name="sample-script---preview-extension"></a>Ukázkový skript – rozšíření ve verzi Preview

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-resource-group-preview/subscribe-to-resource-group-preview.sh "Subscribe to resource group")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření odběru událostí používá následující příkaz. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | Vytvoří odběr Event Gridu. |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) – verze rozšíření | Vytvoří odběr Event Gridu. |

## <a name="next-steps"></a>Další kroky

* Informace o dotazování předplatných najdete v tématu [Dotazování odběrů Event Gridu](../query-event-subscriptions.md).
* Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).
