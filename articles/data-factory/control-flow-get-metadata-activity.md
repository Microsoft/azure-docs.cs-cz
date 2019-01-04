---
title: Získání metadat aktivity v Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak můžete pomocí SQL serveru aktivitě uložené procedury k vyvolání uložené procedury do služby Azure SQL Database a Azure SQL Data Warehouse z kanálu Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: shlo
ms.openlocfilehash: e733b82e1b5870d98de5d65771bd621d9bffdf44
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54024885"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Získání metadat aktivity v Azure Data Factory
Aktivita GetMetadata slouží k načtení **metadat** jakýchkoli dat ve službě Azure Data Factory. Tato aktivita se dá použít v následujících scénářích:

- Ověřit informace metadat jakýchkoli dat
- Aktivace kanálu, pokud jsou data připravena / k dispozici

Následující funkce jsou dostupné v toku řízení:

- Výstup z aktivita GetMetadata slouží v podmíněných výrazech provést ověření.
- Kanál můžete aktivuje, když je splněna podmínka prostřednictvím proveďte – dokud opakování ve smyčce

## <a name="supported-capabilities"></a>Podporované funkce

Aktivita GetMetadata přijímá datovou sadu jako požadovaný vstup a výstup informací metadat k dispozici jako výstup aktivity. V současné době jsou podporovány následující konektory s odpovídající získat metadata a metadata maximální podporovaná velikost je až **1MB**.

>[!NOTE]
>Pokud spustíte aktivitě GetMetadata na modul Integration Runtime, podporuje se verze 3.6 nebo vyšší nejnovější funkce. 

### <a name="supported-connectors"></a>Podporované konektory

**Úložiště souborů:**

| Connector/metadat | Název položky<br>(soubor/složka) | itemType<br>(soubor/složka) | velikost<br>(soubor) | vytvořené<br>(soubor/složka) | lastModified<br>(soubor/složka) |childItems<br>(složka) |contentMD5<br>(soubor) | Struktura<br/>(soubor) | počet sloupců<br>(soubor) | Existuje<br>(soubor/složka) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Amazon S3 | √/√ | √/√ | √ | x/x | √ / √ * | √ | x | √ | √ | √ / √ * |
| Azure Blob | √/√ | √/√ | √ | x/x | √ / √ * | √ | √ | √ | √ | √/√ |
| Azure Data Lake Store | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Azure File Storage | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| Systém souborů | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| SFTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| FTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

- Pro Amazon S3 `lastModified` se vztahuje na kbelík a klíč, ale ne virtuální složka; a `exists` se vztahuje na kontejneru a klíč, ale ne předponu nebo virtuální složka.
- U objektů Blob v Azure `lastModified` platí do kontejnerů a objektů blob, ale není virtuální složku.

**Relační databáze:**

| Connector/metadat | Struktura | počet sloupců | Existuje |
|:--- |:--- |:--- |:--- |
| Azure SQL Database | √ | √ | √ |
| Azure SQL Database Managed Instance | √ | √ | √ |
| Azure SQL Data Warehouse | √ | √ | √ |
| SQL Server | √ | √ | √ |

### <a name="metadata-options"></a>Možnosti metadat

Dá se zadat následující typy metadat v seznamu polí aktivita GetMetadata načíst:

| Typ metadat | Popis |
|:--- |:--- |
| Název položky | Název souboru nebo složky. |
| itemType | Typ souboru nebo složky. Výstupní hodnota je `File` nebo `Folder`. |
| velikost | Velikost souboru v bajtech. Použít pouze soubor. |
| vytvořené | Vytvořený data a času souboru nebo složky. |
| lastModified | Poslední změny data a času souboru nebo složky. |
| childItems | Seznam podsložky a soubory v rámci dané složky. Vztahuje se pouze pro složku. Výstupní hodnota je seznam názvů a typů položku každé podřízené položky. |
| contentMD5 | MD5 souboru. Použít pouze soubor. |
| Struktura | Struktura dat v souboru nebo relační databázové tabulky. Výstupní hodnota je seznam sloupec název a typ sloupce. |
| počet sloupců | Počet sloupců v souboru nebo relační tabulky. |
| Existuje| Určuje, zda existuje soubor/složka/tabulky nebo ne. Mějte na paměti, že pokud "existuje" v seznamu polí GetaMetadata zadána, aktivita selhání i v případě, že neexistuje položka (soubor/složka/tabulky); Místo toho vrátí `exists: false` ve výstupu. |

>[!TIP]
>Pokud chcete ověřit, jestli existuje soubor/složka/tabulky nebo Ne, zadejte `exists` v seznamu polí aktivita GetMetadata, pak můžete zkontrolovat `exists: true/false` výsledkem výstup aktivity. Pokud `exists` není nakonfigurované v seznamu polí, GetMetadata aktivita selže, pokud objekt nebyl nalezen.

## <a name="syntax"></a>Syntaxe

**Aktivita GetMetadata:**

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

**Datová sada:**

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

## <a name="type-properties"></a>Typ vlastnosti

Aktivita GetMetadata aktuálně můžete načíst následující typy informací o metadatech.

Vlastnost | Popis | Požaduje se
-------- | ----------- | --------
fieldList | Seznam typů informací metadat. Podrobnosti najdete v [metadat možnosti](#metadata-options) části podporované metadat. | Ano 
Datové sady | Referenční datová sada má být načtena aktivita GetMetadata jehož metadat aktivity. Zobrazit [podporované schopnosti](#supported-capabilities) části na podporovaných konektorů a najdete v tématu konektor na podrobnosti o syntaxi datové sady. | Ano

## <a name="sample-output"></a>Ukázkový výstup

GetMetadata výsledek se zobrazí ve výstupu aktivity. Níže jsou uvedeny dva ukázky s kompletní metadat možnosti vybrané v seznamu polí jako odkaz. Výsledek v následné aktivity, použijte vzor `@{activity('MyGetMetadataActivity').output.itemName}`.

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

### <a name="get-a-folders-metadata"></a>Získat metadata složky

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
Zobrazit další aktivity toku řízení podporovaných službou Data Factory: 

- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Webová aktivita](control-flow-web-activity.md)
