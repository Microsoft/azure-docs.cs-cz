---
title: Získání metadat aktivity v Azure Data Factory | Microsoft Docs
description: Zjistěte, jak můžete pomocí SQL Server aktivity uložené procedury vyvolat uloženou proceduru v databázi SQL Azure nebo Azure SQL Data Warehouse z objektu pro vytváření dat kanál.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: e8e40b763f0c6f1f994535ab2ff335cfcbf02cf7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Získání metadat aktivity v Azure Data Factory
Aktivita GetMetadata slouží k načtení metadat jakýchkoli dat ve službě Azure Data Factory. Tato aktivita je podporována pouze pro datové továrny verze 2. Dá se v následujících scénářích:

- Ověřit informace o všech datech metadat
- Kanál aktivovat, pokud jsou data připravena / k dispozici

Následující funkce jsou k dispozici v toku řízení:
- Výstup z aktivity GetMetaData – lze v podmíněné výrazy k provedení ověřování.
- Kanál může aktivuje, když je splněna podmínka prostřednictvím proveďte – dokud opakování ve smyčce

GetMetaData – aktivita trvá a datovou sadu jako požadované vstup a výstupy metadata informace k dispozici jako výstup. V současné době se podporuje jenom datovou sadu objektu blob Azure. Pole podporované metadata jsou velikost, struktura a změněno času.  

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [Data Factory V1 dokumentaci](v1/data-factory-introduction.md).


## <a name="syntax"></a>Syntaxe

### <a name="get-metadata-activity-definition"></a>Získáte definici metadat aktivity:
V následujícím příkladu vrátí aktivita GetMetaData – metadata o data reprezentována MyDataset. 

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```
### <a name="dataset-definition"></a>Definice datové sady:

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "Filename": "file.json",
            "format":{
                "type":"JsonFormat"
                "nestedSeperator": ","
            }
        }
    }
}
```

### <a name="output"></a>Výstup
```json
{
    "size": 1024,
    "structure": [
        {
            "name": "id",
            "type": "Int64"
        }, 
    ],
    "lastModified": "2016-07-12T00:00:00Z"
}
```

## <a name="type-properties"></a>Vlastnosti typu
GetMetaData – aktivita aktuálně můžete načíst následující typy informací metadat z datové sadě služby Azure storage.

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
Seznam polí | Seznam typů požadované informace metadat.  | <ul><li>velikost</li><li>Struktura</li><li>lastModified</li></ul> |    Ne<br/>Pokud je prázdný, vrátí aktivita všechny informace o 3 podporované metadat. 
dataset | Referenční datová sada je jejichž metadat aktivity mají být načteny aktivitou GetMetaData –. <br/><br/>Je typ aktuálně podporovanou datovou sadu objektu Blob Azure. Jsou dvě dílčí vlastnosti: <ul><li><b>Název_odkazu</b>: odkaz na existující datovou sadu objektu Blob Azure</li><li><b>typ</b>: vzhledem k tomu, že se odkazuje datovou sadu, je typu "DatasetReference"</li></ul> |    <ul><li>Řetězec</li><li>DatasetReference</li></ul> | Ano

## <a name="next-steps"></a>Další postup
Najdete v části Další aktivity toku řízení podporovaných službou Data Factory: 

- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Webové aktivity](control-flow-web-activity.md)