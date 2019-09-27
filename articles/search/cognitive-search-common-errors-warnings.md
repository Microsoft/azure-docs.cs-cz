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
ms.subservice: cognitive-search
ms.openlocfilehash: 4e31f818e96ae9f13e3ce8892e575318831848f6
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329380"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Běžné chyby a upozornění kanálu obohacení AI v Azure Search

Tento článek poskytuje informace a řešení běžných chyb a varování, se kterými se můžete setkat při obohacení AI v Azure Search.

## <a name="errors"></a>Chyby
Indexování se zastaví, když počet chyb překročí [' maxfaileditems '](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). Následující části vám pomůžou vyřešit chyby, takže indexování může pokračovat.

### <a name="could-not-read-document"></a>Nepovedlo se přečíst dokument.
Indexer nemohl přečíst dokument ze zdroje dat. K tomu může dojít v důsledku:

| Reason | Příklad | Action |
| --- | --- | --- |
| Nekonzistentní typy polí v různých dokumentech | Typ hodnoty se neshoduje s typem sloupce. Nepodařilo se uložit `'{47.6,-122.1}'` ve sloupci autoři.  Očekávaný typ je JArray. | Ujistěte se, že je typ každého pole stejný v různých dokumentech. Pokud je například prvním dokumentem `'startTime'` pole DateTime a druhý dokument je řetězec, tato chyba bude dosaženo. |
| chyby ze základní služby zdroje dat | (z Cosmos DB) `{"Errors":["Request rate is large"]}` | Zkontrolujte instanci úložiště, abyste měli jistotu, že je v pořádku. Možná budete muset upravit škálování nebo dělení na oddíly. |
| přechodné problémy | Při přijímání výsledků ze serveru došlo k chybě na úrovni přenosu. zprostředkovatele Poskytovatel protokolu TCP, chyba: 0 – vzdálený hostitel nuceně uzavřel existující připojení. | Občas dojde k neočekávaným potížím s připojením. Zkuste znovu spustit dokument v indexeru později. |

### <a name="could-not-extract-document-content"></a>Nelze extrahovat obsah dokumentu.
Indexer se zdrojem dat objektu BLOB nemohl extrahovat obsah z dokumentu (například soubor PDF). K tomu může dojít v důsledku:

| Reason | Příklad | Action |
| --- | --- | --- |
| objekt BLOB překračuje limit velikosti. | Dokument je `'150441598'` bajtů, což překračuje maximální velikost `'134217728'` bajtů pro extrakci dokumentů pro aktuální úroveň služby. | [chyby indexování objektů BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| objekt BLOB má nepodporovaný typ obsahu. | Dokument má nepodporovaný typ obsahu `'image/png'`. | [chyby indexování objektů BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| objekt BLOB je zašifrovaný. | Dokument se nepovedlo zpracovat – může být zašifrovaný nebo chráněný heslem. | [nastavení objektu BLOB](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| přechodné problémy | Chyba při zpracování objektu BLOB: Požadavek byl přerušen: Žádost se zrušila. | Občas dojde k neočekávaným potížím s připojením. Zkuste znovu spustit dokument v indexeru později. |

### <a name="could-not-parse-document"></a>Nepovedlo se analyzovat dokument.
Indexer si přečte dokument ze zdroje dat, ale při převodu obsahu dokumentu do zadaného schématu mapování polí došlo k problému. K tomu může dojít v důsledku:

| Reason | Příklad | Action |
| --- | --- | --- |
| Chybí klíč dokumentu. | Klíč dokumentu nemůže být chybí nebo je prázdný. | Zajistěte, aby všechny dokumenty měly platný klíč dokumentu. |
| Klíč dokumentu je neplatný. | Klíč dokumentu nemůže být delší než 1024 znaků. | Upravte klíč dokumentu tak, aby splňoval požadavky na ověření. |
| Mapování polí pro pole nelze použít. | Nelze použít funkci mapování `'functionName'` do pole `'fieldName'`. Pole nemůže mít hodnotu null. Název parametru: bajty | Překontrolujte [mapování polí](search-indexer-field-mappings.md) definovaná v indexeru a porovnejte je s daty zadaného pole neúspěšného dokumentu. Může být nutné změnit mapování polí nebo data dokumentu. |
| Nepodařilo se přečíst hodnotu pole. | Nelze načíst hodnotu sloupce `'fieldName'` v indexu `'fieldIndex'`. Při přijímání výsledků ze serveru došlo k chybě na úrovni přenosu. zprostředkovatele Poskytovatel protokolu TCP, chyba: 0 – vzdálený hostitel nuceně zavřel existující připojení.) | Tyto chyby jsou obvykle způsobeny neočekávanými problémy s připojením ke zdrojové službě zdroje dat. Zkuste znovu spustit dokument v indexeru později. |

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