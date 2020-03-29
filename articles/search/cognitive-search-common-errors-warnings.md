---
title: Chyby a upozornění indexeru
titleSuffix: Azure Cognitive Search
description: Tento článek obsahuje informace a řešení běžných chyb a upozornění, se kterými se můžete setkat během obohacení ai v Azure Cognitive Search.
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72bf08dce36d857c1fe91bbe9806336dfa185f7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671979"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Řešení potíží s běžnými chybami a upozorněními indexeru v Azure Cognitive Search

Tento článek obsahuje informace a řešení běžných chyb a upozornění, se kterými se můžete setkat během indexování a obohacení ai v Azure Cognitive Search.

Indexování se zastaví, pokud počet chyb překročí [hodnotu maxFailedItems](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Pokud chcete, aby indexery tyto chyby ignorovaly (a přeskočily "neúspěšné dokumenty"), zvažte aktualizaci `maxFailedItems` a `maxFailedItemsPerBatch` jak je popsáno [zde](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers).

> [!NOTE]
> Každý neúspěšný dokument spolu s jeho klíčem dokumentu (pokud je k dispozici) se zobrazí jako chyba ve stavu spuštění indexeru. Rozhraní API [indexu](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) můžete použít k ručnímu nahrání dokumentů později, pokud jste nastavili indexer tolerovat selhání.

Informace o chybě v tomto článku vám mohou pomoci vyřešit chyby a umožnit pokračování indexování.

Upozornění nezastaví indexování, ale označují podmínky, které by mohly vést k neočekávaným výsledkům. Zda budete mít akci nebo ne, závisí na datech a scénáři.

Počínaje verzí `2019-05-06`rozhraní API , chyby indexeru na úrovni položek a upozornění jsou strukturovány tak, aby poskytovaly větší srozumitelnost příčin a dalších kroků. Obsahují následující vlastnosti:

| Vlastnost | Popis | Příklad |
| --- | --- | --- |
| key | ID dokumentu ovlivněného chybou nebo upozorněním. | https:\//coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| jméno | Název operace popisující, kde došlo k chybě nebo upozornění. To je generováno následující strukturou: [kategorie]. [podkategorie]. [resourceType]. [resourceName] | DocumentExtraction.azureblob.myBlobContainerName Enrichment.WebApiSkill.mySkillName Projection.SearchIndex.OutputFieldMapping.myOutputFieldName Projection.SearchIndex.MergeOrUpload.myIndexName Projection.KnowledgeStore.Table.myTableName |
| zpráva | Popis chyby nebo upozornění na vysoké úrovni. | Dovednost se nezdařila, protože požadavek webového rozhraní api se nezdařil. |
| Podrobnosti | Jakékoli další podrobnosti, které mohou být užitečné pro diagnostiku problému, například odpověď WebApi, pokud se nepodařilo spustit vlastní dovednost. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 zdroj,`2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.` Func ... zbytek trasování zásobníku... |
| dokumentaceOdkaz | Odkaz na příslušnou dokumentaci s podrobnými informacemi k ladění a vyřešení problému. Tento odkaz často odkazuje na jeden z níže uvedených oddílů na této stránce. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"/>

## <a name="error-could-not-read-document"></a>Chyba: Nelze číst dokument.

Indexer nemohl číst dokument ze zdroje dat. K tomu může dojít v důsledku:

| Důvod | Podrobnosti/Příklad | Řešení |
| --- | --- | --- |
| Nekonzistentní typy polí v různých dokumentech | "Typ hodnoty má neshodu s typem sloupce. Nelze uložit `'{47.6,-122.1}'` do sloupce autoři.  Očekávaný typ je JArray."  "Při převodu datového typu nvarchar na float došlo k chybě."  "Převod se nezdařil při převodu hodnoty nvarchar '12 měsíců' na int datového typu."  "Aritmetická chyba přetečení při převodu výrazu na int datového typu." | Ujistěte se, že typ každého pole je stejný v různých dokumentech. Například pokud první `'startTime'` pole dokumentu je DateTime a ve druhém dokumentu je řetězec, bude tato chyba přístupů. |
| chyby ze základní služby zdroje dat | (z Cosmos DB)`{"Errors":["Request rate is large"]}` | Zkontrolujte instanci úložiště, abyste se ujistili, že je v pořádku. Možná budete muset upravit měřítko nebo dělení. |
| přechodné problémy | Při příjmu výsledků ze serveru došlo k chybě na úrovni přenosu. (zprostředkovatel: Zprostředkovatel TCP, chyba: 0 - Existující připojení bylo násilně uzavřeno vzdáleným hostitelem | V některých se občas objevují neočekávané problémy s připojením. Zkuste dokument spustit prostřednictvím indexeru znovu později. |

<a name="could-not-extract-document-content"/>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>Chyba: Z dokumentu nelze extrahovat obsah nebo metadata.
Indexer se zdrojem dat objektu Blob nemohl extrahovat obsah nebo metadata z dokumentu (například soubor PDF). K tomu může dojít v důsledku:

| Důvod | Podrobnosti/Příklad | Řešení |
| --- | --- | --- |
| objekt blob překročil limit velikosti | Dokument `'150441598'` je bajty, které přesahují maximální velikost `'134217728'` bajtů pro extrakci dokumentu pro aktuální úroveň služby. | [chyby indexování objektů blob](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| objekt blob má nepodporovaný typ obsahu | Dokument má nepodporovaný typ obsahu.`'image/png'` | [chyby indexování objektů blob](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| objekt blob je zašifrován | Dokument nelze zpracovat – může být šifrován nebo chráněn heslem. | Objekt blob můžete přeskočit s [nastavením objektů blob](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed). |
| přechodné problémy | "Chyba zpracování objektu blob: Požadavek byl přerušen: Požadavek byl zrušen." "Časový čas dokumentu během zpracování." | V některých se občas objevují neočekávané problémy s připojením. Zkuste dokument spustit prostřednictvím indexeru znovu později. |

<a name="could-not-parse-document"/>

## <a name="error-could-not-parse-document"></a>Chyba: Dokument nelze analyzovat.
Indexer četl dokument ze zdroje dat, ale došlo k problému s převodem obsahu dokumentu do zadaného schématu mapování polí. K tomu může dojít v důsledku:

| Důvod | Podrobnosti/Příklad | Řešení |
| --- | --- | --- |
| Chybí klíč dokumentu. | Klíč dokumentu nemůže chybět nebo vyprázdnit. | Ujistěte se, že všechny dokumenty mají platné klíče dokumentu |
| Klíč dokumentu je neplatný. | Klíč dokumentu nesmí být delší než 1024 znaků. | Upravte klíč dokumentu tak, aby splňoval požadavky na ověření. |
| Mapování polí nelze použít na pole. | Nelze použít funkci `'functionName'` mapování `'fieldName'`na pole . Pole nemůže mít hodnotu null. Název parametru: bajty | Zkontrolujte [mapování polí](search-indexer-field-mappings.md) definované v indexeru a porovnejte s daty zadaného pole neúspěšného dokumentu. Může být nutné upravit mapování polí nebo data dokumentu. |
| Nelze číst hodnotu pole. | Nelze přečíst hodnotu `'fieldName'` sloupce `'fieldIndex'`v indexu . Při příjmu výsledků ze serveru došlo k chybě na úrovni přenosu. (Zprostředkovatel: Zprostředkovatel TCP, chyba: 0 - Existující připojení bylo násilně uzavřeno vzdáleným hostitelem.) | Tyto chyby jsou obvykle z důvodu neočekávané problémy s připojením základní služby zdroje dat. Zkuste dokument spustit prostřednictvím indexeru znovu později. |

<a name="could-not-execute-skill"/>

## <a name="error-could-not-execute-skill"></a>Chyba: Nelze provést dovednost.
Indexer nebyl schopen spustit dovednost v skillset.

| Důvod | Podrobnosti/Příklad | Řešení |
| --- | --- | --- |
| Přechodné problémy s připojením | Došlo k přechodné chybě. Zkuste to prosím znovu později. | V některých se občas objevují neočekávané problémy s připojením. Zkuste dokument spustit prostřednictvím indexeru znovu později. |
| Potenciální chyba produktu | Došlo k neočekávané chybě. | To označuje neznámou třídu selhání a může znamenat chybu produktu. Chcete-li získat pomoc, podejte [lístek podpory.](https://ms.portal.azure.com/#create/Microsoft.Support) |
| Dovednost zjistila chybu během provádění | (Z dovedností sloučit) Jedna nebo více hodnot posunu byla neplatná a nebylo možné je analyzovat. Položky byly vloženy na konec textu | Problém můžete vyřešit pomocí informací v chybové zprávě. Tento druh selhání bude vyžadovat akci k vyřešení. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"/>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Chyba: Nelze spustit dovednost, protože požadavek webového rozhraní API se nezdařil.
Spuštění dovednosti se nezdařilo, protože volání webového rozhraní API se nezdařilo. Obvykle tato třída selhání dochází při použití vlastní dovednosti, v takovém případě budete muset ladit vlastní kód k vyřešení problému. Pokud místo toho selhání pochází z předdefinované dovednosti, naleznete v chybové zprávě nápovědu k řešení problému.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"/>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Chyba: Nelze spustit dovednost, protože odpověď dovednosti webového rozhraní API je neplatná.
Spuštění dovednosti se nezdařilo, protože volání webového rozhraní API vrátilo neplatnou odpověď. Obvykle tato třída selhání dochází při použití vlastní dovednosti, v takovém případě budete muset ladit vlastní kód k vyřešení problému. Pokud místo toho selhání pochází z vestavěné dovednosti, podejte [lístek podpory,](https://ms.portal.azure.com/#create/Microsoft.Support) abyste získali pomoc.

<a name="skill-did-not-execute-within-the-time-limit"/>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Chyba: Dovednost nebyla provedena ve stanovené lhůtě.
Existují dva případy, ve kterých se může setkat s touto chybovou zprávou, z nichž každý by měl být zacházeno odlišně. Postupujte podle níže uvedených pokynů v závislosti na tom, jaké dovednosti vrátily tuto chybu za vás.

### <a name="built-in-cognitive-service-skills"></a>Integrované dovednosti v oblasti kognitivních služeb
Mnoho předdefinovaných kognitivních dovedností, jako je například detekce jazyka, rozpoznávání entit nebo rozpoznávání OCR, je zálohováno koncovým bodem rozhraní API služby Cognitive Service. Někdy existují přechodné problémy s těmito koncovými body a požadavek bude časový mat. Pro přechodné problémy neexistuje žádný lék, kromě čekání a zkuste to znovu. Jako zmírnění, zvažte nastavení indexeru [spustit podle plánu](search-howto-schedule-indexers.md). Plánované indexování navazuje tam, kde skončilo. Za předpokladu, že jsou vyřešeny přechodné problémy, indexování a kognitivní zpracování dovedností by mělo být možné pokračovat v dalším naplánovaném spuštění.

Pokud budete i nadále vidět tuto chybu ve stejném dokumentu pro vestavěné kognitivní dovednosti, prosím, soubor [lístek podpory](https://ms.portal.azure.com/#create/Microsoft.Support) získat pomoc, protože to není očekáváno.

### <a name="custom-skills"></a>Vlastní dovednosti
Pokud narazíte na chybu časového opojení s vlastní dovedností, kterou jste vytvořili, můžete vyzkoušet několik věcí. Nejprve zkontrolujte své vlastní dovednosti a ujistěte se, že se neuvízne v nekonečné smyčce a že se neustále vrací výsledek. Jakmile potvrdíte, že tomu tak je, zjistěte, jaká je doba provedení vaší dovednosti. Pokud jste explicitně nenastavili hodnotu `timeout` vlastní definice `timeout` dovedností, je výchozí hodnota 30 sekund. Pokud 30 sekund není dostatečně dlouhá pro vaše dovednosti `timeout` k provedení, můžete zadat vyšší hodnotu na vlastní definici dovedností. Zde je příklad vlastní definice dovedností, kde je časový čas nastaven na 90 sekund:

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

Maximální hodnota, kterou můžete `timeout` nastavit pro parametr je 230 sekund.  Pokud vaše vlastní dovednost není schopna provést konzistentně `batchSize` do 230 sekund, můžete zvážit snížení vlastní dovednosti tak, aby měl méně dokumentů ke zpracování v rámci jednoho spuštění.  Pokud jste již `batchSize` nastavili hodnotu 1, budete muset přepsat dovednost, abyste ji mohli provést za méně než 230 sekund, nebo ji jinak rozdělit do více vlastních dovedností, takže doba provádění pro jednu vlastní dovednost je maximálně 230 sekund. Další informace naleznete v dokumentaci k [vlastním dovednostem.](cognitive-search-custom-skill-web-api.md)

<a name="could-not-mergeorupload--delete-document-to-the-search-index"/>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Chyba: Nelze`MergeOrUpload`' | '`Delete`' dokument do indexu vyhledávání

Dokument byl přečten a zpracován, ale indexer jej nemohl přidat do indexu vyhledávání. K tomu může dojít v důsledku:

| Důvod | Podrobnosti/Příklad | Řešení |
| --- | --- | --- |
| Pole obsahuje termín, který je příliš velký | Termín v dokumentu je větší než [limit 32 kB](search-limits-quotas-capacity.md#api-request-limits) | Tomuto omezení se můžete vyhnout tím, že zajistíte, aby pole nebylo nakonfigurováno jako filtrovatelné, směrovatelné nebo seřaditelné.
| Dokument je příliš velký na indexování. | Dokument je větší než [maximální velikost požadavku rozhraní API.](search-limits-quotas-capacity.md#api-request-limits) | [Jak indexovat velké datové sady](search-howto-large-index.md)
| Dokument obsahuje příliš mnoho objektů v kolekci. | Kolekce v dokumentu překračuje [maximální počet prvků ve všech komplexních kolekcích limit](search-limits-quotas-capacity.md#index-limits) "Dokument s klíčem `'1000052'` má `'4303'` objekty v kolekcích (JSON pole). U `'3000'` většiny objektů je povoleno být v kolekcích v celém dokumentu. Odeberte objekty z kolekcí a zkuste dokument indexovat znovu." | Doporučujeme zmenšit velikost komplexní kolekce v dokumentu pod limit a vyhnout se vysokévyužití úložiště.
| Potíže s připojením k cílovému indexu (který přetrvává po opakování) protože služba je pod jiným zatížením, jako je například dotazování nebo indexování. | Nepodařilo se navázat připojení k aktualizaci indexu. Vyhledávací služba je pod velkým zatížením. | [Škálování vyhledávací služby](search-capacity-planning.md)
| Vyhledávací služba je opravována pro aktualizaci služby nebo je uprostřed změny konfigurace topologie. | Nepodařilo se navázat připojení k aktualizaci indexu. Vyhledávací služba je v současné době dolů / Vyhledávací služba prochází přechodem. | Konfigurace služby s alespoň 3 replikami pro 99,9% dostupnost na [dokumentaci s la](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Selhání základního výpočetního prostředku/síťového prostředku (vzácné) | Nepodařilo se navázat připojení k aktualizaci indexu. Došlo k neznámému selhání. | Nakonfigurujte indexery tak, aby [se spouštěli podle plánu,](search-howto-schedule-indexers.md) aby se vyzvedly ze stavu selhání.
| Požadavek na indexování pro cílový index nebyl potvrzen v časovém období z důvodu problémů se sítí. | Nelze navázat připojení k indexu vyhledávání včas. | Nakonfigurujte indexery tak, aby [se spouštěli podle plánu,](search-howto-schedule-indexers.md) aby se vyzvedly ze stavu selhání. Dále zkuste snížit [velikost dávky](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters) indexeru, pokud tento chybový stav přetrvává.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"/>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>Chyba: Dokument nelze indexovat, protože některá data dokumentu nebyla platná.

Dokument byl přečten a zpracován indexerem, ale z důvodu neshody v konfiguraci indexových polí a dat extrahovaných a zpracovávaných indexerem jej nelze přidat do indexu vyhledávání. K tomu může dojít v důsledku:

| Důvod | Podrobnosti/Příklad
| --- | ---
| Datový typ polí extrahovaných indexerem není kompatibilní s datovým modelem odpovídajícího cílového pole indexu. | Datové pole "_data_" v dokumentu s klíčem '888' má neplatnou hodnotu typu Edm.String. Očekávaný typ byl Collection(Edm.String)'. |
| Nepodařilo se extrahovat žádnou entitu JSON z hodnoty řetězce. | Nelze analyzovat hodnotu typu 'Edm.String'' pole '_data_' jako objekt JSON. Chyba:'Po analýzě hodnoty byl zjištěn neočekávaný znak: ''. Cesta '_cesta_', řádek 1, pozice 3162.' |
| Nepodařilo se extrahovat kolekci entit JSON z řetězcové hodnoty.  | Nelze analyzovat hodnotu typu Edm.String " pole '_data_' jako pole JSON. Chyba:'Po analýzě hodnoty byl zjištěn neočekávaný znak: ''. Cesta '[0]', řádek 1, pozice 27.' |
| Ve zdrojovém dokumentu byl zjištěn neznámý typ. | Neznámý typ _"_ nelze indexovat. |
| Ve zdrojovém dokumentu byl použit nekompatibilní zápis pro body zeměpisné oblasti. | Literály řetězce WKT POINT nejsou podporovány. Místo toho použijte literály bodů GeoJson |

Ve všech těchto případech naleznete [podporované datové typy](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) a mapování [datových typů pro indexery](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) a ujistěte se, že jste správně vytvořili schéma indexu a nastavili příslušná [mapování polí indexovacího modulu](search-indexer-field-mappings.md). Chybová zpráva bude obsahovat podrobnosti, které mohou pomoci sledovat zdroj neshody.

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>Chyba: Integrovanou zásadu sledování změn nelze použít, protože tabulka obsahuje složený primární klíč.

To platí pro tabulky SQL a obvykle se stane, když je klíč definován jako složený klíč nebo, když tabulka definovala jedinečný seskupený index (jako v indexu SQL, nikoli index Azure Search). Hlavním důvodem je, že atribut klíče je upraven tak, aby složený primární klíč v případě [jedinečného seskupeného indexu](https://docs.microsoft.com/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver15). V takovém případě se ujistěte, že tabulka SQL nemá jedinečný seskupený index nebo že mapujete klíčové pole na pole, u kterého je zaručeno, že nemá duplicitní hodnoty.

<a name="could-not-process-document-within-indexer-max-run-time"/>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Chyba: Dokument nelze zpracovat v rámci maximální doby běhu indexeru.

K této chybě dochází, když indexer není schopen dokončit zpracování jednoho dokumentu ze zdroje dat v rámci povolené doby provádění. [Maximální doba běhu](search-limits-quotas-capacity.md#indexer-limits) je kratší při použití skillsets. Dojde-li k této chybě, pokud máte maxFailedItems nastavena na hodnotu než 0, indexer obchází dokument na budoucí spuštění tak, aby indexování může postupovat. Pokud si nemůžete dovolit přeskočit žádný dokument nebo pokud se tato chyba zobrazuje konzistentně, zvažte rozdělení dokumentů do menších dokumentů, aby bylo možné provést částečný průběh v rámci jednoho provádění indexeru.

<a name="could-not-project-document"/>

## <a name="error-could-not-project-document"></a>Chyba: Dokument projektu nebyl a nebyl

K této chybě dochází, když indexer se pokouší [promítnout data do úložiště znalostí](knowledge-store-projection-overview.md) a došlo k selhání v našem pokusu o to.  Tato chyba může být konzistentní a opravitelné nebo může být přechodné selhání s jímkou výstupu projekce, které budete muset počkat a opakovat, aby bylo možné vyřešit.  Zde je sada známých stavů selhání a možných řešení.

| Důvod | Podrobnosti/Příklad | Řešení |
| --- | --- | --- |
| Nelze aktualizovat objekt `'blobUri'` blob projekce v kontejneru.`'containerName'` |Zadaný kontejner neexistuje. | Indexer zkontroluje, zda byl zadaný kontejner dříve vytvořen, a v případě potřeby jej vytvoří, ale tuto kontrolu provede pouze jednou za spuštění indexeru. Tato chyba znamená, že něco odstranil kontejner po tomto kroku.  Chcete-li tuto chybu vyřešit, zkuste toto: nechte informace o účtu úložiště na pokoji, počkejte na dokončení indexeru a znovu spusťte indexer. |
| Nelze aktualizovat objekt `'blobUri'` blob projekce v kontejneru.`'containerName'` |Nelze zapisovat data do přenosového připojení: Existující připojení bylo násilně uzavřeno vzdáleným hostitelem. | To se očekává, že přechodné selhání s Azure Storage a proto by měl být vyřešen opětovným spuštěním indexeru. Pokud narazíte na tuto chybu konzistentně, prosím, soubor [lístek podpory,](https://ms.portal.azure.com/#create/Microsoft.Support) takže to může být dále zkoumány.  |
| Řádek `'projectionRow'` v tabulce nelze aktualizovat.`'tableName'` | Server je zaneprázdněn. | To se očekává, že přechodné selhání s Azure Storage a proto by měl být vyřešen opětovným spuštěním indexeru. Pokud narazíte na tuto chybu konzistentně, prosím, soubor [lístek podpory,](https://ms.portal.azure.com/#create/Microsoft.Support) takže to může být dále zkoumány.  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"/>

## <a name="warning-skill-input-was-invalid"></a>Upozornění: Vstup dovednosti byl neplatný.
Vstup do dovednosti chyběl, nesprávný typ nebo jinak neplatný. Varovná zpráva bude uvádět dopad:
1) Nelze provést dovednost.
2) Dovednost byla provedena, ale může mít neočekávané výsledky

Kognitivní dovednosti vyžadovaly vstupy a volitelné vstupy. Například [extrakce klíčové fráze](cognitive-search-skill-keyphrases.md) má `text`dva `languageCode`požadované vstupy a žádné volitelné vstupy. Vlastní vstupy dovedností jsou považovány za volitelné vstupy.

Pokud některé požadované vstupy chybí nebo pokud některý vstup není správný typ, dovednost získá přeskočena a generuje upozornění. Přeskočené dovednosti negenerují žádné výstupy, takže pokud jiné dovednosti používají výstupy přeskočené dovednosti, mohou generovat další varování.

Pokud chybí volitelný vstup, dovednost bude stále spuštěna, ale může způsobit neočekávaný výstup z důvodu chybějícího vstupu.

V obou případech lze toto upozornění očekávat z důvodu tvaru dat. Máte-li například dokument obsahující informace o osobách `middleName`s `lastName`poli `firstName`, a , můžete mít `middleName`některé dokumenty, které nemají položku pro . Pokud předat `middleName` jako vstup do dovednosti v kanálu, pak se očekává, že tento vstup dovednosti může chybět některé čas. Budete muset vyhodnotit data a scénář k určení, zda je vyžadována nějaká akce v důsledku tohoto upozornění.

Pokud chcete zadat výchozí hodnotu v případě chybějícího vstupu, můžete použít [podmíněnou dovednost](cognitive-search-skill-conditional.md) ke generování výchozí hodnoty a potom použít výstup [podmíněné dovednosti](cognitive-search-skill-conditional.md) jako vstup dovednosti.


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'en'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

| Důvod | Podrobnosti/Příklad | Řešení |
| --- | --- | --- |
| Vstup dovednosti je nesprávný typ | "Požadovaný vstup dovednosti nebyl očekávaného typu `String`. Název: `text`, `/document/merged_content`Zdroj: ."  "Požadovaný vstup dovednosti nebyl očekávaného formátu. Název: `text`, `/document/merged_content`Zdroj: ."  "Nelze iterate přes `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`non-pole."  "Nelze vybrat `0` v non-array" `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` | Určité dovednosti očekávají vstupy určitých typů, `text` například dovednost [mínění](cognitive-search-skill-sentiment.md) očekává, že bude řetězec. Pokud vstup určuje hodnotu bez řetězce, dovednost se nespustí a nevygeneruje žádné výstupy. Ujistěte se, že vaše sada dat má vstupní hodnoty jednotné v textu, nebo použijte [vlastní webové rozhraní API dovednost](cognitive-search-custom-skill-web-api.md) předem zpracovat vstup. Pokud iterace dovednosti přes pole, zkontrolujte, zda dovednosti `*` kontextu a vstup mají ve správných pozicích. Kontext i vstupní zdroj by `*` obvykle měly končit pro pole. |
| Chybí zadání dovednosti | "Chybí požadovaný vstup dovednosti. Název: `text`, `/document/merged_content`Zdroj: " `/document/normalized_images/0/imageTags`"Chybějící hodnota " .  "Nelze vybrat `0` v `/document/pages` poli `0`délky ." | Pokud všechny dokumenty získat toto upozornění, s největší pravděpodobností je překlep ve vstupních cestách `*` a měli byste zkontrolovat název vlastnosti, extra nebo chybí v cestě a ujistěte se, že dokumenty ze zdroje dat poskytují požadované vstupy. |
| Vstup kódu jazyka odbornosti je neplatný. | Zadání `languageCode` dovednosti má následující `X,Y,Z`kódy jazyků , z nichž alespoň jeden je neplatný. | Další podrobnosti [naleznete níže](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"/>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Upozornění: Skill vstup 'languageCode' má následující kódy jazyka 'X,Y,Z', z nichž alespoň jeden je neplatný.
Jedna nebo více hodnot předaných do volitelného `languageCode` vstupu navazující dovednosti není podporována. Tato situace může nastat, pokud předáváte výstup [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) na následné dovednosti a výstup se skládá z více jazyků, než jsou podporovány v těchto podřízené dovednosti.

Pokud víte, že vaše datová sada je vše v jednom `languageCode` jazyce, měli `defaultLanguageCode` byste odebrat [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) a vstup dovednosti a použít parametr dovednosti pro tuto dovednost místo, za předpokladu, že jazyk je podporován pro tuto dovednost.

Pokud víte, že vaše sada dat obsahuje více jazyků, `languageCode` a proto potřebujete [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) a vstup, zvažte přidání [ConditionalSkill](cognitive-search-skill-conditional.md) odfiltrovat text s jazyky, které nejsou podporovány před předáním v textu nanavazující dovednosti.  Zde je příklad toho, jak to může vypadat entityRecognitionSkill:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

Zde jsou některé odkazy pro aktuálně podporované jazyky pro každou z dovedností, které mohou způsobit tuto chybovou zprávu:
* [Podporované jazyky pro analýzu textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) (pro [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md), [SentimentSkill](cognitive-search-skill-sentiment.md)a [PIIDetectionSkill](cognitive-search-skill-pii-detection.md))
* [Překladatel podporované jazyky](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (pro [překlad textuSkill](cognitive-search-skill-text-translation.md))
* [Rozdělení textu](cognitive-search-skill-textsplit.md) Podporované jazyky:`da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"/>

## <a name="warning-skill-input-was-truncated"></a>Upozornění: Vstup dovednosti byl zkrácen.
Kognitivní dovednosti mají limity na délku textu, který lze analyzovat najednou. Pokud je zadávání textu těchto dovedností nad tímto limitem, zkrátíme text tak, aby splňoval limit, a potom provedeme obohacení tohoto zkráceného textu. To znamená, že dovednost je provedena, ale ne přes všechna data.

V příkladu LanguageDetectionSkill `'text'` níže může vstupní pole aktivovat toto upozornění, pokud je nad limit znaků. Limity pro vstup y dovedností naleznete v dokumentaci k [dovednostem](cognitive-search-predefined-skills.md).

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

Pokud chcete zajistit, aby byl analyzován veškerý text, zvažte použití [dovednosti Rozdělit](cognitive-search-skill-textsplit.md).

<a name="web-api-skill-response-contains-warnings"/>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Upozornění: Odpověď dovednosti webového rozhraní API obsahuje upozornění
Indexer byl schopen spustit dovednost v skillset, ale odpověď z požadavku webového rozhraní API uvedeno, že byly varování během provádění. Zkontrolujte upozornění, abyste pochopili, jaký vliv mají vaše data a zda je vyžadována akce.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"/>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Upozornění: Aktuální konfigurace indexeru nepodporuje přírůstkový průběh.

K tomuto upozornění dochází pouze pro zdroje dat Cosmos DB.

Přírůstkové průběh během indexování zajišťuje, že pokud indexer uprovádění je přerušeno přechodné selhání nebo časový limit spuštění, indexer může vyzvednout, kde skončil při příštím spuštění, namísto nutnosti znovu indexovat celou kolekci od začátku. To je zvláště důležité při indexování velké kolekce.

Možnost pokračovat v nedokončené úlohě indexování je založena `_ts` na tom, že dokumenty jsou seřazeny ve sloupci. Indexer používá časové razítko k určení, který dokument vyzvednout další. Pokud `_ts` sloupec chybí nebo pokud indexer nemůže určit, pokud je vlastní dotaz objednán, indexer začíná na začátku a zobrazí se toto upozornění.

Je možné přepsat toto chování, povolení přírůstkového průběhu a `assumeOrderByHighWatermarkColumn` potlačení tohoto upozornění pomocí vlastnosti konfigurace.

Další informace naleznete [v tématu Přírůstkový průběh a vlastní dotazy](search-howto-index-cosmosdb.md#IncrementalProgress).

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"/>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Upozornění: Některá data byla během projekce ztracena. Řádek 'X' v tabulce 'Y' má vlastnost řetězce 'Z', která byla příliš dlouhá.

[Služba Table Storage](https://azure.microsoft.com/services/storage/tables) má omezení na to, jak velké [vlastnosti entity](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types) mohou být. Řetězce mohou mít 32 000 znaků nebo méně. Pokud se promítá řádek s vlastností string delší než 32 000 znaků, bude zachováno pouze prvních 32 000 znaků. Chcete-li tento problém vyřešit, vyhněte se promítání řádků s vlastnostmi řetězce delší než 32 000 znaků.

<a name="truncated-extracted-text-to-x-characters"/>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Upozornění: Zkrácený extrahovaný text na znaky X
Indexery omezují, kolik textu lze extrahovat z jednoho dokumentu. Toto omezení závisí na cenové úrovni: 32 000 znaků pro úroveň Free, 64 000 pro základní úroveň, 4 miliony pro standard, 8 milionů pro standard S2 a 16 milionů pro standard S3. Zkrácený text nebude indexován. Chcete-li se tomuto upozornění vyhnout, zkuste rozdělit dokumenty s velkým množstvím textu na více menších dokumentů. 

Další informace naleznete v tématu [Omezení indexeru](search-limits-quotas-capacity.md#indexer-limits).

<a name="could-not-map-output-field-x-to-search-index"/>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Upozornění: Nelze mapovat výstupní pole 'X' pro vyhledávací index
Mapování výstupního pole, která odkazují na neexistující/nulová data, vytvoří upozornění pro každý dokument a výsledkem bude prázdné pole indexu. Chcete-li tento problém vyřešit, zkontrolujte zdrojové cesty mapování výstupních polí pro možné překlepy nebo nastavte výchozí hodnotu pomocí [podmíněné dovednosti](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist).

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"/>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Upozornění: Zásada detekce změn dat je nakonfigurována tak, aby používala sloupec klíče "X"
[Zásady zjišťování změn dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies) mají specifické požadavky na sloupce, které používají ke zjišťování změn. Jedním z těchto požadavků je, že tento sloupec je aktualizován při každé změně zdrojové položky. Dalším požadavkem je, že nová hodnota pro tento sloupec je větší než předchozí hodnota. Klíčové sloupce tento požadavek nesplňují, protože se při každé aktualizaci nemění. Chcete-li tento problém vyřešit, vyberte jiný sloupec pro zásady zjišťování změn.

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"/>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>Upozornění: Text dokumentu se zdá být kódován UTF-16, ale chybí značka pořadí bajtu

[Režimy analýzy indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer#blob-configuration-parameters) potřebují vědět, jak je text kódován před jeho analýzou. Dva nejběžnější způsoby kódování textu jsou UTF-16 a UTF-8. UTF-8 je kódování proměnné délky, kde každý znak je mezi 1 bajt a 4 bajty dlouhé. UTF-16 je kódování s pevnou délkou, kde každý znak je dlouhý 2 bajty. UTF-16 má dvě různé varianty, "big endian" a "little endian". Kódování textu je určeno "značkou pořadí bajtů", řadou bajtů před textem.

| Kódování | Značka objednávky bajtů |
| --- | --- |
| UTF-16 Velký Endian | 0xFE 0xFF |
| UTF-16 Malý Endian | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

Pokud není k dispozici žádná značka pořadí bajtů, předpokládá se, že text je kódován jako UTF-8.

Chcete-li toto upozornění obejít, zjistěte, co je kódování textu pro tento objekt blob, a přidejte příslušnou značku pořadí bajtu.

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"/>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>Upozornění: Kolekce Cosmos DB 'X' má zásady indexování Lazy. Některá data mohou být ztracena

Kolekce s [opožděné](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) indexování zásady nelze dotazovat konzistentně, výsledkem je, že indexer chybějící data. Chcete-li toto upozornění obejít, změňte zásady indexování na Konzistentní.
