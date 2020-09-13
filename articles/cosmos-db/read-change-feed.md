---
title: Čtení z kanálu změn služby Azure Cosmos DB
description: Tento článek popisuje různé možnosti, které jsou k dispozici pro čtení a přístup k informačnímu kanálu změny v Azure Cosmos DB.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/09/2020
ms.reviewer: sngun
ms.openlocfilehash: 58db7dcade7567d632fb405b31c4ff7bdbc6e71a
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90018965"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Čtení z kanálu změn služby Azure Cosmos DB

S kanálem pro změnu Azure Cosmos DB můžete pracovat pomocí modelu push nebo modelu Pull. Při použití modelu nabízeného oznámení Server (procesor změn kanálu) přenáší práci do klienta, který má obchodní logiku pro zpracování této práce. Složitá kontrola práce a ukládání stavu pro poslední zpracovávanou práci se však zpracovává na serveru.

U modelu Pull musí klient vyžádat si práci ze serveru. Klient, v tomto případě má nejen obchodní logiku pro zpracování práce, ale také ukládá stav pro poslední zpracovávanou práci, zpracovává vyrovnávání zatížení mezi více klienty zpracovávajících práci paralelně a zpracovává chyby.

Při čtení z kanálu změn Azure Cosmos DB obvykle doporučujeme použít model nabízených oznámení, protože nebudete si muset dělat starosti:

