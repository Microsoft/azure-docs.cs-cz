---
title: Přístup ke změně kanálu v Azure Cosmos DB Azure Cosmos DB
description: Tento článek popisuje různé možnosti, které jsou k dispozici pro čtení a přístup k informačnímu kanálu změny v Azure Cosmos DB.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2020
ms.reviewer: sngun
ms.openlocfilehash: 266a13b7702f567e69129c0b4e92c4bd7c0f29ef
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982492"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Čtení z kanálu změn služby Azure Cosmos DB

S kanálem pro změnu Azure Cosmos DB můžete pracovat pomocí kterékoli z následujících možností:

* Použití Azure Functions
* Použití procesoru Change feed
* Použití sady SQL API SDK služby Azure Cosmos DB
* Použití modelu vyžádání obsahu kanálu změn (Preview)

## <a name="using-azure-functions"></a>Použití Azure Functions

Azure Functions je nejjednodušší a doporučená možnost. Když vytvoříte Trigger Azure Functions pro Cosmos DB, můžete vybrat kontejner, který se má připojit, a funkce Azure se aktivuje, kdykoli dojde ke změně kontejneru. Aktivační události se dají vytvořit pomocí portálu Azure Functions, Azure Cosmos DBho portálu nebo programově se sadami SDK. Visual Studio a VS Code poskytují podporu pro psaní Azure Functions a můžete dokonce používat rozhraní příkazového řádku Azure Functions pro vývoj pro různé platformy. Můžete napsat a ladit kód na ploše a pak nasadit funkci jediným kliknutím. Další informace najdete v článcích [databáze bez serveru s využitím Azure Functions](serverless-computing-database.md) a s [využitím článku změny kanálu s Azure Functions](change-feed-functions.md)).

## <a name="using-the-change-feed-processor"></a>Použití procesoru Change feed

Procesor Change feed skrývá složitost a stále poskytuje úplnou kontrolu nad kanálem změn. Knihovna sleduje model pozorovatele, kde je vaše funkce pro zpracování volána knihovnou. Pokud máte kanál změny s vysokou propustností, můžete vytvořit instanci více klientů pro čtení kanálu změn. Vzhledem k tomu, že používáte knihovnu Change feed Processor, bude zatížení automaticky rozděleno mezi různé klienty, aniž byste museli implementovat tuto logiku. Veškerou složitost zpracovává knihovna. Další informace najdete v tématu [použití procesoru Change feed](change-feed-processor.md). Procesor změn kanálu je součástí sady [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Použití sady SQL API SDK služby Azure Cosmos DB

Pomocí sady SDK získáte řízení kanálu změn na nízké úrovni. Můžete spravovat kontrolní bod, přistupovat k určitému klíči logického oddílu atd. Pokud máte více čtenářů, můžete použít `ChangeFeedOptions` k distribuci čtení zatížení do různých vláken nebo různých klientů.

## <a name="using-the-change-feed-pull-model"></a>Použití modelu vyžádání změny kanálu

[Model vyžádání změny kanálu](change-feed-pull-model.md) umožňuje využívat kanál změn vlastním tempem a paralelizovatým zpracováním změn pomocí FeedRanges. FeedRange zahrnuje rozsah hodnot klíče oddílu. Pomocí modelu vyžádání změny kanálu je také snadné zpracovat změny konkrétního klíče oddílu.

> [!NOTE]
> Model Pull pro změnu kanálu je momentálně ve [verzi Preview v sadě Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview) . Verze Preview není ještě dostupná pro jiné verze sady SDK.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Změna kanálu rozhraní API pro Cassandra a MongoDB

Funkce Change feed je v rozhraní MongoDB API označená jako datový proud a dotaz s predikátem v rozhraní API Cassandra. Další informace o podrobnostech implementace rozhraní MongoDB API najdete v tématu [Změna datových proudů v rozhraní Azure Cosmos DB API pro MongoDB](mongodb-change-streams.md).

Nativní Apache Cassandra poskytuje Change Data Capture (CDC), mechanismus pro označení určitých tabulek pro archivaci a také odmítnutí zápisů do těchto tabulek, jakmile se dosáhne konfigurovatelné velikosti na disku pro protokol CDC. Funkce kanálu změn v rozhraní Azure Cosmos DB API pro Cassandra vylepšuje schopnost dotazovat se na změny pomocí predikátu prostřednictvím CQL. Další informace o podrobnostech implementace najdete v tématu [Změna kanálu v rozhraní Azure Cosmos DB API pro Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Další kroky

Teď můžete pokračovat a získat další informace o službě Change feed v následujících článcích:

* [Přehled kanálu změn](change-feed.md)
* [Použití Change feed s Azure Functions](change-feed-functions.md)
* [Použití knihovny Change feed Processor](change-feed-processor.md)
