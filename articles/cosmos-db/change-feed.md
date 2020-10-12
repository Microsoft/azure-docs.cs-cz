---
title: Práce s podporou kanálu změn v Azure Cosmos DB
description: Pomocí Azure Cosmos DB podpory pro změnu kanálu můžete sledovat změny v dokumentech, jako jsou triggery založené na událostech, jako triggery a uchovávat mezipaměti a analytické systémy v aktuálním stavu.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 4cd0ad1553f04a781349a4664fbb408108015632
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88510279"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Kanál změn ve službě Azure Cosmos DB

Podpora kanálu změn ve službě Azure Cosmos DB funguje díky naslouchání změnám kontejneru Azure Cosmos. Výstupem je pak seznam změněných dokumentů v pořadí podle času úprav. Změny jsou trvalé, dají se zpracovat asynchronně a přírůstkově a výstup se dá distribuovat jednomu nebo více příjemcům k paralelnímu zpracování.

Přečtěte si další informace o [vzorech návrhu kanálu změn](change-feed-design-patterns.md).

## <a name="supported-apis-and-client-sdks"></a>Podporovaná rozhraní API a klientské sady SDK

Tato funkce je v současnosti podporovaná následujícími Azure Cosmos DB rozhraní API a klientské sady SDK.

| **Klientské ovladače** | **ROZHRANÍ SQL API** | **Rozhraní API pro Cassandra Azure Cosmos DB** | **Rozhraní API služby Azure Cosmos DB pro MongoDB** | **Rozhraní Gremlin API**|**Rozhraní Table API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Yes | Yes | Yes | Yes | No |
|Java|Yes|Yes|Yes|Yes|No|
|Python|Yes|Yes|Yes|Yes|No|
|Uzel/JS|Yes|Yes|Yes|Yes|No|

## <a name="change-feed-and-different-operations"></a>Změna kanálu a různých operací

V dnešní době se v kanálu změn zobrazí všechna vložení a aktualizace. Nelze filtrovat kanál změn pro určitý typ operace. Jedním z možných alternativ je přidat "měkký Marker" na položku pro aktualizace a filtr na základě toho, že při zpracování položek v kanálu změn.

V současné době se změna kanálu neodstraní. Podobně jako v předchozím příkladu můžete přidat měkké označení pro položky, které se odstraňují. Můžete například přidat atribut do položky s názvem "Deleted" a nastavit ji na hodnotu "true" a nastavit hodnotu TTL pro položku, aby ji bylo možné automaticky odstranit. Můžete si přečíst kanál změn pro historické položky (Poslední změna odpovídající položce, nezahrnuje mezilehlé změny), například položky přidané před pěti lety. Kanál změn si můžete přečíst až zpátky jako počátek svého kontejneru, ale pokud se položka odstraní, odebere se z kanálu změn.

### <a name="sort-order-of-items-in-change-feed"></a>Pořadí řazení položek v kanálu změn

Položky kanálu změny přicházejí v pořadí podle času změny. Toto pořadí řazení je zaručeno na jeden klíč logického oddílu.

### <a name="consistency-level"></a>Úroveň konzistence

Při využívání kanálu změn v konečné úrovni konzistence může dojít k duplicitním událostem v-mezi následnými operacemi čtení změn kanálu (poslední událost operace čtení se zobrazí jako první z následujících).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Změna kanálu v účtech Azure Cosmos ve více oblastech

Pokud dojde k převzetí služeb při selhání v účtu Azure Cosmos ve více oblastech, bude kanál změn fungovat v rámci ruční operace převzetí služeb při selhání a bude souvislý.

### <a name="change-feed-and-time-to-live-ttl"></a>Změnit kanál a TTL (Time to Live)

Pokud je vlastnost TTL (Time to Live) nastavená na položku na hodnotu-1, kanál změn zůstane trvale uložený. Pokud se data neodstraní, zůstane v informačním kanálu změn.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Změna kanálu a _etag _lsn nebo _ts

Formát _etag je interní a na něj byste neměli mít závislost, protože se může kdykoli změnit. _ts je změna nebo časové razítko vytvoření. K chronologickému porovnání můžete použít _ts. _lsn je ID dávky, které se přidají jenom pro změny kanálu. představuje ID transakce. Mnoho položek může mít stejné _lsn. ETag v FeedResponse se liší od _etag, které vidíte na položce. _etag je interní identifikátor, který se používá pro řízení souběžnosti. Vlastnost _etag informuje o verzi položky, zatímco vlastnost ETag se používá pro sekvencování informačního kanálu.

