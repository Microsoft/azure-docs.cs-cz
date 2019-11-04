---
title: Indexování jednoho objektu blob do mnoha dokumentů indexu hledání z Azure Blob indexeru pro fulltextové vyhledávání
titleSuffix: Azure Cognitive Search
description: Procházení objektů blob Azure pro obsah textu pomocí indexeru objektů BLOB služby Azure Cognitive Search. Každý objekt BLOB může poskytovat jeden nebo více dokumentů indexu hledání.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 863d38f6ea0f071a1c1a6678d025ec5b37a306dc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466427"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indexování objektů BLOB pro vytváření více dokumentů hledání
Ve výchozím nastavení bude indexer objektů BLOB zacházet s obsahem objektu BLOB jako s jedním vyhledávacím dokumentem. Některé hodnoty **parsingMode** podporují scénáře, kdy jednotlivý objekt BLOB může mít za následek více dokumentů pro hledání. Různé typy **parsingMode** , které umožňují indexeru extrahovat více než jeden dokument hledání z objektu BLOB:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Klíč dokumentu 1: n
Každý dokument, který se zobrazuje v indexu služby Azure Kognitivní hledání, je jednoznačně identifikovaný klíčem dokumentu. 

Pokud není zadaný žádný režim analýzy a pokud pro klíčové pole v indexu není k dispozici žádné explicitní mapování, Kognitivní hledání automaticky [namapovat](search-indexer-field-mappings.md) vlastnost `metadata_storage_path` jako klíč. Toto mapování zajišťuje, že se každý objekt BLOB zobrazí jako odlišný vyhledávací dokument.

Při použití některého z výše uvedených režimů analýzy jeden objekt BLOB namapuje na "mnoho" vyhledávacích dokumentů, takže klíč dokumentu je výhradně založený na metadatech objektu BLOB nevhodný. K překonání tohoto omezení Azure Kognitivní hledání umožňuje vygenerovat klíč dokumentu "1: n" pro každou jednotlivou entitu extrahovanou z objektu BLOB. Tato vlastnost má název `AzureSearch_DocumentKey` a je přidána do každé z nich extrahované z objektu BLOB. Hodnota této vlastnosti zaručuje, že pro každou jednotlivou entitu _napříč objekty blob_ je jedinečná a entity se zobrazí jako samostatné dokumenty hledání.

Ve výchozím nastavení platí, že pokud nejsou zadána explicitní mapování polí pro pole index klíče, je `AzureSearch_DocumentKey` k němu namapována pomocí funkce mapování `base64Encode` pole.

## <a name="example"></a>Příklad:
Předpokládejme, že máte definici indexu s následujícími poli:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

A váš kontejner objektů BLOB obsahuje objekty BLOB s následující strukturou:

_Blob1. JSON_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2. JSON_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Když vytvoříte indexer a nastavíte **parsingMode** na `jsonLines` – bez zadání explicitních mapování polí pro klíčové pole, použije se implicitně následující mapování.
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Výsledkem tohoto nastavení je index služby Azure Kognitivní hledání obsahující následující informace (pro zkrácení se zkrátilo ID kódované v kódování Base64).

| id | teplota | tlak | časové razítko |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1\. místo | 1\. místo | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapování vlastních polí pro pole indexového klíče

Za předpokladu, že v předchozím příkladu je stejná definice indexu, řekněme, že váš kontejner objektů BLOB obsahuje objekty BLOB s následující strukturou:

_Blob1. JSON_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2. JSON_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Když vytvoříte indexer s `delimitedText` **parsingMode**, může to mít přirozené nastavení funkce mapování polí na pole Key takto:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Toto mapování však nebude mít za následek 4 dokumenty _, které se_ zobrazují v indexu, protože pole `recordid` není v objektech _BLOB_jedinečné. Proto doporučujeme použít implicitní mapování polí použité z vlastnosti `AzureSearch_DocumentKey` na pole index klíče pro režimy analýzy "1: n".

Pokud chcete nastavit explicitní mapování polí, ujistěte se, že je _sourceField_ jedinečný pro každou jednotlivou entitu **napříč všemi objekty blob**.

> [!NOTE]
> Přístup, který používá `AzureSearch_DocumentKey` o zajištění jedinečnosti na extrahovanou entitu, se může změnit, a proto byste neměli spoléhat na jeho hodnotu pro potřeby vaší aplikace.

## <a name="next-steps"></a>Další kroky

Pokud jste se základní strukturou a pracovním postupem indexování objektů BLOB už neseznámili, měli byste nejdřív projít část [indexování Azure Blob Storage Azure Search](search-howto-index-json-blobs.md) . Další informace o režimech analýzy pro různé typy obsahu objektů BLOB najdete v následujících článcích.

> [!div class="nextstepaction"]
> [Indexování objektů BLOB CSV](search-howto-index-csv-blobs.md) 
> [INDEXOVÁNÍ objektů BLOB JSON](search-howto-index-json-blobs.md)
