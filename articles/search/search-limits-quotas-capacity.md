---
title: Omezení služby pro úrovně a SKU
titleSuffix: Azure Cognitive Search
description: Omezení služeb pro plánování kapacity a maximální limity pro žádosti a odpovědi pro Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: b54905e201ee7a6dbf4c6837960a6e0b63057ea9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80549054"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Limity služby ve službě Azure Cognitive Search

Maximální limity pro úložiště, úlohy a množství indexů a dalších objektů závisí na tom, jestli [zřídíte Azure kognitivní hledání](search-create-service-portal.md) na cenových úrovních **Free**, **Basic**, **Standard**nebo **Storage s optimalizovaným úložištěm** .

+ **Zdarma** je sdílená služba s více klienty, která se dodává s vaším předplatným Azure. Požadavky na indexování a dotazy se spouštějí na replikách a oddílech, které používají jiní klienti.

+ **Základní** poskytuje vyhrazené výpočetní prostředky pro produkční úlohy v menším měřítku, ale sdílí určitou síťovou infrastrukturu s ostatními klienty.

+ **Standardní** běhy na vyhrazených počítačích s větší kapacitou úložiště a zpracování na všech úrovních. Úroveň Standard se nachází na čtyřech úrovních: S1, S2, S3 a S3 HD.

+ **Optimalizované úložiště** se spouští na vyhrazených počítačích s větším množstvím úložiště, šířkou pásma úložiště a pamětí než **standardem**. Optimalizované úložiště se nachází na dvou úrovních: L1 a L2.