## <a name="working-with-change-feed"></a>Práce s kanálem změn

S kanálem změn můžete pracovat pomocí následujících možností:

* [Použití Change feed s Azure Functions](change-feed-functions.md)
* [Použití Change feed s procesorem Change feed](change-feed-processor.md) 

Kanál změn je k dispozici pro každý klíč logického oddílu v rámci kontejneru a lze jej distribuovat mezi jednoho nebo více příjemců pro paralelní zpracování, jak je znázorněno na následujícím obrázku.

:::image type="content" source="./media/change-feed/changefeedvisual.png" alt-text="Distribuované zpracování kanálu změny Azure Cosmos DB" border="false":::

## <a name="features-of-change-feed"></a>Funkce kanálu změn

* Pro všechny účty Azure Cosmos je ve výchozím nastavení povolený kanál změn.

* Pomocí [zřízené propustnosti](request-units.md) můžete číst z kanálu změn stejně jako u jakékoli jiné operace Azure Cosmos DB, a to v jakékoli oblasti přidružené k vaší databázi Azure Cosmos.

* Kanál změn obsahuje operace vložení a aktualizace provedené u položek v rámci kontejneru. Můžete zachytit odstranění nastavením příznaku "obnovitelného odstranění" v rámci položek (například dokumenty) místo odstranění. Případně můžete nastavit omezenou dobu vypršení platnosti vašich položek pomocí [funkce TTL](time-to-live.md). Například 24 hodin a použijte hodnotu této vlastnosti k zachycení odstranění. V tomto řešení je nutné zpracovat změny během kratšího časového intervalu, než je doba vypršení TTL.

* Každá změna položky se v informačním kanálu změny zobrazí přesně jednou a klienti musí spravovat logiku kontrolního bodu. Pokud se chcete vyhnout složitosti správy kontrolních bodů, poskytuje procesor Change feed automatické vytváření kontrolních bodů a alespoň jednou sémantiku. Viz [použití kanálu změn s procesorem Change feed](change-feed-processor.md).

* V protokolu změn je zahrnutá jenom Poslední změna dané položky. Přechodné změny nemusí být k dispozici.

* Kanál změn je seřazen podle pořadí úprav v rámci každé hodnoty klíče logického oddílu. V rámci hodnot klíčů oddílu není zaručeno žádné zaručené pořadí.

* Změny je možné synchronizovat z libovolného bodu v čase, to znamená, že neexistuje žádná pevná doba uchovávání dat, pro kterou jsou k dispozici změny.

* Změny jsou k dispozici paralelně pro všechny klíče logického oddílu kontejneru Azure Cosmos. Tato funkce umožňuje, aby se změny z velkých kontejnerů zpracovaly paralelně více příjemcům.

* Aplikace mohou současně požadovat více kanálů změn ve stejném kontejneru. ChangeFeedOptions. StartTime lze použít k poskytnutí počátečního počátečního bodu. Například chcete-li najít token pro pokračování odpovídající danému časovému okamžiku. Token continuationtoken, pokud je zadáno, má přednost před hodnotami StartTime a StartFromBeginning. Přesnost ChangeFeedOptions. StartTime je ~ 5 sekund.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Změna kanálu rozhraní API pro Cassandra a MongoDB

Funkce Change feed je v rozhraní MongoDB API označená jako datový proud a dotaz s predikátem v rozhraní API Cassandra. Další informace o podrobnostech implementace rozhraní MongoDB API najdete v tématu [Změna datových proudů v rozhraní Azure Cosmos DB API pro MongoDB](mongodb-change-streams.md).

Nativní Apache Cassandra poskytuje Change Data Capture (CDC), mechanismus pro označení určitých tabulek pro archivaci a také odmítnutí zápisů do těchto tabulek, jakmile se dosáhne konfigurovatelné velikosti na disku pro protokol CDC. Funkce kanálu změn v rozhraní Azure Cosmos DB API pro Cassandra vylepšuje schopnost dotazovat se na změny pomocí predikátu prostřednictvím CQL. Další informace o podrobnostech implementace najdete v tématu [Změna kanálu v rozhraní Azure Cosmos DB API pro Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Další kroky

Teď můžete pokračovat a získat další informace o službě Change feed v následujících článcích:

* [Možnosti čtení kanálu změn](read-change-feed.md)
* [Použití Change feed s Azure Functions](change-feed-functions.md)
* [Použití procesoru Change feed](change-feed-processor.md)
