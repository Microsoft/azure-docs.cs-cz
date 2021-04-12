---
title: Omezení služby pro úrovně a SKU
titleSuffix: Azure Cognitive Search
description: Omezení služeb pro plánování kapacity a maximální limity pro žádosti a odpovědi pro Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 994ed74750d159dfdb83259e9fe921f870ec2241
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99509363"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Limity služby ve službě Azure Cognitive Search

Maximální limity pro úložiště, úlohy a množství indexů a dalších objektů závisí na tom, jestli [zřídíte Azure kognitivní hledání](search-create-service-portal.md) na cenových úrovních **Free**, **Basic**, **Standard** nebo **Storage s optimalizovaným úložištěm** .

+ **Zdarma** je sdílená služba s více klienty, která se dodává s vaším předplatným Azure. 

+ **Základní** poskytuje vyhrazené výpočetní prostředky pro produkční úlohy v menším měřítku, ale sdílí určitou síťovou infrastrukturu s ostatními klienty.

+ **Standardní** běhy na vyhrazených počítačích s větší kapacitou úložiště a zpracování na všech úrovních. Úroveň Standard se nachází na čtyřech úrovních: S1, S2, S3 a S3 HD. Vysoká hustota S3 (S3 HD) je navržena pro [více tenantů](search-modeling-multitenant-saas-applications.md) a velké množství malých indexů (3000 indexů na službu). S3 HD neposkytuje [funkci indexeru](search-indexer-overview.md) a ingestování dat musí využívat rozhraní API, která zadávají data ze zdroje do indexu. 

+ **Optimalizované úložiště** se spouští na vyhrazených počítačích s větším množstvím úložiště, šířkou pásma úložiště a pamětí než **standardem**. Tato vrstva cílí na velké a pomalé změny indexů. Optimalizované úložiště se nachází na dvou úrovních: L1 a L2.

## <a name="subscription-limits"></a>Omezení předplatného
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Omezení úložiště
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Omezení indexu

| Prostředek | Free | Základní &nbsp; <sup>1</sup>  | S1 | S2 | S3 | S3 &nbsp; HD | L1 | Paměť |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Maximální počet indexů |3 |5 nebo 15 |50 |200 |200 |1 000 na oddíl nebo 3 000 na službu |10 |10 |
| Maximální počet jednoduchých polí na index |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Maximální složitá pole kolekce na index |40 |40 |40 |40 |40 |40 |40 |40 |
| Maximální počet prvků napříč všemi komplexními kolekcemi na dokument &nbsp; <sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Maximální hloubka složitých polí |10 |10 |10 |10 |10 |10 |10 |10 |
| Maximální počet modulu pro [návrhy](/rest/api/searchservice/suggesters) na index |1 |1 |1 |1 |1 |1 |1 |1 |
| Maximální počet [profilů vyhodnocování](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) na index |100 |100 |100 |100 |100 |100 |100 |100 |
| Maximální počet funkcí na profil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> základní služby vytvořené před prosince 2017 mají nižší limity (5 namísto 15) na indexech. Úroveň Basic je jediná SKU s nižším limitem 100 polí na jeden index.