- Dotazování kanálu změn pro budoucí změny.
- Ukládání stavu pro poslední zpracovanou změnu. Při čtení z kanálu změn se tento údaj automaticky uloží do [kontejneru zapůjčení](change-feed-processor.md#components-of-the-change-feed-processor).
- Vyrovnávání zatížení mezi více klienty, které spotřebovávají změny. Například pokud jeden klient nemůže uchovávat změny zpracování a další má dostupnou kapacitu.
- [Zpracování chyb](change-feed-processor.md#error-handling). Například automatické opakování neúspěšných změn, které nebyly správně zpracovány po neošetřené výjimce v kódu nebo přechodný problém sítě.

Většina scénářů, které používají kanál změny Azure Cosmos DB, bude používat jednu z možností modelu nabízených oznámení. Existují však situace, kdy můžete chtít další řízení nízké úrovně pro model Pull. Zde jsou některé z nich:

- Čtení změn z konkrétního klíče oddílu
- Řízení tempa, ve kterém klient přijímá změny ke zpracování
- Provedení jednorázového čtení stávajících dat v kanálu změn (například k migraci dat)

## <a name="reading-change-feed-with-a-push-model"></a>Čtení kanálu změn pomocí modelu push

Použití modelu nabízeného oznámení je nejjednodušší způsob, jak číst z kanálu změn. Existují dva způsoby, jak můžete číst z kanálu změn pomocí modelu push: [Azure Functions Cosmos DB triggery](change-feed-functions.md) a [Knihovna Change feed Processor](change-feed-processor.md). Azure Functions používá procesor změn kanálu na pozadí, takže tyto možnosti jsou stejně podobné způsobům čtení kanálu změn. Představte si Azure Functions jako jednoduše hostující platformu pro procesor Change feed, a ne zcela jiný způsob čtení kanálu změn.

### <a name="azure-functions"></a>Azure Functions

Azure Functions je nejjednodušší volbou v případě, že právě začínáte používat kanál změn. Vzhledem k jednoduchosti je to také doporučená možnost pro většinu případů použití kanálu změn. Když vytvoříte aktivační událost Azure Functions pro Azure Cosmos DB, vyberete kontejner, který se má připojit, a funkce Azure se aktivuje, kdykoli dojde ke změně v kontejneru. Vzhledem k tomu, že Azure Functions používá procesor změn v kanálu na pozadí, automaticky parallelizes změnu zpracování v [oddílech](partition-data.md)kontejneru.

Vývoj v prostředí Azure Functions je jednoduchý a může být rychlejší než nasazení procesoru Change feed. Aktivační události se dají vytvořit pomocí Azure Functionsového portálu nebo programově pomocí sad SDK. Visual Studio a VS Code poskytují podporu pro psaní Azure Functions a můžete dokonce používat rozhraní příkazového řádku Azure Functions pro vývoj pro různé platformy. Můžete napsat a ladit kód na ploše a pak nasadit funkci jediným kliknutím. Další informace najdete v tématu [výpočetní databáze bez serveru s využitím Azure Functions](serverless-computing-database.md) a [používáním informačního kanálu se Azure Functionsmi](change-feed-functions.md) články.

### <a name="change-feed-processor-library"></a>Změna knihovny procesoru kanálu

Procesor změn kanálu vám poskytne lepší kontrolu nad kanálem změn a stále skrývá většinu složitosti. Knihovna Change feed Processor se řídí modelem pozorovatele, kde je vaše funkce pro zpracování volána knihovnou. Knihovna Change feed Processor bude automaticky kontrolovat změny a v případě, že se změny naleznou, nahrajte je do klienta. Pokud máte kanál změny s vysokou propustností, můžete vytvořit instanci více klientů pro čtení kanálu změn. Knihovna Change feed Processor bude zatížení automaticky rozdělit mezi různé klienty. Nebudete muset implementovat žádnou logiku pro vyrovnávání zatížení mezi více klienty nebo jakoukoli logiku pro udržení stavu zapůjčení.

Knihovna Change feed Processor garantuje doručení všech změn "alespoň jednou". Jinými slovy, pokud použijete knihovnu Change feed Processor, vaše funkce zpracování bude pro každou položku v kanálu změn povolána úspěšně. Pokud v obchodní logice ve vaší funkci zpracování dojde k neošetřené výjimce, budou se neúspěšné změny opakovat, dokud nebudou úspěšně zpracovány. Chcete-li zabránit tomu, aby procesor změn v kanálu se neustále znovu zablokoval "zablokování", přidejte do funkce zpracování logiku, která do fronty nedoručených zpráv zapisuje dokumenty, a to na základě výjimky. Přečtěte si další informace o [zpracování chyb](change-feed-processor.md#error-handling).

V Azure Functions je doporučení pro zpracování chyb stejné. Přesto byste měli do svého kódu delegáta přidat logiku pro zápis dokumentů, na základě výjimky, do fronty nedoručených zpráv. Pokud ale ve funkci Azure Function existuje Neošetřená výjimka, změna, která výjimku vygenerovala, se automaticky zopakuje. Pokud v obchodní Logic dojde k neošetřené výjimce, funkce Azure se přesune na zpracování další změny. Funkce Azure neopakuje stejnou neúspěšnou změnu.

Podobně jako Azure Functions je vývoj s knihovnou Change feed Processor snadno snadný. Zodpovídáte však za nasazení jednoho nebo více hostitelů pro procesor změn kanálu. Hostitel je instance aplikace, která používá procesor změn kanálu k naslouchání změnám. I když má Azure Functions možnosti automatického škálování, zodpovídáte za škálování hostitelů. Další informace najdete v tématu [použití procesoru Change feed](change-feed-processor.md#dynamic-scaling). Knihovna Change feed Processor je součástí sady [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3).

## <a name="reading-change-feed-with-a-pull-model"></a>Čtení kanálu změn pomocí modelu pull

[Model vyžádání změny kanálu](change-feed-pull-model.md) umožňuje využívat kanál změn vlastním tempem. Změny musí požadovat klient a neexistují žádné automatické dotazování pro změny. Pokud chcete trvale zpracovat poslední změnu pomocí záložky (podobně jako kontejner zapůjčení modelu push), budete muset [Uložit token pro pokračování](change-feed-pull-model.md#saving-continuation-tokens).

Pomocí modelu Pull pro změnu kanálu získáte větší kontrolu nad informačním kanálem změn. Při čtení kanálu změn pomocí modelu Pull máte tři možnosti:

- Číst změny celého kontejneru
- Číst změny pro konkrétní [FeedRange](change-feed-pull-model.md#using-feedrange-for-parallelization)
- Načte změny pro konkrétní hodnotu klíče oddílu.

Můžete paralelizovat zpracování změn napříč více klienty, stejně jako s procesorem Change feed. Model pro vyžádání obsahu ale automaticky nezpracovává vyrovnávání zatížení napříč klienty. Když použijete model Pull k paralelizovat zpracování kanálu změn, nejprve získáte seznam FeedRanges. FeedRange zahrnuje rozsah hodnot klíče oddílu. Budete potřebovat proces Orchestrator, který získá FeedRanges a distribuuje je mezi vaše počítače. Pak můžete tyto FeedRanges použít k paralelnímu čtení kanálu změn.

Pro model Pull není k dispozici žádná předdefinovaná "záruka na doručení" alespoň jednou ". Model pro vyžádání obsahu poskytuje řízení nízké úrovně pro rozhodování o tom, jakým způsobem chcete zpracovávat chyby.

> [!NOTE]
> Model Pull pro změnu kanálu je momentálně ve [verzi Preview v sadě Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.13.0-preview) . Verze Preview není ještě dostupná pro jiné verze sady SDK.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Změna kanálu rozhraní API pro Cassandra a MongoDB

Funkce Change feed je v rozhraní MongoDB API označená jako Změna datových proudů a dotaz s predikátem v rozhraní API Cassandra. Další informace o podrobnostech implementace rozhraní MongoDB API najdete v tématu [Změna datových proudů v rozhraní Azure Cosmos DB API pro MongoDB](mongodb-change-streams.md).

Nativní Apache Cassandra poskytuje Change Data Capture (CDC), mechanismus pro označení určitých tabulek pro archivaci a také odmítnutí zápisů do těchto tabulek, jakmile se dosáhne konfigurovatelné velikosti na disku pro protokol CDC. Funkce kanálu změn v rozhraní Azure Cosmos DB API pro Cassandra vylepšuje schopnost dotazovat se na změny pomocí predikátu prostřednictvím CQL. Další informace o podrobnostech implementace najdete v tématu [Změna kanálu v rozhraní Azure Cosmos DB API pro Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Další kroky

Teď můžete dál získat další informace o službě Change feed v následujících článcích:

* [Přehled kanálu změn](change-feed.md)
* [Použití Change feed s Azure Functions](change-feed-functions.md)
* [Použití knihovny Change feed Processor](change-feed-processor.md)
