---
title: Práce s změnu podpora kanálu ve službě Azure Cosmos DB
description: Pomocí podpory informačního kanálu změn služby Azure Cosmos DB sledovat změny v dokumentech a provádět zpracování jako aktivační události na základě událostí a průběžná mezipaměti a analytické systémy.
author: rafats
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rafats
ms.openlocfilehash: dfb0048b29c5d63a2eee4c239e17ef222aad331e
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52837456"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Změna informačního kanálu ve službě Azure Cosmos DB

Podpora ve službě Azure Cosmos DB funguje kanálu změn prostřednictvím naslouchání kontejneru Azure Cosmos DB k nějakým změnám. Potom vypíše seřazený seznam dokumentů, které byly změněny v pořadí, ve kterém byly změněny. Změny jsou trvalé, dají se zpracovat asynchronně a přírůstkově a výstup se dá distribuovat jednomu nebo více příjemcům k paralelnímu zpracování. 

Azure Cosmos DB je velmi vhodná pro Internet věcí, hraní her, maloobchod a provozní protokolování aplikací. Běžný vzor návrhu v těchto aplikacích je použít změny v datech k aktivaci dalších akcí. Příklady dalších akcí:

* Při vložení nebo aktualizaci položky, která aktivuje upozornění nebo volání rozhraní API.
* V reálném čase pro IoT a analýzy v reálném čase na provozních dat pro zpracování datových proudů.
* Přesun dat další synchronizaci s mezipaměti nebo vyhledávacího webu nebo datového skladu nebo archivaci dat do studeného úložiště.

Změna informačního kanálu ve službě Azure Cosmos DB umožňuje vytvářet efektivní a škálovatelné řešení pro každou z těchto vzorců, jak je znázorněno na následujícím obrázku:

