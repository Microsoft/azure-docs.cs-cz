---
title: Vytvoření definice spravované aplikace – Azure CLI
description: Poskytuje ukázkový skript Azure CLI, který v předplatném vytvoří definici spravované aplikace.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2430b14ce3a3ba578787cefa85d95475c3e9b920
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775452"
---
# <a name="create-a-managed-application-definition-with-azure-cli"></a>Vytvoření definice spravované aplikace pomocí Azure CLI

Tento skript publikuje definici spravované aplikace do katalogu služeb. 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-definition/create-definition.sh "Create definition")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření definice spravované aplikace používá následující příkaz. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az managedapp definition create](/cli/azure/managedapp/definition#az_managedapp_definition_create) | Vytvoří definici spravované aplikace. Zadejte balíček obsahující požadované soubory. |


## <a name="next-steps"></a>Další kroky

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](../overview.md).
* Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).
