---
title: Ukázkový skript Azure CLI – Obměna přístupových klíčů účtu úložiště | Microsoft Docs
description: Vytvoříte účet služby Azure Storage a pak načtete a obměníte přístupové klíče tohoto účtu.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: cli
ms.topic: sample
ms.date: 10/20/2020
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: 08e1b3837863b197f8463a0d969e78afab2b9858
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92370402"
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
| [az group create](/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az storage account create](/cli/azure/storage/account) | Vytvoří účet služby Azure Storage v zadané skupině prostředků. |
| [az storage account keys list](/cli/azure/storage/account/keys) | Zobrazí přístupové klíče účtu úložiště pro zadaný účet. |
| [az storage account keys renew](/cli/azure/storage/account/keys) | Znovu vygeneruje primární nebo sekundární přístupový klíč účtu úložiště. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro úložiště najdete v tématu [Ukázky v Azure CLI pro úložiště objektů blob v Azure](../blobs/storage-samples-blobs-cli.md).
