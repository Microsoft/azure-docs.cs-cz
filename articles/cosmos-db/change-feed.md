---
title: Práce s podporou kanálu změn v Azure Cosmos DB
description: Využijte podporu Azure Cosmos DB změn kanálu ke sledování změn v dokumentech a provádění zpracování na základě událostí, jako jsou triggery a udržování mezipamětí a analytických systémů v aktuálním stavu.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 8e6bd3dadd636127f212db0ea0c0755a6b52a087
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757029"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Změnit informační kanál v Azure Cosmos DB – přehled

Změna podpory kanálu v Azure Cosmos DB funguje tak, že se v kontejneru Azure Cosmos poslouchat jakýmkoli změnám. Výstupem je pak seznam změněných dokumentů v pořadí podle času úprav. Změny jsou trvalé, dají se zpracovat asynchronně a přírůstkově a výstup se dá distribuovat jednomu nebo více příjemcům k paralelnímu zpracování. 

Azure Cosmos DB je vhodný pro aplikace pro IoT, hraní, maloobchodní a provozní protokolování. Běžným vzorem návrhu v těchto aplikacích je použití změn dat pro aktivaci dalších akcí. Mezi další akce patří:

* Aktivace oznámení nebo volání rozhraní API, když je položka vložena nebo aktualizována.
* Zpracování datových proudů v reálném čase pro práci v reálném čase a zpracování analýz na provozních datech.
* Další přesun dat buď synchronizací s mezipamětí, nebo vyhledávacím modulem nebo datovým skladem nebo archivací dat do studeného úložiště.

Informační kanál změny v Azure Cosmos DB umožňuje vytváření efektivních a škálovatelných řešení pro každý z těchto vzorů, jak je znázorněno na následujícím obrázku:

