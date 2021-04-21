---
title: Ukázkový skript Azure CLI – vytvoření účtu Batch – služba Batch
description: Tento skript vytvoří účet Azure Batch v režimu služby Batch a předvede dotazování a aktualizaci různých vlastností účtu.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3d978cf56e92abf9824c116e51f94d53bbe14bbb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768318"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>Příklad rozhraní příkazového řádku: Vytvoření účtu Batch v režimu služby Batch

Tento skript vytvoří účet Azure Batch v režimu služby Batch a předvede dotazování a aktualizaci různých vlastností účtu. Při vytvoření účtu Batch ve výchozím režimu služby Batch se jeho výpočetní uzly přiřadí interně službou Batch. Na přidělené výpočetní uzly se vztahuje samostatná kvóta virtuálních procesorů (jader) a účet se může ověřovat přes přihlašovací údaje sdíleného klíče nebo token Azure Active Directory.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- V tomto kurzu se vyžaduje verze rozhraní příkazového řádku Azure 2.0.20 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná. 

## <a name="example-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Vytvoří účet Batch. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Vytvoří účet úložiště. |
| [az batch account set](/cli/azure/batch/account#az_batch_account_set) | Aktualizuje vlastnosti účtu Batch.  |
| [az batch account show](/cli/azure/batch/account#az_batch_account_show) | Načte podrobnosti o zadaném účtu Batch.  |
| [az batch account keys list](/cli/azure/batch/account/keys#az_batch_account_keys_list) | Načte přístupové klíče zadaného účtu Batch.  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Provede ověření v zadaném účtu Batch pro další práci s rozhraním příkazového řádku.  |
| [az group delete](/cli/azure/group#az_group_delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).
