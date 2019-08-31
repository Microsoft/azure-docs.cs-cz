---
title: Indexování objektů blob, které obsahují více dokumentů indexu hledání z Azure Blob indexeru pro fulltextové vyhledávání – Azure Search
description: Procházení objektů blob Azure pro obsah textu pomocí Azure Search indexeru objektů BLOB Každý objekt BLOB může obsahovat jeden nebo více indexovaných dokumentů Azure Search.
ms.date: 05/02/2019
author: arv100kri
manager: nitinme
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seofeb2018
ms.openlocfilehash: 2c2a17d006f65854a89b9fac1818fcec420c07dc
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182297"
---
# <a name="indexing-blobs-producing-multiple-search-documents"></a>Indexování objektů BLOB vytvářejících více vyhledávacích dokumentů
Ve výchozím nastavení bude indexer objektů BLOB zacházet s obsahem objektu BLOB jako s jedním vyhledávacím dokumentem. Některé hodnoty **parsingMode** podporují scénáře, kdy jednotlivý objekt BLOB může mít za následek více dokumentů pro hledání. Různé typy **parsingMode** , které umožňují indexeru extrahovat více než jeden dokument hledání z objektu BLOB:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Klíč dokumentu 1: n
Každý dokument, který se zobrazí v indexu Azure Search, jednoznačně identifikuje klíč dokumentu. 

Pokud není zadán žádný režim analýzy a neexistuje žádné explicitní mapování pro klíčové pole v indexu, Azure Search automaticky `metadata_storage_path` namapovat vlastnost jako [](search-indexer-field-mappings.md) klíč. Toto mapování zajišťuje, že se každý objekt BLOB zobrazí jako odlišný vyhledávací dokument.

Při použití některého z výše uvedených režimů analýzy jeden objekt BLOB namapuje na "mnoho" vyhledávacích dokumentů, takže klíč dokumentu je výhradně založený na metadatech objektu BLOB nevhodný. Chcete-li toto omezení překonat, Azure Search je možné vygenerovat klíč dokumentu "1: n" pro každou jednotlivou entitu extrahovanou z objektu BLOB. Tato vlastnost je pojmenována `AzureSearch_DocumentKey` a přidána do každé z nich vyjmuté z objektu BLOB. Hodnota této vlastnosti zaručuje, že pro každou jednotlivou entitu _napříč objekty blob_ je jedinečná a entity se zobrazí jako samostatné dokumenty hledání.

Ve výchozím nastavení, pokud nejsou zadána explicitní mapování polí pro pole index klíče, `AzureSearch_DocumentKey` je k němu namapováno `base64Encode` pomocí funkce mapování pole.

## <a name="example"></a>Příklad
Předpokládejme, že máte definici indexu s následujícími poli:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

A váš kontejner objektů BLOB obsahuje objekty BLOB s následující strukturou:

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Když vytvoříte indexer a nastavíte **parsingMode** na `jsonLines` -bez zadání explicitních mapování polí pro klíčové pole, použije se implicitně následující mapování.
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Výsledkem tohoto nastavení bude Azure Search index obsahující následující informace (pro zkrácení se zkrátilo ID kódované v kódování Base64).

| id | teplota | pressure | timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapování vlastních polí pro pole indexového klíče

Za předpokladu, že v předchozím příkladu je stejná definice indexu, řekněme, že váš kontejner objektů BLOB obsahuje objekty BLOB s následující strukturou:

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Při vytváření indexeru pomocí `delimitedText` **parsingMode**může být přirozené nastavit funkci mapování polí na klíčové pole následujícím způsobem:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Toto mapování ale nevede k tomu, aby se v indexu zobrazovaly 4 dokumenty, protože `recordid` pole není v objektech _BLOB_jedinečné. Proto doporučujeme použít implicitní mapování polí použité z `AzureSearch_DocumentKey` vlastnosti na pole index klíče pro režimy analýzy "1: n".

Pokud chcete nastavit explicitní mapování polí, ujistěte se, že je _sourceField_ jedinečný pro každou jednotlivou entitu **napříč všemi objekty blob**.

> [!NOTE]
> Přístup, který se `AzureSearch_DocumentKey` používá při zajištění jedinečnosti na extrahovanou entitu, se může změnit, a proto byste neměli spoléhat na jeho hodnotu pro potřeby vaší aplikace.

## <a name="see-also"></a>Viz také:

+ [Indexery v Azure Search](search-indexer-overview.md)
+ [Indexování služby Azure Blob Storage s využitím Azure Search](search-howto-index-json-blobs.md)
+ [Indexování objektů BLOB CSV s Azure Search indexerem objektů BLOB](search-howto-index-csv-blobs.md)
+ [Indexování objektů BLOB JSON s Azure Search indexerem objektů BLOB](search-howto-index-json-blobs.md)

## <a name="NextSteps"></a>Další kroky
* Další informace o Azure Search najdete na [stránce vyhledávací služby](https://azure.microsoft.com/services/search/).