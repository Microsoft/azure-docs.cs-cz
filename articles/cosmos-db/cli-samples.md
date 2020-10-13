---
title: Ukázky v Azure CLI pro Azure Cosmos DB
description: Ukázky v Azure CLI pro Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 58460dffad3d44090644a544f4082b7727ece3f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91840332"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Ukázky v Azure CLI pro službu Azure Cosmos DB

Následující tabulka obsahuje odkazy na ukázkové skripty Azure CLI pro službu Azure Cosmos DB. Pomocí odkazů na pravé straně přejděte na ukázky specifické pro rozhraní API. Běžné ukázky jsou stejné napříč všemi rozhraními API. Referenční stránky pro všechny příkazy rozhraní příkazového řádku Azure Cosmos DB jsou k dispozici v [referenčních informacích k Azure CLI](/cli/azure/cosmosdb). Ukázkové skripty Azure Cosmos DB CLI můžete najít také v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Tyto ukázky vyžadují Azure CLI verze 2.9.1 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli) .

## <a name="common-samples"></a>Běžné ukázky

Tyto ukázky se vztahují na všechna rozhraní Azure Cosmos DB API.

|Úloha | Popis |
|---|---|
| [Oblasti přidání nebo převzetí služeb při selhání](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Přidání oblasti, změna priority převzetí služeb při selhání, aktivace ručního převzetí služeb při selhání|
| [Klíče účtu a připojovací řetězce](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Vypíše klíče účtu, klíče jen pro čtení, znovu vygeneruje klíče a seznam připojovacích řetězců.|
| [Zabezpečení pomocí brány firewall protokolu IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvořte účet Cosmos s nakonfigurovanou bránou firewall protokolu IP.|
| [Zabezpečený nový účet pomocí koncových bodů služby](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvořte účet Cosmos a zabezpečte ho pomocí koncových bodů služby.|
| [Zabezpečení stávajícího účtu pomocí koncových bodů služby](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Aktualizujte účet Cosmos na zabezpečení pomocí koncových bodů služby, když je podsíť nakonec nakonfigurovaná.|
|||

## <a name="core-sql-api-samples"></a>Core (SQL) – ukázky rozhraní API

|Úloha | Popis |
|---|---|
| [Vytvoření účtu Azure Cosmos, databáze a kontejneru](scripts/cli/sql/create.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří účet Azure Cosmos DB, databázi a kontejner pro rozhraní API jádra (SQL). |
| [Vytvoření účtu Azure Cosmos, databáze a kontejneru pomocí automatického škálování](scripts/cli/sql/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří účet Azure Cosmos DB, databázi a kontejner s funkcí automatického škálování pro rozhraní API jádra (SQL). |
| [Operace propustnosti](scripts/cli/sql/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Čtení, aktualizace a migrace mezi automatické škálování a standardní propustností databáze a kontejneru.|
| [Uzamčení prostředků před odstraněním](scripts/cli/sql/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků|
|||

## <a name="mongodb-api-samples"></a>Ukázky rozhraní API pro MongoDB

|Úloha | Popis |
|---|---|
| [Vytvoření účtu Azure Cosmos, databáze a kolekce](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří účet Azure Cosmos DB, databázi a kolekci pro rozhraní API MongoDB. |
| [Vytvoření účtu Azure Cosmos, databáze s možností automatického škálování a dvou kolekcí se sdílenou propustností](scripts/cli/mongodb/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří účet Azure Cosmos DB, databázi s možností automatického škálování a dvě kolekce se sdílenou propustností pro rozhraní MongoDB API. |
| [Operace propustnosti](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Přečtěte si, aktualizujte a migrujte mezi automatické škálování a standardní propustností databáze a kolekce.|
| [Uzamčení prostředků před odstraněním](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků|
|||

## <a name="cassandra-api-samples"></a>Ukázky rozhraní API Cassandra

|Úloha | Popis |
|---|---|
| [Vytvoření účtu Azure Cosmos, místa a tabulky](scripts/cli/cassandra/create.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří účet Azure Cosmos DB, prostor a tabulku pro rozhraní API Cassandra. |
| [Vytvoření účtu Azure Cosmos, místa a tabulky s použitím automatického škálování](scripts/cli/cassandra/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří Azure Cosmos DB účet, prostor a tabulku s AutoScale rozhraní API Cassandra. |
| [Operace propustnosti](scripts/cli/cassandra/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Přečtěte si, aktualizujte a migrujte mezi automatické škálování a standardní propustností v prostoru a tabulce klíčů.|
| [Uzamčení prostředků před odstraněním](scripts/cli/cassandra/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků|
|||

## <a name="gremlin-api-samples"></a>Ukázky rozhraní API pro Gremlin

|Úloha | Popis |
|---|---|
| [Vytvoření účtu Azure Cosmos, databáze a grafu](scripts/cli/gremlin/create.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří účet Azure Cosmos DB, databázi a graf pro rozhraní Gremlin API. |
| [Vytvoření účtu Azure Cosmos, databáze a grafu pomocí automatického škálování](scripts/cli/gremlin/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří účet Azure Cosmos DB, databázi a graf s možností automatického škálování pro rozhraní Gremlin API. |
| [Operace propustnosti](scripts/cli/gremlin/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Přečtěte si, aktualizujte a migrujte mezi automatické škálování a standardní propustností databáze a grafu.|
| [Uzamčení prostředků před odstraněním](scripts/cli/gremlin/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků|
|||

## <a name="table-api-samples"></a>Ukázky rozhraní API pro tabulky

|Úloha | Popis |
|---|---|
| [Vytvoření účtu Azure Cosmos a tabulky](scripts/cli/table/create.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří účet Azure Cosmos DB a tabulku pro rozhraní API pro tabulky. |
| [Vytvoření účtu Azure Cosmos a tabulky pomocí automatického škálování](scripts/cli/table/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří účet Azure Cosmos DB a tabulku s automatickém škálováním pro rozhraní API pro tabulky. |
| [Operace propustnosti](scripts/cli/table/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Přečtěte si, aktualizujte a migrujte mezi automatické škálování a standardní propustností tabulky.|
| [Uzamčení prostředků před odstraněním](scripts/cli/table/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Zabránit odstranění prostředků s zámky prostředků|
|||