<sup>2</sup> pro prvky existuje horní limit, protože větší počet výrazně zvyšuje kapacitu úložiště potřebnou pro váš index. Prvek komplexní kolekce je definován jako člen této kolekce. Předpokládejme například, že máte [hotelový dokument s složitou kolekcí místností](search-howto-complex-data-types.md#indexing-complex-types), kde každá místnost v kolekci místností je považována za prvek. Při indexování může modul indexování bezpečně zpracovávat maximálně 3000 prvků v celém dokumentu. [Toto omezení](search-api-migration.md#upgrade-to-2019-05-06) bylo zavedeno v `api-version=2019-05-06` a vztahuje se pouze na komplexní kolekce, nikoli na kolekce řetězců ani na složitá pole.

<a name="document-limits"></a>

## <a name="document-limits"></a>Omezení dokumentů 

Od října 2018 již neplatí žádné omezení počtu dokumentů pro žádnou novou službu vytvořenou v jakékoli Fakturovatelné úrovni (Basic, S1, S2, S3, S3 HD) v jakékoli oblasti. Starším službám vytvořeným před říjnu 2018 může i nadále platit omezení počtu dokumentů.

Pokud chcete zjistit, jestli vaše služba má omezení dokumentů, použijte [REST API získat statistiku služby](/rest/api/searchservice/get-service-statistics). Omezení dokumentů se odrazí v odpovědi, `null` což znamená, že nejsou nijak omezeny žádné limity.

> [!NOTE]
> I když služba neukládá žádná omezení, má horizontálních oddílů limit přibližně 24 000 000 000 dokumentů na index v rámci služeb vyhledávání Basic, S1, S2 a S3. V případě S3 HD je limit horizontálních oddílů 2 000 000 000 dokumentů na jeden index. Každý prvek komplexní kolekce se počítá jako samostatný dokument z hlediska horizontálních oddílů omezení.

### <a name="document-size-limits-per-api-call"></a>Omezení velikosti dokumentů na volání rozhraní API

Maximální velikost dokumentu při volání rozhraní API indexu je přibližně 16 megabajtů.

Velikost dokumentu je ve skutečnosti omezení velikosti textu požadavku rozhraní API pro index. Vzhledem k tomu, že do rozhraní API indexu můžete najednou předat dávku více dokumentů, omezení velikosti realisticky závisí na tom, kolik dokumentů je v dávce. Pro dávku s jedním dokumentem je maximální velikost dokumentu 16 MB formátu JSON.

Při odhadování velikosti dokumentu nezapomeňte zvážit pouze ta pole, která může služba vyhledávání spotřebovat. Data binárního souboru nebo obrázku ve zdrojových dokumentech by měla být vynechána při výpočtech.  

## <a name="indexer-limits"></a>Omezení indexeru

Pro zajištění rovnováhy a stability služby jako celku existovala maximální doba běhu, ale větší sady dat můžou potřebovat více času indexování než maximální povolený počet. Pokud úloha indexování nemůže být dokončena v maximálním povoleném čase, zkuste ji spustit podle plánu. Scheduler sleduje stav indexování. Pokud z nějakého důvodu dojde k přerušení plánované úlohy indexování, může indexer vybrat, kde se naposledy nacházelo při příštím naplánovaném spuštění.


| Prostředek | Zdarma &nbsp; <sup>1</sup> | Základní &nbsp; <sup>2</sup>| S1 | S2 | S3 | S3 &nbsp; HD &nbsp; <sup>3</sup>|L1 |Paměť |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Maximální počet indexerů |3 |5 nebo 15|50 |200 |200 |– |10 |10 |
| Maximální počet zdrojů dat |3 |5 nebo 15 |50 |200 |200 |– |10 |10 |
| Maximální dovednosti <sup>4</sup> |3 |5 nebo 15 |50 |200 |200 |– |10 |10 |
| Maximální zatížení indexování na vyvolání |dokumenty 10 000 |Omezeno pouze na maximum dokumentů |Omezeno pouze na maximum dokumentů |Omezeno pouze na maximum dokumentů |Omezeno pouze na maximum dokumentů |– |Bez omezení |Bez omezení |
| Minimální plán | 5 minut |5 minut |5 minut |5 minut |5 minut |5 minut |5 minut | 5 minut |
| Maximální doba běhu| 1-3 minut |24 hodin |24 hodin |24 hodin |24 hodin |–  |24 hodin |24 hodin |
| Maximální doba běhu indexerů s dovednosti <sup>5</sup> | 3-10 minut |2 hodiny |2 hodiny |2 hodiny |2 hodiny |–  |2 hodiny |2 hodiny |
| Indexer objektů BLOB: maximální velikost objektu blob, MB |16 |16 |128 |256 |256 |–  |256 |256 |
| Indexer objektů BLOB: maximální počet znaků obsahu extrahovaných z objektu BLOB |32 000 |64 000 |4 &nbsp; miliony |8 &nbsp; milionů |16 &nbsp; milionů |– |4 &nbsp; miliony |4 &nbsp; miliony |

<sup>1</sup> bezplatné služby mají maximální dobu provádění indexeru 3 minuty pro zdroje objektů BLOB a 1 minutu pro všechny ostatní zdroje dat. V případě indexování AI, který volá Cognitive Services, jsou bezplatné služby omezené na 20 bezplatných transakcí za den, kde transakce je definovaná jako dokument, který úspěšně projde kanálem pro obohacení.

<sup>2</sup> základní služby vytvořené před prosince 2017 mají nižší omezení (5 namísto 15) u indexerů, zdrojů dat a dovednosti.

<sup>3</sup> S3 HD Services nezahrnují podporu indexeru.

<sup>4</sup> maximálně 30 dovedností na dovednosti.

<sup>5</sup> rozšíření AI a analýza obrázků jsou výpočty náročné a využívají neúměrné objemy dostupného výpočetního výkonu. Čas spuštění těchto úloh byl zkrácen, aby bylo možné další úlohy ve frontě spustit více příležitostí.

> [!NOTE]
> Jak je uvedeno v [omezeních indexu](#index-limits), indexery taky vyhodnotí horní limit 3000 prvků napříč všemi složitými kolekcemi na dokument počínaje nejnovější verzí rozhraní GA API, která podporuje komplexní typy ( `2019-05-06` ) a vyšší. To znamená, že pokud jste indexer vytvořili s předchozí verzí rozhraní API, nebudete platit od tohoto omezení. Aby se zachovala maximální kompatibilita, indexer, který byl vytvořen s předchozí verzí rozhraní API a který se pak aktualizuje pomocí verze rozhraní API `2019-05-06` nebo novější, bude v omezeních nadále **vyloučený** . Zákazníci by měli mít pozor na nepříznivý dopad na velmi velké komplexní kolekce (jak bylo uvedeno dříve) a důrazně doporučujeme vytvořit nové indexery s nejnovější verzí rozhraní GA API.

## <a name="shared-private-link-resource-limits"></a>Omezení sdíleného prostředku privátního propojení

Indexery mají přístup k dalším prostředkům Azure [prostřednictvím privátních koncových bodů](search-indexer-howto-access-private.md) spravovaných prostřednictvím [rozhraní API prostředků sdíleného privátního propojení](/rest/api/searchmanagement/sharedprivatelinkresources). Tato část popisuje omezení související s touto funkcí.

| Prostředek | Free | Základní | S1 | S2 | S3 | S3 HD | L1 | Paměť
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Podpora indexeru privátního koncového bodu | No | Yes | Yes | Yes | Yes | No | Yes | Yes |
| Podpora privátního koncového bodu pro indexery s dovednosti<sup>1</sup> | No | No | No | Yes | Yes | No | Yes | Yes |
| Maximální počet privátních koncových bodů | – | 10 nebo 30 | 100 | 400 | 400 | – | 20 | 20 |
| Maximální počet různých typů prostředků<sup>2</sup> | Není k dispozici | 4 | 7 | 15 | 15 | Není k dispozici | 4 | 4 |

<sup>1</sup> obohacení a analýza obrázků AI jsou výpočty náročné a využívají neúměrné objemy dostupného výpočetního výkonu. Z tohoto důvodu jsou privátní připojení na nižších úrovních zakázaná, aby se předešlo nepříznivému dopadu na výkon a stabilitu vyhledávací služby.

<sup>2</sup> počet různých typů prostředků se vypočítává jako počet jedinečných `groupId` hodnot, které se používají ve všech sdílených prostředcích privátního propojení pro danou vyhledávací službu bez ohledu na stav prostředku.

## <a name="synonym-limits"></a>Omezení synonym

Maximální počet mapování synonym se liší podle úrovně. Každé pravidlo může mít až 20 rozšíření, kde rozšíření je ekvivalentní termín. Například dané "Cat", asociace s "Kitty", "Feline" a "Felis" (rod pro kočky) by se znamenaly jako 3 rozšíření.

| Prostředek | Free | Základní | S1 | S2 | S3 | S3-HD |L1 | Paměť |
| -------- | -----|------ |----|----|----|-------|---|----|
| Maximální počet mapování synonym |3 |3|5 |10 |20 |20 | 10 | 10 |
| Maximální počet pravidel na mapování |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="data-limits-ai-enrichment"></a>Omezení pro data (rozšíření AI)

[Kanál pro obohacení AI](cognitive-search-concept-intro.md) , který umožňuje volat prostředek analýza textu pro [rozpoznávání entit](cognitive-search-skill-entity-recognition.md), [extrakci klíčových frází](cognitive-search-skill-keyphrases.md), [analýzu míněníí](cognitive-search-skill-sentiment.md)a [rozpoznávání jazyka](cognitive-search-skill-language-detection.md)a [zjišťování osobních údajů](cognitive-search-skill-pii-detection.md) podléhá omezením dat. Maximální velikost záznamu musí být 50 000 znaků měřených podle [`String.Length`](/dotnet/api/system.string.length) . Pokud potřebujete data před odesláním do nástroje mínění Analyzer rozdělit, použijte [dovednost rozdělení textu](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Omezení omezování

Požadavky rozhraní API jsou omezené, protože systém přistupuje ke špičkové kapacitě. Omezení se u různých rozhraní API chová jinak. Rozhraní API pro dotazy (hledání, návrhy/dokončování) a indexování rozhraní API se dynamicky vycházejí z zatížení služby. Rozhraní API indexů a Operational Service API mají omezení statické míry požadavků. 

Omezení požadavků na statickou rychlost pro operace související s indexem:

+ Seznam indexů (GET/Indexes): 3 za sekundu na jednotku vyhledávání
+ Získat index (GET/Indexes/MyIndex): 10 za sekundu na jednotku vyhledávání
+ Vytvořit index (POST/Indexes): 12 za minutu na jednotku vyhledávání
+ Vytvořit nebo aktualizovat index (PUT/Indexes/MyIndex): 6 za sekundu na jednotku vyhledávání
+ Odstranit index (odstranit/Indexes/MyIndex): 12 za minutu na jednotku vyhledávání 

Omezení požadavků na statickou rychlost pro operace související se službou:

+ Statistika služby (GET/servicestats): 4 za sekundu na jednotku vyhledávání

## <a name="api-request-limits"></a>Omezení požadavků na rozhraní API
* Maximálně 16 MB na žádost <sup>1</sup>
* Maximální délka adresy URL pro 8 KB
* Maximum 1000 dokumentů na jednu dávku pro nahrávání, sloučení nebo odstranění indexu
* Maximálně 32 polí v klauzuli $orderby
* Maximální velikost hledaného výrazu je 32 766 bajtů (32 KB minus 2 bajty) textu kódovaného v kódování UTF-8.

<sup>1</sup> v Azure kognitivní hledání, tělo požadavku podléhá hornímu limitu 16 MB a zaznamenání praktického limitu obsahu jednotlivých polí nebo kolekcí, které nejsou jinak omezeny teoretickými limity (Další informace o složení polí a omezeních najdete v tématu [podporované datové typy](/rest/api/searchservice/supported-data-types) ).

## <a name="api-response-limits"></a>Omezení odezvy rozhraní API
* Maximální počet 1000 dokumentů vrácených na stránku výsledků hledání
* Maximální počet 100 návrhů vrácených za požadavek rozhraní API pro návrh

## <a name="api-key-limits"></a>Omezení klíčů rozhraní API
Klíče rozhraní API se používají pro ověřování služby. Existují dva typy. Klíče správce jsou zadány v hlavičce požadavku a udělují službě úplný přístup pro čtení i zápis. Klíče dotazů jsou jen pro čtení, zadané na adrese URL a obvykle distribuované pro klientské aplikace.

* Maximálně 2 klíče správce na službu
* Maximálně 50 klíčů dotazů na službu
