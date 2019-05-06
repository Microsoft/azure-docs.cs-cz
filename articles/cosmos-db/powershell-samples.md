---
title: Ukázky Azure Powershellu pro službu Azure Cosmos DB
description: Ukázky Azure PowerShellu – skripty, které vám pomohou vytvořit a spravovat účty služby Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/08/2019
ms.author: mjbrown
ms.openlocfilehash: 68e845a05f4ebe2d1f25b55c00042c8925c8109e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65069301"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Ukázky Azure PowerShellu pro službu Azure Cosmos DB

Následující tabulka obsahuje odkazy na ukázkové skripty Azure Powershellu pro službu Azure Cosmos DB pro jádra (SQL) rozhraní API.

| |  |
|---|---|
|**Účty služby Azure Cosmos**||
|[Vytvoření účtu](scripts/powershell/sql/ps-account-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří účet rozhraní SQL API služby Azure Cosmos. |
|[Vytvoření účtu](scripts/powershell/sql/ps-account-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získání vlastností účtu Azure Cosmos. |
|[Přidat oblast](scripts/powershell/sql/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoření účtu Azure Cosmos a přidejte do seznamu umístění oblast. |
|[Změna priority převzetí služeb při selhání](scripts/powershell/sql/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Změna priority převzetí služeb při selhání účtu Azure Cosmos pomocí triggeru ruční převzetí služeb při selhání. |
|[Aktualizace značky](scripts/powershell/sql/ps-account-tags-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizace značky pro účet Azure Cosmos. |
|[Získání klíčů účtu](scripts/powershell/sql/ps-account-key-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získejte primární a sekundární klíče účtu Azure Cosmos. |
|[Opětovné vygenerování klíčů účtu](scripts/powershell/sql/ps-account-key-regenerate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Znovu vygenerujete primární a sekundární klíče účtu Azure Cosmos. |
|[Seznam připojovacích řetězců](scripts/powershell/sql/ps-account-connection-string-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Získejte primární a sekundární připojovací řetězce účtu Azure Cosmos. |
|[Vytvoření brány Firewall protokolu IP](scripts/powershell/sql/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoření brány Firewall protokolu IP pro účet Azure Cosmos. |
|[Odstranit účet Azure Cosmos](scripts/powershell/sql/ps-account-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Odstraňte účet Azure Cosmos. |
|**Databáze Azure Cosmos**||
| [Vytvoření databáze](scripts/powershell/sql/ps-database-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoření databáze v rámci účtu Azure Cosmos.|
| [Vytvoření databáze s propustností sdílené/úrovni databáze](scripts/powershell/sql/ps-database-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvořte databázi Azure Cosmos pomocí databáze úroveň propustnosti, jež jsou sdílena s jejím kontejnerům.|
| [Vypsat všechny databáze](scripts/powershell/sql/ps-database-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vypsat všechny databáze v účtu služby Azure Cosmos.|
| [Získat databázi](scripts/powershell/sql/ps-database-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Umožňuje získáte vlastnosti databáze Azure Cosmos.|
|**Azure Cosmos containers**||
| [Vytvoření kontejneru](scripts/powershell/sql/ps-container-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoření kontejneru Azure Cosmos s vyhrazenou propustností.|
| [Vytvořit kontejner s sdílené propustnost](scripts/powershell/sql/ps-container-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoření kontejneru Azure Cosmos s propustností dostaly jiných kontejnerů v databázi.|
| [Vytvořit kontejner s indexem zásad](scripts/powershell/sql/ps-container-create-index-custom.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoření kontejneru Azure Cosmos pomocí zásad vlastní indexu.|
| [Vytvořit kontejner s žádná zásada indexu](scripts/powershell/sql/ps-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoření kontejneru Azure Cosmos zásadám index vypnuté.|
| [Vytvořit kontejner s jedinečné klíče a hodnoty TTL](scripts/powershell/sql/ps-container-create-unique-key-ttl.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoření Azure Cosmos nakonfigurovaný kontejner s jedinečnosti klíče a time-to-live.|
| [Vytvořit kontejner s řešení konfliktů](scripts/powershell/sql/ps-container-create-conflict-policy.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoření kontejneru Azure Cosmos se poslední zápis konflikt zásada řešení.|
| [Vypsat všechny kontejnery](scripts/powershell/sql/ps-container-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vypsat všechny kontejnery v databázi Azure Cosmos.|
| [Získání kontejneru](scripts/powershell/sql/ps-container-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Získá vlastnosti pro kontejner v databázi Azure Cosmos.|
| [Odstranění kontejneru](scripts/powershell/sql/ps-container-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Odstraňte kontejner v databázi Azure Cosmos.|
|||