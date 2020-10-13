---
title: Indexování objektů BLOB obsahujících více dokumentů
titleSuffix: Azure Cognitive Search
description: Procházejte objekty blob Azure pro textový obsah pomocí indexeru objektů BLOB v Azure Kognitivní hledání, kde každý objekt BLOB může vracet jeden nebo několik dokumentů indexu hledání.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: e5a69525c4bd0717c0561bc61ee3c52aa68e1c9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91533957"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indexování objektů BLOB pro vytváření více dokumentů hledání
Ve výchozím nastavení bude indexer objektů BLOB zacházet s obsahem objektu BLOB jako s jedním vyhledávacím dokumentem. Některé hodnoty **parsingMode** podporují scénáře, kdy jednotlivý objekt BLOB může mít za následek více dokumentů pro hledání. Různé typy **parsingMode** , které umožňují indexeru extrahovat více než jeden dokument hledání z objektu BLOB:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Klíč dokumentu 1: n
Každý dokument, který se zobrazuje v indexu služby Azure Kognitivní hledání, je jednoznačně identifikovaný klíčem dokumentu. 

Pokud není zadaný žádný režim analýzy a pokud pro klíčové pole v indexu není k dispozici žádné explicitní mapování, Kognitivní hledání se vlastnost [maps](search-indexer-field-mappings.md) automaticky mapuje `metadata_storage_path` jako klíč. Toto mapování zajišťuje, že se každý objekt BLOB zobrazí jako odlišný vyhledávací dokument.

Při použití některého z výše uvedených režimů analýzy jeden objekt BLOB namapuje na "mnoho" vyhledávacích dokumentů, takže klíč dokumentu je výhradně založený na metadatech objektu BLOB nevhodný. K překonání tohoto omezení Azure Kognitivní hledání umožňuje vygenerovat klíč dokumentu "1: n" pro každou jednotlivou entitu extrahovanou z objektu BLOB. Tato vlastnost je pojmenována `AzureSearch_DocumentKey` a přidána do každé z nich vyjmuté z objektu BLOB. Hodnota této vlastnosti zaručuje, že pro každou jednotlivou entitu _napříč objekty blob_ je jedinečná a entity se zobrazí jako samostatné dokumenty hledání.

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
    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }
```

_Blob2.jsna_

```json
    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }
```

Když vytvoříte indexer a nastavíte **parsingMode** na `jsonLines` -bez zadání explicitních mapování polí pro klíčové pole, použije se implicitně následující mapování.

```http
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }
```

Výsledkem tohoto nastavení je index služby Azure Kognitivní hledání obsahující následující informace (pro zkrácení se zkrátilo ID kódované v kódování Base64).

| ID | teplota | tlak | časové razítko |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapování vlastních polí pro pole indexového klíče

Za předpokladu, že v předchozím příkladu je stejná definice indexu, řekněme, že váš kontejner objektů BLOB obsahuje objekty BLOB s následující strukturou:

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

Při vytváření indexeru pomocí `delimitedText` **parsingMode**může být přirozené nastavit funkci mapování polí na klíčové pole následujícím způsobem:

```http
    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }
```

Toto mapování ale _nevede k tomu,_ aby se v indexu zobrazovaly 4 dokumenty, protože pole není v objektech `recordid` _BLOB_jedinečné. Proto doporučujeme použít implicitní mapování polí použité z `AzureSearch_DocumentKey` vlastnosti na pole index klíče pro režimy analýzy "1: n".

Pokud chcete nastavit explicitní mapování polí, ujistěte se, že je _sourceField_ jedinečný pro každou jednotlivou entitu **napříč všemi objekty blob**.

> [!NOTE]
> Přístup, který se používá při `AzureSearch_DocumentKey` zajištění jedinečnosti na extrahovanou entitu, se může změnit, a proto byste neměli spoléhat na jeho hodnotu pro potřeby vaší aplikace.

## <a name="help-us-make-azure-cognitive-search-better"></a>Pomozte nám zdokonalit Azure Kognitivní hledání
Pokud máte na vylepšení žádosti o funkce nebo nápady, poskytněte svůj vstup na [UserVoice](https://feedback.azure.com/forums/263029-azure-search/). Pokud potřebujete pomoci s používáním stávající funkce, vystavte svůj dotaz na [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870).

## <a name="next-steps"></a>Další kroky

Pokud jste už neznáte základní strukturu a pracovní postup indexování objektů blob, měli byste nejdřív projít téma [indexování azure BLOB Storage s azure kognitivní hledání](search-howto-index-json-blobs.md) . Další informace o režimech analýzy pro různé typy obsahu objektů BLOB najdete v následujících článcích.

> [!div class="nextstepaction"]
> [Indexování objektů BLOB CSV](search-howto-index-csv-blobs.md) 
>  [Indexování objektů BLOB JSON](search-howto-index-json-blobs.md)
