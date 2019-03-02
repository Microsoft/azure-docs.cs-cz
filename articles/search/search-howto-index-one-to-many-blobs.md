---
title: Objekty BLOB indexu, které obsahují více hledání dokumentů indexu z objektů Blob v Azure indexeru pro fulltextové vyhledávání – Azure Search
description: Procházejte objekty BLOB Azure pro textový obsah pomocí indexeru Azure Search Blob. Každý objekt blob může obsahovat jeden nebo více dokumentů index Azure Search.
ms.date: 02/12/2019
author: arv100kri
manager: briansmi
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seofeb2018
ms.openlocfilehash: e95eff015340659b642dff800a03f615e22c1577
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2019
ms.locfileid: "57220568"
---
# <a name="indexing-blobs-producing-multiple-search-documents"></a>Indexování objektů BLOB vytváření více vyhledávání dokumentů
Ve výchozím nastavení indexeru blob bude považovat za obsah objektu blob jednotné hledání v dokumentech. Některé **parsingMode** hodnoty podporují scénáře, kde jednotlivých objektů blob může vést k více vyhledávání dokumentů. Různé druhy **parsingMode** , která umožňují indexer pro extrahování a více než jeden hledání v dokumentech z objektu blob jsou:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

> [!IMPORTANT]
> `jsonLines` režim parsování je ve verzi public preview a neměli byste používat v produkčním prostředí. Další informace najdete v tématu [REST api-version = 2017-11-11-Preview](search-api-2017-11-11-preview.md). 

## <a name="one-to-many-document-key"></a>Klíč dokumentu jeden mnoho
Každý dokument, který se zobrazuje v indexu Azure Search je jedinečně identifikovaný klíč dokumentu. 

Pokud je zadán žádný analytický režim, a pokud neexistuje žádné explicitní mapování pro klíčové pole v indexu Azure Search automaticky [mapuje](search-indexer-field-mappings.md) `metadata_storage_path` vlastnost jako klíč. Toto mapování se zajistí, že každý objekt blob se zobrazí jako různé hledání v dokumentech.

Při používání jakéhokoli režimy parsování, uvedené výše, jeden objekt blob se mapuje na "řada" hledání dokumentů, provádění klíč dokumentu výhradně podle nevhodný metadata objektu blob. K překonání tohoto omezení, je schopna Generovat klíč dokumentu "jedna k mnoha" pro každé jednotlivé entity extrahují z objektu blob Azure Search. Tato vlastnost má název `AzureSearch_DocumentKey` a že je přidaný do každé jednotlivé entity extrahovat z objektu blob. Hodnota této vlastnosti se musí být jedinečný pro každé jednotlivé entity _mezi objekty BLOB_ a entity, které se zobrazí jako samostatné vyhledávání dokumentů.

Ve výchozím nastavení, když nejsou zadány žádné explicitní pole mapování pro pole klíče indexu `AzureSearch_DocumentKey` je k němu mapována, pomocí `base64Encode` funkce mapování polí.

## <a name="example"></a>Příklad:
Předpokládejme, že jste definici indexu u následujících polí:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

A objektů blob v kontejneru objektů BLOB s následující strukturou:

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Když vytvoříte indexeru a nastavte **parsingMode** k `jsonLines` – bez zadání jakékoli explicitní pole mapování pro pole klíče následující mapování se implicitně použije
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Toto nastavení způsobí index Azure Search, který obsahuje následující informace (pro zkrácení zkrátila kódovanou jako base64 id)

| id | teplota | tlak | časové razítko |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapování vlastních polí pro pole klíče indexu

Za předpokladu, že definici indexu jako předchozí příklad, Řekněme, že má objektů blob v kontejneru objektů BLOB s následující strukturou:

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Když vytvoříte indexer s `delimitedText` **parsingMode**, můžou mít pocit přirozené funkce mapování polí na pole klíče wmm nastavit takto:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Nicméně toto mapování se _není_ za následek 4 dokumenty, které se zobrazují v indexu, protože `recordid` pole není jedinečný _mezi objekty BLOB_. Proto doporučujeme, abyste provedli pomocí implicitního pole mapování z `AzureSearch_DocumentKey` vlastnost pole klíče indexu pro režimy parsování "jedna k mnoha".

Pokud chcete nastavit mapování explicitní pole, ujistěte se, že _sourceField_ se liší u každé jednotlivé entity **přes všechny objekty BLOB**.

> [!NOTE]
> Tento přístup používá `AzureSearch_DocumentKey` zajištění jedinečnosti za extrahované entity se může měnit, a proto byste neměli spoléhat na hodnotu pro potřeby vaší aplikace.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Indexery ve službě Azure Search](search-indexer-overview.md)
+ [Indexování služby Azure Blob Storage pomocí služby Azure Search](search-howto-index-json-blobs.md)
+ [Indexování objektů BLOB CSV pomocí indexeru Azure Search blob](search-howto-index-csv-blobs.md)
+ [Indexování objektů BLOB JSON pomocí indexeru Azure Search blob](search-howto-index-csv-blobs.md)

## <a name="NextSteps"></a>Další kroky
* Další informace o službě Azure Search najdete v tématu [stránku vyhledávací služby](https://azure.microsoft.com/services/search/).