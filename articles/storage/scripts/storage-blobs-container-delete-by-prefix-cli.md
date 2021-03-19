---
title: Ukázkový skript Azure CLI – Odstranění kontejnerů na základě předpony | Microsoft Docs
description: Odstraňte Azure Storage kontejnery objektů blob na základě předpony názvu kontejneru a potom nasazení vyčistěte. Příkazy používané v ukázce skriptu najdete v tématu věnovaném odkazům na nápovědu.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: cli
ms.topic: sample
ms.date: 06/22/2017
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: aeccf255004cd4512fbc591942324341504b20f7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87901874"
---
# <a name="use-an-azure-cli-script-to-delete-containers-based-on-container-name-prefix"></a>Použití skriptu Azure CLI k odstranění kontejnerů na základě předpony názvu kontejneru

Tento skript nejprve vytvoří několik ukázkových kontejnerů v úložišti objektů blob v Azure a pak některé z kontejnerů odstraní na základě předpony v názvu kontejneru.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.sh?highlight=2-3 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků, zbývající kontejnery a všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k odstranění kontejnerů na základě předpony názvu kontejneru používá následující příkazy. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az storage account create](/cli/azure/storage/account) | Vytvoří účet služby Azure Storage v zadané skupině prostředků. |
| [az storage container create](/cli/azure/storage/container) | Vytvoří kontejner v úložišti objektů blob v Azure. |
| [az storage container list](/cli/azure/storage/container) | Vypíše kontejnery v účtu služby Azure Storage. |
| [az storage container delete](/cli/azure/storage/container) | Odstraní kontejnery v účtu služby Azure Storage. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro úložiště najdete v tématu [Ukázky v Azure CLI pro službu Azure Storage](../blobs/storage-samples-blobs-cli.md).