> [!NOTE]
> Od 1. července jsou všechny úrovně všeobecně dostupné, včetně úrovně optimalizované pro úložiště. Všechny ceny najdete na stránce s [podrobnostmi o cenách](https://azure.microsoft.com/pricing/details/search/) .

  Vysoká hustota S3 (S3 HD) je navržena pro konkrétní úlohy: [víceklientské](search-modeling-multitenant-saas-applications.md) a velké množství malých indexů (3000 indexů na službu). Tato vrstva neposkytuje [funkci indexeru](search-indexer-overview.md). V případě S3 HD musí ingestování dat využívat nabízený přístup pomocí volání rozhraní API k odesílání dat ze zdroje do indexu. 

> [!NOTE]
> Služba se zřizuje v určité úrovni. Přechod vrstev na získání kapacity zahrnuje zřízení nové služby (k dispozici není žádný místní upgrade). Další informace najdete v tématu [Výběr SKU nebo úrovně](search-sku-tier.md). Další informace o tom, jak upravit kapacitu v rámci služby, kterou jste už zřídili, najdete v tématu věnovaném [škálování úrovní prostředků pro úlohy dotazů a indexování](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Omezení předplatného
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Omezení úložiště
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Omezení indexu

| Prostředek | Free | Základní&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | Paměť |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Maximální počet indexů |3 |5 nebo 15 |50 |200 |200 |1 000 na oddíl nebo 3 000 na službu |10 |10 |
| Maximální počet jednoduchých polí na index |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Maximální složitá pole kolekce na index |40 |40 |40 |40 |40 |40 |40 |40 |
| Maximální počet prvků napříč všemi komplexními kolekcemi na dokument&nbsp;<sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Maximální hloubka složitých polí |10 |10 |10 |10 |10 |10 |10 |10 |
| Maximální počet modulu pro [návrhy](https://docs.microsoft.com/rest/api/searchservice/suggesters) na index |1 |1 |1 |1 |1 |1 |1 |1 |
| Maximální počet [profilů vyhodnocování](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) na index |100 |100 |100 |100 |100 |100 |100 |100 |
| Maximální počet funkcí na profil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> základní služby vytvořené před prosince 2017 mají nižší limity (5 namísto 15) na indexech. Úroveň Basic je jediná SKU s nižším limitem 100 polí na jeden index.

<sup>2</sup> má velký počet prvků v komplexních kolekcích pro každý dokument v současné době způsobuje vysoké využití úložiště. Jedná se o známý problém. Do té doby je limit 3000 bezpečný horní mez pro všechny úrovně služeb. Tento limit se vynutil jenom pro operace indexování, které využívají nejstarší dostupnou verzi rozhraní API GA (GA), která podporuje pole komplexního typu (`2019-05-06`) a vyšší. Aby nedošlo k přerušení klientů, kteří používají dřívější verze Preview rozhraní API (které podporují pole komplexních typů), nebudeme toto omezení vynucovat pro operace indexování, které používají tyto verze rozhraní API Preview. Verze Preview rozhraní API není určena k použití v produkčních scénářích a důrazně doporučujeme zákazníkům přejít na nejnovější verzi rozhraní API GA.

> [!NOTE]
> I když je maximální kapacita jednoho indexu obvykle omezená o dostupné úložiště, existují maximální horní meze celkového počtu dokumentů, které mohou být uloženy v jednom indexu. Toto omezení je přibližně 24 000 000 000 dokumentů na jeden index pro služby Search úrovně Basic, S1, S2 a S3 a 2 000 000 000 dokumentů na index pro služby S3HD Search. Každý prvek komplexních kolekcí se počítá jako samostatné dokumenty pro účely těchto omezení.

<a name="document-limits"></a>

## <a name="document-limits"></a>Omezení dokumentů 

Od října 2018 již neplatí žádné omezení počtu dokumentů pro žádnou novou službu vytvořenou v jakékoli Fakturovatelné úrovni (Basic, S1, S2, S3, S3 HD) v jakékoli oblasti. Starším službám vytvořeným před říjnu 2018 může i nadále platit omezení počtu dokumentů.

Pokud chcete zjistit, jestli vaše služba má omezení dokumentů, použijte [REST API získat statistiku služby](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics). Omezení dokumentů se odrazí v odpovědi, `null` což znamená, že nejsou nijak omezeny žádné limity.

### <a name="document-size-limits-per-api-call"></a>Omezení velikosti dokumentů na volání rozhraní API

Maximální velikost dokumentu při volání rozhraní API indexu je přibližně 16 megabajtů.

Velikost dokumentu je ve skutečnosti omezení velikosti textu požadavku rozhraní API pro index. Vzhledem k tomu, že do rozhraní API indexu můžete najednou předat dávku více dokumentů, omezení velikosti realisticky závisí na tom, kolik dokumentů je v dávce. Pro dávku s jedním dokumentem je maximální velikost dokumentu 16 MB formátu JSON.

Aby se zachovala velikost dokumentu, nezapomeňte z požadavku vyloučit data, která nejsou Queryable. Image a další binární data se přímo Queryable a neměly by se ukládat do indexu. Pro integraci nequeryablech dat do výsledků hledání Definujte pole bez možností vyhledávání, které ukládá odkaz na adresu URL prostředku.

## <a name="indexer-limits"></a>Omezení indexeru

Pro zajištění rovnováhy a stability služby jako celku existovala maximální doba běhu, ale větší sady dat můžou potřebovat více času indexování než maximální povolený počet. Pokud úloha indexování nemůže být dokončena v maximálním povoleném čase, zkuste ji spustit podle plánu. Scheduler sleduje stav indexování. Pokud z nějakého důvodu dojde k přerušení plánované úlohy indexování, může indexer vybrat, kde se naposledy nacházelo při příštím naplánovaném spuštění.


| Prostředek | Zdarma&nbsp;<sup>1</sup> | Základní&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |Paměť |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Maximální počet indexerů |3 |5 nebo 15|50 |200 |200 |– |10 |10 |
| Maximální počet zdrojů dat |3 |5 nebo 15 |50 |200 |200 |– |10 |10 |
| Maximální dovednosti <sup>4</sup> |3 |5 nebo 15 |50 |200 |200 |– |10 |10 |
| Maximální zatížení indexování na vyvolání |dokumenty 10 000 |Omezeno pouze na maximum dokumentů |Omezeno pouze na maximum dokumentů |Omezeno pouze na maximum dokumentů |Omezeno pouze na maximum dokumentů |– |Bez omezení |Bez omezení |
| Minimální plán | 5 minut |5 minut |5 minut |5 minut |5 minut |5 minut |5 minut | 5 minut |
| Maximální doba běhu <sup>5</sup> | 1-3 minut |24 hodin |24 hodin |24 hodin |24 hodin |–  |24 hodin |24 hodin |
| Maximální doba spuštění pro rozpoznávání dovednosti nebo indexování objektů BLOB s analýzou obrázku <sup>5</sup> | 3-10 minut |2 hodiny |2 hodiny |2 hodiny |2 hodiny |–  |2 hodiny |2 hodiny |
| Indexer objektů BLOB: maximální velikost objektu blob, MB |16 |16 |128 |256 |256 |–  |256 |256 |
| Indexer objektů BLOB: maximální počet znaků obsahu extrahovaných z objektu BLOB |32 000 |64 000 |4&nbsp;miliony |8&nbsp;milionů |16&nbsp;milionů |– |4&nbsp;miliony |4&nbsp;miliony |

<sup>1</sup> bezplatné služby mají maximální dobu provádění indexeru 3 minuty pro zdroje objektů BLOB a 1 minutu pro všechny ostatní zdroje dat. V případě indexování AI, který volá Cognitive Services, jsou bezplatné služby omezené na 20 bezplatných transakcí za den, kde transakce je definovaná jako dokument, který úspěšně projde kanálem pro obohacení.

<sup>2</sup> základní služby vytvořené před prosince 2017 mají nižší omezení (5 namísto 15) u indexerů, zdrojů dat a dovednosti.

<sup>3</sup> S3 HD Services nezahrnují podporu indexeru.

<sup>4</sup> maximálně 30 dovedností na dovednosti.

<sup>5</sup> zátěžového vyhledávání a analýza obrázků v Azure Blob indexování mají kratší dobu běhu než běžné indexování textu. Analýza obrázků a zpracování přirozeného jazyka jsou výpočty náročné a využívají neúměrné objemy dostupného výpočetního výkonu. Čas spuštění byl snížen, aby bylo možné v frontě poskytnout ostatním úlohám možnost spustit.  

> [!NOTE]
> Jak je uvedeno v [omezeních indexu](#index-limits), indexery taky vyhodnotí horní limit 3000 prvků napříč všemi složitými kolekcemi na dokument počínaje nejnovější verzí rozhraní GA API, která podporuje komplexní`2019-05-06`typy () a vyšší. To znamená, že pokud jste indexer vytvořili s předchozí verzí rozhraní API, nebudete platit od tohoto omezení. Aby se zachovala maximální kompatibilita, indexer, který byl vytvořen s předchozí verzí rozhraní API a který se pak aktualizuje pomocí `2019-05-06` verze rozhraní API nebo novější, bude v omezeních nadále **vyloučený** . Zákazníci by měli mít pozor na nepříznivý dopad na velmi velké komplexní kolekce (jak bylo uvedeno dříve) a důrazně doporučujeme vytvořit nové indexery s nejnovější verzí rozhraní GA API.

## <a name="synonym-limits"></a>Omezení synonym

Maximální povolený počet map synonym se liší podle cenové úrovně. Každé pravidlo může mít až 20 rozšíření, kde rozšíření je ekvivalentní termín. Například dané "Cat", asociace s "Kitty", "Feline" a "Felis" (rod pro kočky) by se znamenaly jako 3 rozšíření.

| Prostředek | Free | Základní | S1 | S2 | S3 | S3-HD |L1 | Paměť |
| -------- | -----|------ |----|----|----|-------|---|----|
| Maximální počet mapování synonym |3 |3|5 |10 |20 |20 | 10 | 10 |
| Maximální počet pravidel na mapování |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>Dotazů za sekundu (QPS)

Odhady QPS musí být vyvíjeny nezávisle na každém zákazníkovi. Velikost indexu a složitost, velikost a složitost dotazů a množství přenosů jsou primárními determinanty QPS. Neexistuje žádný způsob, jak nabízet smysluplné odhady v případě, že tyto faktory nejsou známy.

Odhady jsou předvídatelné při výpočtu na službách, které běží na vyhrazených prostředcích (úrovně Basic a Standard). QPS můžete odhadnout přesněji, protože máte kontrolu nad více parametry. Pokyny pro přístup k odhadu najdete v tématu [výkon a optimalizace pro Azure kognitivní hledání](search-performance-optimization.md).

Pro vrstvy optimalizované pro úložiště byste měli očekávat nižší propustnost dotazů a vyšší latenci než na úrovni Standard.  Metodologie pro odhad výkonu dotazů, které budete mít, je stejná jako u standardních úrovní.

## <a name="data-limits-ai-enrichment"></a>Omezení pro data (rozšíření AI)

[Kanál pro obohacení AI](cognitive-search-concept-intro.md) , který umožňuje volat prostředek analýza textu pro [rozpoznávání entit](cognitive-search-skill-entity-recognition.md), [extrakci klíčových frází](cognitive-search-skill-keyphrases.md), [analýzu míněníí](cognitive-search-skill-sentiment.md), [detekci jazyka](cognitive-search-skill-language-detection.md)a [zjišťování PII](cognitive-search-skill-pii-detection.md) , podléhá omezením dat. Maximální velikost záznamu musí být 50 000 znaků měřených podle [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Pokud potřebujete data před odesláním do nástroje mínění Analyzer rozdělit, použijte [dovednost rozdělení textu](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Omezení omezování

Vyhledávací dotaz a požadavky na indexování jsou omezené, protože systém přistupuje ke špičkové kapacitě. Omezení se u různých rozhraní API chová jinak. Rozhraní API pro dotazy (hledání, návrhy/dokončování) a indexování rozhraní API se dynamicky vycházejí z zatížení služby. Rozhraní API indexu mají omezení statické míry požadavků. 

Omezení požadavků na statickou rychlost pro operace související s indexem:

+ Seznam indexů (GET/Indexes): 5 za sekundu na jednotku vyhledávání
+ Získat index (GET/Indexes/MyIndex): 10 za sekundu na jednotku vyhledávání
+ Vytvořit index (POST/Indexes): 12 za minutu na jednotku vyhledávání
+ Vytvořit nebo aktualizovat index (PUT/Indexes/MyIndex): 6 za sekundu na jednotku vyhledávání
+ Odstranit index (odstranit/Indexes/MyIndex): 12 za minutu na jednotku vyhledávání 

## <a name="api-request-limits"></a>Omezení požadavků na rozhraní API
* Maximálně 16 MB na žádost <sup>1</sup>
* Maximální délka adresy URL pro 8 KB
* Maximum 1000 dokumentů na jednu dávku pro nahrávání, sloučení nebo odstranění indexu
* Maximálně 32 polí v klauzuli $orderby
* Maximální velikost hledaného výrazu je 32 766 bajtů (32 KB minus 2 bajty) textu kódovaného v kódování UTF-8.

<sup>1</sup> v Azure kognitivní hledání, tělo požadavku podléhá hornímu limitu 16 MB a zaznamenání praktického limitu obsahu jednotlivých polí nebo kolekcí, které nejsou jinak omezeny teoretickými limity (Další informace o složení polí a omezeních najdete v tématu [podporované datové typy](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) ).

## <a name="api-response-limits"></a>Omezení odezvy rozhraní API
* Maximální počet 1000 dokumentů vrácených na stránku výsledků hledání
* Maximální počet 100 návrhů vrácených za požadavek rozhraní API pro návrh

## <a name="api-key-limits"></a>Omezení klíčů rozhraní API
Klíče rozhraní API se používají pro ověřování služby. Existují dva typy. Klíče správce jsou zadány v hlavičce požadavku a udělují službě úplný přístup pro čtení i zápis. Klíče dotazů jsou jen pro čtení, zadané na adrese URL a obvykle distribuované pro klientské aplikace.

* Maximálně 2 klíče správce na službu
* Maximálně 50 klíčů dotazů na službu