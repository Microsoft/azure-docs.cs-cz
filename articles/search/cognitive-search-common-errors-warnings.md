---
title: Chyby a upozornění indexeru
titleSuffix: Azure Cognitive Search
description: Tento článek poskytuje informace a řešení běžných chyb a varování, se kterými se můžete setkat při obohacení AI v Azure Kognitivní hledání.
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2da009189e0265aafcb26b7ec96837965f1ea0c5
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76838543"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Řešení běžných chyb a upozornění v indexeru v Azure Kognitivní hledání

Tento článek poskytuje informace a řešení běžných chyb a varování, se kterými se můžete setkat při indexování a obohacení AI v Azure Kognitivní hledání.

Indexování se zastaví, když počet chyb překročí [' maxFailedItems '](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Pokud chcete, aby indexery ignorovaly tyto chyby (a přeskočíte "neúspěšné dokumenty"), zvažte aktualizaci `maxFailedItems` a `maxFailedItemsPerBatch`, jak je popsáno [zde](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers).

> [!NOTE]
> Každý neúspěšný dokument spolu s klíčem dokumentu (Pokud je k dispozici) se zobrazí jako chyba ve stavu provádění indexeru. [Rozhraní API indexu](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) můžete použít k ručnímu nahrání dokumentů později, pokud jste nastavili indexer k tolerování chyb.

Informace o chybě v tomto článku vám pomůžou vyřešit chyby, takže indexování bude pokračovat.

Upozornění neukončí indexování, ale označují podmínky, které by mohly vést k neočekávaným výsledkům. Bez ohledu na to, jestli provedete akci, nebo ne, závisí na datech a vašem scénáři.

Počínaje rozhraním API `2019-05-06`se chyby a upozornění indexerů na úrovni položek strukturují tak, aby poskytovaly lepší srozumitelnost příčin a dalších kroků. Obsahují následující vlastnosti:

| Vlastnost | Popis | Příklad: |
| --- | --- | --- |
| key | ID dokumentu dokumentu ovlivněného chybou nebo upozorněním. | https:\//coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| jméno | Název operace popisující, kde došlo k chybě nebo upozornění. Tato struktura je generována následující strukturou: [Category]. [Subcategory]. [ResourceType]. resourceName | DocumentExtraction. azureblobu. myBlobContainerName obohacení. WebApiSkill. mySkillName projekce. SearchIndex. OutputFieldMapping. myOutputFieldName projekce. SearchIndex. MergeOrUpload. myIndexName Projekce. KnowledgeStore. Table. myTableName |
| zpráva | Popis chyby nebo varování na nejvyšší úrovni. | Nelze provést dovednost, protože požadavek webového rozhraní API se nezdařil. |
| details | Jakékoli další podrobnosti, které mohou být užitečné při diagnostice problému, jako je například odpověď WebApi při provádění vlastní dovednosti, se nezdařila. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 zdroj, Func`2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.`... zbytek trasování zásobníku... |
| documentationLink | Odkaz na příslušnou dokumentaci s podrobnými informacemi pro ladění a vyřešení problému. Tento odkaz často odkazuje na jednu z níže uvedených částí na této stránce. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"/>

## <a name="error-could-not-read-document"></a>Chyba: nepovedlo se přečíst dokument.

Indexer nemohl přečíst dokument ze zdroje dat. K tomu může dojít v důsledku:

| Důvod | Podrobnosti/příklad | Rozlišení |
| --- | --- | --- |
| Nekonzistentní typy polí v různých dokumentech | Typ hodnoty se neshoduje s typem sloupce. Nepovedlo se uložit `'{47.6,-122.1}'` ve sloupci autoři.  Očekávaný typ je JArray. | Ujistěte se, že je typ každého pole stejný v různých dokumentech. Pokud je například prvním dokumentem `'startTime'` pole DateTime a druhý dokument je řetězec, tato chyba bude dosaženo. |
| chyby ze základní služby zdroje dat | (z Cosmos DB) `{"Errors":["Request rate is large"]}` | Zkontrolujte instanci úložiště, abyste měli jistotu, že je v pořádku. Možná budete muset upravit škálování nebo dělení na oddíly. |
| přechodné problémy | Při přijímání výsledků ze serveru došlo k chybě na úrovni přenosu. (poskytovatel: Zprostředkovatel TCP, chyba: 0 – existující připojení bylo vynuceně ukončeno vzdáleným hostitelem | Občas dojde k neočekávaným potížím s připojením. Zkuste znovu spustit dokument v indexeru později. |

<a name="could-not-extract-document-content"/>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>Chyba: nepovedlo se extrahovat obsah nebo metadata z dokumentu.
Indexer se zdrojem dat objektu BLOB nemohl extrahovat obsah nebo metadata z dokumentu (například soubor PDF). K tomu může dojít v důsledku:

| Důvod | Podrobnosti/příklad | Rozlišení |
| --- | --- | --- |
| objekt BLOB překračuje limit velikosti. | Dokument je `'150441598'` bajtů, což překračuje maximální velikost `'134217728'` bajtů pro extrakci dokumentů pro aktuální úroveň služby. | [chyby indexování objektů BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| objekt BLOB má nepodporovaný typ obsahu. | Dokument má nepodporovaný typ obsahu `'image/png'` | [chyby indexování objektů BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| objekt BLOB je zašifrovaný. | Dokument se nepovedlo zpracovat – může být zašifrovaný nebo chráněný heslem. | Objekt blob můžete přeskočit s [nastavením objektu BLOB](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed). |
| přechodné problémy | "Při zpracování objektu BLOB došlo k chybě: požadavek byl zrušen: požadavek byl zrušen." "Při zpracování vypršel časový limit dokumentu." | Občas dojde k neočekávaným potížím s připojením. Zkuste znovu spustit dokument v indexeru později. |

<a name="could-not-parse-document"/>

## <a name="error-could-not-parse-document"></a>Chyba: nepovedlo se analyzovat dokument.
Indexer si přečte dokument ze zdroje dat, ale při převodu obsahu dokumentu do zadaného schématu mapování polí došlo k problému. K tomu může dojít v důsledku:

| Důvod | Podrobnosti/příklad | Rozlišení |
| --- | --- | --- |
| Chybí klíč dokumentu. | Klíč dokumentu nemůže být chybí nebo je prázdný. | Zajistěte, aby všechny dokumenty měly platný klíč dokumentu. |
| Klíč dokumentu je neplatný. | Klíč dokumentu nemůže být delší než 1024 znaků. | Upravte klíč dokumentu tak, aby splňoval požadavky na ověření. |
| Mapování polí pro pole nelze použít. | Nelze použít funkci mapování `'functionName'` k poli `'fieldName'`. Pole nemůže mít hodnotu null. Název parametru: bajty | Překontrolujte [mapování polí](search-indexer-field-mappings.md) definovaná v indexeru a porovnejte je s daty zadaného pole neúspěšného dokumentu. Může být nutné změnit mapování polí nebo data dokumentu. |
| Nepodařilo se přečíst hodnotu pole. | Nelze načíst hodnotu sloupce `'fieldName'` v indexu `'fieldIndex'`. Při přijímání výsledků ze serveru došlo k chybě na úrovni přenosu. (poskytovatel: poskytovatel protokolu TCP, chyba: 0 – existující připojení bylo vynuceně ukončeno vzdáleným hostitelem.) | Tyto chyby jsou obvykle způsobeny neočekávanými problémy s připojením ke zdrojové službě zdroje dat. Zkuste znovu spustit dokument v indexeru později. |

<a name="could-not-execute-skill"/>

## <a name="error-could-not-execute-skill"></a>Chyba: nepovedlo se spustit dovednost.
Indexer nemohl v dovednosti spustit dovednost.

| Důvod | Podrobnosti/příklad | Rozlišení |
| --- | --- | --- |
| Problémy s přechodným připojením | Došlo k přechodné chybě. Zkuste to prosím znova později. | Občas dojde k neočekávaným potížím s připojením. Zkuste znovu spustit dokument v indexeru později. |
| Potenciální chyba produktu | Došlo k neočekávané chybě. | To indikuje neznámou třídu selhání a může znamenat, že došlo k chybě produktu. Požádejte o pomoc [lístek podpory](https://ms.portal.azure.com/#create/Microsoft.Support) . |
| Při provádění dovednosti došlo k chybě. | (Z dovednosti sloučení) Jedna nebo více hodnot posunutí bylo neplatných a nelze je analyzovat. Na konec textu byly vloženy položky. | K vyřešení problému použijte informace v chybové zprávě. Tento druh selhání bude vyžadovat akci, která se má vyřešit. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"/>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Chyba: nelze provést dovednost, protože požadavek webového rozhraní API se nezdařil.
Provádění dovedností se nezdařilo, protože volání webového rozhraní API se nezdařilo. K této třídě selhání obvykle dochází při použití vlastních dovedností. v takovém případě budete muset ladit vlastní kód pro vyřešení problému. Pokud místo toho dojde k chybě z předdefinované dovednosti, podrobnější informace najdete v chybové zprávě v tématu o řešení problému.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"/>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Chyba: nepovedlo se spustit dovednost, protože odpověď na dovednosti webového rozhraní API není platná.
Spuštění dovednosti se nezdařilo, protože volání webového rozhraní API vrátilo neplatnou odpověď. K této třídě selhání obvykle dochází při použití vlastních dovedností. v takovém případě budete muset ladit vlastní kód pro vyřešení problému. Pokud místo toho dojde k chybě z integrované dovednosti, požádejte o pomoc [lístek podpory](https://ms.portal.azure.com/#create/Microsoft.Support) .

<a name="skill-did-not-execute-within-the-time-limit"/>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Chyba: dovednost neproběhla v časovém limitu.
Existují dva případy, kdy se může zobrazit tato chybová zpráva, z nichž každá by měla být zpracována jinak. Postupujte podle pokynů níže v závislosti na tom, jaká dovednost vrátila tuto chybu za vás.

### <a name="built-in-cognitive-service-skills"></a>Integrované dovednosti služby pro rozpoznávání
Mnohé z vestavěných dovedností rozpoznávání, jako je rozpoznávání jazyka, rozpoznávání entit nebo rozpoznávání OCR, jsou zajištěny koncovým bodem rozhraní API služby vnímání. Někdy dochází k přechodným problémům s těmito koncovými body a vyprší časový limit žádosti. V případě přechodných problémů nedochází k nápravě, s výjimkou čekání a akci opakujte. Jako zmírnění zvažte nastavení indexeru, který se bude [spouštět podle plánu](search-howto-schedule-indexers.md). Naplánované indexování se ponechá tam, kde skončila. Za předpokladu, že dojde k vyřešení přechodných problémů, by mělo být při dalším naplánovaném spuštění možné pokračovat v indexování a zpracování dovedností pro rozpoznávání.

Pokud se tato chyba bude dál zobrazovat u stejného dokumentu pro vestavěnou funkci pro rozpoznávání hlasu, požádejte o pomoc [lístek podpory](https://ms.portal.azure.com/#create/Microsoft.Support) , protože to se neočekává.

### <a name="custom-skills"></a>Vlastní dovednosti
Pokud narazíte na chybu s časovým limitem s vlastní dovedností, kterou jste vytvořili, můžete si vyzkoušet několik věcí. Nejdřív zkontrolujte svoji vlastní dovednost a ujistěte se, že není zablokovaná v nekonečné smyčce a že vrátí výsledek konzistentně. Jakmile ověříte, že se jedná o tento případ, určete, co je doba provádění vaší dovednosti. Pokud jste nezadali explicitně `timeout` hodnotu pro vlastní definici dovedností, výchozí `timeout` je 30 sekund. Pokud není dostatečná doba 30 sekund, než se vaše dovednost spustí, můžete zadat vyšší hodnotu `timeout` v definici vlastní dovednosti. Tady je příklad vlastní definice dovednosti, kde časový limit je nastavený na 90 sekund:

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

Maximální hodnota, kterou můžete nastavit pro parametr `timeout`, je 230 sekund.  Pokud vaše vlastní dovednost nemůže běžet konzistentně během 230 sekund, můžete zvážit omezení `batchSize` vlastní dovednosti, aby bylo možné zpracovávat méně dokumentů v rámci jednoho spuštění.  Pokud jste již `batchSize` nastavili na 1, bude nutné přepište dovednost, aby bylo možné ji spustit za méně než 230 sekund, nebo jinak rozdělit do několika vlastních dovedností tak, aby čas spuštění pro jednu vlastní dovednost byl maximálně 230 sekund. Další informace najdete v [dokumentaci k vlastním dovednostím](cognitive-search-custom-skill-web-api.md) .

<a name="could-not-mergeorupload--delete-document-to-the-search-index"/>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Chyba: nepovedlo se`MergeOrUpload`| dokument '`Delete`' do indexu hledání

Dokument byl načten a zpracován, ale indexer ho nemohl přidat do indexu vyhledávání. K tomu může dojít v důsledku:

| Důvod | Podrobnosti/příklad | Rozlišení |
| --- | --- | --- |
| Pole obsahuje termín, který je příliš velký. | Termín v dokumentu je větší než [limit 32 KB](search-limits-quotas-capacity.md#api-request-limits) . | Tomuto omezení se můžete vyhnout tím, že zajistíte, že pole není nakonfigurováno jako filtrovatelné, plošky nebo seřaditelné.
| Dokument je moc velký a nedá se indexovat. | Dokument je větší než [maximální velikost požadavku rozhraní API](search-limits-quotas-capacity.md#api-request-limits) . | [Indexování velkých datových sad](search-howto-large-index.md)
| Dokument obsahuje příliš mnoho objektů v kolekci. | Kolekce v dokumentu překročila [maximální počet prvků napříč všemi složitými kolekcemi](search-limits-quotas-capacity.md#index-limits) . dokument s klíčovým `'1000052'` má `'4303'` objekty v kolekcích (pole JSON). V rámci celého dokumentu může být nejvýše `'3000'` objektů v kolekcích. Odeberte prosím objekty z kolekcí a zkuste dokument indexovat znovu. " | Doporučujeme zmenšit velikost komplexní kolekce v dokumentu pod limit a vyhnout se vysokému využití úložiště.
| Problémy s připojením k cílovému indexu (které přetrvávají po opakování), protože služba je pod jiným zatížením, jako je například dotazování nebo indexování. | Nepovedlo se navázat spojení s indexem aktualizace. Služba vyhledávání je zatížená velkým zatížením. | [Horizontální navýšení kapacity služby Search](search-capacity-planning.md)
| Služba Search je opravena pro aktualizaci služby nebo je uprostřed rekonfigurace topologie. | Nepovedlo se navázat spojení s indexem aktualizace. Služba Search je momentálně mimo provoz. služba Search prochází přechodem. | Konfigurace služby s minimálně 3 replikami pro 99,9% dostupnost na jednu z [dokladů SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Selhání základního výpočetního/síťového prostředku (zřídka) | Nepovedlo se navázat spojení s indexem aktualizace. Došlo k neznámé chybě. | Nakonfigurujte indexery, které se [spustí podle plánu](search-howto-schedule-indexers.md) pro výběr ze stavu selhání.
| Požadavek na indexování provedený do cílového indexu nebyl potvrzen v časovém limitu kvůli problémům se sítí. | Nepovedlo se včas navázat spojení s indexem vyhledávání. | Nakonfigurujte indexery, které se [spustí podle plánu](search-howto-schedule-indexers.md) pro výběr ze stavu selhání. Dále zkuste snížit [velikost dávky](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters) indexeru, pokud tento chybový stav přetrvává.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"/>

## <a name="error-could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>Chyba: nepovedlo se indexovat dokument, protože data indexeru pro index nejsou platná.

Dokument byl načten a zpracován, ale kvůli neshodě v konfiguraci polí indexu a povaze dat extrahovaných indexerem nelze přidat do indexu vyhledávání. K tomu může dojít v důsledku:

| Důvod | Podrobnosti/příklad
| --- | ---
| Datový typ polí extrahovaných indexerem není kompatibilní s datovým modelem odpovídajícího pole cílového indexu. | Datové pole '_data_' v dokumentu s klíčem ' 888 ' má neplatnou hodnotu ' typu ' EDM. String ' '. Očekával se typ Collection (EDM. String). |
| Nepovedlo se extrahovat žádnou entitu JSON z řetězcové hodnoty. | Hodnotu typu EDM. String pole_data_nelze analyzovat jako objekt JSON. Chyba: po analýze hodnoty byl zjištěn neočekávaný znak: ' '. Cesta '_cesta_', řádek 1, pozice 3162. ' |
| Extrakce kolekce entit JSON z řetězcové hodnoty se nezdařila.  | Hodnotu typu EDM. String pole_data_nelze analyzovat jako pole JSON. Chyba: po analýze hodnoty byl zjištěn neočekávaný znak: ' '. Cesta ' [0] ', řádek 1, pozice 27. ' |
| Ve zdrojovém dokumentu byl zjištěn neznámý typ. | Neznámý typ_Unknown_nejde indexovat. |
| Ve zdrojovém dokumentu se použil nekompatibilní zápis pro geografické body. | Řetězcové literály Well bodu nejsou podporovány. Místo toho prosím použijte literály bodu injson. |

Ve všech těchto případech odkazujete na [podporované typy dat](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) a [mapování datových typů pro indexery](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) , abyste se ujistili, že schéma indexů sestavíte správně a že jste nastavili odpovídající [mapování polí indexeru](search-indexer-field-mappings.md). Chybová zpráva bude obsahovat podrobnosti, které mohou přispět ke sledování zdroje neshody.

<a name="could-not-process-document-within-indexer-max-run-time"/>

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>Chyba: zásady integrovaného sledování změn nelze použít, protože tabulka má složený primární klíč.

To platí pro tabulky SQL a obvykle se stane, když je klíč buď definovaný jako složený klíč, nebo, když tabulka definuje jedinečný clusterovaný index (jako v indexu SQL, nikoli Azure Search index). Hlavním důvodem je, že klíč atributu je upraven jako složený primární klíč v případě [jedinečného clusterovaného indexu](https://docs.microsoft.com/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver15). V takovém případě se ujistěte, že tabulka SQL neobsahuje jedinečný clusterovaný index nebo že pole klíče namapujete na pole, u kterého je zaručeno, že neobsahují duplicitní hodnoty.


## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Chyba: nepovedlo se zpracovat dokument v rámci maximální doby běhu indexeru.

K této chybě dochází, pokud indexer nemůže dokončit zpracování jednoho dokumentu ze zdroje dat v rámci povolené doby provádění. [Maximální doba](search-limits-quotas-capacity.md#indexer-limits) běhu je kratší, když se dovednosti používají. Pokud k této chybě dojde, pokud máte maxFailedItems nastavenou na jinou hodnotu než 0, indexer ho při budoucích spuštěních obejít, takže indexování může probíhat. Pokud si nemůžete dovolit přeskočit libovolný dokument, nebo pokud se tato chyba zobrazuje konzistentně, zvažte možnost rozdělit dokumenty na menší dokumenty, aby bylo možné provést částečný průběh v rámci jednoho spuštění indexeru.

<a name="could-not-project-document"/>

## <a name="error-could-not-project-document"></a>Chyba: dokument nelze zamítnout

K této chybě dochází, když se indexer pokouší o [projektování dat do znalostní databáze](knowledge-store-projection-overview.md) a při pokusu o provedení došlo k chybě.  Tato chyba může být konzistentní a fixablea nebo se může jednat o přechodné selhání s výstupní jímkou projekce, kterou možná budete muset počkat, a zkuste to znovu, aby se dalo vyřešit.  Tady je sada známých stavů selhání a možná řešení.

| Důvod | Podrobnosti/příklad | Rozlišení |
| --- | --- | --- |
| Nepovedlo se aktualizovat `'blobUri'` objektů BLOB projekce v kontejneru `'containerName'` |Zadaný kontejner neexistuje. | Indexer zkontroluje, jestli se zadaný kontejner dřív vytvořil, a v případě potřeby ho vytvoří, ale tuto kontrolu provede jenom jednou pro spuštění indexeru. Tato chyba znamená, že něco odstranilo kontejner po tomto kroku.  Tuto chybu můžete vyřešit tímto způsobem: ponechte si informace o svém účtu úložiště, počkejte, až se indexer dokončí, a pak znovu spusťte indexer. |
| Nepovedlo se aktualizovat `'blobUri'` objektů BLOB projekce v kontejneru `'containerName'` |Nelze zapsat data do přenosového připojení: vzdálený hostitel nuceně zavřel existující připojení. | Očekává se, že se jedná o přechodné selhání s Azure Storage, takže by se mělo vyřešit tak, že znovu spustíte indexer. Pokud se tato chyba vyskytne konzistentně, uložte prosím [lístek podpory](https://ms.portal.azure.com/#create/Microsoft.Support) , aby ho bylo možné dále prozkoumat.  |
| Řádek `'projectionRow'` v tabulce nelze aktualizovat `'tableName'` | Server je zaneprázdněný. | Očekává se, že se jedná o přechodné selhání s Azure Storage, takže by se mělo vyřešit tak, že znovu spustíte indexer. Pokud se tato chyba vyskytne konzistentně, uložte prosím [lístek podpory](https://ms.portal.azure.com/#create/Microsoft.Support) , aby ho bylo možné dále prozkoumat.  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"/>

## <a name="warning-skill-input-was-invalid"></a>Upozornění: vstup dovedností byl neplatný.
Vstup pro dovednost chyběl, nesprávný typ nebo je jinak neplatný. Zpráva upozornění bude označovat dopad:
1) Nepovedlo se spustit dovednost.
2) Byly provedeny dovednosti, ale mohou mít neočekávané výsledky.

Dovednosti v rozpoznávání mají požadované vstupy a volitelné vstupy. Například dovednost pro [extrakci klíčových frází](cognitive-search-skill-keyphrases.md) má dva požadované vstupy `text`, `languageCode`a žádné volitelné vstupy. Vlastní vstupy dovedností se považují za volitelné vstupy.

Pokud nějaké požadované vstupy chybí nebo pokud nějaký vstup není správného typu, dovednost se přeskočí a vygeneruje upozornění. Přeskočené dovednosti negenerují žádné výstupy, takže pokud jiné dovednosti využívají výstupy vynechaných dovedností, můžou generovat další upozornění.

Pokud chybí nepovinný vstup, dovednost se pořád spustí, ale může způsobit neočekávaný výstup z důvodu chybějícího vstupu.

V obou případech může být toto upozornění očekávané kvůli tvaru vašich dat. Například pokud máte dokument obsahující informace o lidech s poli `firstName`, `middleName`a `lastName`, můžete mít některé dokumenty, které pro `middleName`nemají položku. Pokud předáte `middleName` jako vstup do dovednosti v kanálu, očekává se, že tento vstup dovednosti může chybět určitou dobu. Budete muset vyhodnotit vaše data a scénář, abyste zjistili, jestli se v důsledku tohoto upozornění vyžaduje nějaká akce.

Pokud chcete zadat výchozí hodnotu pro případ chybějícího vstupu, můžete použít [podmíněnou dovednost](cognitive-search-skill-conditional.md) k vygenerování výchozí hodnoty a potom použít výstup [podmíněné dovednosti](cognitive-search-skill-conditional.md) jako vstup dovednosti.


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

| Důvod | Podrobnosti/příklad | Rozlišení |
| --- | --- | --- |
| Nesprávný vstup dovednosti je nesprávného typu. | "Požadovaný vstup dovedností nebyl očekávaného typu `String`. Název: `text`, zdroj: `/document/merged_content`. "  "Požadovaný vstup odbornosti nemá očekávaný formát. Název: `text`, zdroj: `/document/merged_content`. "  "Nelze iterovat přes `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`bez pole."  "Nelze vybrat `0` v `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`mimo pole" | Určité dovednosti očekávají vstupy konkrétního typu, například [mínění dovednost](cognitive-search-skill-sentiment.md) očekává, že `text` být řetězec. Pokud vstup Určuje hodnotu, která není typu řetězec, pak se dovednost nespustí a negeneruje žádné výstupy. Ujistěte se, že vaše datová sada má v typu stejné vstupní hodnoty, nebo použijte [vlastní dovednost webového rozhraní API](cognitive-search-custom-skill-web-api.md) k předzpracování vstupu. Pokud provádíte iteraci dovedností v poli, ověřte, že kontext dovednosti a vstup mají `*` ve správných pozicích. Pro pole by měl být obvykle jak kontext, tak vstupní zdroj `*`. |
| Chybí vstup dovedností. | Nebyl nalezen požadovaný vstup dovednosti. Název: `text`, zdroj: `/document/merged_content`"" chybí hodnota `/document/normalized_images/0/imageTags`. "  "Nelze vybrat `0` v `/document/pages` pole délky `0`." | Pokud se zobrazí všechny dokumenty s tímto upozorněním, pravděpodobně dojde k překlepu ve vstupních cestách a v cestě byste měli poklepat na název vlastnosti velká a malá písmena, `*` v cestě a zajistit, aby dokumenty ze zdroje dat poskytovaly požadované vstupy. |
| Vstup kódu pro jazyk dovednosti je neplatný. | `languageCode` pro zadávání dovedností má `X,Y,Z`následující kódy jazyka, minimálně jeden z nich je neplatný. | Další podrobnosti najdete [níže](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) . |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"/>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Upozornění: vstup dovedností ' languageCode ' má následující kódy jazyka ' X, Y, Z ', minimálně jeden z nich je neplatný.
Jedna nebo více hodnot předaných do volitelného `languageCode` vstupu odbornosti pro příjem dat se nepodporuje. Tato situace může nastat, Pokud předáváte výstup [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) k následným dovednostím a výstup se skládá z více jazyků, než jaké jsou podporované v těchto dovednostech.

Pokud víte, že je vaše datová sada v jednom jazyce, měli byste odebrat [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) `languageCode` a vstup odbornosti a místo toho použít pro tuto dovednost parametr dovednosti `defaultLanguageCode`, za předpokladu, že je jazyk pro tuto dovednost podporovaný.

Pokud víte, že vaše datová sada obsahuje několik jazyků, takže budete potřebovat [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) a `languageCode`, zvažte přidání [ConditionalSkill](cognitive-search-skill-conditional.md) pro odfiltrování textu s jazyky, které nejsou podporované před předáním textu do dovednosti pro příjem dat.  Tady je příklad toho, co může vypadat jako u EntityRecognitionSkill:

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

Tady jsou některé odkazy na aktuálně podporované jazyky pro každou dovednost, která může způsobit tuto chybovou zprávu:
* [Analýza textu podporované jazyky](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) (pro rozhraní [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md), [SentimentSkill](cognitive-search-skill-sentiment.md)a [PIIDetectionSkill](cognitive-search-skill-pii-detection.md))
* [Překladatelé podporované jazyky](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (pro [text TranslationSkill](cognitive-search-skill-text-translation.md))
* [SplitSkill textu](cognitive-search-skill-textsplit.md) Podporované jazyky: `da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"/>

## <a name="warning-skill-input-was-truncated"></a>Upozornění: vstup dovedností byl zkrácen.
Vnímání dovedností má omezení na délku textu, který lze analyzovat najednou. Pokud je textový vstup těchto dovedností nad tímto limitem, zkrátili jsme text tak, aby se dosáhlo limitu, a pak se na tomto zkráceném textu provede obohacení. To znamená, že se dovednost spustí, ale ne všechna vaše data.

V následujícím příkladu LanguageDetectionSkill může vstupní pole `'text'` aktivovat toto upozornění, pokud se překročí limit znaků. Omezení pro zadávání dovedností najdete v [dokumentaci k dovednostem](cognitive-search-predefined-skills.md).

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

Pokud chcete zajistit, aby byl veškerý text analyzován, zvažte použití [rozdělené dovednosti](cognitive-search-skill-textsplit.md).

<a name="web-api-skill-response-contains-warnings"/>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Upozornění: odpověď dovednosti webového rozhraní API obsahuje upozornění.
Indexer mohl v dovednosti spustit dovednost, ale odpověď z požadavku webového rozhraní API zjistila při spuštění upozornění. Přečtěte si upozornění, abyste porozuměli tomu, jak jsou vaše data ovlivněná a zda je akce nutná.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"/>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Upozornění: aktuální konfigurace indexeru nepodporuje přírůstkový průběh.

K tomuto upozornění dochází pouze u Cosmos DB zdrojů dat.

Přírůstkový průběh při indexování zajišťuje, že pokud je provádění indexeru přerušeno přechodnými chybami nebo časovým limitem, může indexer vyzvednutí, kde byl při příštím spuštění ponechán, místo nutnosti znovu indexovat celou kolekci od začátku. To je obzvláště důležité při indexování velkých kolekcí.

Možnost obnovit nedokončenou úlohu indexování je predikátem, ve kterém jsou pořízeny dokumenty seřazené podle sloupce `_ts`. Indexer používá časové razítko k určení, který dokument se má vybrat jako další. Pokud sloupec `_ts` chybí nebo pokud indexer nedokáže určit, jestli je vlastní dotaz seřazený podle IT, indexer začne na začátku a toto upozornění se zobrazí.

Toto chování je možné přepsat, což umožňuje přírůstkové průběh a potlačení tohoto upozornění pomocí vlastnosti `assumeOrderByHighWatermarkColumn` Configuration.

Další informace najdete v tématu [přírůstkový průběh a vlastní dotazy](search-howto-index-cosmosdb.md#IncrementalProgress).

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"/>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Upozornění: během projekce došlo ke ztrátě některých dat. Řádek X v tabulce Y má vlastnost řetězce Z, která byla příliš dlouhá.

[Služba Table Storage](https://azure.microsoft.com/services/storage/tables) má omezení, jak mohou být [vlastnosti velkých entit](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types) . Řetězce můžou mít 32 000 znaků nebo méně. Pokud je řádek s vlastností řetězce delší než 32 000 znaků, zachová se pouze prvních 32 000 znaků. Pokud chcete tento problém obejít, vyhněte se projekci řádků s řetězcovými vlastnostmi delšími než 32 000 znaků.

<a name="truncated-extracted-text-to-x-characters"/>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Upozornění: zkrácený extrahovaný text na X znaků
Indexery omezují, kolik textu lze z jednoho dokumentu extrahovat. Toto omezení závisí na cenové úrovni: 32 000 znaků pro úroveň Free, 64 000 pro Basic, 4 000 000 pro standard, 8 000 000 pro standard S2 a 16 000 000 pro standard S3. Zkrácený text nebude indexován. Chcete-li se tomuto upozornění vyhnout, zkuste rozdělit dokumenty s velkými objemy textu do několika menších dokumentů. 

Další informace najdete v tématu [omezení indexerů](search-limits-quotas-capacity.md#indexer-limits).

<a name="could-not-map-output-field-x-to-search-index"/>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Upozornění: výstupní pole ' X ' nelze namapovat na index vyhledávání
Mapování polí výstupu, které odkazují na neexistující nebo null data, vytvoří upozornění pro každý dokument a výsledkem bude prázdné pole indexu. Pokud chcete tento problém vyřešit, poklikejte na výstupní pole – mapování zdrojových cest pro možné překlepy nebo nastavte výchozí hodnotu pomocí [podmíněné dovednosti](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist).

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"/>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Upozornění: zásady detekce změny dat jsou nakonfigurovány tak, aby používaly klíčový sloupec X.
[Zásady detekce změn dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies) mají specifické požadavky pro sloupce, které používají ke zjištění změny. Jedním z těchto požadavků je, že tento sloupec se aktualizuje pokaždé, když se změní zdrojová položka. Dalším požadavkem je, aby nová hodnota pro tento sloupec byla větší než předchozí hodnota. Klíčové sloupce tento požadavek nesplňují, protože se při každé aktualizaci nezmění. Pokud chcete tento problém obejít, vyberte pro zásady zjišťování změn jiný sloupec.

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"/>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>Upozornění: text dokumentu vypadá jako kódovaný v kódování UTF-16, ale chybí mu znak pořadí bajtů.

[Režimy analýzy indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer#blob-configuration-parameters) musí před analýzou zjistit, jak je text kódovaný. Dvěma nejběžnějšími způsoby kódování textu jsou UTF-16 a UTF-8. UTF-8 je kódování s proměnlivou délkou, kde každý znak je dlouhý 1 bajt a 4 bajty. UTF-16 je kódování s pevnou délkou, kde každý znak je dlouhý 2 bajty. UTF-16 má dvě různé varianty, "big endian" a "Little endian". Kódování textu je určeno označením "znak pořadí bajtů", řady bajtů před textem.

| Encoding | Znak pořadí bajtů |
| --- | --- |
| UTF-16 big endian | 0xFE 0xFF |
| UTF-16 Little endian | 0xFF – 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

Pokud není k dispozici žádný znak pořadí bajtů, je text považován za kódování UTF-8.

Chcete-li toto upozornění obejít, určete, co je kódování textu tohoto objektu blob, a přidejte odpovídající značku pořadí bajtů.

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"/>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>Upozornění: Cosmos DB kolekce X má zásady opožděného indexování. Některá data mohou být ztracena.

Kolekce s zásadami [opožděného](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) indexování se nedají dotazovat konzistentně, takže v indexeru chybí data. Pokud chcete toto upozornění obejít, změňte zásady indexování na konzistentní.
