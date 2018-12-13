---
title: Omezení služby pro vrstvy a skladové položky – Azure Search
description: Limity pro plánování kapacity a maximálního počtu žádostí a odpovědí pro službu Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 98902c7a27d769b59b20d4560b2cda21bfcff6c6
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310208"
---
# <a name="service-limits-in-azure-search"></a>Omezení služby Azure Search
Maximální omezuje na úložiště, úlohy a množství indexů, dokumenty, a dalších objektů závisí na tom, zda jste [zřízení Azure Search](search-create-service-portal.md) na **Free**, **základní**, nebo **Standardní** cenové úrovně.

+ **Bezplatné** je víceklientská služba sdílené, který je součástí vašeho předplatného Azure.

+ **Základní** zajišťují vyhrazené výpočetní prostředky pro produkční úlohy v menším měřítku.

+ **Standardní** běží na vyhrazených počítačů s další kapacitou úložiště a zpracování na všech úrovních. Standard je k dispozici ve čtyřech úrovních: S1, S2, S3 a S3 HD, High Density.

  S3 High Density (S3 HD, High Density) je navržené pro konkrétní úlohy: [víceklientská](search-modeling-multitenant-saas-applications.md) a velké množství malých indexy (1 000 dokumentů na index, tři tisíc indexů na službu). Tato úroveň neposkytuje [indexer funkce](search-indexer-overview.md). Na S3 HD, High Density musí využívat ingestování přístupu nabízených oznámení pomocí volání rozhraní API k odeslání dat ze zdroje do indexu. 

