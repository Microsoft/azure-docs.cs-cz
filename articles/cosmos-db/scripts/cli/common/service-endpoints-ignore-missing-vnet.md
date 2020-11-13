---
title: Připojení existujícího účtu Azure Cosmos k koncovým bodům služby virtuální sítě
description: Připojení existujícího účtu Azure Cosmos k koncovým bodům služby virtuální sítě
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: f66d219eff1919e62088e5c4f7aa72aab97ea4f9
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566227"
---
# <a name="connect-an-existing-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Připojení existujícího účtu Azure Cosmos k koncovým bodům služby virtuální sítě pomocí Azure CLI
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.9.1 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="sample-script"></a>Ukázkový skript

Tato ukázka je určená k tomu, aby ukázala, jak připojit existující účet Azure Cosmos k existující nové virtuální síti, ve které ještě není nakonfigurovaná podsíť pro koncové body služby pomocí `ignore-missing-vnet-service-endpoint` parametru. To umožňuje, aby se konfigurace účtu Cosmos dokončila bez chyby, než se dokončí konfigurace podsítě virtuální sítě. Po dokončení konfigurace podsítě bude účet Cosmos dostupný přes nakonfigurovanou podsíť.

> [!NOTE]
> Tato ukázka předvádí použití účtu rozhraní API SQL (Core). Chcete-li použít tuto ukázku pro jiná rozhraní API, použijte `enable-virtual-network` `virtual-network-rules` parametry a ve skriptu níže do skriptu specifického pro rozhraní API.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/service-endpoints-ignore-missing-vnet.sh "Create an Azure Cosmos account with service endpoints.")]

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
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) | Aktualizuje podsíť pro virtuální síť Azure. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure Cosmos DB CLI najdete v [dokumentaci k](/cli/azure/cosmosdb)rozhraní příkazového řádku Azure Cosmos DB.

Všechny ukázkové skripty Azure Cosmos DB CLI najdete v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
