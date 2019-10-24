---
title: Přístup ke změně kanálu v Azure Cosmos DB Azure Cosmos DB
description: Tento článek popisuje různé možnosti, které jsou k dispozici pro čtení a přístup k informačnímu kanálu změn v Azure Cosmos DB Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 3d30c9f946f97f06c1a3ba1cd2e77e1ab151a572
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754875"
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

## <a name="next-steps"></a>Další kroky

Teď můžete pokračovat a získat další informace o službě Change feed v následujících článcích:

* [Přehled kanálu změn](change-feed.md)
* [Použití Change feed s Azure Functions](change-feed-functions.md)
* [Použití knihovny Change feed Processor](change-feed-processor.md)
