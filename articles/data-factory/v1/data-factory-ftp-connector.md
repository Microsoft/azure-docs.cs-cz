---
title: Přesouvání dat ze serveru FTP pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Další informace o tom, jak přesunout data z FTP server pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 20f1d8ca67a38a9dc262845d87b77e2bc3fc9fb7
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55810575"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Přesun dat pomocí služby Azure Data Factory ze serveru FTP
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-ftp-connector.md)
> * [Verze 2 (aktuální verze)](../connector-ftp.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor FTP ve verzi V2](../connector-ftp.md).

Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunu dat ze serveru FTP. Je nástavbou [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který nabízí obecný přehled o přesun dat pomocí aktivity kopírování.

Kopírovat data ze serveru FTP na jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako jímky, najdete v článku [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. Data Factory v současné době podporuje pouze přesouvá data ze serveru FTP do jiných úložišť dat, ale ne přesouvá data z jiných data ukládá na FTP server. Podporuje i s místními a cloudovými servery FTP.

> [!NOTE]
> Aktivita kopírování nedojde k odstranění zdrojového souboru, co se úspěšně zkopíruje do cíle. Pokud je potřeba odstranit zdrojový soubor po úspěšném kopírování, vytvoření vlastní aktivity odstranit soubor a pomocí aktivity v kanálu.

## <a name="enable-connectivity"></a>Povolení připojení
Pokud přesouváte data z **místní** serveru FTP do cloudu data ukládat (například do úložiště objektů Blob v Azure), nainstalovat a používat bránu správy dat. Brána správy dat je klientský agent, který je nainstalován na svém místním počítači a umožňuje cloudové služby pro připojení k místnímu prostředku. Podrobnosti najdete v tématu [brána správy dat](data-factory-data-management-gateway.md). Pro podrobné pokyny týkající se nastavení brány nahoru a jeho použití, naleznete v tématu [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md). Použití brány pro připojení k serveru FTP, i v případě, že je server v Azure infrastruktury jako služba (IaaS) virtuálních počítačů (VM).

Je možné nainstalovat na stejnou místní počítač nebo virtuální počítač IaaS jako FTP server. Doporučujeme však, že bránu instalujete na samostatný počítač nebo virtuální počítač IaaS předejít sporu prostředků a pro lepší výkon. Když bránu instalujete na samostatný počítač, na počítači měli mít přístup k serveru FTP.

## <a name="get-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z FTP zdroje pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním služby Data Factory**. Zobrazit [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) rychlý návod.

Tyto nástroje můžete také použít k vytvoření kanálu: **Azure portal**, **sady Visual Studio**, **PowerShell**, **šablony Azure Resource Manageru**, **rozhraní .NET API**a **Rozhraní REST API**. Zobrazit [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování.

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do služby data factory.
2. Vytvoření **datových sad** k představují vstupní a výstupní data pro operaci kopírování.
3. Vytvoření **kanálu** s aktivitou kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup.

Při použití Průvodce definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál) se automaticky vytvoří za vás. Při použití nástroje nebo rozhraní API (s výjimkou rozhraní .NET API), můžete definovat tyto entity služby Data Factory ve formátu JSON. Tady je příklad s definice JSON entit služby Data Factory, které se používají ke kopírování dat z úložiště dat serveru FTP, najdete v článku [příklad JSON: Kopírování dat ze serveru FTP do objektů blob v Azure](#json-example-copy-data-from-ftp-server-to-azure-blob) části tohoto článku.

> [!NOTE]
> Podrobnosti o podporovaných formátech souborů a komprese používat, naleznete v tématu [formáty souborů a komprese ve službě Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

Následující části obsahují podrobnosti o vlastnostech JSON, které se používají k definování entit služby Data Factory konkrétní FTP.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka popisuje elementy JSON jsou specifické pro službu FTP propojené.

| Vlastnost | Popis | Požaduje se | Výchozí |
| --- | --- | --- | --- |
| type |Nastavte na Server_ftp. |Ano |&nbsp; |
| hostitel |Zadejte název nebo IP adresu serveru FTP. |Ano |&nbsp; |
| authenticationType. |Zadejte typ ověřování. |Ano |Basic, anonymní |
| uživatelské jméno |Zadejte uživatele, který má přístup k serveru FTP. |Ne |&nbsp; |
| heslo |Zadejte heslo pro uživatele (uživatelské jméno). |Ne |&nbsp; |
| encryptedCredential |Zadejte šifrované přihlašovací údaje pro přístup k serveru FTP. |Ne |&nbsp; |
| gatewayName |Zadejte název brány v brána správy dat pro připojení k serveru FTP na místě. |Ne |&nbsp; |
| port |Zadejte port, na kterém naslouchá FTP server. |Ne |21 |
| enableSsl |Určete, zda chcete pomocí funkce FTP přes kanál SSL/TLS. |Ne |true (pravda) |
| enableServerCertificateValidation |Určete, zda povolit ověření certifikátu serveru SSL při použití FTP přes kanál SSL/TLS. |Ne |true (pravda) |

>[!NOTE]
>Konektor FTP podporuje přístup k serveru FTP bez šifrování nebo explicitní šifrování SSL/TLS. nepodporuje implicitní šifrování SSL/TLS.

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

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Pomocí uživatelského jména a hesla v prostém textu pro základní ověřování

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

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Use port, enableSsl, enableServerCertificateValidation

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
Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, naleznete v tématu [vytváření datových sad](data-factory-create-datasets.md). Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datové sady.

**TypeProperties** oddílu se liší pro každý typ datové sady. Poskytuje informace, které jsou specifické pro daný typ datové sady. **TypeProperties** části datové sady typu **sdílení souborů** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Podřízená cesta ke složce. Použijte řídicí znak "\" pro zvláštní znaky v řetězci. Viz ukázka propojené služby a datové sady definice příklady.<br/><br/>Můžete zkombinovat tato vlastnost se **partitionBy** cesty ke složkám podle řez start a end data a časy. |Ano |
| fileName |Zadejte název souboru **folderPath** Pokud má tabulka, která má odkazovat na konkrétní soubor ve složce. Pokud je nezadávejte žádnou hodnotu pro tuto vlastnost, v tabulce odkazuje na všechny soubory ve složce.<br/><br/>Když **fileName** není zadaný pro výstupní datovou sadu, název generovaného souboru je v následujícím formátu: <br/><br/>Data. <Guid>.txt (například: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Ne |
| fileFilter |Určete filtr, který slouží k výběru podmnožinu souborů v **folderPath**, ne všechny soubory.<br/><br/>Povolené hodnoty jsou: `*` (více znaků) a `?` (jeden znak).<br/><br/>Příklad 1: `"fileFilter": "*.log"`<br/>Příklad 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** platí pro vstupní datovou sadu sdílení souborů. Tato vlastnost není podporována s HDFS Hadoop Distributed File System (). |Ne |
| partitionedBy |Používá se k určení dynamické **folderPath** a **fileName** dat časové řady. Například můžete zadat **folderPath** , který je s parametry pro každou hodinu data. |Ne |
| formát | Jsou podporovány následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formát](data-factory-supported-file-and-compression-formats.md#orc-format), a [formát Parquet ](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete zkopírovat soubory jsou mezi souborové úložiště (binární kopie), přeskočte část o formátu v definicích oba vstupní a výstupní datové sady. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**, a jsou podporované úrovně **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese ve službě Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |
| useBinaryTransfer |Určete, jestli se má použít režim binární přenos. Hodnoty jsou true pro binárním režimu (to je výchozí hodnota) a hodnotu false pro ASCII. Tuto vlastnost lze použít pouze v případě typu přidružené propojené služby typu: FtpServer. |Ne |

> [!NOTE]
> **Název souboru** a **fileFilter** nelze používat současně.

### <a name="use-the-partionedby-property"></a>Použijte vlastnost partionedBy
Jak je uvedeno v předchozí části, můžete určit dynamickou **folderPath** a **fileName** pro data časových řad s **partitionedBy** vlastnost.

Další informace o čase řady datových sad, plánování a řezů, naleznete v tématu [vytváření datových sad](data-factory-create-datasets.md), [plánování a provádění](data-factory-scheduling-and-execution.md), a [vytváření kanálů](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Ukázka 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
V tomto příkladu {řez} se nahradí hodnotu proměnné objektu pro vytváření dat systému SliceStart, ve formátu určeném (YYYYMMDDHH). Vlastnosti SliceStart odkazuje na počáteční čas řezu. Cesta ke složce se liší pro každý řez. (Například wikidatagateway/wikisampledataout/2014100103 nebo wikidatagateway/wikisampledataout/2014100104.)

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
V tomto příkladu se extrahují rok, měsíc, den a čas z vlastnosti SliceStart do samostatných proměnných, které jsou používány **folderPath** a **fileName** vlastnosti.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [vytváření kanálů](data-factory-create-pipelines.md). Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

K dispozici ve vlastnosti **typeProperties** části aktivity, na druhé straně lišit podle typu každé aktivity. Pro aktivitu kopírování typ vlastnosti lišit v závislosti na typy zdroje a jímky.

V aktivitě kopírování, pokud je zdroj typu **FileSystemSource**, následující vlastnost je k dispozici v **typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| rekurzivní |Určuje, jestli se data číst rekurzivně z podsložky, nebo jenom ze zadané složky. |Hodnota TRUE, False (výchozí) |Ne |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>Příklad JSON: Kopírování dat ze serveru FTP do objektů Blob v Azure
Tato ukázka předvádí, jak kopírovat data ze serveru FTP do úložiště objektů Blob v Azure. Ale data se dají zkopírovat přímo do libovolné jímky uvádí [podporovaných úložišť dat a formáty](data-factory-data-movement-activities.md#supported-data-stores-and-formats), s využitím aktivity kopírování ve službě Data Factory.

Následující příklady popisují ukázkový JSON definice, které můžete použít k vytvoření kanálu pomocí [webu Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), nebo [Powershellu](data-factory-copy-activity-tutorial-using-powershell.md):

* Propojené služby typu [Server_ftp](#linked-service-properties)
* Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [sdílenou složku.](#dataset-properties)
* Výstup [datovou sadu](data-factory-create-datasets.md) typu [Azureblobu](data-factory-azure-blob-connector.md#dataset-properties)
* A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [FileSystemSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

Ukázce kopíruje data ze serveru FTP do objektu blob Azure každou hodinu. Vlastnostech JSON použitých v tyto ukázky jsou popsány v části podle ukázky.

### <a name="ftp-linked-service"></a>Propojená služba FTP

Tento příklad používá základní ověřování pomocí uživatelského jména a hesla v prostém textu. Můžete také použít jednu z následujících způsobů:

* Anonymní ověřování
* Základní ověřování se zašifrovanými přihlašovacími údaji
* FTP přes SSL/TLS (FTP)

Najdete v článku [FTP propojená služba](#linked-service-properties) oddílu pro různé typy ověřování můžete použít.

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

Tato datová sada odkazuje na složce serveru FTP `mysharedfolder` a soubor `test.csv`. Kanál kopíruje soubor do cílového umístění.

Nastavení **externí** k **true** služby Data Factory informuje, že datová sada je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

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

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob se dynamicky vyhodnocuje, podle času spuštění řezu, který se právě zpracovává. Cesta ke složce používá rok, měsíc, den a části hodin čas spuštění.

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


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Aktivita kopírování v kanálu pomocí systému souborů zdroje a objektů blob jímky

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **FileSystemSource**a **jímky** je typ nastaven na **BlobSink**.

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
> Pokud chcete namapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, najdete v článku [mapování sloupců v datové sadě ve službě Azure Data Factory](data-factory-map-columns.md).

## <a name="next-steps"></a>Další postup
Viz následující články:

* Další informace o klíčových faktorů této ovlivnit výkon přesouvání dat (aktivita kopírování) ve službě Data Factory a různé způsoby, jak optimalizovat, najdete v článku [Průvodce laděním a výkonem aktivity kopírování](data-factory-copy-activity-performance.md).

* Podrobné pokyny pro vytvoření kanálu s aktivitou kopírování najdete v tématu [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
