---
title: Azure CLI skriptu ukázkové – vytvoření mezipaměť Azure Redis Cache Premium s clusteringem | Microsoft Docs
description: Azure CLI skriptu ukázkové – vytvoření úroveň Premium Azure Redis Cache s clusteringem
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 07bcceae-2521-4fe3-b88f-ed833104ddd2
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: fb97d19d50ac9845c2495bd167a9cd30805d464b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
ms.locfileid: "29846294"
---
# <a name="create-a-premium-azure-redis-cache-with-clustering"></a>Vytvořit mezipaměť Azure Redis Cache Premium s clusteringem

V tomto scénáři zjistíte, jak vytvořit úrovni Premium 6 GB Azure Redis Cache s povoleným clusteringem a dvě horizontálních oddílů.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Redis Cache")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy k vytvoření skupiny prostředků a mezipaměti redis úroveň Premium s clusteringem povolit. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Vytvoření az redis](https://docs.microsoft.com/cli/azure/redis#az_redis_create) | Vytvořte instanci služby Redis Cache. |


## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázky skriptu Azure Redis Cache rozhraní příkazového řádku najdete v [dokumentace k Azure Redis Cache](../cli-samples.md).