![Použití Azure Cosmos DB změn kanálu k napájení analýz v reálném čase a výpočetních scénářů založených na událostech](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>Podporovaná rozhraní API a klientské sady SDK

Tato funkce je v současnosti podporovaná následujícími Azure Cosmos DB rozhraní API a klientské sady SDK.

| **Klientské ovladače** | **Azure CLI** | **ROZHRANÍ SQL API** | **rozhraní API Cassandra** | **Rozhraní API pro MongoDB Azure Cosmos DB** | **Rozhraní API pro Gremlin**|**Rozhraní Table API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | není k dispozici | Ano | Ne | Ne | Ano | Ne |
|Java|není k dispozici|Ano|Ne|Ne|Ano|Ne|
|Python|není k dispozici|Ano|Ne|Ne|Ano|Ne|
|Uzel/JS|není k dispozici|Ano|Ne|Ne|Ano|Ne|

## <a name="change-feed-and-different-operations"></a>Změna kanálu a různých operací

V dnešní době se zobrazí všechny operace v kanálu změn. Funkce, ve kterých můžete ovládat kanál změn pro konkrétní operace, jako jsou pouze aktualizace a nikoli vložení, ještě nejsou k dispozici. Můžete přidat "měkký Marker" na položku pro aktualizace a filtr na základě toho, že při zpracování položek v kanálu změn. V současné době se změna kanálu neodstraní. Podobně jako v předchozím příkladu můžete přidat měkké označení pro položky, které jsou odstraňovány, například můžete přidat atribut v položce s názvem "Deleted" a nastavit ji na hodnotu "true" a nastavit hodnotu TTL pro položku, aby mohla být automaticky odstraněna. Můžete si přečíst kanál změn pro historické položky (Poslední změna odpovídající položce, nezahrnuje mezilehlé změny), například položky přidané před pěti lety. Pokud se položka neodstraní, můžete si přečíst kanál změn až do počátku kontejneru.

### <a name="sort-order-of-items-in-change-feed"></a>Pořadí řazení položek v kanálu změn

Položky kanálu změny přicházejí v pořadí podle času změny. Toto pořadí řazení je zaručeno na jeden klíč logického oddílu.

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Změna kanálu v účtech Azure Cosmos ve více oblastech

Pokud dojde k převzetí služeb při selhání v účtu Azure Cosmos ve více oblastech, bude kanál změn fungovat v rámci ruční operace převzetí služeb při selhání a bude souvislý.

### <a name="change-feed-and-time-to-live-ttl"></a>Změnit kanál a TTL (Time to Live)

Pokud je vlastnost TTL (Time to Live) nastavená na položku na hodnotu-1, kanál změn zůstane trvale uložený. Pokud se data neodstraní, zůstane v informačním kanálu změn.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Změna kanálu a _etag, _lsn nebo _ts

Formát _etag je interní a na něj byste neměli mít závislost, protože se může kdykoli změnit. _ts je změna nebo časové razítko vytvoření. _Ts můžete použít k chronologickému porovnání. _lsn je ID dávky, které je přidáno pouze pro změnu kanálu. představuje ID transakce. Mnoho položek může mít stejný _lsn. ETag v FeedResponse se liší od _etag, které vidíte na položce. _etag je interní identifikátor, který se používá pro řízení souběžnosti o verzi položky, zatímco značka ETag se používá pro sekvencování informačního kanálu.

## <a name="change-feed-use-cases-and-scenarios"></a>Změny případů použití kanálu a scénářů

Kanál změn umožňuje efektivní zpracování velkých datových sad s velkým objemem zápisů. Kanál změn také nabízí alternativu k dotazování celé datové sady, aby bylo možné zjistit, co se změnilo.

### <a name="use-cases"></a>Případy použití

Například u kanálu změn můžete efektivně provádět následující úlohy:

* Aktualizace mezipaměti, aktualizace indexu vyhledávání nebo aktualizace datového skladu s daty uloženými v Azure Cosmos DB.

* Implementujte datovou škálu a archivaci dat na úrovni aplikace, například uložte "Hot data" v Azure Cosmos DB a stáří vychladit do jiných systémů úložiště, například [Azure Blob Storage](../storage/common/storage-introduction.md).

* Proveďte žádné migrace za provozu do jiného účtu Azure Cosmos nebo jiného kontejneru Azure Cosmos s jiným klíčem logického oddílu.

* Implementujte [architekturu lambda](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) pomocí Azure Cosmos DB, kde Azure Cosmos DB podporuje jak v reálném čase, tak i v dávkovém dotazování, což umožňuje architekturu lambda s nízkou úrovní nákladů na vlastnictví.

* Můžete přijímat a ukládat data událostí ze zařízení, senzorů, infrastruktury a aplikací a zpracovávat tyto události v reálném čase, například pomocí [Sparku](../hdinsight/spark/apache-spark-overview.md).  Následující obrázek ukazuje, jak můžete implementovat architekturu lambda pomocí Azure Cosmos DB prostřednictvím kanálu změn:

![Azure Cosmos DB kanál lambda založený na ingestování a dotazování](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Scénáře

Níže jsou uvedeny některé z scénářů, které lze snadno implementovat pomocí kanálu změn:

* V rámci webových nebo mobilních aplikací bez [serveru](https://azure.microsoft.com/solutions/serverless/) můžete sledovat události, jako jsou všechny změny v profilu vašeho zákazníka, předvolby nebo jejich umístění a aktivovat určité akce, například odesílání nabízených oznámení do jejich zařízení pomocí [Azure. Funkce](change-feed-functions.md).

* Pokud používáte Azure Cosmos DB k vytvoření hry, můžete například použít kanál Change k implementaci žebříčky v reálném čase na základě skóre z dokončených her.


## <a name="working-with-change-feed"></a>Práce s kanálem změn

S kanálem změn můžete pracovat pomocí následujících možností:

* [Použití Change feed s Azure Functions](change-feed-functions.md)
* [Použití Change feed s procesorem Change feed](change-feed-processor.md) 

Kanál změn je k dispozici pro každý klíč logického oddílu v rámci kontejneru a lze jej distribuovat mezi jednoho nebo více příjemců pro paralelní zpracování, jak je znázorněno na následujícím obrázku.

![Distribuované zpracování kanálu změny Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Funkce kanálu změn

* Pro všechny účty Azure Cosmos je ve výchozím nastavení povolený kanál změn.

* Pomocí [zřízené propustnosti](request-units.md) můžete číst z kanálu změn stejně jako u jakékoli jiné operace Azure Cosmos DB, a to v jakékoli oblasti přidružené k vaší databázi Azure Cosmos.

* Kanál změn obsahuje operace vložení a aktualizace provedené u položek v rámci kontejneru. Můžete zachytit odstranění nastavením příznaku "obnovitelného odstranění" v rámci položek (například dokumenty) místo odstranění. Případně můžete nastavit omezenou dobu vypršení platnosti vašich položek pomocí [funkce TTL](time-to-live.md). Například 24 hodin a použijte hodnotu této vlastnosti k zachycení odstranění. V tomto řešení je nutné zpracovat změny během kratšího časového intervalu, než je doba vypršení TTL. 

* Každá změna položky se v informačním kanálu změny zobrazí přesně jednou a klienti musí spravovat logiku kontrolního bodu. Pokud se chcete vyhnout složitosti správy kontrolních bodů, poskytuje procesor Change feed automatické vytváření kontrolních bodů a alespoň jednou sémantiku. Viz [použití kanálu změn s procesorem Change feed](change-feed-processor.md).

* V protokolu změn je zahrnutá jenom Poslední změna dané položky. Přechodné změny nemusí být k dispozici.

* Kanál změn je seřazen podle pořadí úprav v rámci každé hodnoty klíče logického oddílu. V rámci hodnot klíčů oddílu není zaručeno žádné zaručené pořadí.

* Změny je možné synchronizovat z libovolného bodu v čase, to znamená, že neexistuje žádná pevná doba uchovávání dat, pro kterou jsou k dispozici změny.

* Změny jsou k dispozici paralelně pro všechny klíče logického oddílu kontejneru Azure Cosmos. Tato funkce umožňuje, aby se změny z velkých kontejnerů zpracovaly paralelně více příjemcům.

* Aplikace mohou současně požadovat více kanálů změn ve stejném kontejneru. ChangeFeedOptions. StartTime lze použít k poskytnutí počátečního počátečního bodu. Například chcete-li najít token pro pokračování odpovídající danému časovému okamžiku. Token continuationtoken je-li tento parametr zadán, služba WINS překračuje hodnoty StartTime a StartFromBeginning. Přesnost ChangeFeedOptions. StartTime je ~ 5 sekund. 

## <a name="next-steps"></a>Další kroky

Teď můžete pokračovat a získat další informace o službě Change feed v následujících článcích:

* [Možnosti čtení kanálu změn](read-change-feed.md)
* [Použití Change feed s Azure Functions](change-feed-functions.md)
* [Použití procesoru Change feed](change-feed-processor.md)
