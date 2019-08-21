---
title: Omezení služby pro vrstvy a SKU – Azure Search
description: Omezení služby pro plánování kapacity a maximální limity pro žádosti a odpovědi pro Azure Search.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: heidist
ms.openlocfilehash: 9ddc7ad8882b30a17be5820116da72c5ab32fad9
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640610"
---
# <a name="service-limits-in-azure-search"></a>Omezení služby v Azure Search
Maximální limity úložiště, zatížení a množství indexů, dokumentů a dalších objektů závisí na tom, jestli jste zřídili [Azure Search](search-create-service-portal.md) na cenových úrovních **Free**, **Basic**, **Standard**nebo **Storage** s optimalizovaným úložištěm.

+ **Zdarma** je sdílená služba s více klienty, která se dodává s vaším předplatným Azure.

+ **Základní** poskytuje vyhrazené výpočetní prostředky pro produkční úlohy v menším měřítku.

+ **Standardní** běhy na vyhrazených počítačích s větší kapacitou úložiště a zpracování na všech úrovních. Úroveň Standard se nachází na čtyřech úrovních: S1, S2, S3 a S3 HD.

+ **Optimalizované úložiště** se spouští na vyhrazených počítačích s větším množstvím úložiště, šířkou pásma úložiště a pamětí než **standardem**. Optimalizované úložiště se nachází na dvou úrovních: L1 a L2

