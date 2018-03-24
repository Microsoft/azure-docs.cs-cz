---
title: Přesun dat ze serveru FTP pomocí Azure Data Factory | Microsoft Docs
description: Další informace o tom, jak přesunout data ze serveru FTP pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 17dea2d1106a57aa678a88db6647c71048d8c38f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Přesun dat ze serveru FTP pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](data-factory-ftp-connector.md)
> * [Verze 2 – Preview](../connector-ftp.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [konektor FTP v V2](../connector-ftp.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory pro přesun dat ze serveru FTP. Vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který představuje obecný přehled přesun dat s aktivitou kopírování.

Ze serveru FTP můžete zkopírovat data do úložiště dat žádné podporované jímky. Seznam úložišť dat jako jímky nepodporuje aktivitě kopírování najdete v tématu [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. Objekt pro vytváření dat aktuálně podporuje pouze přesunutí dat ze serveru FTP na jiným úložištím dat, ale není přesouvání dat od ostatních dat ukládá na FTP server. Podporuje místní a cloudové servery FTP.

> [!NOTE]
> Aktivita kopírování nedojde k odstranění zdrojového souboru po byl úspěšně zkopírován do cílové. Pokud potřebujete odstranit zdrojový soubor po úspěšné kopie, vytvořte vlastní aktivity odstranit soubor a použijte aktivitu v kanálu. 

## <a name="enable-connectivity"></a>Povolit připojení
Pokud přesouváte data ze **místní** serveru FTP do cloudu dat uložit (například do Azure Blob storage), instalaci a používání brány pro správu dat. Brána pro správu dat je klientský agent, který je nainstalován v místním počítači a umožňuje cloudové služby pro připojení k místnímu prostředku. Podrobnosti najdete v tématu [Brána pro správu dat](data-factory-data-management-gateway.md). Podrobné pokyny k nastavení registrace brány a pomocí, najdete v tématu [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md). Používáte bránu pro připojení k serveru FTP, i, pokud je server v Azure infrastruktury jako služby (IaaS) virtuální počítač (VM).

Je možné nainstalovat bránu na stejný místní počítač nebo virtuální počítač IaaS jako FTP server. Nicméně doporučujeme nainstalovat bránu na samostatný počítač nebo virtuální počítač IaaS předejít sporu prostředků a pro dosažení vyššího výkonu. Při instalaci brány na samostatný počítač, na počítač byste měli mít přístup k serveru FTP.

## <a name="get-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z zdroje FTP pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním služby Data Factory**. V tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) rychlé návod.

Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí PowerShell**, **šablony Azure Resource Manageru**, **.NET API**, a **REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování.

Jestli používáte nástroje nebo rozhraní API, proveďte následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený:

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory.
2. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování.
3. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup.

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Pokud používáte rozhraní API (s výjimkou .NET API) nebo nástroje, definujete tyto entity služby Data Factory pomocí formátu JSON. Ukázku s definicemi JSON entit služby Data Factory, které se používají ke zkopírování dat z úložiště dat služby FTP, najdete [JSON příklad: kopírování dat ze serveru FTP do objektu blob Azure](#json-example-copy-data-from-ftp-server-to-azure-blob) tohoto článku.

> [!NOTE]
> Podrobnosti o podporovaných formátech souborů a komprese používat najdete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které se používají pro definování konkrétní entity služby Data Factory k serveru FTP.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka popisuje elementy JSON, které jsou specifické pro služby FTP propojený.

| Vlastnost | Popis | Požaduje se | Výchozí |
| --- | --- | --- | --- |
| type |Tuto možnost nastavíte na Server_ftp. |Ano |&nbsp; |
| hostitel |Zadejte název nebo IP adresu serveru FTP. |Ano |&nbsp; |
| authenticationType. |Zadejte typ ověřování. |Ano |Anonymní, základní |
| uživatelské jméno |Zadejte uživatele, který má přístup k serveru FTP. |Ne |&nbsp; |
| heslo |Zadejte heslo pro uživatele (uživatelské jméno). |Ne |&nbsp; |
| encryptedCredential |Zadejte šifrované pověření pro přístup k serveru FTP. |Ne |&nbsp; |
| gatewayName |Zadejte název brány v Brána pro správu dat pro připojení k serveru FTP na místě. |Ne |&nbsp; |
| port |Zadejte port, na kterém naslouchá FTP server. |Ne |21 |
| enableSsl |Určete, zda chcete pomocí funkce FTP přes kanál SSL/TLS. |Ne |true (pravda) |
| enableServerCertificateValidation |Určete, zda chcete povolit ověřování certifikátu serveru SSL při použití FTP přes kanál SSL/TLS. |Ne |true (pravda) |

### <a name="use-anonymous-authentication"></a>Anonymní ověřování použijte

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {        
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Použít uživatelské jméno a heslo v prostém textu pro základní ověřování

```JSON
{
    "name": "FTPLinkedService",
      "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
      }
}
```

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Použijte port, enableSsl, enableServerCertificateValidation

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",    
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>Použití encryptedCredential pro ověřování a brány

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
      }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v části [vytváření datových sad](data-factory-create-datasets.md). Oddíly jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu.

**Rámci typeProperties** části se liší pro jednotlivé typy datovou sadu. Poskytuje informace, které jsou specifické pro daný typ datové sady. **Rámci typeProperties** části datové sady typu **sdílení souborů** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Dílčí cestou ke složce. Použít řídicí znak ' \ ' pro speciální znaky v řetězci. V tématu [ukázka propojené definice služby a datovou sadu](#sample-linked-service-and-dataset-definitions) příklady.<br/><br/>Tato vlastnost se můžete kombinovat **partitionBy** cesty ke složce zadat podle řez spuštění a ukončení hodnoty data a času. |Ano |
| fileName |Zadejte název souboru do **folderPath** Pokud chcete, aby v tabulce odkazovat na konkrétní soubor ve složce. Pokud nezadáte žádnou hodnotu pro tuto vlastnost, tabulka odkazuje na všechny soubory ve složce.<br/><br/>Když **fileName** není zadané pro datovou sadu výstupů, je název vygenerovaný soubor v následujícím formátu: <br/><br/>Data.<Guid>.txt (Example: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Ne |
| fileFilter |Zadejte filtr pro umožňuje vybrat podmnožinu souborů v **folderPath**, ne všechny soubory.<br/><br/>Povolené hodnoty jsou: `*` (více znaků) a `?` (jeden znak).<br/><br/>Příklad 1: `"fileFilter": "*.log"`<br/>Příklad 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** lze použít pro datové sadě služby vstupní sdílení souborů. Tato vlastnost není podporována s Hadoop Distributed File System (HDFS). |Ne |
| partitionedBy |Slouží k zadání dynamický **folderPath** a **fileName** pro data časové řady. Například můžete zadat **folderPath** , je pro každou hodinu dat parametry. |Ne |
| Formát | Jsou podporovány následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte **typ** vlastnost pod formát na jednu z těchto hodnot. Další informace najdete v tématu [textovém formátu](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátu](data-factory-supported-file-and-compression-formats.md#orc-format), a [Parquet formát](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete zkopírovat soubory, jako jsou mezi souborové úložiště (binární kopie), přejděte v části formátu v obou definice vstupní a výstupní datové sady. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**, a jsou podporované úrovně **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |
| useBinaryTransfer |Určete, zda chcete použít režim binární přenosu. Hodnoty jsou pro binárního režimu (to je výchozí hodnota), na hodnotu true a false pro ASCII. Tuto vlastnost lze použít pouze v případě typu přidružené propojené služby typu: Server_ftp. |Ne |

> [!NOTE]
> **Název souboru** a **fileFilter** nelze používat současně.

### <a name="use-the-partionedby-property"></a>Použijte vlastnost partionedBy
Jak je uvedeno v předchozí části, můžete zadat dynamický **folderPath** a **fileName** pro data časové řady s **partitionedBy** vlastnost.

Další informace o datové sady času řady, plánování a řezy najdete v tématu [vytváření datových sad](data-factory-create-datasets.md), [plánování a provádění](data-factory-scheduling-and-execution.md), a [vytváření kanálů](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Ukázka 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
V tomto příkladu {řez} se nahradí hodnotu objektu pro vytváření dat systému proměnné SliceStart, ve formátu určeném (YYYYMMDDHH). Vlastnosti SliceStart odkazuje na spuštění řezu. Cesta ke složce se liší pro každý řez. (Například wikidatagateway/wikisampledataout/2014100103 nebo wikidatagateway/wikisampledataout/2014100104.)

#### <a name="sample-2"></a>Ukázka 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
V tomto příkladu jsou extrahován rok, měsíc, den a čas SliceStart do samostatné proměnné, které jsou používány **folderPath** a **fileName** vlastnosti.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [vytváření kanálů](data-factory-create-pipelines.md). Vlastnosti, například název, popis, vstupní a výstupní tabulky a zásad jsou dostupné pro všechny typy aktivit.

Vlastnosti, které jsou k dispozici v **rámci typeProperties** části aktivity, na druhé straně lišit každý typ aktivity. Pro aktivitu kopírování vlastnosti typu lišit v závislosti na typech zdrojů a jímky.

Při aktivitě kopírování, pokud je zdroj typu **FileSystemSource**, je k dispozici v této vlastnosti **rámci typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| Rekurzivní |Označuje, zda je data načíst rekurzivně z podsložky nebo pouze do zadané složky. |Hodnota TRUE, False (výchozí) |Ne |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>Příklad JSON: kopírování dat ze serveru FTP do objektu Blob Azure
Tento příklad ukazuje postup kopírování dat ze serveru FTP do úložiště objektů Blob v Azure. Ale data se dají zkopírovat přímo do jakéhokoli z jímky uvádí [podporované úložiště dat a formáty](data-factory-data-movement-activities.md#supported-data-stores-and-formats), pomocí aktivity kopírování v datové továrně.  

Následující příklady poskytují ukázka JSON definice, které můžete použít k vytvoření kanálu pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), nebo [prostředí PowerShell](data-factory-copy-activity-tutorial-using-powershell.md):

* Propojené služby typu [Server_ftp](#linked-service-properties)
* Propojené služby typu [azurestorage.](data-factory-azure-blob-connector.md#linked-service-properties)
* Vstup [datovou sadu](data-factory-create-datasets.md) typu [sdílení souborů](#dataset-properties)
* Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [FileSystemSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

Ukázka kopíruje data ze serveru FTP do objektu blob Azure každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

### <a name="ftp-linked-service"></a>Propojená služba FTP

Tento příklad používá základní ověřování, uživatelské jméno a heslo v prostém textu. Můžete také použít jednu z následujících způsobů:

* Anonymní ověřování
* Základní ověřování s zašifrované přihlašovací údaje
* FTP přes SSL/TLS (FTPS)

Najdete v článku [FTP propojená služba](#linked-service-properties) části pro různé typy ověřování můžete použít.

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
    "typeProperties": {
        "host": "myftpserver.com",           
        "authenticationType": "Basic",
        "username": "Admin",
        "password": "123456"
    }
  }
}
```
### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
### <a name="ftp-input-dataset"></a>FTP vstupní datové sady

Tato datová sada odkazuje na složku FTP `mysharedfolder` a soubor `test.csv`. Kanál zkopíruje soubor do cílového umístění.

Nastavení **externí** k **true** informuje služba Data Factory, že je externí k objektu pro vytváření dat datové sady a není vyprodukované aktivitu v datové továrně.

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

### <a name="azure-blob-output-dataset"></a>Výstupní datová sada Azure Blob

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Cesta ke složce pro tento objekt blob se dynamicky vyhodnotí, podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc, den a čas části čas spuštění.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Aktivita kopírování v kanálu s podřízený zdroj a objektů blob systému souborů

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **FileSystemSource**a **podřízený** je typ nastaven na **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```
> [!NOTE]
> Mapování sloupců z datové sady zdroje na sloupce ze sady jímku dat naleznete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

## <a name="next-steps"></a>Další postup
Viz následující články:

* Další informace o klíčových faktorů této dopad výkon přesun dat (aktivita kopírování) v objektu pro vytváření dat a různé způsoby, jak ji optimalizovat, najdete v článku [zkopírujte aktivity výkonu a vyladění průvodce](data-factory-copy-activity-performance.md).

* Podrobné pokyny pro vytvoření kanálu s aktivitou kopírování najdete v tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
