---
title: Přidání oblastí, změna priority převzetí služeb při selhání, aktivace převzetí služeb při selhání pro účet Azure Cosmos
description: Přidání oblastí, změna priority převzetí služeb při selhání, aktivace převzetí služeb při selhání pro účet Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: b7b6be0ce781debcb19b5c0fb7b6a4b0123ef366
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275546"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>Přidání oblastí, změna priority převzetí služeb při selhání, aktivace převzetí služeb při selhání pro účet Azure Cosmos pomocí azure cli

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat příkaz cli místně, toto téma vyžaduje, abyste spouštěli Azure CLI verze 2.0.73 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

Tento skript ukazuje tři operace.

- Přidejte oblast do existujícího účtu Azure Cosmos.
- Změna místní priority převzetí služeb při selhání (platí pro účty pomocí automatického převzetí služeb při selhání)
- Aktivace ručního převzetí služeb při selhání z primárních do sekundárních oblastí (platí pro účty s ručním převzetím služeb při selhání)

> [!NOTE]
> Přidání a odebrání operací oblasti na účtu Cosmos nelze provést při změně jiných vlastností.

> [!NOTE]
> Tato ukázka ukazuje pomocí účtu rozhraní API SQL (Core), ale tyto operace jsou identické ve všech databázových rozhraní API v Cosmos DB.

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
| [az cosmosdb update](/cli/azure/cosmosdb#az-cosmosdb-update) | Aktualizuje účet Azure Cosmos DB (přidat nebo odebrat oblast). |
| [az cosmosdb převzetí služeb při selhání-priorita-změna](/cli/azure/cosmosdb#az-cosmosdb-failover-priority-change) | Aktualizujte prioritu převzetí služeb při selhání nebo aktivujete převzetí služeb při selhání na účtu Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o cli DB Azure Cosmos najdete v [dokumentaci k příkazu KONT db Azure Cosmos](/cli/azure/cosmosdb)DB .

Všechny ukázky skriptu Rozhraní příkazového od Db BZI Azure Cosmos najdete v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
