---
title: Běžné chyby a upozornění – Azure Search
description: Tento článek poskytuje informace a řešení běžných chyb a varování, se kterými se můžete setkat při obohacení AI v Azure Search.
services: search
manager: heidist
author: amotley
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: abmotley
ms.openlocfilehash: 6455ac9dbe0933f6d46d1137e0a19dcc388d8c80
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2019
ms.locfileid: "73243049"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Běžné chyby a upozornění kanálu obohacení AI v Azure Search

Tento článek poskytuje informace a řešení běžných chyb a varování, se kterými se můžete setkat při obohacení AI v Azure Search.

## <a name="errors"></a>Chyby
Indexování se zastaví, když počet chyb překročí [' maxFailedItems '](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Pokud chcete, aby indexery ignorovaly tyto chyby (a přeskočíte "neúspěšné dokumenty"), zvažte aktualizaci `maxFailedItems` a `maxFailedItemsPerBatch`, jak je popsáno [zde](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers).

> [!NOTE]
> Každý neúspěšný dokument spolu s klíčem dokumentu (Pokud je k dispozici) se zobrazí jako chyba ve stavu provádění indexeru. [Rozhraní API indexu](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) můžete použít k ručnímu nahrání dokumentů později, pokud jste nastavili indexer k tolerování chyb.

Následující části vám pomůžou vyřešit chyby, takže indexování může pokračovat.

### <a name="could-not-read-document"></a>Nepovedlo se přečíst dokument.
Indexer nemohl přečíst dokument ze zdroje dat. K tomu může dojít v důsledku:

| Důvod | Příklad: | Akce |
| --- | --- | --- |
| Nekonzistentní typy polí v různých dokumentech | Typ hodnoty se neshoduje s typem sloupce. Nepodařilo se uložit `'{47.6,-122.1}'` ve sloupci autoři.  Očekávaný typ je JArray. | Ujistěte se, že je typ každého pole stejný v různých dokumentech. Pokud je například prvním dokumentem `'startTime'` pole DateTime a druhý dokument je řetězec, tato chyba bude dosaženo. |
| chyby ze základní služby zdroje dat | (z Cosmos DB) `{"Errors":["Request rate is large"]}` | Zkontrolujte instanci úložiště, abyste měli jistotu, že je v pořádku. Možná budete muset upravit škálování nebo dělení na oddíly. |
| přechodné problémy | Při přijímání výsledků ze serveru došlo k chybě na úrovni přenosu. (poskytovatel: Zprostředkovatel TCP, chyba: 0 – existující připojení bylo vynuceně ukončeno vzdáleným hostitelem | Občas dojde k neočekávaným potížím s připojením. Zkuste znovu spustit dokument v indexeru později. |

### <a name="could-not-extract-document-content"></a>Nelze extrahovat obsah dokumentu.
Indexer se zdrojem dat objektu BLOB nemohl extrahovat obsah z dokumentu (například soubor PDF). K tomu může dojít v důsledku:

| Důvod | Příklad: | Akce |
| --- | --- | --- |
| objekt BLOB překračuje limit velikosti. | Dokument je `'150441598'` bajtů, což překračuje maximální velikost `'134217728'` bajtů pro extrakci dokumentů pro aktuální úroveň služby. | [chyby indexování objektů BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| objekt BLOB má nepodporovaný typ obsahu. | Dokument má nepodporovaný typ obsahu `'image/png'`. | [chyby indexování objektů BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| objekt BLOB je zašifrovaný. | Dokument se nepovedlo zpracovat – může být zašifrovaný nebo chráněný heslem. | [nastavení objektu BLOB](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| přechodné problémy | Chyba při zpracování objektu BLOB: požadavek byl přerušen: požadavek byl zrušen. | Občas dojde k neočekávaným potížím s připojením. Zkuste znovu spustit dokument v indexeru později. |

### <a name="could-not-parse-document"></a>Nepovedlo se analyzovat dokument.
Indexer si přečte dokument ze zdroje dat, ale při převodu obsahu dokumentu do zadaného schématu mapování polí došlo k problému. K tomu může dojít v důsledku:

| Důvod | Příklad: | Akce |
| --- | --- | --- |
| Chybí klíč dokumentu. | Klíč dokumentu nemůže být chybí nebo je prázdný. | Zajistěte, aby všechny dokumenty měly platný klíč dokumentu. |
| Klíč dokumentu je neplatný. | Klíč dokumentu nemůže být delší než 1024 znaků. | Upravte klíč dokumentu tak, aby splňoval požadavky na ověření. |
| Mapování polí pro pole nelze použít. | Nelze použít funkci mapování `'functionName'` do pole `'fieldName'`. Pole nemůže mít hodnotu null. Název parametru: bajty | Překontrolujte [mapování polí](search-indexer-field-mappings.md) definovaná v indexeru a porovnejte je s daty zadaného pole neúspěšného dokumentu. Může být nutné změnit mapování polí nebo data dokumentu. |
| Nepodařilo se přečíst hodnotu pole. | Nelze načíst hodnotu sloupce `'fieldName'` v indexu `'fieldIndex'`. Při přijímání výsledků ze serveru došlo k chybě na úrovni přenosu. (poskytovatel: poskytovatel protokolu TCP, chyba: 0 – existující připojení bylo vynuceně ukončeno vzdáleným hostitelem.) | Tyto chyby jsou obvykle způsobeny neočekávanými problémy s připojením ke zdrojové službě zdroje dat. Zkuste znovu spustit dokument v indexeru později. |

### <a name="could-not-execute-skill"></a>Nepovedlo se spustit dovednost.
Indexer nemohl v dovednosti spustit dovednost.

| Důvod | Příklad: | Akce |
| --- | --- | --- |
| Problémy s přechodným připojením | Došlo k přechodné chybě. Zkuste to prosím znova později. | Občas dojde k neočekávaným potížím s připojením. Zkuste znovu spustit dokument v indexeru později. |
| Potenciální chyba produktu | Došlo k neočekávané chybě. | To indikuje neznámou třídu selhání a může znamenat, že došlo k chybě produktu. Požádejte o pomoc [lístek podpory](https://ms.portal.azure.com/#create/Microsoft.Support) . |
| Při provádění dovednosti došlo k chybě. | (Z dovednosti sloučení) Jedna nebo více hodnot posunutí bylo neplatných a nelze je analyzovat. Na konec textu byly vloženy položky. | K vyřešení problému použijte informace v chybové zprávě. Tento druh selhání bude vyžadovat akci, která se má vyřešit. |

### <a name="could-not-execute-skill-because-the-web-api-request-failed"></a>Nelze provést dovednost, protože požadavek webového rozhraní API se nezdařil.
Provádění dovedností se nezdařilo, protože volání webového rozhraní API se nezdařilo. K této třídě selhání obvykle dochází při použití vlastních dovedností. v takovém případě budete muset ladit vlastní kód pro vyřešení problému. Pokud místo toho dojde k chybě z předdefinované dovednosti, podrobnější informace najdete v chybové zprávě v tématu o řešení problému.

### <a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Nebylo možné provést dovednost, protože odpověď na dovednosti webového rozhraní API není platná.
Spuštění dovednosti se nezdařilo, protože volání webového rozhraní API vrátilo neplatnou odpověď. K této třídě selhání obvykle dochází při použití vlastních dovedností. v takovém případě budete muset ladit vlastní kód pro vyřešení problému. Pokud místo toho dojde k chybě z integrované dovednosti, požádejte o pomoc [lístek podpory](https://ms.portal.azure.com/#create/Microsoft.Support) .

### <a name="skill-did-not-execute-within-the-time-limit"></a>Neproběhla dovednost v časovém limitu.
Existují dva případy, kdy se může zobrazit tato chybová zpráva, z nichž každá by měla být zpracována jinak. Postupujte podle pokynů níže v závislosti na tom, jaká dovednost vrátila tuto chybu za vás.

#### <a name="built-in-cognitive-service-skills"></a>Integrované dovednosti služby pro rozpoznávání
Mnohé z vestavěných dovedností rozpoznávání, jako je rozpoznávání jazyka, rozpoznávání entit nebo rozpoznávání OCR, jsou zajištěny koncovým bodem rozhraní API služby vnímání. Někdy dochází k přechodným problémům s těmito koncovými body a vyprší časový limit žádosti. V případě přechodných problémů nedochází k nápravě, s výjimkou čekání a akci opakujte. Jako zmírnění zvažte nastavení indexeru, který se bude [spouštět podle plánu](search-howto-schedule-indexers.md). Naplánované indexování se ponechá tam, kde skončila. Za předpokladu, že dojde k vyřešení přechodných problémů, by mělo být při dalším naplánovaném spuštění možné pokračovat v indexování a zpracování dovedností pro rozpoznávání.

#### <a name="custom-skills"></a>Vlastní dovednosti
Pokud narazíte na chybu s časovým limitem s vlastní dovedností, kterou jste vytvořili, můžete si vyzkoušet několik věcí. Nejdřív zkontrolujte svoji vlastní dovednost a ujistěte se, že není zablokovaná v nekonečné smyčce a že vrátí výsledek konzistentně. Jakmile ověříte, že se jedná o tento případ, určete, co je doba provádění vaší dovednosti. Pokud jste nezadali explicitně hodnotu `timeout` na vlastní definici dovednosti, pak je výchozí `timeout` 30 sekund. Pokud není dostatečná doba 30 sekund, než se vaše dovednost spustí, můžete zadat vyšší hodnotu `timeout` v definici vlastní dovednosti. Tady je příklad vlastní definice dovednosti, kde časový limit je nastavený na 90 sekund:

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

Maximální hodnota, kterou můžete nastavit pro parametr `timeout`, je 230 sekund.  Pokud vaše vlastní dovednost nemůže běžet konzistentně během 230 sekund, můžete zvážit omezení `batchSize` vlastní dovednosti, takže bude mít méně dokumentů pro zpracování v rámci jednoho spuštění.  Pokud jste již nastavili `batchSize` na 1, bude nutné přepište dovednost, aby bylo možné ji spustit za méně než 230 sekund, nebo jinak rozdělit do několika vlastních dovedností, aby čas spuštění pro jednu vlastní dovednost byl maximálně 230 sekund. Další informace najdete v [dokumentaci k vlastním dovednostím](cognitive-search-custom-skill-web-api.md) .

### <a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>Nepovedlo se `MergeOrUpload` | dokument `Delete` do indexu hledání

Dokument byl načten a zpracován, ale indexer ho nemohl přidat do indexu vyhledávání. K tomu může dojít v důsledku:

| Důvod | Příklad: | Akce |
| --- | --- | --- |
| Termín v dokumentu je větší než [limit 32 KB](search-limits-quotas-capacity.md#api-request-limits) . | Pole obsahuje termín, který je příliš velký. | Tomuto omezení se můžete vyhnout tím, že zajistíte, že pole není nakonfigurováno jako filtrovatelné, plošky nebo seřaditelné.
| Dokument je větší než [maximální velikost požadavku rozhraní API](search-limits-quotas-capacity.md#api-request-limits) . | Dokument je moc velký a nedá se indexovat. | [Indexování velkých datových sad](search-howto-large-index.md)
| Problémy s připojením k cílovému indexu (které přetrvávají po opakování), protože služba je pod jiným zatížením, jako je například dotazování nebo indexování. | Nepovedlo se navázat spojení s indexem aktualizace. Služba vyhledávání je zatížená velkým zatížením. | [Horizontální navýšení kapacity služby Search](search-capacity-planning.md)
| Služba Search je opravena pro aktualizaci služby nebo je uprostřed rekonfigurace topologie. | Nepovedlo se navázat spojení s indexem aktualizace. Služba Search je momentálně mimo provoz. služba Search prochází přechodem. | Konfigurace služby s minimálně 3 replikami pro 99,9% dostupnost na jednu z [dokladů SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Selhání základního výpočetního/síťového prostředku (zřídka) | Nepovedlo se navázat spojení s indexem aktualizace. Došlo k neznámé chybě. | Nakonfigurujte indexery, které se [spustí podle plánu](search-howto-schedule-indexers.md) pro výběr ze stavu selhání.

### <a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>Nelze indexovat dokument, protože data indexeru k indexu byla neplatná.

Dokument byl načten a zpracován, ale kvůli neshodě v konfiguraci polí indexu a povaze dat extrahovaných indexerem nelze přidat do indexu vyhledávání. K tomu může dojít v důsledku:

| Důvod | Příklad:
| --- | ---
| Datový typ polí extrahovaných indexerem není kompatibilní s datovým modelem odpovídajícího pole cílového indexu. | Datové pole '_data_' v dokumentu s klíčem '_data_' má neplatnou hodnotu ' typu ' EDM. String ' '. Očekával se typ Collection (EDM. String). |
| Nepovedlo se extrahovat žádnou entitu JSON z řetězcové hodnoty. | Hodnotu typu EDM. String pole_data_nelze analyzovat jako objekt JSON. Chyba: po analýze hodnoty byl zjištěn neočekávaný znak: ' '. Cesta '_cesta_', řádek 1, pozice 3162. ' |
| Extrakce kolekce entit JSON z řetězcové hodnoty se nezdařila.  | Hodnotu typu EDM. String pole_data_nelze analyzovat jako pole JSON. Chyba: po analýze hodnoty byl zjištěn neočekávaný znak: ' '. Cesta ' [0] ', řádek 1, pozice 27. ' |
| Ve zdrojovém dokumentu byl zjištěn neznámý typ. | Neznámý typ_Unknown_nejde indexovat. |
| Ve zdrojovém dokumentu se použil nekompatibilní zápis pro geografické body. | Řetězcové literály Well bodu nejsou podporovány. Místo toho prosím použijte literály bodu injson. |

Ve všech těchto případech odkazujete na [podporované datové typy (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) a [mapování datových typů pro indexery v Azure Search](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) , abyste se ujistili, že schéma indexu správně sestavíte a že jste nastavili odpovídající [mapování polí indexeru](search-indexer-field-mappings.md). Chybová zpráva bude obsahovat podrobnosti, které mohou přispět ke sledování zdroje neshody.

### <a name="could-not-process-document-within-indexer-max-run-time"></a>Nelze zpracovat dokument v rámci maximální doby běhu indexeru

K této chybě dochází, pokud indexer nemůže dokončit zpracování jednoho dokumentu ze zdroje dat v rámci povolené doby provádění. [Maximální doba](search-limits-quotas-capacity.md#indexer-limits) běhu je kratší, když se dovednosti používají. Pokud k této chybě dojde, pokud máte maxFailedItems nastavenou na jinou hodnotu než 0, indexer ho při budoucích spuštěních obejít, takže indexování může probíhat. Pokud si nemůžete dovolit přeskočit libovolný dokument, nebo pokud se tato chyba zobrazuje konzistentně, zvažte možnost rozdělit dokumenty na menší dokumenty, aby bylo možné provést částečný průběh v rámci jednoho spuštění indexeru.

##  <a name="warnings"></a>Varování
Upozornění neukončí indexování, ale označují podmínky, které by mohly vést k neočekávaným výsledkům. Bez ohledu na to, jestli provedete akci, nebo ne, závisí na datech a vašem scénáři.

### <a name="could-not-execute-skill-because-a-skill-input-was-invalid"></a>Nelze provést dovednost, protože vstup odbornosti byl neplatný.
Indexer nemohl v dovednosti spustit dovednost, protože chybí vstup pro dovednost, nesprávný typ nebo jinak neplatné.

Dovednosti v rozpoznávání mají požadované vstupy a volitelné vstupy. Například dovednost pro [extrakci klíčových frází](cognitive-search-skill-keyphrases.md) má dva požadované vstupy `text`, `languageCode` a žádné volitelné vstupy. Pokud jsou požadované vstupy neplatné, dovednost se přeskočí a vygeneruje upozornění. Přeskočené dovednosti negenerují žádné výstupy, takže pokud jiné dovednosti využívají výstupy vynechaných dovedností, můžou generovat další upozornění.

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

| Důvod | Příklad: | Akce |
| --- | --- | --- |
| Nesprávný vstup dovednosti je nesprávného typu. | Požadovaný vstup `X` dovednosti není očekávaného typu `String`. Požadovaný vstup `X` dovednosti není v očekávaném formátu. | Určité dovednosti očekávají vstupy konkrétního typu, například [mínění dovednost](cognitive-search-skill-sentiment.md) očekává, že `text` být řetězec. Pokud vstup Určuje hodnotu, která není typu řetězec, pak se dovednost nespustí a negeneruje žádné výstupy. Ujistěte se, že vaše datová sada má v typu stejné vstupní hodnoty, nebo použijte [vlastní dovednost webového rozhraní API](cognitive-search-custom-skill-web-api.md) k předzpracování vstupu. Pokud provádíte iteraci dovedností v poli, ověřte, že kontext dovednosti a vstup mají `*` ve správných pozicích. Pro pole by měl být obvykle jak kontext, tak vstupní zdroj `*`. |
| Chybí vstup dovedností. | `X` chybí požadovaný vstup pro dovednost. | Pokud se zobrazí všechny dokumenty s tímto upozorněním, pravděpodobně dojde k překlepu ve vstupních cestách a v cestě byste měli poklepat na název vlastnosti velká a malá písmena, `*` v cestě a dokumenty ze zdroje dat definovat požadované vstupy. |
| Vstup kódu pro jazyk dovednosti je neplatný. | `languageCode` pro zadávání dovedností má `X,Y,Z`následující kódy jazyka, minimálně jeden z nich je neplatný. | Další podrobnosti najdete [níže](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) . |

### <a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Vstup dovedností ' languageCode ' má následující kódy jazyka ' X, Y, Z ', minimálně jeden z nich je neplatný.
Jedna nebo více hodnot předaných do volitelného vstupu `languageCode` dovedností pro příjem dat není podporováno. Tato situace může nastat, Pokud předáváte výstup [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) k následným dovednostím a výstup se skládá z více jazyků, než jaké jsou podporované v těchto dovednostech.

Pokud víte, že je vaše datová sada v jednom jazyce, měli byste odebrat [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) a vstup dovedností `languageCode` a místo toho použít pro tuto dovednost parametr dovednosti `defaultLanguageCode`, za předpokladu, že je jazyk podporován pro tuto dovednost.

Pokud víte, že vaše datová sada obsahuje více jazyků, takže potřebujete [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) a `languageCode`, zvažte přidání [ConditionalSkill](cognitive-search-skill-conditional.md) pro odfiltrování textu s jazyky, které nejsou podporované před předáním do text na dovednost pro příjem dat.  Tady je příklad toho, co může vypadat jako u EntityRecognitionSkill:

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
* [Analýza textu podporované jazyky](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) (pro [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)a [SentimentSkill](cognitive-search-skill-sentiment.md))
* [Překladatelé podporované jazyky](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (pro [text TranslationSkill](cognitive-search-skill-text-translation.md))
* [SplitSkill textu](cognitive-search-skill-textsplit.md) Podporované jazyky: `da, de, en, es, fi, fr, it, ko, pt`

### <a name="skill-input-was-truncated"></a>Vstup dovedností byl zkrácen.
Vnímání dovedností má omezení na délku textu, který lze analyzovat najednou. Pokud je textový vstup těchto dovedností nad tímto limitem, zkrátili jsme text tak, aby se dosáhlo limitu, a pak se na tomto zkráceném textu provede obohacení. To znamená, že se dovednost spustí, ale ne všechna vaše data.

V následujícím příkladu LanguageDetectionSkill může vstupní pole `'text'` aktivovat toto upozornění, pokud překročí limit znaků. Omezení pro zadávání dovedností najdete v [dokumentaci k dovednostem](cognitive-search-predefined-skills.md).

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

### <a name="web-api-skill-response-contains-warnings"></a>Reakce na dovednosti webového rozhraní API obsahuje upozornění
Indexer mohl v dovednosti spustit dovednost, ale odpověď z požadavku webového rozhraní API zjistila při spuštění upozornění. Přečtěte si upozornění, abyste porozuměli tomu, jak jsou vaše data ovlivněná a zda je akce nutná.

### <a name="the-current-indexer-configuration-does-not-support-incremental-progress"></a>Aktuální konfigurace indexeru nepodporuje přírůstkový průběh.
K tomuto upozornění dochází pouze u Cosmos DB zdrojů dat.

Přírůstkový průběh při indexování zajišťuje, že pokud je provádění indexeru přerušeno přechodnými chybami nebo časovým limitem, může indexer vyzvednutí, kde byl při příštím spuštění ponechán, místo nutnosti znovu indexovat celou kolekci od začátku. To je obzvláště důležité při indexování velkých kolekcí.

Možnost obnovit nedokončenou úlohu indexování je predikátem, ve kterém jsou pořízeny dokumenty seřazené podle sloupce `_ts`. Indexer používá časové razítko k určení, který dokument se má vybrat jako další. Pokud sloupec `_ts` chybí nebo pokud indexer nedokáže určit, jestli je vlastní dotaz seřazený podle IT, indexer začne na začátku a toto upozornění se zobrazí.

Toto chování je možné přepsat, což umožňuje přírůstkové průběh a potlačení tohoto upozornění pomocí vlastnosti `assumeOrderByHighWatermarkColumn` Configuration.

[Další informace o Cosmos DB přírůstkovém průběhu a vlastních dotazech.](https://go.microsoft.com/fwlink/?linkid=2099593)