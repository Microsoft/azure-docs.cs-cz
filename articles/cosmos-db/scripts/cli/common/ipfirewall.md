---
title: Vytvoření účtu Azure Cosmos s bránou firewall IP
description: Vytvoření účtu Azure Cosmos s bránou firewall IP
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 8b0ab04e2117108566962cc9a473556098e0e51d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275588"
---
# <a name="create-an-azure-cosmos-account-with-ip-firewall-using-azure-cli"></a>Vytvoření účtu Azure Cosmos s ip bránou firewall pomocí Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat příkaz cli místně, toto téma vyžaduje, abyste spouštěli Azure CLI verze 2.0.73 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

> [!NOTE]
> Tato ukázka ukazuje pomocí účtu rozhraní API SQL (Core). Chcete-li použít tuto ukázku `ip-range-filter` pro jiná rozhraní `az cosmosdb account create` API, použijte parametr ve skriptu níže pro příkaz pro skript specifický pro rozhraní API.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/ipfirewall.sh "Create an Azure Cosmos account with ip firewall.")]

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
| [az group delete](/cli/azure/resource#az-resource-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o cli DB Azure Cosmos najdete v [dokumentaci k příkazu KONT db Azure Cosmos](/cli/azure/cosmosdb)DB .

Všechny ukázky skriptu Rozhraní příkazového od Db BZI Azure Cosmos najdete v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
