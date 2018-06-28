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
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: shlo
ms.openlocfilehash: c07199887faf073d19007f1ef410c193bbdbf3ee
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049362"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Získání metadat aktivity v Azure Data Factory
GetMetaData – aktivita slouží k načtení **metadata** všech dat v Azure Data Factory. Tato aktivita lze použít v následujících scénářích:

- Ověřit informace o všech datech metadat
- Kanál aktivovat, pokud jsou data připravena / k dispozici

Následující funkce jsou k dispozici v toku řízení:

- Výstup z aktivity GetMetaData – lze v podmíněné výrazy k provedení ověřování.
- Kanál může aktivuje, když je splněna podmínka prostřednictvím proveďte – dokud opakování ve smyčce

## <a name="supported-capabilities"></a>Podporované možnosti

GetMetaData – aktivity trvá datovou sadu jako požadované vstup a výstupy jako výstup aktivity k dispozici informace metadat. V současné době jsou podporovány následující konektory spolu s odpovídající dá načíst metadata:

>[!NOTE]
>Pokud spustíte GetMetaData – aktivity v modulu Runtime Self-hosted integrace, podporuje se nejnovější funkce ve verzi 3.6 nebo vyšší. 

### <a name="supported-connectors"></a>Podporované konektory

**Úložiště souborů:**

| Konektor nebo Metadata | Název položky<br>(soubor nebo složku) | itemType<br>(soubor nebo složku) | velikost<br>(soubor) | vytvořené<br>(soubor nebo složku) | Změněno<br>(soubor nebo složku) |childItems<br>(složka) |contentMD5<br>(soubor) | Struktura<br/>(soubor) | počet sloupců<br>(soubor) | existuje<br>(soubor nebo složku) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Amazon S3 | √/√ | √/√ | √ | x/x | √ / √ * | √ | x | √ | √ | √ / √ * |
| Azure Blob | √/√ | √/√ | √ | x/x | √ / √ * | √ | √ | √ | √ | √/√ |
| Azure Data Lake Store | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Azure File Storage | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| Systém souborů | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| SFTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| FTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

- Pro Amazon S3 `lastModified` se vztahuje na sady a klíč, ale není virtuální složky; a `exists` se vztahuje na sady a klíč, ale není předpony nebo virtuální složky.
- Pro objekt Blob Azure `lastModified` se vztahuje na kontejner a objektů blob, ale není virtuální složky.

**Relační databáze:**

| Konektor nebo Metadata | Struktura | počet sloupců | existuje |
|:--- |:--- |:--- |:--- |
| Azure SQL Database | √ | √ | √ |
| Azure SQL Data Warehouse | √ | √ | √ |
| SQL Server | √ | √ | √ |

### <a name="metadata-options"></a>Možnosti metadat

V seznamu polí GetMetaData – aktivita načíst lze zadat následující typy metadat:

| Typ metadat | Popis |
|:--- |:--- |
| Název položky | Název souboru nebo složky. |
| itemType | Typ souboru nebo složky. Hodnota výstupu je `File` nebo `Folder`. |
| velikost | Velikost souboru v bajtech. Použít pouze souboru. |
| vytvořené | Vytvořený datetime souboru nebo složce. |
| Změněno | Poslední změna datového typu datetime souboru nebo složce. |
| childItems | Seznam podsložky a soubory v dané složce. Platí pouze pro složku. Výstupní hodnota je seznam názvu a typu každé podřízené položky. |
| contentMD5 | Algoritmus MD5 souboru. Použít pouze souboru. |
| Struktura | Struktura dat v souboru nebo tabulky relační databáze. Výstupní hodnota je seznam název sloupce a typ sloupce. |
| počet sloupců | Počet sloupců v souboru nebo relační tabulky. |
| existuje| Jestli existuje soubor nebo složku nebo tabulku nebo ne. Poznámka: Pokud "existuje" je uveden v seznamu polí GetaMetadata, aktivity nebudou i v případě, že položka (soubor nebo složku nebo tabulky) neexistuje; Místo toho vrátí `exists: false` ve výstupu. |

>[!TIP]
>Pokud chcete ověřit, jestli existuje soubor nebo složku nebo tabulku nebo Ne, zadejte `exists` v seznamu polí GetMetaData – aktivita pak můžete zkontrolovat `exists: true/false` výsledkem výstup aktivity. Pokud `exists` není nakonfigurované v seznamu polí, GetMetaData – aktivita selže, pokud objekt nebyl nalezen.

## <a name="syntax"></a>Syntaxe

**GetMetaData – aktivity:**

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

**Datové sady:**

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
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>Vlastnosti typu

GetMetaData – aktivita aktuálně můžete získat následující typy informací metadat.

Vlastnost | Popis | Požaduje se
-------- | ----------- | --------
Seznam polí | Seznam typů požadované informace metadat. Zobrazit podrobnosti v [Metadata možnosti](#metadata-options) části na podporované metadat. | Ano 
Datové sady | Referenční datová sada je jejichž metadat aktivity mají být načteny aktivitou GetMetaData –. V tématu [podporované schopnosti](#supported-capabilities) části na podporované konektory a odkazovat na konektoru téma na datovou sadu syntaxe podrobnosti. | Ano

## <a name="sample-output"></a>Ukázkový výstup

GetMetaData – výsledků se zobrazí v výstup aktivity. Níže jsou uvedeny dvě ukázky s kompletní metadata možnosti vybrané v seznamu polí jako odkaz. Pokud chcete použít výsledek následné aktivity, použijte vzor `@{activity('MyGetMetadataActivity').output.itemName}`.

### <a name="get-a-files-metadata"></a>Získat metadata souboru

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>Získat metadata do složky

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>Další postup
Najdete v části Další aktivity toku řízení podporovaných službou Data Factory: 

- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Webové aktivity](control-flow-web-activity.md)
