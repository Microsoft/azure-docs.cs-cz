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
ms.openlocfilehash: 18befbfb924129518ac32a7fdddaa9ee573840b0
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936489"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Běžné chyby a upozornění kanálu obohacení AI v Azure Search

Tento článek poskytuje informace a řešení běžných chyb a varování, se kterými se můžete setkat při obohacení AI v Azure Search.

## <a name="errors"></a>Vyskytl
Indexování se zastaví, když počet chyb překročí [' maxfaileditems '](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). Následující části vám pomůžou vyřešit chyby, takže indexování může pokračovat.

### <a name="could-not-read-document"></a>Nepovedlo se přečíst dokument.
Indexer nemohl přečíst dokument ze zdroje dat. K tomu může dojít v důsledku:

| Důvod | Příklad | Akce |
| --- | --- | --- |
| Nekonzistentní typy polí v různých dokumentech | Typ hodnoty se neshoduje s typem sloupce. Nepodařilo se uložit `'{47.6,-122.1}'` ve sloupci autoři.  Očekávaný typ je JArray. | Ujistěte se, že je typ každého pole stejný v různých dokumentech. Pokud je například prvním dokumentem `'startTime'` pole DateTime a druhý dokument je řetězec, tato chyba bude dosaženo. |
| chyby ze základní služby zdroje dat | (z Cosmos DB) `{"Errors":["Request rate is large"]}` | Zkontrolujte instanci úložiště, abyste měli jistotu, že je v pořádku. Možná budete muset upravit škálování nebo dělení na oddíly. |
| přechodné problémy | Při přijímání výsledků ze serveru došlo k chybě na úrovni přenosu. (poskytovatel: Zprostředkovatel TCP, chyba: 0 – existující připojení bylo vynuceně ukončeno vzdáleným hostitelem | Občas dojde k neočekávaným potížím s připojením. Zkuste znovu spustit dokument v indexeru později. |

### <a name="could-not-extract-document-content"></a>Nelze extrahovat obsah dokumentu.
Indexer se zdrojem dat objektu BLOB nemohl extrahovat obsah z dokumentu (například soubor PDF). K tomu může dojít v důsledku:

| Důvod | Příklad | Akce |
| --- | --- | --- |
| objekt BLOB překračuje limit velikosti. | Dokument je `'150441598'` bajtů, což překračuje maximální velikost `'134217728'` bajtů pro extrakci dokumentů pro aktuální úroveň služby. | [chyby indexování objektů BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| objekt BLOB má nepodporovaný typ obsahu. | Dokument má nepodporovaný typ obsahu `'image/png'`. | [chyby indexování objektů BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| objekt BLOB je zašifrovaný. | Dokument se nepovedlo zpracovat – může být zašifrovaný nebo chráněný heslem. | [nastavení objektu BLOB](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| přechodné problémy | Chyba při zpracování objektu BLOB: požadavek byl přerušen: požadavek byl zrušen. | Občas dojde k neočekávaným potížím s připojením. Zkuste znovu spustit dokument v indexeru později. |

### <a name="could-not-parse-document"></a>Nepovedlo se analyzovat dokument.
Indexer si přečte dokument ze zdroje dat, ale při převodu obsahu dokumentu do zadaného schématu mapování polí došlo k problému. K tomu může dojít v důsledku:

| Důvod | Příklad | Akce |
| --- | --- | --- |
| Chybí klíč dokumentu. | Klíč dokumentu nemůže být chybí nebo je prázdný. | Zajistěte, aby všechny dokumenty měly platný klíč dokumentu. |
| Klíč dokumentu je neplatný. | Klíč dokumentu nemůže být delší než 1024 znaků. | Upravte klíč dokumentu tak, aby splňoval požadavky na ověření. |
| Mapování polí pro pole nelze použít. | Nelze použít funkci mapování `'functionName'` do pole `'fieldName'`. Pole nemůže mít hodnotu null. Název parametru: bajty | Překontrolujte [mapování polí](search-indexer-field-mappings.md) definovaná v indexeru a porovnejte je s daty zadaného pole neúspěšného dokumentu. Může být nutné změnit mapování polí nebo data dokumentu. |
| Nepodařilo se přečíst hodnotu pole. | Nelze načíst hodnotu sloupce `'fieldName'` v indexu `'fieldIndex'`. Při přijímání výsledků ze serveru došlo k chybě na úrovni přenosu. (poskytovatel: poskytovatel protokolu TCP, chyba: 0 – existující připojení bylo vynuceně ukončeno vzdáleným hostitelem.) | Tyto chyby jsou obvykle způsobeny neočekávanými problémy s připojením ke zdrojové službě zdroje dat. Zkuste znovu spustit dokument v indexeru později. |

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

### <a name="skill-did-not-execute-within-the-time-limit"></a>Neproběhla dovednost v časovém limitu.
Existují dva případy, kdy se může zobrazit tato chybová zpráva, z nichž každá by měla být zpracována jinak. Postupujte podle pokynů níže v závislosti na tom, jaká dovednost vrátila tuto chybu za vás.

#### <a name="built-in-cognitive-service-skills"></a>Integrované dovednosti služby pro rozpoznávání
Mnohé z vestavěných dovedností rozpoznávání, jako je rozpoznávání jazyka, rozpoznávání entit nebo rozpoznávání OCR, jsou zajištěny koncovým bodem rozhraní API služby vnímání. Někdy dochází k přechodným problémům s těmito koncovými body a vyprší časový limit žádosti. V případě přechodných problémů nedochází k nápravě, s výjimkou čekání a akci opakujte. Jako zmírnění zvažte nastavení indexeru, který se bude [spouštět podle plánu](search-howto-schedule-indexers.md). Naplánované indexování se ponechá tam, kde skončila. Za předpokladu, že dojde k vyřešení přechodných problémů, by mělo být při dalším naplánovaném spuštění možné pokračovat v indexování a zpracování dovedností pro rozpoznávání.

#### <a name="custom-skills"></a>Vlastní dovednosti
Pokud narazíte na chybu s časovým limitem s vlastní dovedností, kterou jste vytvořili, můžete si vyzkoušet několik věcí. Nejdřív zkontrolujte svoji vlastní dovednost a ujistěte se, že není zablokovaná v nekonečné smyčce a že vrátí výsledek konzistentně. Jakmile ověříte, že se jedná o tento případ, určete, co je doba provádění vaší dovednosti. Pokud jste nezadali explicitně hodnotu `timeout` na vlastní definici dovednosti, pak je výchozí `timeout` 30 sekund. Pokud nechcete, aby vaše dovednosti běžela, můžete zadat vyšší hodnotu @no__t 0 na vlastní definici dovedností, pokud není dostatečná doba 30 sekund. Tady je příklad vlastní definice dovednosti, kde časový limit je nastavený na 90 sekund:

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

##  <a name="warnings"></a>Upozornění
Upozornění neukončí indexování, ale označují podmínky, které by mohly vést k neočekávaným výsledkům. Bez ohledu na to, jestli provedete akci, nebo ne, závisí na datech a vašem scénáři.

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