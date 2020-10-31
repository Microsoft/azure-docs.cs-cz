---
title: Skript PowerShellu pro vytvoření zámku prostředků pro Azure Cosmos rozhraní API Cassandra prostor a tabulku klíčů
description: Vytvoření zámku prostředků pro Azure Cosmos rozhraní API Cassandra prostoru a tabulce klíčů
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: 2ecbb79272b37634414f077237f67c6d918d3813
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099583"
---
# <a name="create-a-resource-lock-for-azure-cosmos-cassandra-api-keyspace-and-table-using-azure-powershell"></a>Vytvoření zámku prostředků pro službu Azure Cosmos rozhraní API Cassandraho prostoru a tabulky pomocí Azure PowerShell
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

> [!IMPORTANT]
> Zámky prostředků nefungují pro změny provedené uživateli, kteří se připojují pomocí Cassandra SDK, CQL shellu nebo webu Azure Portal, pokud se účet Cosmos DB nejdříve nezamkne s `disableKeyBasedMetadataWriteAccess` povolenou vlastností. Další informace o tom, jak tuto vlastnost Povolit, najdete v tématu [prevence změn ze sad SDK](../../../role-based-access-control.md#prevent-sdk-changes).

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
| [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) | Vytvoří zámek prostředku. |
| [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock) | Získá zámek prostředku nebo vypíše zámky prostředků. |
| [Remove-AzResourceLock](/powershell/module/az.resources/remove-azresourcelock) | Odebere zámek prostředku. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShell najdete v [dokumentaci k Azure PowerShell](/powershell/).