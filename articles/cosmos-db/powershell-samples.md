---
title: Ukázky Azure PowerShellu pro službu Azure Cosmos DB
description: Získání ukázek Azure PowerShell k provádění běžných úloh v Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 0c2d13ef49676917bc48dd5a2e682471558567f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842967"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Ukázky Azure PowerShellu pro službu Azure Cosmos DB

Následující tabulka obsahuje odkazy na běžně používané Azure PowerShell skripty pro Azure Cosmos DB. Pomocí odkazů na pravé straně přejděte na ukázky specifické pro rozhraní API. Běžné ukázky jsou stejné napříč všemi rozhraními API. Referenční stránky pro všechny rutiny Azure Cosmos DB PowerShellu jsou k dispozici v [Referenční příručce Azure PowerShell](/powershell/module/az.cosmosdb). Pravidelně prosím zkontrolujte aktualizace `Az.CosmosDB` . Tyto ukázky PowerShellu můžete také rozvětvit pro Cosmos DB z našeho úložiště GitHub [Cosmos DB ukázky PowerShellu na GitHubu](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Běžné ukázky

|Úloha | Popis |
|---|---|
|[Aktualizace účtu](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizuje výchozí úroveň konzistence účtu Cosmos DB. |
|[Aktualizace oblastí účtu](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizuje oblasti účtu Cosmos DB. |
|[Změna priority převzetí služeb při selhání nebo Aktivace převzetí služeb](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Změňte prioritu regionálního převzetí služeb při selhání účtu Azure Cosmos nebo aktivujte ruční převzetí služeb při selhání. |
|[Klíče účtu nebo připojovací řetězce](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získejte primární a sekundární klíče, připojovací řetězce nebo znovu vygenerujte klíč účtu Azure Cosmos DB účtu. |
|[Vytvoření účtu Cosmos pomocí brány firewall protokolu IP](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte účet Azure Cosmos DB s povolenou bránou firewall protokolu IP. |
|||

## <a name="core-sql-api-samples"></a>Core (SQL) – ukázky rozhraní API

|Úloha | Popis |
|---|---|
|[Vytvoření účtu, databáze a kontejneru](scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte účet Azure Cosmos DB, databázi a kontejner. |
|[Vytvoření účtu, databáze a kontejneru pomocí automatického škálování](scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte účet Azure Cosmos DB, databázi a kontejner s možností automatického škálování. |
|[Vytvoření kontejneru s velkým klíčem oddílu](scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvořte kontejner s velkým klíčem oddílu. |
|[Vytvoření kontejneru bez zásad indexování](scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoření kontejneru Azure Cosmos se zakázanými zásadami indexování.|
|[Vypsat nebo získat databáze nebo kontejnery](scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vypíše nebo Získá databázi nebo kontejnery. |
|[Operace propustnosti](scripts/powershell/sql/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operace propustnosti pro databázi nebo kontejner včetně získání, aktualizace a migrace mezi automatické škálování a standardní propustností. |
|[Uzamčení prostředků před odstraněním](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků |
|||

## <a name="cassandra-api-samples"></a>Ukázky rozhraní API Cassandra

|Úloha | Popis |
|---|---|
|[Vytvoření účtu, místa a tabulky](scripts/powershell/cassandra/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos, prostor a tabulku. |
|[Vytvoření účtu, prostoru a tabulky pomocí automatického škálování](scripts/powershell/cassandra/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos, prostor a tabulku s AutoScale. |
|[Vypsat nebo získat prostory a tabulky](scripts/powershell/cassandra/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vypíše nebo získá prostor nebo tabulky. |
|[Operace propustnosti](scripts/powershell/cassandra/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operace propustnosti pro prostor klíčů nebo tabulku, mezi které patří Get, Update a migrace mezi autoškálou a standardní propustností. |
|[Uzamčení prostředků před odstraněním](scripts/powershell/cassandra/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků |
|||

## <a name="mongo-db-api-samples"></a>Ukázky rozhraní API pro Mongo DB

|Úloha | Popis |
|---|---|
|[Vytvoření účtu, databáze a kolekce](scripts/powershell/mongodb/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos, databázi a kolekci. |
|[Vytvoření účtu, databáze a kolekce pomocí automatického škálování](scripts/powershell/mongodb/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos, databázi a kolekci s použitím automatického škálování. |
|[Vypsat nebo získat databáze nebo kolekce](scripts/powershell/mongodb/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vypíše nebo Získá databázi nebo kolekci. |
|[Operace propustnosti](scripts/powershell/mongodb/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operace propustnosti databáze nebo kolekce, včetně získání, aktualizace a migrace mezi automatické škálování a standardní propustností. |
|[Uzamčení prostředků před odstraněním](scripts/powershell/mongodb/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků |
|||

## <a name="gremlin-api-samples"></a>Ukázky rozhraní API pro Gremlin

|Úloha | Popis |
|---|---|
|[Vytvoření účtu, databáze a grafu](scripts/powershell/gremlin/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos, databázi a graf. |
|[Vytvoření účtu, databáze a grafu pomocí automatického škálování](scripts/powershell/gremlin/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos, databázi a graf s možností automatického škálování. |
|[Vypsat nebo získat databáze nebo grafy](scripts/powershell/gremlin/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vypíše nebo Získá databázi nebo graf. |
|[Operace propustnosti](scripts/powershell/gremlin/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operace propustnosti databáze nebo grafu, včetně získání, aktualizace a migrace mezi automatické škálování a standardní propustností. |
|[Uzamčení prostředků před odstraněním](scripts/powershell/gremlin/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků |
|||

## <a name="table-api-samples"></a>Ukázky rozhraní API pro tabulky

|Úloha | Popis |
|---|---|
|[Vytvoření účtu a tabulky](scripts/powershell/table/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos a tabulku. |
|[Vytvoření účtu a tabulky pomocí automatického škálování](scripts/powershell/table/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet Azure Cosmos a automatické škálování tabulky. |
|[Vypsat nebo získat tabulky](scripts/powershell/table/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vypíše nebo získá tabulky. |
|[Operace propustnosti](scripts/powershell/table/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operace propustnosti tabulky, včetně získání, aktualizace a migrace mezi automatické škálování a standardní propustností. |
|[Uzamčení prostředků před odstraněním](scripts/powershell/table/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků |
|||
