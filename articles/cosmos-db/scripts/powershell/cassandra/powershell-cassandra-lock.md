---
title: Skript PowerShellu pro vytvoření zámku prostředků pro Azure Cosmos rozhraní API Cassandra prostor a tabulku klíčů
description: Vytvoření zámku prostředků pro Azure Cosmos rozhraní API Cassandra prostoru a tabulce klíčů
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: 9debd77699a33854d639cc911437a4b533dafc54
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2020
ms.locfileid: "85126257"
---
# <a name="create-a-resource-lock-for-azure-cosmos-cassandra-api-keyspace-and-table-using-azure-powershell"></a>Vytvoření zámku prostředků pro službu Azure Cosmos rozhraní API Cassandraho prostoru a tabulky pomocí Azure PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

> [!IMPORTANT]
> Zámky prostředků nefungují pro změny provedené uživateli, kteří se připojují pomocí Cassandra SDK, CQL shellu nebo webu Azure Portal, pokud se účet Cosmos DB nejdříve nezamkne s `disableKeyBasedMetadataWriteAccess` povolenou vlastností. Další informace o tom, jak tuto vlastnost Povolit, najdete v tématu [prevence změn ze sad SDK](../../../role-based-access-control.md#preventing-changes-from-cosmos-sdk).

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-lock.ps1 "Create, list, and remove resource locks")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
|**Prostředek Azure**| |
| [New-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock) | Vytvoří zámek prostředku. |
| [Get-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcelock) | Získá zámek prostředku nebo vypíše zámky prostředků. |
| [Remove-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcelock) | Odebere zámek prostředku. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShell najdete v [dokumentaci k Azure PowerShell](https://docs.microsoft.com/powershell/).

Další ukázkové skripty PowerShellu pro službu Azure Cosmos DB najdete ve [skriptech PowerShellu pro službu Azure Cosmos DB](../../../powershell-samples.md).