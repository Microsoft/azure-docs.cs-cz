---
title: Ukázkový skript Azure CLI – Výpočet velikosti kontejneru objektů blob | Microsoft Docs
description: Vypočítejte velikost kontejneru v úložišti objektů blob v Azure součtem velikostí jednotlivých objektů blob v kontejneru.
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
ms.date: 06/28/2017
ms.author: tamram
ms.openlocfilehash: c38a49e82a71a23fdf621f5ac350c4242ffc2f8f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
ms.locfileid: "29847103"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Výpočet velikosti kontejneru úložiště objektů blob

Tento skript vypočítá velikost kontejneru v úložišti objektů blob v Azure součtem velikostí jednotlivých objektů blob v kontejneru.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Tento skript rozhraní příkazového řádku poskytuje odhadovanou velikost kontejneru a neměl by se používat k výpočtům fakturace.

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/storage/calculate-container-size/calculate-container-size.sh?highlight=2-3 "Calculate container size")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, kontejner a všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k výpočtu velikosti kontejneru úložiště objektů blob používá následující příkazy. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az storage blob upload](/cli/azure/storage/account#az_storage_account_create) | Nahraje místní soubory do kontejneru úložiště objektů blob v Azure. |
| [az storage blob list](/cli/azure/storage/account/keys#az_storage_account_keys_list) | Vypíše objekty blob v kontejneru úložiště objektů blob v Azure. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro úložiště najdete v tématu [Ukázky v Azure CLI pro úložiště objektů blob v Azure](../blobs/storage-samples-blobs-cli.md).
