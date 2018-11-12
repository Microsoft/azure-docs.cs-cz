---
title: Ukázkový skript Azure CLI – Vytvoření vlastního tématu | Microsoft Docs
description: Ukázkový skript Azure CLI – Vytvoření vlastního tématu
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
ms.openlocfilehash: 16c6586939a6dad248cef3abdabd78faf04a2381
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51034264"
---
# <a name="create-event-grid-custom-topic-with-azure-cli"></a>Vytvoření vlastního tématu Event Gridu pomocí Azure CLI

Tento skript vytvoří vlastní téma Event Gridu.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/event-grid/create-custom-topic/create-custom-topic.sh "Create custom topic")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření vlastního tématu používá následující příkaz. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az eventgrid topic create](https://docs.microsoft.com/cli/azure/eventgrid/topic#az-eventgrid-topic-create) | Vytvoří vlastní téma Event Gridu. |


## <a name="next-steps"></a>Další kroky

* Informace o dotazování předplatných najdete v tématu [Dotazování odběrů Event Gridu](../query-event-subscriptions.md).
* Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).
