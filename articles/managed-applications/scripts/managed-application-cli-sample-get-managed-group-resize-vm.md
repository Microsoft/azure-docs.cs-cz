---
title: Ukázkový skript Azure CLI – Získání spravované skupiny prostředků a změna velikosti virtuálních počítačů | Microsoft Docs
description: Poskytuje ukázkový skript Azure CLI, který načte spravovanou skupinu prostředků ve spravované aplikaci Azure. Skript změní velikost virtuálních počítačů.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 7218e2fd033614b5e9f77cca34a5db82e3571094
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330149"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Získání prostředků ve spravované skupině prostředků a změna velikosti virtuálních počítačů pomocí Azure CLI

Tento skript načte prostředky ze spravované skupiny prostředků a změní velikost virtuálních počítačů v této skupině prostředků.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k nasazení spravované aplikace používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az managedapp list](https://docs.microsoft.com/cli/azure/managedapp#az-managedapp-list) | Vypíše spravované aplikace. Pro zúžení výsledků zadejte hodnoty dotazu. |
| [az resource list](https://docs.microsoft.com/cli/azure/resource#az-resource-list) | Vypíše prostředky. Pro zúžení výsledků zadejte skupinu prostředků a hodnoty dotazu. |
| [az vm resize](https://docs.microsoft.com/cli/azure/vm#az-vm-resize) | Aktualizuje velikost virtuálního počítače. |


## <a name="next-steps"></a>Další kroky

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](../overview.md).
* Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).