![Použití změn databáze Azure Cosmos DB informační kanál k založený na událostech výpočetní scénáře a analýzy v reálném čase power](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>Podporované klientské sady SDK a rozhraní API

Tato funkce je aktuálně podporuje následující klientské sady SDK a rozhraní API služby Azure Cosmos DB.

| **Ovladače klienta** | **Azure CLI** | **ROZHRANÍ SQL API** | **Rozhraní Cassandra API** | **Rozhraní MongoDB API** | **Gremlin API**|**Rozhraní Table API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Není k dispozici | Ano | Ne | Ne | Ano | Ne |
|Java|Není k dispozici|Ano|Ne|Ne|Ano|Ne|
|Python|Není k dispozici|Ano|Ne|Ne|Ano|Ne|
|Uzel/JS|Není k dispozici|Ano|Ne|Ne|Ano|Ne|

## <a name="change-feed-and-different-operations"></a>Kanál změn a různé operace

V současné době se zobrazí všechny operace v kanálu změn. Funkce, kde můžete řídit změny informační kanál pro určité operace, například jenom aktualizace a vkládání není ještě není k dispozici. "Měkké značky" můžete přidat v položce pro aktualizace a filtr na základě při zpracování položek v kanálu změn. Kanál změn není aktuálně protokolu odstraní. Podobně jako v předchozím příkladu můžete přidat softwarové značky u položek, které jsou mazány, například můžete přidat atribut v položce nazývá "odstraněné" a nastavte na hodnotu "true" a nastavte hodnotu TTL na položce, takže se dají automaticky odstranit. Si můžete přečíst, že změna položky informačního kanálu pro historické položky, například, kteří byli přidáni před pěti lety. Pokud není položka smazána si můžete přečíst tato změna kanálu až na hodnotu původ vašeho kontejneru.

### <a name="sort-order-of-items-in-change-feed"></a>Pořadí položek v kanálu změn řazení

Změna položky informačního kanálu dodávají v pořadí jejich čas změny. Toto pořadí řazení je zaručeno, že každý klíč logického oddílu.

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Kanál v Azure Cosmos účty ve více oblastech změn

V účtu Azure Cosmos ve více oblastech Pokud oblast zápisu převezme služby při selhání, budou fungovat kanálu změn napříč operace ruční převzetí služeb při selhání a bude souvislé.

### <a name="change-feed-and-time-to-live-ttl"></a>Kanál změn a čas to Live (TTL)

Pokud hodnota TTL (Time to Live) vlastnost nastavena na položku na hodnotu -1, kanál změn se uloží navždy. Když se neodstraní data, zůstane v kanálu změn.  

### <a name="change-feed-and-etag-lsn-or-ts"></a>Kanál změn a _etag, _lsn nebo _ts

Formát _etag je interní a neměla by mít závislost, protože se sice dají kdykoli měnit. _ts je úprava nebo časové razítko vytvoření. Můžete použít _ts chronologickém porovnání. _lsn je id batch přidaný pro pouze; kanálu změn představuje id transakce. Stejné _lsn může mít mnoho položek. Značka ETag na FeedResponse se liší od _etag, který se zobrazí v položce. _etag je interní identifikátor a používá se pro souběžnost ovládací prvek informuje o verzi položek, že značka ETag se používá pro pořadí úloh informačního kanálu.

## <a name="change-feed-use-cases-and-scenarios"></a>Scénáře a případy použití kanálu změn

Kanál změn umožňuje efektivní zpracování velkých datových sad s velký počet zápisů. Kanál změn také nabízí alternativu k dotazování celou datovou sadu k identifikaci, co se změnilo.

### <a name="use-cases"></a>Případy použití

Například díky kanálu změn můžete provádět následující úlohy efektivně:

* Aktualizovat mezipaměť, aktualizovat index vyhledávání nebo aktualizovat datový sklad s daty uloženými ve službě Azure Cosmos DB.

* Úrovni aplikace využití dat vrstvení a archivace, například "horkých dat." ukládat ve službě Azure Cosmos DB a stáří "studených"data do jiných systémů úložiště, například [Azure Blob Storage](../storage/common/storage-introduction.md).

* Provádění nula dolů čas migrace do jiného účtu Azure Cosmos nebo jiný kontejner Azure Cosmos pomocí klíče jiný logický oddíl.

* Implementace [architektury lambda](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) pomocí služby Azure Cosmos DB, kam Azure Cosmos DB podporuje v reálném čase, batch i query sloužící vrstvy, což umožní architektury lambda s nízkou celkových nákladů na vlastnictví.

* Zobrazí data události ze zařízení, senzorů, infrastruktury a aplikací a ukládat zpracování těchto událostí v reálném čase, například pomocí [Spark](../hdinsight/spark/apache-spark-overview.md).  Následující obrázek ukazuje, jak můžete implementovat architektury lambda pomocí služby Azure Cosmos DB prostřednictvím kanálu změn:

![Azure Cosmos DB na základě lambda kanál pro příjem dat a dotazu](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Scénáře

Tady jsou některé scénáře, které je možné snadno implementovat pomocí kanálu změn:

* V rámci vaší [bez serveru](http://azure.com/serverless) webových nebo mobilních aplikací, můžete sledovat události, například všechny změny do profilu vašich zákazníků, předvolby nebo jejich umístění a aktivovat určité akce, například odesílání nabízených oznámení do zařízení pomocí [Azure Functions](#azure-functions). 

* Pokud používáte službu Azure Cosmos DB k vytvoření hry, je možné, například použití změnit informační kanál k implementaci v reálném čase žebříčky podle skóre, které se od dokončených hry.


## <a name="working-with-change-feed"></a>Práce s kanálu změn

Můžete pracovat pomocí kanálu změn pomocí následujících možností:

* [Změna kanálu s využitím Azure Functions](change-feed-functions.md)
* [Změna kanálu s knihovnou change feed processor](change-feed-processor.md) 

Kanál změn je k dispozici pro každý logický oddíl klíč v kontejneru a jeho mohou být distribuovány na jeden nebo více příjemců pro paralelní zpracování, jak je znázorněno na následujícím obrázku.

![Distribuované zpracování kanálu změn služby Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Funkce kanálu změn

* Kanál změn je povoleno standardně pro všechny účty služby Azure Cosmos.

* Můžete použít vaše [zřízená propustnost](request-units.md) čtení z kanálu změn, stejně jako jakékoli jiné služby Azure Cosmos DB operace v libovolné oblasti přidružené k vaší databázi Azure Cosmos.

* Kanál změn zahrnuje vložení a operace aktualizace provedené u položek v rámci kontejneru. Můžete zaznamenat odstraní nastavením příznaku "obnovitelného odstranění" v rámci vašich položek (například dokumenty) místo odstraní. Alternativně můžete nastavit určité omezené vypršení platnosti pro vaše položky s [interval TTL, ZÍSKÁ možnost](time-to-live.md). Například 24 hodin a použití odstraní hodnota dané vlastnosti pro zachycení. Pomocí tohoto řešení je nutné zpracovat změny v časovém intervalu kratší než interval TTL, ZÍSKÁ dobu vypršení platnosti. 

* Každé změně položky se zobrazí přesně jednou v kanálu změn a klienti musí spravovat logiky vytváření kontrolních bodů. Pokud chcete se vyhnout složitosti správy kontrolních bodů, knihovnou change feed processor poskytuje automatické vytváření kontrolních bodů a "alespoň jednou" sémantiku. Zobrazit [změn pomocí kanálu s knihovnou processor kanálu změn](change-feed-processor.md).

* Protokol změn je součástí pouze posledních změn pro danou položku. Přechodných změn nemusí být k dispozici.

* Kanál změn je řazen pořadím úpravy v rámci každé hodnoty klíče logického oddílu. Není zaručeno pořadí, v rámci hodnoty klíče oddílu.

* Změny můžete synchronizované z libovolný bod za běhu, který je neexistuje žádné pevné data uchovávají, pro které změny jsou k dispozici.

* Změny jsou k dispozici současně pro všechny klíče logický oddíl kontejneru Azure Cosmos. Tato funkce umožňuje, aby změny z velkých kontejnerů ke zpracování paralelní několik příjemců.

* Aplikace můžou požádat o více změn kanálů současně na stejném kontejneru. ChangeFeedOptions.StartTime slouží k poskytování počátečního bodu. Chcete-li například najít odpovídající dané čas token pro pokračování. Token ContinuationToken, je-li zadána, wins přes hodnoty StartTime a StartFromBeginning. Přesnost ChangeFeedOptions.StartTime je přibližně 5 sekund. 

## <a name="next-steps"></a>Další postup

Teď můžete přejít k další informace o změně v následujících článcích:

* [Možnosti ke čtení kanálu změn](read-change-feed.md)
* [Změna kanálu s využitím Azure Functions](change-feed-functions.md)
* [Změna kanálu knihovny procesoru](change-feed-processor.md)
