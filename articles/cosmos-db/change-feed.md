---
title: Práce s podporou kanálu změn v Azure Cosmos DB
description: Pomocí podpory zdroje změn Azure Cosmos DB můžete sledovat změny v dokumentech, zpracování založené na událostech, jako jsou aktivační události, a udržovat mezipaměti a analytické systémy aktuální.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 898dfe7a619981b93af98effa942fdecbeb42dde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368124"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Kanál změn ve službě Azure Cosmos DB – Přehled

Podpora kanálu změn ve službě Azure Cosmos DB funguje díky naslouchání změnám kontejneru Azure Cosmos. Výstupem je pak seznam změněných dokumentů v pořadí podle času úprav. Změny jsou trvalé, dají se zpracovat asynchronně a přírůstkově a výstup se dá distribuovat jednomu nebo více příjemcům k paralelnímu zpracování. 

Azure Cosmos DB je vhodný pro IoT, hraní her, maloobchod a provozní protokolování aplikací. Běžným návrhovým vzorem v těchto aplikacích je použití změn dat k aktivaci dalších akcí. Příklady dalších opatření:

* Aktivace oznámení nebo volání rozhraní API při vložení nebo aktualizaci položky.
* Zpracování datových proudů v reálném čase pro zpracování IoT nebo analýzy v reálném čase na provozních datech.
* Další přesun dat synchronizací s mezipamětí nebo vyhledávačem nebo datovým skladem nebo archivací dat do úložiště do chladírenského úložiště.

Kanál změn v Azure Cosmos DB umožňuje vytvářet efektivní a škálovatelná řešení pro každý z těchto vzorů, jak je znázorněno na následujícím obrázku:

![Použití zdroje změn Azure Cosmos DB k využití analýz v reálném čase a výpočetních scénářů řízených událostmi](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>Podporovaná řešení API a klientské sady SDK

Tato funkce je aktuálně podporovaná následujícími sadami API Azure Cosmos DB a klientskými sadami SDK.

| **Klientské ovladače** | **Azure CLI** | **SQL API** | **Rozhraní API Azure Cosmos DB pro Cassandru** | **Rozhraní API služby Azure Cosmos DB pro MongoDB** | **Gremlin API**|**Rozhraní Table API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Není k dispozici | Ano | Ano | Ano | Ano | Ne |
|Java|Není k dispozici|Ano|Ano|Ano|Ano|Ne|
|Python|Není k dispozici|Ano|Ano|Ano|Ano|Ne|
|Uzel/JS|Není k dispozici|Ano|Ano|Ano|Ano|Ne|

## <a name="change-feed-and-different-operations"></a>Změna informačního kanálu a různé operace

Dnes se zobrazí všechny operace v kanálu změn. Funkce, kde můžete řídit změnu informačního kanálu, pro konkrétní operace, jako jsou pouze aktualizace a ne vloží ještě není k dispozici. Můžete přidat "soft marker" na položku pro aktualizace a filtr na základě toho při zpracování položek v kanálu změn. V současné době změna kanálu není protokol odstraní. Podobně jako v předchozím příkladu můžete přidat měkkou značku na položky, které jsou odstraněny, například můžete přidat atribut v položce s názvem "odstraněno" a nastavit jej na "true" a nastavit TTL na položku, takže může být automaticky odstraněn. Můžete si přečíst zdroj změn pro historické položky (poslední změna odpovídající položce, nezahrnuje mezilehlé změny), například položky, které byly přidány před pěti lety. Pokud položka není odstraněna, můžete si přečíst zdroj změn až k původu kontejneru.

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

## <a name="change-feed-use-cases-and-scenarios"></a>Změna případů použití informačního kanálu a scénářů

Kanál změn umožňuje efektivní zpracování velkých datových sad s velkým objemem zápisů. Kanál změn také nabízí alternativu k dotazování celé datové sady k identifikaci toho, co se změnilo.

### <a name="use-cases"></a>Případy použití

Pomocí zdroje změn můžete například efektivně provádět následující úkoly:

* Aktualizujte mezipaměť, aktualizujte index vyhledávání nebo aktualizujte datový sklad s daty uloženými v Azure Cosmos DB.

* Implementujte vrstvení a archivaci na úrovni aplikace, například ukládat "horká data" v Azure Cosmos DB a stárnout "studená data" do jiných úložných systémů, například [Azure Blob Storage](../storage/common/storage-introduction.md).

* Provádějte žádné migrace na jiný účet Azure Cosmos nebo do jiného kontejneru Azure Cosmos s jiným klíčem logického oddílu.

* Implementujte [architekturu lambda](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) pomocí Azure Cosmos DB, kde Azure Cosmos DB podporuje vrstvy obsluhující v reálném čase, dávky a dotazy, což umožňuje architekturu lambda s nízkými skutečnými užitky.

* Přijímejte a uklápěte data událostí ze zařízení, senzorů, infrastruktury a aplikací a zpracovávejte tyto události v reálném čase, například pomocí [Spark](../hdinsight/spark/apache-spark-overview.md).  Následující obrázek ukazuje, jak můžete implementovat architekturu lambda pomocí Azure Cosmos DB prostřednictvím kanálu změn:

![Kanál lambda založený na Azure Cosmos DB pro ingestování a dotaz](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Scénáře

Níže jsou uvedeny některé scénáře, které můžete snadno implementovat s kanálem změn:

* V rámci [webových](https://azure.microsoft.com/solutions/serverless/) nebo mobilních aplikací bez serveru můžete sledovat události, jako jsou všechny změny profilu zákazníka, předvolby nebo jejich umístění, a aktivovat určité akce, například odesílat nabízená oznámení do svých zařízení pomocí [funkce Azure](change-feed-functions.md).

* Pokud používáte Azure Cosmos DB k vytvoření hry, můžete například použít kanál změn k implementaci žebříčků v reálném čase na základě skóre z dokončených her.


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

* Aplikace mohou požadovat více zdrojů změn ve stejném kontejneru současně. ChangeFeedOptions.StartTime lze použít k poskytnutí počátečního počátečního bodu. Například chcete-li najít token pokračování odpovídající danému času hodin. ContinuationToken, pokud je zadán, vyhrává nad StartTime a StartFromBeginning hodnoty. Přesnost ChangeFeedOptions.StartTime je ~5 sekund. 

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Změna kanálu v rozhraní API pro Cassandra a MongoDB

Funkce kanálu změn se zobrazí jako datový proud změn v rozhraní MongoDB API a query s predikátem v rozhraní CASSANDRA API. Další informace o podrobnostech implementace rozhraní MongoDB, najdete v tématu [change streams v rozhraní API Db Azure Cosmos pro MongoDB](mongodb-change-streams.md).

Nativní Apache Cassandra poskytuje sběr dat změny (CDC), mechanismus pro označení konkrétní tabulky pro archivaci, stejně jako odmítnutí zápisy do těchto tabulek, jakmile je dosaženo konfigurovatelné velikosti na disku pro protokol CDC. Funkce kanálu změn v rozhraní AZURE Cosmos DB API pro Cassandra zvyšuje možnost dotazování změny s predikátem přes CQL. Další informace o podrobnostech implementace najdete [v tématu Změna kanálu v rozhraní API Azure Cosmos DB pro Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Další kroky

Další informace o kanálu změn můžete získat v následujících článcích:

* [Možnosti čtení informačního kanálu o změnách](read-change-feed.md)
* [Používání informačního kanálu o změnách pomocí funkcí Azure](change-feed-functions.md)
* [Použití kanálu změn](change-feed-processor.md)