> [!NOTE]
> Od 1. července jsou všechny úrovně všeobecně dostupné, včetně úrovně optimalizované pro úložiště. Všechny ceny najdete na stránce s [podrobnostmi o cenách](https://azure.microsoft.com/pricing/details/search/) .

  Vysoká hustota S3 (S3 HD) je navržena pro konkrétní zatížení: [víceklientské](search-modeling-multitenant-saas-applications.md) a velké množství malých indexů (1 000 000 dokumentů na index, 3000 indexů na službu). Tato vrstva neposkytuje [funkci indexeru](search-indexer-overview.md). V případě S3 HD musí ingestování dat využívat nabízený přístup pomocí volání rozhraní API k odesílání dat ze zdroje do indexu. 

> [!NOTE]
> Služba se zřizuje v určité úrovni. Přechod vrstev na získání kapacity zahrnuje zřízení nové služby (k dispozici není žádný místní upgrade). Další informace najdete v tématu [Výběr SKU nebo úrovně](search-sku-tier.md). Další informace o tom, jak upravit kapacitu v rámci služby, kterou jste už zřídili, najdete v tématu věnovaném [škálování úrovní prostředků pro úlohy dotazů a indexování](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Omezení předplatného
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Omezení úložiště
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Omezení indexu

| Resource | Zdarma | Základní&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Maximální počet indexů |3 |5 nebo 15 |50 |200 |200 |1 000 na oddíl nebo 3 000 na službu |10 |10 |
| Maximální počet jednoduchých polí na index |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Maximální složitá pole kolekce na index |40 |40 |40 |40 |40 |40 |40 |40 |
| Maximální počet prvků napříč všemi složitými kolekcemi na dokument |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Maximální hloubka složitých polí |10 |10 |10 |10 |10 |10 |10 |10 |
| Maximální počet modulu pro [návrhy](https://docs.microsoft.com/rest/api/searchservice/suggesters) na index |1 |1 |1 |1 |1 |1 |1 |1 |
| Maximální počet [profilů vyhodnocování](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) na index |100 |100 |100 |100 |100 |100 |100 |100 |
| Maximální počet funkcí na profil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> základní služby vytvořené před prosince 2017 mají nižší limity (5 namísto 15) na indexech. Úroveň Basic je jediná SKU s nižším limitem 100 polí na jeden index.

<a name="document-limits"></a>

## <a name="document-limits"></a>Omezení dokumentů 

Od října 2018 již neplatí žádná omezení pro žádnou novou službu vytvořenou v jakékoli Fakturovatelné úrovni (Basic, S1, S2, S3, S3 HD) v jakékoli oblasti. I když většina oblastí obsahovala neomezený počet dokumentů od listopadu 2017, mělo by dotrvat pět oblastí, které budou pokračovat v omezení dokumentů. V závislosti na tom, kdy a kde jste vytvořili vyhledávací službu, možná budete používat službu, na které se pořád vztahují omezení dokumentů.

Pokud chcete zjistit, jestli vaše služba má omezení dokumentů, podívejte se na dlaždici využití na stránce Přehled vaší služby. Počty dokumentů jsou buď neomezené, nebo podléhají omezení na základě úrovně.

  ![Dlaždice využití](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-previously-having-document-limits"></a>Oblasti, které dříve mají omezení dokumentů

Pokud portál indikuje omezení počtu dokumentů, byla služba vytvořena před 2017 nebo byla vytvořena v datovém centru s využitím clusterů s nižší kapacitou pro hostování Azure Searchch služeb:

+ Austrálie – východ
+ Východní Asie
+ Střed Indie
+ Japonsko – západ
+ Západní střed USA

Pro služby podléhající omezením dokumentu platí následující maximální limity:

|  Zdarma | Basic | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10,000 |1&nbsp;milion |15 milionů na oddíl nebo 180 milionů na službu |60 milionů na oddíl nebo 720 milionů na službu |120 milionů na oddíl nebo 1,4 miliard na službu |1 milion na index nebo 200 milionů na oddíl |

Pokud vaše služba obsahuje omezení, která vás zablokují, vytvořte novou službu a pak znovu publikujte veškerý obsah do této služby. Neexistuje žádný mechanismus pro bezproblémové opětovné zřízení vaší služby na nový hardware na pozadí.

> [!Note] 
> Pro služby s vysokou hustotou, které se vytvořily po pozdě 2017, se dokument 200 000 000 na oddíl odebral, 1 000 000 ale omezení počtu dokumentů na index zůstane.


### <a name="document-size-limits-per-api-call"></a>Omezení velikosti dokumentů na volání rozhraní API

Maximální velikost dokumentu při volání rozhraní API indexu je přibližně 16 megabajtů.

Velikost dokumentu je ve skutečnosti omezení velikosti textu požadavku rozhraní API pro index. Vzhledem k tomu, že do rozhraní API indexu můžete najednou předat dávku více dokumentů, omezení velikosti realisticky závisí na tom, kolik dokumentů je v dávce. Pro dávku s jedním dokumentem je maximální velikost dokumentu 16 MB formátu JSON.

Aby se zachovala velikost dokumentu, nezapomeňte z požadavku vyloučit data, která nejsou Queryable. Image a další binární data se přímo Queryable a neměly by se ukládat do indexu. Pro integraci nequeryablech dat do výsledků hledání Definujte pole bez možností vyhledávání, které ukládá odkaz na adresu URL prostředku.

## <a name="indexer-limits"></a>Omezení indexeru

Pro zajištění rovnováhy a stability služby jako celku existovala maximální doba běhu, ale větší sady dat můžou potřebovat více času indexování než maximální povolený počet. Pokud úloha indexování nemůže být dokončena v maximálním povoleném čase, zkuste ji spustit podle plánu. Scheduler sleduje stav indexování. Pokud z nějakého důvodu dojde k přerušení plánované úlohy indexování, může indexer vybrat, kde se naposledy nacházelo při příštím naplánovaném spuštění.


| Resource | Zdarma&nbsp;<sup>1</sup> | Základní&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Maximální počet indexerů |3 |5 nebo 15|50 |200 |200 |Není k dispozici |10 |10 |
| Maximální počet zdrojů dat |3 |5 nebo 15 |50 |200 |200 |Není k dispozici |10 |10 |
| Maximální dovednosti <sup>4</sup> |3 |5 nebo 15 |50 |200 |200 |Není k dispozici |10 |10 |
| Maximální zatížení indexování na vyvolání |dokumenty 10 000 |Omezeno pouze na maximum dokumentů |Omezeno pouze na maximum dokumentů |Omezeno pouze na maximum dokumentů |Omezeno pouze na maximum dokumentů |Není k dispozici |Bez omezení |Bez omezení |
| Minimální plán | 5 minut |5 minut |5 minut |5 minut |5 minut |5 minut |5 minut | 5 minut |
| Maximální doba běhu <sup>5</sup> | 1-3 minut |24 hodin |24 hodin |24 hodin |24 hodin |Není k dispozici  |24 hodin |24 hodin |
| Maximální doba spuštění pro rozpoznávání dovednosti nebo indexování objektů BLOB s analýzou obrázku <sup>5</sup> | 3-10 minut |2 hodiny |2 hodiny |2 hodiny |2 hodiny |Není k dispozici  |2 hodiny |2 hodiny |
| Indexer objektů BLOB: maximální velikost objektu blob, MB |16 |16 |128 |256 |256 |Není k dispozici  |256 |256 |
| Indexer objektů BLOB: maximální počet znaků obsahu extrahovaných z objektu BLOB |32,000 |64,000 |4&nbsp;miliony |4&nbsp;miliony |4&nbsp;miliony |Není k dispozici |4&nbsp;miliony |4&nbsp;miliony |

<sup>1</sup> bezplatné služby mají maximální dobu provádění indexeru 3 minuty pro zdroje objektů BLOB a 1 minutu pro všechny ostatní zdroje dat. V případě indexování AI, který volá Cognitive Services, jsou bezplatné služby omezené na 20 bezplatných transakcí za den, kde transakce je definovaná jako dokument, který úspěšně projde kanálem pro obohacení.

<sup>2</sup> základní služby vytvořené před prosince 2017 mají nižší omezení (5 namísto 15) u indexerů, zdrojů dat a dovednosti.

<sup>3</sup> S3 HD Services nezahrnují podporu indexeru.

<sup>4</sup> maximálně 30 dovedností na dovednosti.

<sup>5</sup> zátěžového vyhledávání a analýza obrázků v Azure Blob indexování mají kratší dobu běhu než běžné indexování textu. Analýza obrázků a zpracování přirozeného jazyka jsou výpočty náročné a využívají neúměrné objemy dostupného výpočetního výkonu. Čas spuštění byl snížen, aby bylo možné v frontě poskytnout ostatním úlohám možnost spustit.  

## <a name="queries-per-second-qps"></a>Dotazů za sekundu (QPS)

Odhady QPS musí být vyvíjeny nezávisle na každém zákazníkovi. Velikost indexu a složitost, velikost a složitost dotazů a množství přenosů jsou primárními determinanty QPS. Neexistuje žádný způsob, jak nabízet smysluplné odhady v případě, že tyto faktory nejsou známy.

Odhady jsou předvídatelné při výpočtu na službách, které běží na vyhrazených prostředcích (úrovně Basic a Standard). QPS můžete odhadnout přesněji, protože máte kontrolu nad více parametry. Pokyny pro přístup k odhadu najdete v tématu [Azure Search výkon a optimalizace](search-performance-optimization.md).

Pro vrstvy optimalizované pro úložiště byste měli očekávat nižší propustnost dotazů a vyšší latenci než na úrovni Standard.  Metodologie pro odhad výkonu dotazů, které budete mít, je stejná jako u standardních úrovní.

## <a name="data-limits-cognitive-search"></a>Omezení pro data (vyhledávání rozpoznávání)

[Kanál hledání rozpoznávání](cognitive-search-concept-intro.md) , který volá analýza textu prostředek pro [rozpoznávání entit](cognitive-search-skill-entity-recognition.md), [extrakci klíčových frází](cognitive-search-skill-keyphrases.md), [analýzu mínění](cognitive-search-skill-sentiment.md)a [rozpoznávání jazyka](cognitive-search-skill-language-detection.md) , podléhá omezením dat. Maximální velikost záznamu musí být 50 000 znaků měřených podle [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Pokud potřebujete data před odesláním do nástroje mínění Analyzer rozdělit, použijte [dovednost rozdělení textu](cognitive-search-skill-textsplit.md).

## <a name="api-request-limits"></a>Omezení požadavků na rozhraní API
* Maximálně 16 MB na žádost <sup>1</sup>
* Maximální délka adresy URL pro 8 KB
* Maximum 1000 dokumentů na jednu dávku pro nahrávání, sloučení nebo odstranění indexu
* Maximálně 32 polí v klauzuli $orderby
* Maximální velikost hledaného výrazu je 32 766 bajtů (32 KB minus 2 bajty) textu kódovaného v kódování UTF-8.

<sup>1</sup> v Azure Search tělo požadavku podléhá hornímu limitu 16 MB, čímž se zavedlo praktické omezení obsahu jednotlivých polí nebo kolekcí, které nejsou jinak omezeny teoretickými limity (Další informace najdete v tématu [podporované datové typy](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) . informace o složení polí a omezeních.

## <a name="api-response-limits"></a>Omezení odezvy rozhraní API
* Maximální počet 1000 dokumentů vrácených na stránku výsledků hledání
* Maximální počet 100 návrhů vrácených za požadavek rozhraní API pro návrh

## <a name="api-key-limits"></a>Omezení klíčů rozhraní API
Klíče rozhraní API se používají pro ověřování služby. Existují dva typy. Klíče správce jsou zadány v hlavičce požadavku a udělují službě úplný přístup pro čtení i zápis. Klíče dotazů jsou jen pro čtení, zadané na adrese URL a obvykle distribuované pro klientské aplikace.

* Maximálně 2 klíče správce na službu
* Maximálně 50 klíčů dotazů na službu
