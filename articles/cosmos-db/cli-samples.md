---
title: Ukázky v Azure CLI pro službu Azure Cosmos DB | Microsoft Docs
description: Ukázky v Azure CLI – Vytváření a správa účtů služby Azure Cosmos DB, databází, kontejnerů, oblastí a bran firewall
services: cosmos-db
author: SnehaGunda
manager: kfile
tags: azure-service-management
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/29/2017
ms.author: sngun
ms.openlocfilehash: a6c6f61f9aa3e821ec048c1516c0e433c3c64e30
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963964"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Ukázky v Azure CLI pro službu Azure Cosmos DB

Následující tabulka obsahuje odkazy na ukázkové skripty Azure CLI pro službu Azure Cosmos DB. Stránky s referenčními informacemi pro všechny příkazy rozhraní příkazového řádku Azure Cosmos DB jsou k dispozici v [referenčních informacích k Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb).

| |  |
|---|---|
|**Vytvoření účtu služby Azure Cosmos DB, databáze a kontejnerů**||
|[Vytvoření účtu rozhraní SQL API](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří jeden účet rozhraní API služby Azure Cosmos DB, databázi a kontejner pro použití s rozhraním SQL API. |
| [Vytvoření účtu rozhraní MongoDB API](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří jeden účet rozhraní MongoDB API služby Azure Cosmos DB, databázi a kolekci. |
| [Vytvoření účtu rozhraní Gremlin API](scripts/create-graph-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří jeden účet rozhraní Gremlin API služby Azure Cosmos DB, databázi a kontejner. |
|**Škálování služby Azure Cosmos DB**||
| [Škálování propustnosti kontejneru](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Změní zřízenou propustnosti kontejneru.|
|[Replikace účtu databáze Azure Cosmos DB ve více oblastech a konfigurace priorit převzetí služeb při selhání](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Globálně replikuje data účtu do několika oblastí s určenou prioritou převzetí služeb při selhání.|
|**Zabezpečení služby Azure Cosmos DB**||
| [Získání klíčů účtu](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Získá pro účet primární a sekundární hlavní klíč pro zápis a primární a sekundární klíč jen pro čtení.|
| [Získání připojovacího řetězce MongoDB](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Získá připojovací řetězec pro připojení aplikace MongoDB k účtu služby Azure Cosmos DB.|
|[Opětovné vygenerování klíčů účtu](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Znovu vygeneruje pro účet hlavní klíč nebo klíč jen pro čtení.|
|[Vytvoření brány firewall](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří zásadu řízení příchozího přístupu z IP adres pro omezení přístupu k účtu na schválenou sadu počítačů nebo cloudových služeb.|
|**Vysoká dostupnost, zotavení po havárii, zálohování a obnovení**||
|[Konfigurace zásad převzetí služeb při selhání](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Nastaví prioritu převzetí služeb při selhání pro všechny oblasti, do kterých se účet replikuje.|
|**Připojení služby Azure Cosmos DB k prostředkům**||
|[Připojení webové aplikace ke službě Azure Cosmos DB](../app-service/scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Vytvoří a propojí databázi Azure Cosmos DB a webovou aplikaci Azure.|
|||
