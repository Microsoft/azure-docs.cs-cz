---
title: Kopírování dat ze serveru FTP pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data ze serveru FTP podporovaného úložiště dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 0e1127d90aeb4c59687ac4df7fb7ebae1901cee8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65228423"
---
# <a name="copy-data-from-ftp-server-by-using-azure-data-factory"></a>Kopírování dat ze serveru FTP pomocí služby Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, který používáte:"]
>
> * [Verze 1](v1/data-factory-ftp-connector.md)
> * [Aktuální verze](connector-ftp.md)

Tento článek popisuje, jak kopírovat data ze serveru FTP. Další informace o Azure Data Factory najdete v článku [úvodní článek](introduction.md).

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor FTP je podporována pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporované matice zdroj/jímka](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)

Konkrétně tento konektor FTP podporuje:

- Kopírování souborů pomocí **základní** nebo **anonymní** ověřování.
- Kopírování souborů jako-je nebo analýza souborů pomocí [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní FTP.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro FTP propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **FtpServer**. | Ano |
| host | Zadejte název nebo IP adresu serveru FTP. | Ano |
| port | Zadejte port, na kterém naslouchá FTP server.<br/>Povolené hodnoty jsou: výchozí hodnota je celé číslo, **21**. | Ne |
| enableSsl | Určete, zda chcete pomocí funkce FTP přes kanál SSL/TLS.<br/>Povolené hodnoty jsou: **true** (výchozí), **false**. | Ne |
| enableServerCertificateValidation | Určete, zda povolit ověření certifikátu serveru SSL při použití FTP přes kanál SSL/TLS.<br/>Povolené hodnoty jsou: **true** (výchozí), **false**. | Ne |
| authenticationType | Zadejte typ ověřování.<br/>Povolené hodnoty jsou: **Základní**, **anonymní** | Ano |
| userName | Zadejte uživatele, který má přístup k serveru FTP. | Ne |
| password | Zadejte heslo pro uživatele (uživatelské jméno). Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

>[!NOTE]
>Konektor FTP podporuje přístup k serveru FTP bez šifrování nebo explicitní šifrování SSL/TLS. nepodporuje implicitní šifrování SSL/TLS.

**Příklad 1: použití anonymní ověřování**

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "<ftp server>",
            "port": 21,
            "enableSsl": true,
            "enableServerCertificateValidation": true,
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad 2: použití základního ověřování**

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "<ftp server>",
            "port": 21,
            "enableSsl": true,
            "enableServerCertificateValidation": true,
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. 

- Pro **Parquet a formátu odděleného textu**, odkazovat na [datovou sadu formátu Parquet a text s oddělovači](#parquet-and-delimited-text-format-dataset) oddílu.
- Pro ostatní formáty, jako jsou **formát ORC nebo Avro/JSON nebo binární**, odkazovat na [jiné datové sady formátu](#other-format-dataset) oddílu.

### <a name="parquet-and-delimited-text-format-dataset"></a>Datová sada formátu parquet a text s oddělovači

Ke zkopírování dat z FTP v **Parquet nebo formátu odděleného textu**, odkazovat na [formát Parquet](format-parquet.md) a [formátu textu odděleného](format-delimited-text.md) článek věnovaný tomu, na základě formátu datovou sadu a podporovaných nastavení . Následující vlastnosti jsou podporovány pro FTP v rámci `location` nastavení v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Požaduje se |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Vlastnost type v rámci `location` v datové sadě musí být nastaveno na **FtpServerLocation**. | Ano      |
| folderPath | Cesta ke složce. Pokud chcete použít zástupný znak, do složky filtr, přeskočte toto nastavení a zadejte v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru v rámci v dané cestě folderPath. Pokud chcete použít zástupný znak pro filtr souborů, přeskočte toto nastavení a zadejte v nastavení zdroje aktivity. | Ne       |

> [!NOTE]
> **Sdílení souborů** typ datové sady uvedené v další části formát Parquet/textu je stále podporovány jako-je pro aktivitu kopírování a vyhledávání/GetMetadata z důvodu zpětné kompatibility. Byly navrženy používat tento nový model do budoucna a ADF vytváření uživatelského rozhraní se přepnulo na generování tyto nové typy.

**Příklad:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<FTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "FtpServerLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Další datové sady formátu

Ke zkopírování dat z FTP v **formát ORC nebo Avro/JSON nebo binární**, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **FileShare** |Ano |
| folderPath | Cesta ke složce. Filtr zástupných znaků je podporován, povolené zástupné znaky jsou: `*` (odpovídá nula nebo více znaků) a `?` (porovnává nulu nebo jeden znak); použijte `^` řídicí Pokud název skutečné složky obsahuje zástupný znak nebo tento znak escape uvnitř. <br/><br/>Příklady: rootfolder/podsložka/Další příklady naleznete v [složky a souboru filtrů příklady](#folder-and-file-filter-examples). |Ano |
| fileName | **Název nebo zástupný filtr** pro soubory v zadané "folderPath". Pokud nezadáte hodnotu pro tuto vlastnost, datová sada odkazuje na všechny soubory ve složce. <br/><br/>Pro filtr, povoleny zástupné znaky jsou: `*` (odpovídá žádnému nebo více znaků) a `?` (odpovídá nula nebo jeden znak).<br/>– Příklad 1: `"fileName": "*.csv"`<br/>– Příklad 2: `"fileName": "???20180427.txt"`<br/>Použití `^` dostala mimo vašeho skutečného názvu souboru má zástupných znaků nebo tento znak escape uvnitř. |Ne |
| format | Pokud chcete **kopírovat soubory jako-je** mezi souborové úložištěm (binární kopie) a přeskočit část o formátu v definicích oba vstupní a výstupní datové sady.<br/><br/>Pokud chcete analyzovat soubory s konkrétním formátu, jsou podporovány následující typy formátů souboru: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](supported-file-formats-and-compression-codecs.md#text-format), [formátu Json](supported-file-formats-and-compression-codecs.md#json-format), [Avro formát](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formát](supported-file-formats-and-compression-codecs.md#orc-format), a [formát Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) oddíly. |Ne (pouze pro binární kopie scénář) |
| compression | Zadejte typ a úroveň komprese pro data. Další informace najdete v tématu [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**.<br/>Jsou podporované úrovně: **Optimální** a **nejrychlejší**. |Ne |
| useBinaryTransfer | Určete, jestli se má použít režim binární přenos. Hodnoty jsou true pro binárním režimu (výchozí) a hodnotu false pro ASCII. |Ne |

>[!TIP]
>Zkopírujte všechny soubory ve složce, zadejte **folderPath** pouze.<br>Pokud chcete zkopírovat jeden soubor s daným názvem, zadejte **folderPath** s složkovou část a **fileName** s názvem souboru.<br>Chcete-li zkopírovat podmnožinu souborů ve složce, zadejte **folderPath** s složkovou část a **fileName** s filtr zástupných znaků.

>[!NOTE]
>Pokud jste používali vlastnost "fileFilter" pro filtr souborů, je stále podporovány jako-se, když jsou navržena nová funkce filtru, které jsou přidány do "název_souboru" do budoucna.

**Příklad:**

```json
{
    "name": "FTPDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<FTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "myfile.csv.gz",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných zdrojem FTP.

### <a name="ftp-as-source"></a>FTP jako zdroj

- Pro kopírování z **Parquet a formátu odděleného textu**, odkazovat na [Parquet a zdroj obsahující text oddělený znaky formátu](#parquet-and-delimited-text-format-source) oddílu.
- Pro kopírování z dalších formátech, jako jsou **formát ORC nebo Avro/JSON nebo binární**, odkazovat na [jiný formát zdroj](#other-format-source) oddílu.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet a zdroj formátu odděleného textu

Ke zkopírování dat z FTP v **Parquet nebo formátu odděleného textu**, odkazovat na [formát Parquet](format-parquet.md) a [formátu textu odděleného](format-delimited-text.md) článek věnovaný tomu zdroj aktivity kopírování založená na formát a podporovaná nastavení. Následující vlastnosti jsou podporovány pro FTP v rámci `storeSettings` nastavení zdroje kopírování založená na formát:

| Vlastnost                 | Popis                                                  | Požaduje se                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Vlastnost type v rámci `storeSettings` musí být nastaveno na **FtpReadSetting**. | Ano                                           |
| recursive                | Určuje, jestli se data číst rekurzivně z podsložky nebo pouze z určené složky. Všimněte si, že pokud rekurzivní je nastavena na hodnotu true a jímku je souborové úložiště prázdnou složku nebo podsložku není zkopírován či vytvořili na jímce. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne                                            |
| wildcardFolderPath       | Cesta ke složce se zástupnými znaky pro filtrování zdrojové složky. <br>Povolené zástupné znaky jsou: `*` (odpovídá nula nebo více znaků) a `?` (porovnává nulu nebo jeden znak); použijte `^` řídicí Pokud název skutečné složky obsahuje zástupný znak nebo tento znak escape uvnitř. <br>Další příklady naleznete v [složky a souboru filtrů příklady](#folder-and-file-filter-examples). | Ne                                            |
| wildcardFileName         | Název souboru se zástupnými znaky v rámci dané folderPath/wildcardFolderPath ke zdrojovým souborům filtru. <br>Povolené zástupné znaky jsou: `*` (odpovídá nula nebo více znaků) a `?` (porovnává nulu nebo jeden znak); použijte `^` řídicí Pokud název skutečné složky obsahuje zástupný znak nebo tento znak escape uvnitř.  Další příklady naleznete v [složky a souboru filtrů příklady](#folder-and-file-filter-examples). | Ano, pokud `fileName` není zadaný v datové sadě |
| modifiedDatetimeStart    | Filtr souborů na základě atributu: Poslední změny. Soubory bude vybrána, pokud jejich poslední úpravy jsou v rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti může mít hodnotu NULL, což znamená, že žádný soubor filtr atributu se použijí k datové sadě.  Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná s hodnotou data a času bude vybrána.  Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že soubory, jejichž poslední upravené atribut je menší než hodnota data a času bude vybraná. | Ne                                            |
| modifiedDatetimeEnd      | Stejné jako výše.                                               | Ne                                            |
| useBinaryTransfer        | Zadejte, jestli se má použít režim binární přenos pro úložiště FTP. Hodnoty jsou true pro binárním režimu (výchozí) a hodnotu false pro ASCII. | Ne                                            |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti storage současně. Zadejte pouze v případě, že chcete omezit souběžných připojení k úložišti. | Ne                                            |

> [!NOTE]
> Pro formátu Parquet nebo odděleného textu **FileSystemSource** zdroj aktivity kopírování typů uvedených v následující části je stále podporovány jako-je z důvodu zpětné kompatibility. Byly navrženy používat tento nový model do budoucna a ADF vytváření uživatelského rozhraní se přepnulo na generování tyto nové typy.

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "FtpReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Jiný zdroj formátu

Ke zkopírování dat z FTP v **formát ORC nebo Avro/JSON nebo binární**, jsou podporovány následující vlastnosti v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **FileSystemSource** |Ano |
| recursive | Určuje, jestli se data ze složek sub nebo pouze z určené složky Číst rekurzivně. Poznámka: Pokud rekurzivní je nastavena na hodnotu true a datovou sadu jímky souborové úložiště je prázdné složky/dílčí-folder nebudou zkopírovány/vytvořili na jímky.<br/>Povolené hodnoty jsou: **true** (výchozí), **false** | Ne |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti storage současně. Zadejte pouze v případě, že chcete omezit souběžných připojení k úložišti. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<FTP input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Složky a příklady filtr souborů

Tato část popisuje výsledné chování název složky a cesta k souboru s filtry zástupný znak.

| folderPath | fileName | recursive | Zdrojové složky struktury a filtrování výsledků (soubory v **tučné** načtením)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (prázdný, použijte výchozí) | false (nepravda) | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (prázdný, použijte výchozí) | true (pravda) | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false (nepravda) | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true (pravda) | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
