---
title: Přístup ke změně kanálu v Azure Cosmos DB Azure Cosmos DB
description: Tento článek popisuje různé možnosti, které jsou k dispozici pro čtení a přístup k informačnímu kanálu změn v Azure Cosmos DB Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: fc7e78a44d03af8952c1e178a3e92b1ee0c6fe66
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688119"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Čtení kanálu změn Azure Cosmos DB

S kanálem pro změnu Azure Cosmos DB můžete pracovat pomocí kterékoli z následujících možností:

* Použití Azure Functions
* Použití knihovny Change feed Processor
* Použití sady SDK rozhraní Azure Cosmos DB SQL API

## <a name="using-azure-functions"></a>Použití Azure Functions

Azure Functions je nejjednodušší a doporučená možnost. Když vytvoříte Trigger Azure Functions pro Cosmos DB, můžete vybrat kontejner, který se má připojit, a funkce Azure se aktivuje, kdykoli dojde ke změně kontejneru. Aktivační události se dají vytvořit pomocí portálu Azure Functions, Azure Cosmos DBho portálu nebo programově se sadami SDK. Visual Studio a VS Code poskytují podporu pro psaní Azure Functions a můžete dokonce používat rozhraní příkazového řádku Azure Functions pro vývoj pro různé platformy. Můžete napsat a ladit kód na ploše a pak nasadit funkci jediným kliknutím. Další informace najdete v článcích [databáze bez serveru s využitím Azure Functions](serverless-computing-database.md) a s [využitím článku změny kanálu s Azure Functions](change-feed-functions.md)).

## <a name="using-the-change-feed-processor-library"></a>Použití knihovny Change feed Processor

Knihovna Change feed Processor skrývá složitost a stále poskytuje úplnou kontrolu nad kanálem změn. Knihovna sleduje model pozorovatele, kde je vaše funkce pro zpracování volána knihovnou. Pokud máte kanál změny s vysokou propustností, můžete vytvořit instanci více klientů pro čtení kanálu změn. Vzhledem k tomu, že používáte knihovnu Change feed Processor, bude zatížení automaticky rozděleno mezi různé klienty, aniž byste museli implementovat tuto logiku. Veškerou složitost zpracovává knihovna. Pokud chcete mít vlastní nástroj pro vyrovnávání zatížení, můžete implementovat `IPartitionLoadBalancingStrategy` pro strategii vlastních oddílů ke zpracování kanálu změn. Další informace najdete v tématu [použití knihovny Change feed Processor](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Použití sady SDK rozhraní Azure Cosmos DB SQL API

Pomocí sady SDK získáte řízení kanálu změn na nízké úrovni. Můžete spravovat kontrolní bod, přistupovat k určitému klíči logického oddílu atd. Pokud máte více čtenářů, můžete použít `ChangeFeedOptions` k distribuci načtení čtení do různých vláken nebo různých klientů.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Změna kanálu rozhraní API pro Cassandra a MongoDB

Funkce Change feed je v rozhraní MongoDB API označená jako datový proud a dotaz s predikátem v rozhraní API Cassandra. Další informace o podrobnostech implementace rozhraní MongoDB API najdete v tématu [Změna datových proudů v rozhraní Azure Cosmos DB API pro MongoDB](mongodb-change-streams.md).

Nativní Apache Cassandra poskytuje Change Data Capture (CDC), mechanismus pro označení určitých tabulek pro archivaci a také odmítnutí zápisů do těchto tabulek, jakmile se dosáhne konfigurovatelné velikosti na disku pro protokol CDC. Funkce kanálu změn v rozhraní Azure Cosmos DB API pro Cassandra vylepšuje schopnost dotazovat se na změny pomocí predikátu prostřednictvím CQL. Další informace o podrobnostech implementace najdete v tématu [Změna kanálu v rozhraní Azure Cosmos DB API pro Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Další kroky

Teď můžete pokračovat a získat další informace o službě Change feed v následujících článcích:

* [Přehled kanálu změn](change-feed.md)
* [Použití Change feed s Azure Functions](change-feed-functions.md)
* [Použití knihovny Change feed Processor](change-feed-processor.md)
