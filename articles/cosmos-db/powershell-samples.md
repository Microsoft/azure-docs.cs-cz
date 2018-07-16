---
title: Ukázky Azure PowerShellu pro službu Azure Cosmos DB | Microsoft Docs
description: Ukázky Azure PowerShellu – skripty, které vám pomohou vytvořit a spravovat účty služby Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
tags: azure-service-management
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: na
ms.topic: sample
ms.date: 10/16/2017
ms.author: sngun
ms.openlocfilehash: 066826661381f40af2943359eb75abae8306bb7b
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855579"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Ukázky Azure PowerShellu pro službu Azure Cosmos DB

Následující tabulka obsahuje odkazy na ukázkové skripty Azure PowerShellu pro službu Azure Cosmos DB. V současné době můžete prostřednictvím PowerShellu spravovat jenom účet služby Azure Cosmos DB. Jiné prostředky, například databáze a kontejnery, nelze prostřednictvím PowerShellu spravovat.

| |  |
|---|---|
|**Vytvoření účtu služby Azure Cosmos DB**||
|[Vytvoření účtu rozhraní SQL API](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří jeden účet služby Azure Cosmos DB k použití s rozhraním SQL API. |
|[Vytvoření účtu rozhraní MongoDB API](scripts/create-mongodb-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří jeden účet služby Azure Cosmos DB k použití s rozhraním MongoDB API. |
|[Vytvoření účtu rozhraní Gremlin API](scripts/create-graph-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří jeden účet služby Azure Cosmos DB k použití s rozhraním Gremlin API. |
|**Škálování služby Azure Cosmos DB**||
|[Replikace účtu služby Azure Cosmos DB ve více oblastech a konfigurace priorit převzetí služeb při selhání](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Globálně replikuje data účtu do několika oblastí s určenou prioritou převzetí služeb při selhání.|
|**Zabezpečení služby Azure Cosmos DB**||
| [Získání klíčů účtu](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Získá pro účet primární a sekundární hlavní klíč pro zápis a primární a sekundární klíč jen pro čtení.|
| [Získání připojovacího řetězce MongoDB](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Získá připojovací řetězec pro připojení aplikace MongoDB k účtu služby Azure Cosmos DB.|
|[Opětovné vygenerování klíčů účtu](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Znovu vygeneruje pro účet hlavní klíč nebo klíč jen pro čtení.|
|[Vytvoření brány firewall](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří zásadu řízení příchozího přístupu z IP adres pro omezení přístupu k účtu na schválenou sadu počítačů nebo cloudových služeb.|
|**Vysoká dostupnost, zotavení po havárii, zálohování a obnovení**||
|[Konfigurace zásad převzetí služeb při selhání](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Nastaví prioritu převzetí služeb při selhání pro všechny oblasti, do kterých se účet replikuje.|
|||
