---
title: Ukázkový skript Azure CLI – Přihlášení k odběru předplatného Azure | Microsoft Docs
description: Ukázkový skript Azure CLI – Přihlášení k odběru předplatného Azure
services: event-grid
documentationcenter: na
author: tfitzmac
ms.service: event-grid
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2018
ms.author: tomfitz
ms.openlocfilehash: 9d1aa53ede323c2bb536c74eeaaba9fd28b01712
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037528"
---
# <a name="subscribe-to-events-for-an-azure-subscription-with-azure-cli"></a>Přihlášení k odběru událostí předplatného Azure pomocí Azure CLI

Tento skript vytvoří odběr Event Gridu pro události předplatného Azure.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Ukázkový skript ve verzi Preview vyžaduje rozšíření Event Grid. Pokud ho chcete nainstalovat, spusťte příkaz `az extension add --name eventgrid`.

## <a name="sample-script---stable"></a>Ukázkový skript – stabilní

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-azure-subscription/subscribe-to-azure-subscription.sh "Subscribe to Azure subscription")]

## <a name="sample-script---preview-extension"></a>Ukázkový skript – rozšíření ve verzi Preview

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-azure-subscription-preview/subscribe-to-azure-subscription-preview.sh "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření odběru událostí používá následující příkaz. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | Vytvoří odběr Event Gridu. |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) – verze rozšíření | Vytvoří odběr Event Gridu. |

## <a name="next-steps"></a>Další kroky

* Informace o dotazování předplatných najdete v tématu [Dotazování odběrů Event Gridu](../query-event-subscriptions.md).
* Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).
