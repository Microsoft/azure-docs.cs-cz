---
title: Přístup k informačnímu kanálu změn v Azure Cosmos DB Azure Cosmos DB
description: Tento článek popisuje různé možnosti, které jsou k dispozici pro čtení a přístup ke změně kanálu v Azure Cosmos DB Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: fc7e78a44d03af8952c1e178a3e92b1ee0c6fe66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688119"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Čtení z kanálu změn služby Azure Cosmos DB

S informačním kanálem změn Azure Cosmos DB můžete pracovat pomocí některé z následujících možností:

* Použití Azure Functions
* Použití knihovny Change Feed Processor
* Použití sady SQL API SDK služby Azure Cosmos DB

## <a name="using-azure-functions"></a>Použití Azure Functions

Azure Functions je nejjednodušší a doporučená možnost. Když vytvoříte aktivační událost Funkce Azure pro Cosmos DB, můžete vybrat kontejner pro připojení a funkce Azure se aktivuje vždy, když dojde ke změně kontejneru. Aktivační události lze vytvořit pomocí portálu Azure Functions, portálu Azure Cosmos DB nebo programově pomocí sad SDK. Visual Studio a VS Code poskytují podporu pro psaní funkcí Azure a můžete dokonce použít funkce Azure CLI pro vývoj napříč platformami. Můžete napsat a ladit kód na ploše a pak nasadit funkci s jedním kliknutím. Další informace najdete [v článcích Sbízet](serverless-computing-database.md) se pomocí azure functions a [using change feed with Azure Functions](change-feed-functions.md)).

## <a name="using-the-change-feed-processor-library"></a>Použití knihovny Change Feed Processor

Knihovna procesoru kanálu změn skryje složitost a stále poskytuje úplnou kontrolu nad zdrojem změn. Knihovna sleduje vzor pozorovatele, kde je vaše funkce zpracování volána knihovnou. Pokud máte kanál pro změnu vysoké propustnost, můžete vytvořit konkretizovat více klientů ke čtení kanálu změn. Vzhledem k tomu, že používáte knihovnu procesoru kanálu změn, automaticky rozdělí zatížení mezi různé klienty, aniž byste museli implementovat tuto logiku. Veškerá složitost je zpracována knihovnou. Pokud chcete mít vlastní nástroj pro vyrovnávání `IPartitionLoadBalancingStrategy` zatížení, pak můžete implementovat pro vlastní strategii oddílu pro zpracování kanálu změn. Další informace najdete [v tématu použití knihovny procesorů kanálu změn](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Použití sady SQL API SDK služby Azure Cosmos DB

S sadou SDK získáte nízkoúrovňové řízení kanálu změn. Můžete spravovat kontrolní bod, přistupovat k určitému klíči logického oddílu atd. Pokud máte více čtecích `ChangeFeedOptions` čtecích dat, můžete použít k distribuci zatížení čtení do různých vláken nebo různých klientů.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Změna kanálu v rozhraní API pro Cassandra a MongoDB

Funkce kanálu změn se zobrazí jako datový proud změn v rozhraní MongoDB API a query s predikátem v rozhraní CASSANDRA API. Další informace o podrobnostech implementace rozhraní MongoDB, najdete v tématu [change streams v rozhraní API Db Azure Cosmos pro MongoDB](mongodb-change-streams.md).

Nativní Apache Cassandra poskytuje sběr dat změny (CDC), mechanismus pro označení konkrétní tabulky pro archivaci, stejně jako odmítnutí zápisy do těchto tabulek, jakmile je dosaženo konfigurovatelné velikosti na disku pro protokol CDC. Funkce kanálu změn v rozhraní AZURE Cosmos DB API pro Cassandra zvyšuje možnost dotazování změny s predikátem přes CQL. Další informace o podrobnostech implementace najdete [v tématu Změna kanálu v rozhraní API Azure Cosmos DB pro Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Další kroky

Další informace o kanálu změn můžete získat v následujících článcích:

* [Přehled zdroje změn](change-feed.md)
* [Používání informačního kanálu o změnách pomocí funkcí Azure](change-feed-functions.md)
* [Použití knihovny procesoru kanálu změn](change-feed-processor.md)
