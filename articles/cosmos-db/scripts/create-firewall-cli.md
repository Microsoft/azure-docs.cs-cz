---
title: Skript Azure CLI – Vytvoření brány firewall pro službu Azure Cosmos DB | Microsoft Docs
description: Ukázkový skript Azure CLI – Vytvoření brány firewall pro službu Azure Cosmos DB
services: cosmos-db
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: cosmos-db
ms.custom: sammvcple
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 06/02/2017
ms.author: mimig
ms.openlocfilehash: 1c43f9cb23598e69341720286882fadcc20a2ff7
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="azure-cosmos-db-create-a-firewall-using-the-azure-cli"></a>Azure Cosmos DB: Vytvoření brány firewall pomocí Azure CLI

Tento ukázkový skript rozhraní příkazového řádku vytvoří zásadu brány firewall pro jakýkoli druh účtu služby Azure Cosmos DB. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/secure-cosmosdb-create-firewall/secure-cosmosdb-create-firewall.sh?highlight=38-42 "Create an Azure Cosmos DB firewall")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az cosmosdb create](https://docs.microsoft.com/cli/azure/cosmosdb#az_cosmosdb_create) | Vytvoří účet služby Azure Cosmos DB. |
| [az cosmosdb update](https://docs.microsoft.com/cli/azure/cosmosdb#az_cosmosdb_update) | Aktualizuje účet služby Azure Cosmos DB tak, aby zahrnoval nastavení brány firewall. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku Azure Cosmos DB najdete v [dokumentaci k rozhraní příkazového řádku Azure Cosmos DB](../cli-samples.md).
