---
title: Práce s změnu podpora kanálu ve službě Azure Cosmos DB
description: Pomocí Azure Cosmos DB podpory pro změnu kanálu můžete sledovat změny v dokumentech, jako jsou triggery založené na událostech, jako triggery a uchovávat mezipaměti a analytické systémy v aktuálním stavu.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 898dfe7a619981b93af98effa942fdecbeb42dde
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368124"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Změnit informační kanál v Azure Cosmos DB – přehled

Podpora kanálu změn ve službě Azure Cosmos DB funguje díky naslouchání změnám kontejneru Azure Cosmos. Potom vypíše seřazený seznam dokumentů, které byly změněny v pořadí, ve kterém byly změněny. Změny jsou trvalé, dají se zpracovat asynchronně a přírůstkově a výstup se dá distribuovat jednomu nebo více příjemcům k paralelnímu zpracování. 

Azure Cosmos DB je velmi vhodná pro Internet věcí, hraní her, maloobchod a provozní protokolování aplikací. Běžný vzor návrhu v těchto aplikacích je použít změny v datech k aktivaci dalších akcí. Příklady dalších akcí:

* Při vložení nebo aktualizaci položky, která aktivuje upozornění nebo volání rozhraní API.
* V reálném čase pro IoT a analýzy v reálném čase na provozních dat pro zpracování datových proudů.
* Přesun dat další synchronizaci s mezipaměti nebo vyhledávacího webu nebo datového skladu nebo archivaci dat do studeného úložiště.

Změna informačního kanálu ve službě Azure Cosmos DB umožňuje vytvářet efektivní a škálovatelné řešení pro každou z těchto vzorců, jak je znázorněno na následujícím obrázku:

![Použití změn databáze Azure Cosmos DB informační kanál k založený na událostech výpočetní scénáře a analýzy v reálném čase power](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>Podporované klientské sady SDK a rozhraní API

Tato funkce je aktuálně podporuje následující klientské sady SDK a rozhraní API služby Azure Cosmos DB.

| **Klientské ovladače** | **Azure CLI** | **ROZHRANÍ SQL API** | **Rozhraní API pro Cassandra Azure Cosmos DB** | **Rozhraní API pro MongoDB Azure Cosmos DB** | **Rozhraní API pro Gremlin**|**Rozhraní Table API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Není k dispozici | Ano | Ano | Ano | Ano | Ne |
|Java|Není k dispozici|Ano|Ano|Ano|Ano|Ne|
|Python|Není k dispozici|Ano|Ano|Ano|Ano|Ne|
|Uzel/JS|Není k dispozici|Ano|Ano|Ano|Ano|Ne|

## <a name="change-feed-and-different-operations"></a>Kanál změn a různé operace

V současné době se zobrazí všechny operace v kanálu změn. Funkce, kde můžete řídit změny informační kanál pro určité operace, například jenom aktualizace a vkládání není ještě není k dispozici. Můžete přidat "měkký Marker" na položku pro aktualizace a filtr na základě toho, že při zpracování položek v kanálu změn. V současné době se změna kanálu neodstraní. Podobně jako v předchozím příkladu můžete přidat softwarové značky u položek, které jsou mazány, například můžete přidat atribut v položce nazývá "odstraněné" a nastavte na hodnotu "true" a nastavte hodnotu TTL na položce, takže se dají automaticky odstranit. Můžete si přečíst kanál změn pro historické položky (Poslední změna odpovídající položce, nezahrnuje mezilehlé změny), například položky přidané před pěti lety. Pokud není položka smazána si můžete přečíst tato změna kanálu až na hodnotu původ vašeho kontejneru.

### <a name="sort-order-of-items-in-change-feed"></a>Pořadí položek v kanálu změn řazení

Změna položky informačního kanálu dodávají v pořadí jejich čas změny. Toto pořadí řazení je zaručeno na jeden klíč logického oddílu.

### <a name="consistency-level"></a>Úroveň konzistence

Při využívání kanálu změn v konečné úrovni konzistence může dojít k duplicitním událostem v-mezi následnými operacemi čtení změn kanálu (poslední událost operace čtení se zobrazí jako první z následujících).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Kanál v Azure Cosmos účty ve více oblastech změn

V účtu Azure Cosmos ve více oblastech Pokud oblast zápisu převezme služby při selhání, budou fungovat kanálu změn napříč operace ruční převzetí služeb při selhání a bude souvislé.

### <a name="change-feed-and-time-to-live-ttl"></a>Kanál změn a čas to Live (TTL)

Pokud hodnota TTL (Time to Live) vlastnost nastavena na položku na hodnotu -1, kanál změn se uloží navždy. Když se neodstraní data, zůstane v kanálu změn.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Kanál změn a _etag, _lsn nebo _ts

Formát _etag je interní a neměla by mít závislost, protože se sice dají kdykoli měnit. _ts je úprava nebo časové razítko vytvoření. Můžete použít _ts chronologickém porovnání. _lsn je ID dávky, které se přidají jenom pro změny kanálu. představuje ID transakce. Stejné _lsn může mít mnoho položek. Značka ETag na FeedResponse se liší od _etag, který se zobrazí v položce. _etag je interní identifikátor a používá se pro souběžnost ovládací prvek informuje o verzi položek, že značka ETag se používá pro pořadí úloh informačního kanálu.

## <a name="change-feed-use-cases-and-scenarios"></a>Scénáře a případy použití kanálu změn

Kanál změn umožňuje efektivní zpracování velkých datových sad s velký počet zápisů. Kanál změn také nabízí alternativu k dotazování celou datovou sadu k identifikaci, co se změnilo.

### <a name="use-cases"></a>Případy použití

Například díky kanálu změn můžete provádět následující úlohy efektivně:

* Aktualizovat mezipaměť, aktualizovat index vyhledávání nebo aktualizovat datový sklad s daty uloženými ve službě Azure Cosmos DB.

* Implementujte datovou škálu a archivaci dat na úrovni aplikace, například uložte "Hot data" v Azure Cosmos DB a stáří vychladit do jiných systémů úložiště, například [Azure Blob Storage](../storage/common/storage-introduction.md).

* Provádění nula dolů čas migrace do jiného účtu Azure Cosmos nebo jiný kontejner Azure Cosmos pomocí klíče jiný logický oddíl.

* Implementujte [architekturu lambda](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) pomocí Azure Cosmos DB, kde Azure Cosmos DB podporuje jak v reálném čase, tak i v dávkovém dotazování, což umožňuje architekturu lambda s nízkou úrovní nákladů na vlastnictví.

* Můžete přijímat a ukládat data událostí ze zařízení, senzorů, infrastruktury a aplikací a zpracovávat tyto události v reálném čase, například pomocí [Sparku](../hdinsight/spark/apache-spark-overview.md).  Následující obrázek ukazuje, jak můžete implementovat architektury lambda pomocí služby Azure Cosmos DB prostřednictvím kanálu změn:

![Azure Cosmos DB na základě lambda kanál pro příjem dat a dotazu](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Scénáře

Tady jsou některé scénáře, které je možné snadno implementovat pomocí kanálu změn:

* V rámci webových nebo mobilních aplikací bez [serveru](https://azure.microsoft.com/solutions/serverless/) můžete sledovat události, jako jsou všechny změny v profilu vašeho zákazníka, předvolby nebo jejich umístění a aktivovat určité akce, například odesílání nabízených oznámení do jejich zařízení pomocí [Azure Functions](change-feed-functions.md).

* Pokud používáte službu Azure Cosmos DB k vytvoření hry, je možné, například použití změnit informační kanál k implementaci v reálném čase žebříčky podle skóre, které se od dokončených hry.


## <a name="working-with-change-feed"></a>Práce s kanálu změn

Můžete pracovat pomocí kanálu změn pomocí následujících možností:

* [Použití Change feed s Azure Functions](change-feed-functions.md)
* [Použití Change feed s procesorem Change feed](change-feed-processor.md) 

Kanál změn je k dispozici pro každý logický oddíl klíč v kontejneru a jeho mohou být distribuovány na jeden nebo více příjemců pro paralelní zpracování, jak je znázorněno na následujícím obrázku.

![Distribuované zpracování kanálu změn služby Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Funkce kanálu změn

* Kanál změn je povoleno standardně pro všechny účty služby Azure Cosmos.

* Pomocí [zřízené propustnosti](request-units.md) můžete číst z kanálu změn stejně jako u jakékoli jiné operace Azure Cosmos DB, a to v jakékoli oblasti přidružené k vaší databázi Azure Cosmos.

* Kanál změn zahrnuje vložení a operace aktualizace provedené u položek v rámci kontejneru. Můžete zaznamenat odstraní nastavením příznaku "obnovitelného odstranění" v rámci vašich položek (například dokumenty) místo odstraní. Případně můžete nastavit omezenou dobu vypršení platnosti vašich položek pomocí [funkce TTL](time-to-live.md). Například 24 hodin a použití odstraní hodnota dané vlastnosti pro zachycení. Pomocí tohoto řešení je nutné zpracovat změny v časovém intervalu kratší než interval TTL, ZÍSKÁ dobu vypršení platnosti. 

* Každé změně položky se zobrazí přesně jednou v kanálu změn a klienti musí spravovat logiky vytváření kontrolních bodů. Pokud se chcete vyhnout složitosti správy kontrolních bodů, poskytuje procesor Change feed automatické vytváření kontrolních bodů a alespoň jednou sémantiku. Viz [použití kanálu změn s procesorem Change feed](change-feed-processor.md).

* Protokol změn je součástí pouze posledních změn pro danou položku. Přechodných změn nemusí být k dispozici.

* Kanál změn je řazen pořadím úpravy v rámci každé hodnoty klíče logického oddílu. Není zaručeno pořadí, v rámci hodnoty klíče oddílu.

* Změny můžete synchronizované z libovolný bod za běhu, který je neexistuje žádné pevné data uchovávají, pro které změny jsou k dispozici.

* Změny jsou k dispozici současně pro všechny klíče logický oddíl kontejneru Azure Cosmos. Tato funkce umožňuje, aby změny z velkých kontejnerů ke zpracování paralelní několik příjemců.

* Aplikace mohou současně požadovat více kanálů změn ve stejném kontejneru. ChangeFeedOptions.StartTime slouží k poskytování počátečního bodu. Chcete-li například najít odpovídající dané čas token pro pokračování. Token ContinuationToken, je-li zadána, wins přes hodnoty StartTime a StartFromBeginning. Přesnost ChangeFeedOptions.StartTime je přibližně 5 sekund. 

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Změna kanálu rozhraní API pro Cassandra a MongoDB

Funkce Change feed je v rozhraní MongoDB API označená jako datový proud a dotaz s predikátem v rozhraní API Cassandra. Další informace o podrobnostech implementace rozhraní MongoDB API najdete v tématu [Změna datových proudů v rozhraní Azure Cosmos DB API pro MongoDB](mongodb-change-streams.md).

Nativní Apache Cassandra poskytuje Change Data Capture (CDC), mechanismus pro označení určitých tabulek pro archivaci a také odmítnutí zápisů do těchto tabulek, jakmile se dosáhne konfigurovatelné velikosti na disku pro protokol CDC. Funkce kanálu změn v rozhraní Azure Cosmos DB API pro Cassandra vylepšuje schopnost dotazovat se na změny pomocí predikátu prostřednictvím CQL. Další informace o podrobnostech implementace najdete v tématu [Změna kanálu v rozhraní Azure Cosmos DB API pro Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Další kroky

Teď můžete přejít k další informace o změně v následujících článcích:

* [Možnosti čtení kanálu změn](read-change-feed.md)
* [Použití Change feed s Azure Functions](change-feed-functions.md)
* [Použití procesoru Change feed](change-feed-processor.md)
