---
title: Skript PowerShellu pro vytvoření Azure Cosmos DB databáze a kontejneru rozhraní API SQL (jádro)
description: Azure PowerShell skriptu – Azure Cosmos DB vytvoření databáze a kontejneru rozhraní API SQL (jádro)
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/20/2019
ms.author: mjbrown
ms.openlocfilehash: 5a57b8cb3e4e7076d3be73a9010d9a29a8b004a3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441363"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-core-api"></a>Vytvoření databáze a kontejneru pro rozhraní API Azure Cosmos DB-SQL (jádro)

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Ukázkový skript

Tento skript vytvoří účet Cosmos pro rozhraní API SQL (Core) ve dvou oblastech s konzistencí úrovně relace, databází se sdílenou propustností a kontejnerem s klíčem oddílu, vlastními zásadami indexování, jedinečnou zásadou klíčů, hodnotou TTL, vyhrazenou propustností a posledními zásadami pro řešení konfliktů WINS, které se použijí při `multipleWriteLocations=true`.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-create.ps1 "Create an account, database, and container for SQL (Core) API")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
|**Prostředky Azure**| |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Vytvoří prostředek. |
|**Skupiny prostředků Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty PowerShellu pro službu Azure Cosmos DB najdete ve [skriptech PowerShellu pro službu Azure Cosmos DB](../../../powershell-samples.md).
