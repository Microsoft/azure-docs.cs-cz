---
title: Vytvoření účtu Azure Cosmos pomocí koncových bodů služby virtuální sítě
description: Vytvoření účtu Azure Cosmos pomocí koncových bodů služby virtuální sítě
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 2c0ae963fbf7298d571f30754574f072927af569
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096202"
---
# <a name="create-an-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Vytvoření účtu Azure Cosmos s použitím koncových bodů služby virtuální sítě pomocí Azure CLI
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.9.1 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

Tato ukázka vytvoří novou virtuální síť s podsítí front-end a povoluje koncové body služby pro `Microsoft.AzureCosmosDB` . Pak načte ID prostředku pro tuto podsíť a použije ho pro účet Azure Cosmos a povolí koncové body služby pro tento účet.

> [!NOTE]
> Tato ukázka předvádí použití účtu Core (SQL) API. Chcete-li použít tuto ukázku pro jiná rozhraní API, použijte `enable-virtual-network` `virtual-network-rules` parametry a ve skriptu níže do skriptu specifického pro rozhraní API.

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
| [AZ Network VNet Subnet show](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show) | Vrátí podsíť pro virtuální síť Azure. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Vytvoří účet služby Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure Cosmos DB CLI najdete v [dokumentaci k](/cli/azure/cosmosdb)rozhraní příkazového řádku Azure Cosmos DB.

Všechny ukázkové skripty Azure Cosmos DB CLI najdete v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
