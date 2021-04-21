---
title: Ukázkový skript Azure CLI – nasazení spravované aplikace
description: Poskytuje ukázkový skript Azure CLI, který nasadí definici spravované aplikace Azure do předplatného.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3ea2baa897efb5c1a01b32e92e76a69c9d1f231c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775483"
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>Nasazení spravované aplikace pro katalog služeb pomocí Azure CLI

Tento skript nasadí definici spravované aplikace z katalogu služeb. 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k nasazení spravované aplikace používá následující příkaz. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az managedapp create](/cli/azure/managedapp#az_managedapp_create) | Vytvoří spravovanou aplikaci. Zadejte pro šablonu parametry a ID definice. |


## <a name="next-steps"></a>Další kroky

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](../overview.md).
* Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).
