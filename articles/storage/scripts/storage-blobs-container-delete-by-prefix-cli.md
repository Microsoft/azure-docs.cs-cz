---
title: Ukázkový skript Azure CLI – Odstranění kontejnerů na základě předpony | Microsoft Docs
description: Kontejnery objektů blob ve službě Azure Storage můžete odstranit na základě předpony názvu kontejneru.
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
ms.openlocfilehash: 01187a4dbcd8333f95cf20b5956b7b81559a19a8
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55730648"
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Odstranění kontejnerů na základě předpony názvu kontejneru

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

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro úložiště najdete v tématu [Ukázky v Azure CLI pro službu Azure Storage](../blobs/storage-samples-blobs-cli.md).
