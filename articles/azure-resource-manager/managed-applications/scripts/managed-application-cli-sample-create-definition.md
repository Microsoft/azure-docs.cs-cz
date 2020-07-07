---
title: Vytvoření definice spravované aplikace – Azure CLI
description: Poskytuje ukázkový skript Azure CLI, který v předplatném vytvoří definici spravované aplikace.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 63182eb382e96f47c1c90dc5d212e064d0945ba7
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86056043"
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
| [az managedapp definition create](/cli/azure/managedapp/definition#az-managedapp-definition-create) | Vytvoří definici spravované aplikace. Zadejte balíček obsahující požadované soubory. |


## <a name="next-steps"></a>Další kroky

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](../overview.md).
* Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).
