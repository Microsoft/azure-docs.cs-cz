---
title: Skript Azure CLI – Obnovení klíče účtu služby Azure Cosmos DB | Microsoft Docs
description: Ukázkový skript Azure CLI – Obnovení klíče účtu služby Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 06a71ce759a72483d9ac3993e82d14af21e7d9d7
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007803"
---
# <a name="regenerate-an-azure-cosmos-db-account-key-using-the-azure-cli"></a>Obnovení klíče účtu služby Azure Cosmos DB pomocí Azure CLI

Tato ukázka obnoví jakýkoli druh klíče účtu služby Azure Cosmos DB pomocí Azure CLI.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/secure-cosmosdb-regenerate-keys/secure-cosmosdb-regenerate-keys.sh "Regenerate Azure Cosmos DB account keys")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Aktualizuje účet služby Azure Cosmos DB. |
| [az cosmosdb list-keys](/cli/azure/cosmosdb#az-cosmosdb-list-keys) | Vypíše přístupové klíče pro účet služby Cosmos DB. |
| [az cosmosdb regenerate-key](/cli/azure/cosmosdb#az-cosmosdb-regenerate-key) | Obnoví klíče účtu služby Azure Cosmos DB. |
| [az group delete](/cli/azure/group#az-group-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku Azure Cosmos DB najdete v [dokumentaci k rozhraní příkazového řádku Azure Cosmos DB](../cli-samples.md).
