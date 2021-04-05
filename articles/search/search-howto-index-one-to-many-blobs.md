---
title: Indexování objektů BLOB obsahujících více dokumentů
titleSuffix: Azure Cognitive Search
description: Procházejte objekty blob Azure pro textový obsah pomocí indexeru objektů BLOB v Azure Kognitivní hledání, kde každý objekt BLOB může vracet jeden nebo několik dokumentů indexu hledání.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ea22b3cff8a0303c4e6698db4090df0f5ed2153a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99430976"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indexování objektů BLOB pro vytváření více dokumentů hledání

Ve výchozím nastavení bude indexer objektů BLOB zacházet s obsahem objektu BLOB jako s jedním vyhledávacím dokumentem. Pokud chcete v indexu vyhledávání podrobnější reprezentace objektu blob, můžete nastavit hodnoty **parsingMode** pro vytvoření více dokumentů hledání z jednoho objektu BLOB. K **parsingMode** hodnotám, které vyplývají z mnoha dokumentů `delimitedText` pro hledání, patří (pro [CSV](search-howto-index-csv-blobs.md)) a `jsonArray` nebo `jsonLines` (pro [JSON](search-howto-index-json-blobs.md)).

Když použijete některý z těchto režimů analýzy, nové vyhledávané dokumenty musí mít jedinečné klíče dokumentů a při určování, odkud pochází tato hodnota, dojde k problému. Nadřazený objekt BLOB má alespoň jednu jedinečnou hodnotu ve formátu `metadata_storage_path property` , ale pokud tuto hodnotu přispívá do více než jednoho vyhledávacího dokumentu, klíč již není v indexu jedinečný.

Pro vyřešení tohoto problému indexer objektů BLOB vytvoří `AzureSearch_DocumentKey` jedinečnou identifikaci každého podřízeného vyhledávacího dokumentu vytvořeného z jednoho nadřazeného objektu BLOB. Tento článek vysvětluje, jak tato funkce funguje.

## <a name="one-to-many-document-key"></a>Klíč dokumentu 1: n

Každý dokument, který se zobrazuje v indexu služby Azure Kognitivní hledání, je jednoznačně identifikovaný klíčem dokumentu. 

Pokud není zadaný žádný režim analýzy a v definici indexeru pro klíč dokumentu hledání není žádné [explicitní mapování polí](search-indexer-field-mappings.md) , indexer objektů BLOB se automaticky mapuje `metadata_storage_path property` jako klíč dokumentu. Toto mapování zajistí, že se každý objekt BLOB zobrazí jako odlišný vyhledávací dokument, a uloží vás krok potřebného vytvořit toto mapování polí sami (obvykle se mapují automaticky jenom pole se stejnými názvy a typy).

Při použití některého z výše uvedených režimů analýzy jeden objekt BLOB namapuje na "mnoho" vyhledávacích dokumentů, takže klíč dokumentu je výhradně založený na metadatech objektu BLOB nevhodný. K překonání tohoto omezení Azure Kognitivní hledání umožňuje vygenerovat klíč dokumentu "1: n" pro každou jednotlivou entitu extrahovanou z objektu BLOB. Tato vlastnost má název AzureSearch_DocumentKey a je přidána do každé z nich extrahované z objektu BLOB. Hodnota této vlastnosti zaručuje, že pro každou jednotlivou entitu napříč objekty BLOB je jedinečná a entity se zobrazí jako samostatné dokumenty hledání.

Ve výchozím nastavení, pokud nejsou zadána explicitní mapování polí pro pole index klíče, `AzureSearch_DocumentKey` je k němu namapováno pomocí `base64Encode` funkce mapování pole.

## <a name="example"></a>Příklad

Předpokládejme, že máte definici indexu s následujícími poli:

+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

A váš kontejner objektů BLOB obsahuje objekty BLOB s následující strukturou:

_Blob1.jsna_

```json
{ "temperature": 100, "pressure": 100, "timestamp": "2020-02-13T00:00:00Z" }
{ "temperature" : 33, "pressure" : 30, "timestamp": "2020-02-14T00:00:00Z" }
```

_Blob2.jsna_

```json
{ "temperature": 1, "pressure": 1, "timestamp": "2019-01-12T00:00:00Z" }
{ "temperature" : 120, "pressure" : 3, "timestamp": "2017-05-11T00:00:00Z" }
```

Když vytvoříte indexer a nastavíte **parsingMode** na `jsonLines` -bez zadání explicitních mapování polí pro klíčové pole, použije se implicitně následující mapování.

```http
{
    "sourceFieldName" : "AzureSearch_DocumentKey",
    "targetFieldName": "id",
    "mappingFunction": { "name" : "base64Encode" }
}
```

Výsledkem tohoto nastavení je nejednoznačný klíč dokumentu podobný následujícímu obrázku (ID kódované v kódování Base64 se zkracuje pro zkrácení).

| ID | teplota | tlak | časové razítko |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2020-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2020-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2019-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2017-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapování vlastních polí pro pole indexového klíče

Za předpokladu, že je jako předchozí příklad definice indexu, Předpokládejme, že váš kontejner objektů BLOB obsahuje objekty BLOB s následující strukturou:

_Blob1.jsna_

```json
recordid, temperature, pressure, timestamp
1, 100, 100,"2019-02-13T00:00:00Z" 
2, 33, 30,"2019-02-14T00:00:00Z" 
```

_Blob2.jsna_

```json
recordid, temperature, pressure, timestamp
1, 1, 1,"2018-01-12T00:00:00Z" 
2, 120, 3,"2013-05-11T00:00:00Z" 
```

Při vytváření indexeru pomocí `delimitedText` **parsingMode** může být přirozené nastavit funkci mapování polí na klíčové pole následujícím způsobem:

```http
{
    "sourceFieldName" : "recordid",
    "targetFieldName": "id"
}
```

Toto mapování ale _nevede k tomu,_ aby se v indexu zobrazovaly 4 dokumenty, protože pole není v objektech `recordid` _BLOB_ jedinečné. Proto doporučujeme použít implicitní mapování polí použité z `AzureSearch_DocumentKey` vlastnosti na pole index klíče pro režimy analýzy "1: n".

Pokud chcete nastavit explicitní mapování polí, ujistěte se, že je _sourceField_ jedinečný pro každou jednotlivou entitu **napříč všemi objekty blob**.

> [!NOTE]
> Přístup, který se používá při `AzureSearch_DocumentKey` zajištění jedinečnosti na extrahovanou entitu, se může změnit, a proto byste neměli spoléhat na jeho hodnotu pro potřeby vaší aplikace.

## <a name="next-steps"></a>Další kroky

Pokud jste už neznáte základní strukturu a pracovní postup indexování objektů blob, měli byste nejdřív projít téma [indexování azure BLOB Storage s azure kognitivní hledání](search-howto-index-json-blobs.md) . Další informace o režimech analýzy pro různé typy obsahu objektů BLOB najdete v následujících článcích.

> [!div class="nextstepaction"]
> [Indexování objektů BLOB CSV](search-howto-index-csv-blobs.md) 
>  [Indexování objektů BLOB JSON](search-howto-index-json-blobs.md)
