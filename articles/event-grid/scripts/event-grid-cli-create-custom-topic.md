---
title: Ukázkový skript Azure CLI – Vytvoření vlastního tématu | Microsoft Docs
description: Tento článek obsahuje ukázkový skript rozhraní příkazového řádku Azure CLI, který ukazuje, jak vytvořit vlastní téma Azure Event Grid.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: d61dac0e76fc7b7006fa36f1cb96965ccee3d863
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87494124"
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
| [az eventgrid topic create](/cli/azure/eventgrid/topic#az-eventgrid-topic-create) | Vytvoří vlastní téma Event Gridu. |


## <a name="next-steps"></a>Další kroky

* Informace o dotazování předplatných najdete v tématu [Dotazování odběrů Event Gridu](../query-event-subscriptions.md).
* Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).
