---
title: Práce s podporou kanálu změn v Azure Cosmos DB
description: Pomocí podpory zdroje změn Azure Cosmos DB můžete sledovat změny v dokumentech, zpracování založené na událostech, jako jsou aktivační události, a udržovat mezipaměti a analytické systémy aktuální.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: e36e95aeb25c83ccd94f11e25bfe9f1b8f7bfdad
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984857"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Kanál změn ve službě Azure Cosmos DB

Podpora kanálu změn ve službě Azure Cosmos DB funguje díky naslouchání změnám kontejneru Azure Cosmos. Výstupem je pak seznam změněných dokumentů v pořadí podle času úprav. Změny jsou trvalé, dají se zpracovat asynchronně a přírůstkově a výstup se dá distribuovat jednomu nebo více příjemcům k paralelnímu zpracování.

Přečtěte si další informace o [změně návrhových vzorů informačního kanálu](change-feed-design-patterns.md).

## <a name="supported-apis-and-client-sdks"></a>Podporovaná řešení API a klientské sady SDK

Tato funkce je aktuálně podporovaná následujícími sadami API Azure Cosmos DB a klientskými sadami SDK.

| **Klientské ovladače** | **SQL API** | **Rozhraní API Azure Cosmos DB pro Cassandru** | **Rozhraní API služby Azure Cosmos DB pro MongoDB** | **Rozhraní Gremlin API**|**Rozhraní Table API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Ano | Ano | Ano | Ano | Ne |
|Java|Ano|Ano|Ano|Ano|Ne|
|Python|Ano|Ano|Ano|Ano|Ne|
|Uzel/JS|Ano|Ano|Ano|Ano|Ne|

## <a name="change-feed-and-different-operations"></a>Změna informačního kanálu a různé operace

Dnes se ve zdroji změn zobrazují všechny vložení a aktualizace. Informační kanál změn nelze filtrovat pro určitý typ operace. Jednou z možných alternativ je přidat "měkkou značku" na položku pro aktualizace a filtr na základě toho při zpracování položek v kanálu změn.

V současné době změna kanálu není protokol odstraní. Podobně jako v předchozím příkladu můžete přidat měkkou značku na položky, které jsou odstraněny. Můžete například přidat atribut v položce s názvem "odstraněno" a nastavit jej na "true" a nastavit TTL na položku, aby mohla být automaticky odstraněna. Můžete si přečíst zdroj změn pro historické položky (poslední změna odpovídající položce, nezahrnuje mezilehlé změny), například položky, které byly přidány před pěti lety. Zdroj změn můžete přečíst již v době počátku kontejneru, ale pokud je položka odstraněna, bude odebrána ze zdroje změn.

### <a name="sort-order-of-items-in-change-feed"></a>Řazení položek v informačním kanálu o změně

Položky kanálu změn jsou v pořadí podle času jejich úpravy. Toto pořadí řazení je zaručeno pro klíč logického oddílu.

### <a name="consistency-level"></a>Úroveň konzistence

Při využívání kanálu změn v konečné úrovni konzistence může být duplicitní události mezi následné operace čtení kanálu změn (poslední událost jedné operace čtení se zobrazí jako první další).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Změna informačního kanálu v účtech Azure Cosmos s více oblastmi

V účtu Azure Cosmos s více oblastmi, pokud oblast pro zápis převezme služby při selhání, bude kanál změn fungovat v rámci ruční operace převzetí služeb při selhání a bude souvislý.

### <a name="change-feed-and-time-to-live-ttl"></a>Změna posuvu a času na život (TTL)

Pokud TTL (Time to Live) vlastnost je nastavena na položku -1, změna krmiva bude přetrvávat navždy. Pokud data nebudou odstraněna, zůstanou v kanálu změn.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Změna informačního kanálu a _etag, _lsn nebo _ts

Formát _etag je interní a neměli byste na něm mít závislost, protože se může kdykoli změnit. _ts je změna nebo časové razítko vytvoření. Můžete použít _ts pro chronologické porovnání. _lsn je ID dávky, které je přidáno pouze pro zdroj změn; představuje ID transakce. Mnoho položek může mít stejné _lsn. ETag na FeedResponse se liší od _etag se zobrazí na položce. _etag je vnitřní identifikátor a používá se pro řízení souběžnosti informuje o verzi položky, zatímco ETag se používá pro sekvenování krmiva.

