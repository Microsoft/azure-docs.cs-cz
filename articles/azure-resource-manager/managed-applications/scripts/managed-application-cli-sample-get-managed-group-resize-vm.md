---
title: Získání spravované skupiny prostředků & změně velikosti virtuálních počítačů – Azure CLI
description: Poskytuje ukázkový skript Azure CLI, který načte spravovanou skupinu prostředků ve spravované aplikaci Azure. Skript změní velikost virtuálních počítačů.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 179b1b64656d3f97778e183d57797e4b3660fece
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775434"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Získání prostředků ve spravované skupině prostředků a změna velikosti virtuálních počítačů pomocí Azure CLI

Tento skript načte prostředky ze spravované skupiny prostředků a změní velikost virtuálních počítačů v této skupině prostředků.


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k nasazení spravované aplikace používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az managedapp list](/cli/azure/managedapp#az_managedapp_list) | Vypíše spravované aplikace. Pro zúžení výsledků zadejte hodnoty dotazu. |
| [az resource list](/cli/azure/resource#az_resource_list) | Vypíše prostředky. Pro zúžení výsledků zadejte skupinu prostředků a hodnoty dotazu. |
| [az vm resize](/cli/azure/vm#az_vm_resize) | Aktualizuje velikost virtuálního počítače. |


## <a name="next-steps"></a>Další kroky

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](../overview.md).
* Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).
