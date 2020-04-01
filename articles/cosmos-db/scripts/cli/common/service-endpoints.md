---
title: Vytvoření účtu Azure Cosmos s koncovými body služby virtuální sítě
description: Vytvoření účtu Azure Cosmos s koncovými body služby virtuální sítě
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: ff700739e2f8c6330ea151dbe489332acea3238c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275406"
---
# <a name="create-an-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Vytvoření účtu Azure Cosmos s koncovými body virtuální síťové služby pomocí azure cli

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat příkaz cli místně, toto téma vyžaduje, abyste spouštěli Azure CLI verze 2.0.73 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

Tato ukázka vytvoří novou virtuální síť s podsítí front a `Microsoft.AzureCosmosDB`back-end a povolí koncové body služby pro aplikaci . Potom načte ID prostředku pro tuto podsíť a použije ji na účet Azure Cosmos a povolí koncové body služby pro účet.

> [!NOTE]
> Tato ukázka ukazuje pomocí účtu rozhraní API SQL (Core). Chcete-li tuto ukázku použít `enable-virtual-network` pro `virtual-network-rules` jiná rozhraní API, použijte parametry a ve skriptu níže pro skript specifický pro rozhraní API.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/service-endpoints.sh "Create an Azure Cosmos account with service endpoints.")]

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
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Vytvoří virtuální síť Azure. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | Vytvoří podsíť pro virtuální síť Azure. |
| [Az síť ová síť podsíť zobrazit](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show) | Vrátí podsíť pro virtuální síť Azure. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Vytvoří účet služby Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o cli DB Azure Cosmos najdete v [dokumentaci k příkazu KONT db Azure Cosmos](/cli/azure/cosmosdb)DB .

Všechny ukázky skriptu Rozhraní příkazového od Db BZI Azure Cosmos najdete v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
