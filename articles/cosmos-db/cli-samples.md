---
title: Ukázky v Azure CLI pro Azure Cosmos DB
description: Ukázky v Azure CLI – Vytváření a správa účtů služby Azure Cosmos DB, databází, kontejnerů, oblastí a bran firewall
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: d7ce5e2c12feeee770de8acfd5df81d340e0a7d0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615571"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Ukázky v Azure CLI pro službu Azure Cosmos DB

Následující tabulka obsahuje odkazy na ukázkové skripty Azure CLI pro službu Azure Cosmos DB. Stránky s referenčními informacemi pro všechny příkazy rozhraní příkazového řádku Azure Cosmos DB jsou k dispozici v [referenčních informacích k Azure CLI](/cli/azure/cosmosdb).

| |  |
|---|---|
|**Vytvoření účtu služby Azure Cosmos DB, databáze a kontejnerů**||
| [Vytvoření účtu Azure Cosmos DB pomocí rozhraní SQL API](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří jeden Azure Cosmos DB účet, databázi a kontejner. |
| [Vytvoření účtu Azure Cosmos DB pomocí rozhraní API Cosmos DB pro MongoDB](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří jeden Azure Cosmos DB účet, databázi a kolekci. |
| [Vytvoření účtu Azure Cosmos DB pomocí rozhraní API Gremlin](scripts/create-gremlin-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří jeden Azure Cosmos DB účet, databázi a graf. |
| [Vytvoření účtu Azure Cosmos DB pomocí rozhraní API Cassandra](scripts/create-cassandra-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří jeden Azure Cosmos DB účet a databázi. |
| [Vytvoření účtu Azure Cosmos DB pomocí rozhraní API pro tabulky](scripts/create-table-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří jeden Azure Cosmos DB účet, databázi a tabulku. |
|**Škálování služby Azure Cosmos DB**||
| [Škálování propustnosti kontejneru](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Změní zřízenou propustnosti kontejneru.|
| [Replikace účtu databáze Azure Cosmos ve více oblastech a konfigurace priorit převzetí služeb při selhání](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Globálně replikuje data účtu do několika oblastí s určenou prioritou převzetí služeb při selhání.|
|**Zabezpečení služby Azure Cosmos DB**||
| [Získání klíčů účtu](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Získá pro účet primární a sekundární hlavní klíč pro zápis a primární a sekundární klíč jen pro čtení.|
| [Získání připojovacího řetězce pro účet Cosmos nakonfigurovaný s rozhraním API Azure Cosmos DB pro MongoDB](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Získá připojovací řetězec pro připojení aplikace MongoDB k vašemu účtu Azure Cosmos DB.|
| [Opětovné vygenerování klíčů účtu](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Obnoví klíče účtu.|
| [Vytvoření brány firewall](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří zásadu řízení příchozího přístupu z IP adres pro omezení přístupu k účtu na schválenou sadu počítačů nebo cloudových služeb.|
|**Vysoká dostupnost, zotavení po havárii, zálohování a obnovení**||
| [Konfigurace zásad převzetí služeb při selhání](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Nastaví prioritu převzetí služeb při selhání pro všechny oblasti, do kterých se účet replikuje.|
|**Připojení služby Azure Cosmos DB k prostředkům**||
| [Připojení webové aplikace ke službě Azure Cosmos DB](../app-service/scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Vytvoření a připojení databáze Azure Cosmos a webové aplikace Azure|
|||