## <a name="working-with-change-feed"></a>Práce s informačním kanálem o změnách

S informačním kanálem změn můžete pracovat pomocí následujících možností:

* [Používání informačního kanálu o změnách pomocí funkcí Azure](change-feed-functions.md)
* [Použití kanálu změn s procesorem kanálu změn](change-feed-processor.md) 

Kanál změn je k dispozici pro každý klíč logického oddílu v rámci kontejneru a může být distribuován mezi jeden nebo více spotřebitelů pro paralelní zpracování, jak je znázorněno na obrázku níže.

![Distribuované zpracování informačního kanálu změn Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Funkce zdroje změn

* Kanál změn je ve výchozím nastavení povolen pro všechny účty Azure Cosmos.

* Zřízenou [propustnost](request-units.md) můžete použít ke čtení z kanálu změn, stejně jako všechny ostatní operace Azure Cosmos DB, v libovolné oblasti přidružené k databázi Azure Cosmos.

* Informační kanál změn zahrnuje vložení a aktualizace operací provedených na položky v kontejneru. Odstranění můžete zachytit nastavením příznaku "obnovitelné odstranění" v položkách (například dokumenty) namísto odstranění. Případně můžete nastavit omezenou dobu vypršení platnosti pro vaše položky s [možností TTL](time-to-live.md). Například 24 hodin a použít hodnotu této vlastnosti k zachycení odstraní. S tímto řešením je nutné zpracovat změny v kratším časovém intervalu, než je doba vypršení platnosti TTL.

* Každá změna položky se zobrazí přesně jednou v kanálu změn a klienti musí spravovat logiku kontrolních bodů. Pokud se chcete vyhnout složitosti správy kontrolních bodů, procesor kanálu změn poskytuje automatické kontrolní body a "alespoň jednou" sémantiku. Viz [použití kanálu změn s procesorem kanálu změn](change-feed-processor.md).

* Do protokolu změn je zahrnuta pouze poslední změna pro danou položku. Mezilehlé změny nemusí být k dispozici.

* Kanál změn je seřazen podle pořadí změn v rámci každé hodnoty klíče logického oddílu. Neexistuje žádné zaručené pořadí mezi hodnotami klíče oddílu.

* Změny lze synchronizovat z libovolného bodu v čase, to znamená, že neexistuje žádné pevné období uchovávání dat, pro které jsou změny k dispozici.

* Změny jsou k dispozici paralelně pro všechny klíče logických oddílů kontejneru Azure Cosmos. Tato možnost umožňuje změny z velkých kontejnerů, které mají být zpracovány paralelně více spotřebitelů.

* Aplikace mohou požadovat více zdrojů změn ve stejném kontejneru současně. ChangeFeedOptions.StartTime lze použít k poskytnutí počátečního počátečního bodu. Například chcete-li najít token pokračování odpovídající danému času hodin. ContinuationToken, pokud je zadán, má přednost před StartTime a StartFromBeginning hodnoty. Přesnost ChangeFeedOptions.StartTime je ~5 sekund.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Změna kanálu v rozhraní API pro Cassandra a MongoDB

Funkce kanálu změn se zobrazí jako datový proud změn v rozhraní MongoDB API a query s predikátem v rozhraní CASSANDRA API. Další informace o podrobnostech implementace rozhraní MongoDB, najdete v tématu [change streams v rozhraní API Db Azure Cosmos pro MongoDB](mongodb-change-streams.md).

Nativní Apache Cassandra poskytuje sběr dat změny (CDC), mechanismus pro označení konkrétní tabulky pro archivaci, stejně jako odmítnutí zápisy do těchto tabulek, jakmile je dosaženo konfigurovatelné velikosti na disku pro protokol CDC. Funkce kanálu změn v rozhraní AZURE Cosmos DB API pro Cassandra zvyšuje možnost dotazování změny s predikátem přes CQL. Další informace o podrobnostech implementace najdete [v tématu Změna kanálu v rozhraní API Azure Cosmos DB pro Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Další kroky

Další informace o kanálu změn můžete získat v následujících článcích:

* [Možnosti čtení informačního kanálu o změnách](read-change-feed.md)
* [Používání informačního kanálu o změnách pomocí funkcí Azure](change-feed-functions.md)
* [Použití kanálu změn](change-feed-processor.md)
