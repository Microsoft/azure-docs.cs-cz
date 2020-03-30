---
title: Indexování objektů BLOB obsahujících více dokumentů
titleSuffix: Azure Cognitive Search
description: Procházení objektů BLOB Azure pro textový obsah pomocí Azure Congitive Search Blob indexer, kde každý objekt blob může přinést jeden nebo více dokumentů indexu vyhledávání.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1840bda0ecc9462a5d8f796b616d728d0bb412f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112261"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indexování objektů BLOB za účelem vytvoření více vyhledávacích dokumentů
Ve výchozím nastavení bude indexer objektů blob považovat obsah objektu blob za jeden vyhledávací dokument. Některé **hodnoty parsingMode** podporují scénáře, kde může mít jednotlivý objekt blob za následek více vyhledávacích dokumentů. Různé typy **analýzyMode,** které umožňují indexer extrahovat více než jeden vyhledávací dokument z objektu blob jsou:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Klíč dokumentu 1:N
Každý dokument, který se zobrazí v indexu Azure Cognitive Search, je jednoznačně identifikován klíčem dokumentu. 

Pokud není zadán žádný režim analýzy a pokud neexistuje žádné explicitní mapování pro klíčové `metadata_storage_path` pole v indexu Azure Cognitive Search automaticky [mapuje](search-indexer-field-mappings.md) vlastnost jako klíč. Toto mapování zajišťuje, že každý objekt blob se zobrazí jako samostatný vyhledávací dokument.

Při použití některého z výše uvedených režimů analýzy se jeden objekt blob mapuje na "mnoho" vyhledávacích dokumentů, takže klíč dokumentu je výhradně založený na metadatech objektu blob nevhodný. K překonání tohoto omezení Azure Cognitive Search je schopen generovat klíč dokumentu "1:N" pro každou jednotlivou entitu extrahované z objektu blob. Tato vlastnost `AzureSearch_DocumentKey` je pojmenována a je přidána do každé jednotlivé entity extrahované z objektu blob. Hodnota této vlastnosti je zaručena jedinečný pro každou jednotlivou entitu _napříč objekty BLOB_ a entity se zobrazí jako samostatné vyhledávací dokumenty.

Ve výchozím nastavení, pokud nejsou zadána žádná explicitní `AzureSearch_DocumentKey` mapování polí pro pole `base64Encode` indexu klíče, je na něj mapována pomocí funkce mapování polí.

## <a name="example"></a>Příklad
Předpokládejme, že máte definici indexu s následujícími poli:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

A kontejner objektů blob má objekty BLOB s následující strukturou:

_Objekt Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Objekt Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Když vytvoříte indexer a nastavíte `jsonLines` **režim analýzy** na – bez zadání explicitního mapování polí pro klíčové pole bude implicitně použito následující mapování.
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Toto nastavení bude mít za následek index Azure Cognitive Search obsahující následující informace (base64 kódované id zkráceno pro stručnost)

| id | teplota | tlak | časové razítko |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Vlastní mapování polí pro pole klíče indexu

Za předpokladu, že stejné definice indexu jako v předchozím příkladu, řekněme, že kontejner objektů blob má objekty BLOB s následující strukturou:

_Objekt Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Objekt Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Když vytvoříte indexer `delimitedText` s **režimem analýzy**, může být přirozené nastavit funkci mapování polí na klíčové pole následujícím způsobem:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Toto mapování však _nebude_ mít za následek 4 `recordid` dokumenty zobrazeny v indexu, protože pole není jedinečný _napříč objekty BLOB_. Proto doporučujeme použít implicitní mapování polí aplikované z `AzureSearch_DocumentKey` vlastnosti na pole indexu klíče pro režimy analýzy 1:N.

Pokud chcete nastavit explicitní mapování polí, ujistěte se, že _sourceField_ je odlišný pro každou jednotlivou entitu **ve všech objektech BLOB**.

> [!NOTE]
> Přístup používaný `AzureSearch_DocumentKey` zajištění jedinečnosti na extrahované entity se může změnit, a proto byste neměli spoléhat na jeho hodnotu pro potřeby vaší aplikace.

## <a name="next-steps"></a>Další kroky

Pokud ještě nejste obeznámeni se základní strukturou a pracovním postupem indexování objektů blob, měli byste nejprve zkontrolovat [indexování azure blob storage pomocí Azure Cognitive Search.](search-howto-index-json-blobs.md) Další informace o režimech analýzy pro různé typy obsahu objektů blob najděte v následujících článcích.

> [!div class="nextstepaction"]
> [Indexování objektů BLOB](search-howto-index-csv-blobs.md)
> CSV[indexování objektů BLOB JSON](search-howto-index-json-blobs.md)
