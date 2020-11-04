---
title: Přidání oblastí, změna priority převzetí služeb při selhání, aktivace převzetí služeb při selhání pro účet Azure Cosmos
description: Přidání oblastí, změna priority převzetí služeb při selhání, aktivace převzetí služeb při selhání pro účet Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 17cddabe4e8e5a4ed7814b89f3b8c5505f0db502
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93316555"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>Přidání oblastí, změna priority převzetí služeb při selhání, aktivace převzetí služeb při selhání pro účet Azure Cosmos pomocí Azure CLI
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.9.1 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

Tento skript ukazuje tři operace.

- Přidejte oblast do existujícího účtu Azure Cosmos.
- Změna priority místního převzetí služeb při selhání (platí pro účty pomocí automatického převzetí služeb při selhání
- Aktivace ručního převzetí služeb při selhání z primární do sekundárních oblastí (platí pro účty s ručním převzetím služeb

> [!NOTE]
> Operace přidávání a odebírání oblastí na účtu Cosmos se nedají provést při změně dalších vlastností.

> [!NOTE]
> Tato ukázka předvádí použití účtu SQL (Core) API, ale tyto operace jsou identické napříč všemi databázovými rozhraními API v Cosmos DB.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/regions.sh "Regional operations for Cosmos DB.")]

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
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Vytvoří účet služby Azure Cosmos DB. |
| [az cosmosdb update](/cli/azure/cosmosdb#az-cosmosdb-update) | Aktualizuje účet Azure Cosmos DB (Přidat nebo odebrat oblast). |
| [AZ cosmosdb Failover-priority-Change](/cli/azure/cosmosdb#az-cosmosdb-failover-priority-change) | Aktualizujte prioritu převzetí služeb při selhání nebo aktivujte převzetí služeb při selhání na účtu Azure Cosmos DB |
| [az group delete](/cli/azure/resource#az-resource-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure Cosmos DB CLI najdete v [dokumentaci k](/cli/azure/cosmosdb)rozhraní příkazového řádku Azure Cosmos DB.

Všechny ukázkové skripty Azure Cosmos DB CLI najdete v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
