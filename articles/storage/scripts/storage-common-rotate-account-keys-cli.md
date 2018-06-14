---
title: Ukázkový skript Azure CLI – Obměna přístupových klíčů účtu úložiště | Microsoft Docs
description: Vytvoříte účet služby Azure Storage a pak načtete a obměníte přístupové klíče tohoto účtu.
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/22/2017
ms.author: tamram
ms.openlocfilehash: 160509a5a82b71b281d57d97e103bb4190605b7c
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
ms.locfileid: "29847807"
---
# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>Vytvoření účtu úložiště a obměna přístupových klíčů tohoto účtu

Tento skript vytvoří účet služby Azure Storage, zobrazí přístupové klíče tohoto nového účtu úložiště a pak klíče obnoví (obmění).

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.sh "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, účet úložiště a všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření účtu úložiště a načtení a obměně jeho přístupových klíčů používá následující příkazy. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Vytvoří účet služby Azure Storage v zadané skupině prostředků. |
| [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list) | Zobrazí přístupové klíče účtu úložiště pro zadaný účet. |
| [az storage account keys renew](/cli/azure/storage/account/keys#az_storage_account_keys_renew) | Znovu vygeneruje primární nebo sekundární přístupový klíč účtu úložiště. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro úložiště najdete v tématu [Ukázky v Azure CLI pro úložiště objektů blob v Azure](../blobs/storage-samples-blobs-cli.md).