> [!NOTE]
> Služba se zřizuje na konkrétní úroveň. Přechod k získání kapacity vrstvy zahrnuje zřizování novou službu (neexistuje žádný místní upgrade). Další informace najdete v tématu [zvolte skladovou Položku nebo úroveň](search-sku-tier.md). Další informace o nastavení kapacity v rámci služby jste zřídili, naleznete v tématu [úrovně škálování prostředků pro dotazy a indexování úloh](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Omezení předplatného
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Omezení úložiště
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Omezení indexu

| Prostředek | Free | Základní&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD, HIGH DENSITY |
| -------- | ---- | ------------------- | --- | --- | --- | --- |
| Maximální počet indexů |3 |5 nebo 15 |50 |200 |200 |1 000 na oddíl nebo 3 000 na službu |
| Maximální počet polí na indexu |1000 |100 |1000 |1000 |1000 |1000 |
| Maximální [moduly pro návrhy](https://docs.microsoft.com/rest/api/searchservice/suggesters) každý index |1 |1 |1 |1 |1 |1 |
| Maximální [profily skórování](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) každý index |100 |100 |100 |100 |100 |100 |
| Maximální funkce podle profilů. |8 |8 |8 |8 |8 |8 |

<sup>1</sup> základní služby vytvořené po pozdní 2017 mají vyšší limit 15 indexy, zdroje dat a indexerů. Services vytvořili dříve k dispozici 5. Úroveň Basic je jenom SKU s nižší maximálně 100 polí v indexu.

<a name="document-limits"></a>

## <a name="document-limits"></a>Limity pro dokumenty 

Od října 2018 je již neexistují žádné limity pro dokumenty pro všechny nové služby vytvoří v jakékoli fakturovatelné úrovni (Basic, S1, S2, S3, S3 HD, High Density) v libovolné oblasti. Když jste využili většinu oblastí neomezený počet dokumentu se počítá od listopadu/prosince 2017, bylo pět oblastí, které se uplatňují omezení dokument i nadále. V závislosti na tom, kdy a kde vytvořit vyhledávací službu může být spuštěna služba, která je stále v souladu s limity pro dokumenty.

Pokud chcete zjistit, zda má vaše služba limity pro dokumenty, zkontrolujte dlaždice využití na stránce Přehled vaší služby. Zdokumentujte počty jsou neomezeného nebo předmětu k omezení založené na úrovni.

  ![Dlaždice využití](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-previously-having-document-limits"></a>Oblasti dříve omezení dokumentů vztahuje

Portál bude informovat limit dokumentů, vaše služba byla vytvořena buď před koncem 2017, zda byl vytvořen v datovém centru pomocí malých kapacity clusterů pro hostování služby Azure Search:

+ Austrálie – východ
+ Východní Asie
+ Střed Indie
+ Japonsko – západ
+ Západní střed USA

Pro služby v souladu s limity pro dokumenty platí následující omezení maximální:

|  Free | Basic | S1 | S2 | S3 | S3&nbsp;HD, HIGH DENSITY |
|-------|-------|----|----|----|-------|
|  10 000 |1 milion |15 milionů na oddíl nebo 180 milionů na službu |60 milionů na oddíl nebo 720 milionů na službu |120 milionů na oddíl nebo 1,4 miliard na službu |1 milion na index nebo 200 milionů na oddíl |

Pokud má služba omezení, které jste se blokovat, vytvořte novou službu a pak znovu publikovat veškerý obsah na tuto službu. Neexistuje žádný mechanismus pro vaši službu na nový hardware na pozadí bez problémů neukončil.

> [!Note] 
> Pro služby S3 High Density vytvořené po pozdní 2017 se odebrala ale dokumentu 1 milion na index limit zůstane 200 milionů dokumentů na oddíl.


### <a name="document-size-limits-per-api-call"></a>Omezení velikosti dokumentu za volání rozhraní API

Dokument maximální velikost při volání rozhraní API indexu je přibližně 16 MB.

Velikost dokumentu je ve skutečnosti nijak velikost textu požadavku rozhraní API indexu. Protože Index rozhraní API můžete předat dávce více dokumentů současně, omezení velikosti realisticky závisí na počtu dokumentů v dávce. Dokument maximální velikost pro službu batch pomocí jednoho dokumentu, je 16 MB JSON.

Omezit velikost dokumentu, nezapomeňte vyloučit bez možnosti dotazování dat z požadavku. Obrázky a další binární data nejsou přímo zadávat dotazy a neměl by být uložen v indexu. K integraci dat bez možnosti dotazování na výsledky hledání, definujte neprohledávatelném pole uchovávající odkaz URL na prostředek.

## <a name="indexer-limits"></a>Omezení indexeru

Základní služby vytvořené po pozdní 2017 mají vyšší limit 15 indexy, zdroje dat, dovednosti a indexery.

Náročná operace, jako je například Analýza obrázků v indexování objektů blob v Azure nebo zpracování přirozeného jazyka v kognitivního vyhledávání mít kratší maximální dobu spuštění, aby ostatní úlohy indexování můžete shromáždit. Pokud úloha indexování nelze dokončit v povoleném čase maximální, ji zkuste spustit podle plánu. Plánovač uchovává informace o stavu indexování. Pokud z nějakého důvodu dojde k plánované úlohy indexování, indexeru můžete pokračovat tam, kde poslední skončila v příštím plánovaném spuštění.

| Prostředek | Bezplatné&nbsp;<sup>1</sup> | Základní&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|
| -------- | ----------------- | ----------------- | --- | --- | --- | --- |
| Maximální počet indexerů |3 |5 nebo 15|50 |200 |200 |neuvedeno |
| Maximální počet zdrojů dat |3 |5 nebo 15 |50 |200 |200 |neuvedeno |
| Maximální dovednosti <sup>4</sup> |3 |5 nebo 15 |50 |200 |200 |neuvedeno |
| Maximální indexování zatížení na vyvolání |10 000 dokumentů |Omezeno pouze maximální počet dokumentů |Omezeno pouze maximální počet dokumentů |Omezeno pouze maximální počet dokumentů |Omezeno pouze maximální počet dokumentů |neuvedeno |
| Maximální doba spuštění <sup>5</sup> | 1 až 3 minut |24 hodin |24 hodin |24 hodin |24 hodin |neuvedeno  |
| Maximální doba běhu pro dovednosti kognitivního vyhledávání nebo objekt blob indexování s analýzou image <sup>5</sup> | 3 až 10 minut |2 hodiny |2 hodiny |2 hodiny |2 hodiny |neuvedeno  |
| Indexování objektů blob: blob maximální velikost, MB |16 |16 |128 |256 |256 |neuvedeno  |
| Indexování objektů blob: maximální počet znaků obsahu extrahují z objektu blob |32,000 |64,000 |4 miliony |4 miliony |4 miliony |neuvedeno |

<sup>1</sup> bezplatné služby mají maximální doba spuštění indexeru 3 minut pro objekt blob zdroje a pro všechny ostatní zdroje dat 1 minuta.

<sup>2</sup> základní služby vytvořené po pozdní 2017 mají vyšší limit 15 indexy, zdroje dat a indexerů. Services vytvořili dříve k dispozici 5.

<sup>3</sup> S3 HD, High Density služby nezahrnují nepodporuje indexery.

<sup>4</sup> maximálně 30 dovednosti na dovednosti.

<sup>5</sup> úlohy kognitivního vyhledávání a analýza obrázků v indexování objektů blob v Azure mají kratší dobu spuštění než indexování běžný text. Analýza a zpracování přirozeného jazyka obrázků jsou náročné na výpočty a využívat neúměrné množství dostupné výpočetní výkon. Doba spuštění se zkrátilo na poskytnout příležitosti ke spuštění dalších úloh ve frontě.  

## <a name="queries-per-second-qps"></a>Dotazů za sekundu (QPS)

Každý zákazník musí být nezávisle na sobě vyvinuté QPS odhady. Velikost indexu a složitosti, dotaz velikost a složitost a objem provozu jsou primární faktory QPS. Neexistuje žádný způsob, jak nabídnout smysluplné odhady při tyto faktory neznámé.

Jsou odhady více předvídatelný při výpočtu na službách, které běží na vyhrazených prostředcích (úrovně Basic a Standard). Chcete-li odhadnout QPS další úzce vzhledem k tomu, že budete mít kontrolu nad více parametrů. Pokyny k odhadu přístup, najdete v části [Azure Search výkon a optimalizace](search-performance-optimization.md).

## <a name="api-request-limits"></a>Omezení žádostí o rozhraní API
* Maximálně 16 MB na požadavek <sup>1</sup>
* Maximální délka adresy URL velikosti 8 KB
* Maximální počet 1 000 dokumentů na dávku index odešle, sloučení nebo odstraní
* Maximální 32 pole v klauzuli $orderby
* Maximální hledání termín velikost je 32 766 bajtů (32 KB minus 2 bajtů) textu kódování UTF-8

<sup>1</sup> v Azure Search text požadavku je v souladu s maximální limit je 16 MB, nastavení praktické omezení na obsahu jednotlivá pole nebo kolekce, které jinak nejsou omezeny teoretické omezení (viz [nepodporuje data typy](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) Další informace o omezení a pole složení).

## <a name="api-response-limits"></a>Omezí odpověď rozhraní API
* Maximální vrácených na stránce výsledků hledání 1 000 dokumentů
* Maximální 100 návrhy vrátí na žádost návrh rozhraní API

## <a name="api-key-limits"></a>Omezení klíč rozhraní API
Klíče rozhraní API se používají pro ověřování služby. Existují dva typy. Klíče správce jsou určené v hlavičce žádosti a udělit přístup k úplné čtení a zápis do služby. Klíče dotazu jsou jen pro čtení, zadaná na adresu URL a obvykle se distribuují klientským aplikacím.

* Maximálně 2 klíče správce na službu
* Maximálně 50 klíče dotaz na službu
