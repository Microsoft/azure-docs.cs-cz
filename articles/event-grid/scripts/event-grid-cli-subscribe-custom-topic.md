---
title: Ukázkový skript Azure CLI – Přihlášení k odběru vlastního tématu | Microsoft Docs
description: Tento článek obsahuje ukázkový skript Azure CLI, který ukazuje, jak se přihlásit k odběru Event Gridch událostí pro vlastní téma.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: eaec3521c90a0fb6425f5b68425ef608ae6db3c5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871304"
---
# <a name="subscribe-to-events-for-a-custom-topic-with-azure-cli"></a>Přihlášení k odběru událostí vlastního tématu pomocí Azure CLI

Tento skript vytvoří odběr Event Gridu pro události vlastního tématu.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Ukázkový skript ve verzi Preview vyžaduje rozšíření Event Grid. Pokud ho chcete nainstalovat, spusťte příkaz `az extension add --name eventgrid`.

## <a name="sample-script---stable"></a>Ukázkový skript – stabilní

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-custom-topic/subscribe-to-custom-topic.sh "Subscribe to custom topic")]

## <a name="sample-script---preview-extension"></a>Ukázkový skript – rozšíření ve verzi Preview

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-custom-topic-preview/subscribe-to-custom-topic-preview.sh "Subscribe to custom topic")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření odběru událostí používá následující příkaz. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) | Vytvoří odběr Event Gridu. |
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) – verze rozšíření | Vytvoří odběr Event Gridu. |

## <a name="next-steps"></a>Další kroky

* Informace o dotazování předplatných najdete v tématu [Dotazování odběrů Event Gridu](../query-event-subscriptions.md).
* Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).
