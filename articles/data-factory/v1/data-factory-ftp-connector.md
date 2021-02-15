---
title: Přesunutí dat ze serveru FTP pomocí Azure Data Factory
description: Přečtěte si informace o tom, jak přesunout data ze serveru FTP pomocí Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a8a8ca44fbdb7610f85bc53c23d502d2efb01c8b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363946"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Přesunutí dat ze serveru FTP pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-ftp-connector.md)
> * [Verze 2 (aktuální verze)](../connector-ftp.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor FTP v v2](../connector-ftp.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory přesouvat data ze serveru FTP. Sestavuje se podle článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který prezentuje obecný přehled přesunu dat s aktivitou kopírování.

Data můžete kopírovat ze serveru FTP do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která aktivita kopírování podporuje jako jímky, najdete v tabulce [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Data Factory aktuálně podporuje pouze přesun dat ze serveru FTP do jiných úložišť dat, ale nepřesouvá data z jiných úložišť dat na server FTP. Podporuje místní i cloudové servery FTP.

> [!NOTE]
> Aktivita kopírování neodstraní zdrojový soubor po úspěšném zkopírování do cíle. Pokud potřebujete zdrojový soubor po úspěšné kopii odstranit, vytvořte vlastní aktivitu k odstranění tohoto souboru a použijte aktivitu v kanálu.

## <a name="enable-connectivity"></a>Povolit připojení
Pokud přesouváte data z **místního** serveru FTP do cloudového úložiště dat (například do Azure Blob Storage), nainstalujte a použijte Správa dat bránu. Brána Správa dat je klientský Agent, který je nainstalovaný na vašem místním počítači a umožňuje cloudovým službám připojit se k místnímu prostředku. Podrobnosti najdete v tématu [Správa dat Gateway](data-factory-data-management-gateway.md). Podrobné pokyny k nastavení brány a jejím používání najdete v tématu [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md). Bránu použijete k připojení k serveru FTP, i když je server na virtuálním počítači infrastruktury Azure jako služba (IaaS).

Bránu je možné nainstalovat na stejný místní počítač nebo virtuální počítač IaaS jako server FTP. Doporučujeme ale nainstalovat bránu do samostatného počítače nebo virtuálního počítače s IaaS, abyste se vyhnuli kolize prostředků a zajistili lepší výkon. Když bránu nainstalujete do samostatného počítače, počítač by měl mít přístup k serveru FTP.

## <a name="get-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data ze zdroje FTP pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním Data Factory**. Rychlý návod najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) .

K vytvoření kanálu můžete použít taky následující nástroje: **Visual Studio**, **PowerShell**, **Azure Resource Manager template**, **.NET API** a **REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, použijte následující postup k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou.
2. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup.

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje nebo rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON. Ukázku s definicemi JSON pro Data Factory entity, které se používají ke kopírování dat z úložiště dat FTP, najdete v části [příklad JSON: kopírování dat z FTP serveru do Azure Blob](#json-example-copy-data-from-ftp-server-to-azure-blob) tohoto článku.

> [!NOTE]
> Podrobnosti o podporovaných formátech souborů a kompresi pro použití naleznete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

Následující části obsahují podrobné informace o vlastnostech JSON, které slouží k definování Data Factorych entit specifických pro protokol FTP.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka popisuje elementy JSON specifické pro propojenou službu FTP.

| Vlastnost | Popis | Povinné | Výchozí |
| --- | --- | --- | --- |
| typ |Nastavte tuto hodnotu na FtpServer. |Yes |&nbsp; |
| Hostitel |Zadejte název nebo IP adresu serveru FTP. |Yes |&nbsp; |
| authenticationType |Zadejte typ ověřování. |Yes |Základní, anonymní |
| username |Zadejte uživatele, který má přístup k serveru FTP. |No |&nbsp; |
| heslo |Zadejte heslo pro uživatele (uživatelské jméno). |No |&nbsp; |
| encryptedCredential |Zadejte šifrované přihlašovací údaje pro přístup k serveru FTP. |No |&nbsp; |
| gatewayName |Zadejte název brány v bráně Správa dat Gateway pro připojení k místnímu serveru FTP. |No |&nbsp; |
| port |Zadejte port, na kterém server FTP naslouchá. |No |21 |
| enableSsl |Určete, jestli se má používat FTP přes kanál SSL/TLS. |No |true |
| enableServerCertificateValidation |Určete, jestli se má povolit ověřování certifikátu TLS/SSL serveru při použití kanálu FTP přes SSL/TLS. |No |true |

>[!NOTE]
>Konektor FTP podporuje přístup k serveru FTP bez šifrování nebo explicitního šifrování pomocí protokolu SSL/TLS; nepodporuje implicitní šifrování SSL/TLS.

### <a name="use-anonymous-authentication"></a>Použít anonymní ověřování

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

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Pro základní ověřování použít uživatelské jméno a heslo v prostém textu

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

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>Použití encryptedCredential pro ověřování a bránu

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
Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, najdete v tématu [vytváření datových sad](data-factory-create-datasets.md). Oddíly, jako jsou struktura, dostupnost a zásady pro datovou sadu JSON, jsou podobné pro všechny typy datových sad.

Oddíl **typeProperties** se liší pro každý typ datové sady. Poskytuje informace, které jsou specifické pro typ datové sady. Oddíl **typeProperties** pro datovou sadu typu **Shared** má následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| folderPath |Dílčí cesta ke složce Pro speciální znaky v řetězci použijte řídicí znak ' \ '. Příklady najdete v tématu Ukázka propojené služby a definice datových sad.<br/><br/>Tuto vlastnost můžete kombinovat s **partitionBy** a mít tak cesty ke složkám na základě počátečního a koncového data v řezu. |Yes |
| fileName |Pokud chcete, aby tabulka odkazovala na konkrétní soubor ve složce, zadejte název souboru do **FolderPath** . Pokud pro tuto vlastnost nezadáte žádnou hodnotu, odkazuje tabulka na všechny soubory ve složce.<br/><br/>Pokud není zadán **název souboru** pro výstupní datovou sadu, je název vygenerovaného souboru v následujícím formátu: <br/><br/>`Data.<Guid>.txt` (Příklad: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |No |
| fileFilter |Určete filtr, který se použije k výběru podmnožiny souborů v **FolderPath**, nikoli všech souborů.<br/><br/>Povolené hodnoty jsou: `*` (více znaků) a `?` (jeden znak).<br/><br/>Příklad 1: `"fileFilter": "*.log"`<br/>Příklad 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> pro sadu vstupních dat Shared je použitelný **Filtr** . Tato vlastnost není podporována se systémem Hadoop systém souborů DFS (Distributed File System) (HDFS). |No |
| partitionedBy |Slouží k zadání dynamického **FolderPath** a **názvu souboru** pro data časové řady. Můžete například zadat **FolderPath** , který je parametrizovaný za každou hodinu dat. |No |
| formát | Podporovány jsou následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. V části formát nastavte vlastnost **typ** na jednu z těchto hodnot. Další informace najdete v oddílech [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), [formát JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), formát [ORC](data-factory-supported-file-and-compression-formats.md#orc-format)a formát [Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Pokud chcete kopírovat soubory, protože jsou mezi úložišti na základě souborů (binární kopie), přeskočte oddíl formát v definicích vstupní i výstupní datové sady. |No |
| komprese | Zadejte typ a úroveň komprese dat. Podporované typy jsou **gzip**, **Deflate**, **bzip2** a **ZipDeflate** a podporované úrovně jsou **optimální** a **nejrychlejší**. Další informace naleznete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |
| useBinaryTransfer |Určete, zda se má použít režim binárního přenosu. Hodnoty jsou pravdivé pro binární režim (Jedná se o výchozí hodnotu) a false pro ASCII. Tato vlastnost se dá použít, jenom když je přidružený typ propojené služby typ: FtpServer. |No |

> [!NOTE]
> **filename** a **FileFilter** nelze použít současně.

### <a name="use-the-partionedby-property"></a>Použití vlastnosti partionedBy
Jak je uvedeno v předchozí části, můžete zadat dynamický **FolderPath** a **filename** pro data časové řady s vlastností **partitionedBy** .

Další informace o datových sadách časových řad, plánování a řezech najdete v tématu [vytváření datových sad](data-factory-create-datasets.md), [plánování a spouštění](data-factory-scheduling-and-execution.md)a [vytváření kanálů](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Ukázka 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
V tomto příkladu je {Slice} nahrazen hodnotou Data Factory systémové proměnné vlastnosti slicestart v zadaném formátu (YYYYMMDDHH). Vlastnosti slicestart odkazuje na počáteční čas řezu. Cesta ke složce je pro každý řez odlišná. (Například wikidatagateway/wikisampledataout/2014100103 nebo wikidatagateway/wikisampledataout/2014100104.)

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
V tomto příkladu je rok, měsíc, den a čas vlastnosti slicestart extrahován do samostatných proměnných, které jsou používány vlastnostmi **FolderPath** a **filename** .

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů a vlastností dostupných pro definování aktivit najdete v tématu [vytváření kanálů](data-factory-create-pipelines.md). Pro všechny typy aktivit jsou k dispozici vlastnosti, jako je název, popis, vstupní a výstupní tabulka a zásady.

Vlastnosti, které jsou k dispozici v části **typeProperties** aktivity, se liší podle typu jednotlivých aktivit. Pro aktivitu kopírování se vlastnosti typu liší v závislosti na typech zdrojů a jímky.

Pokud je zdroj v aktivitě kopírování typu **FileSystemSource**, je v části **typeProperties** k dispozici následující vlastnost:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| zahrnout |Určuje, zda mají být data rekurzivně čtena z podsložek nebo pouze ze zadané složky. |True, false (výchozí) |No |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>Příklad JSON: kopírování dat ze serveru FTP do Azure Blob
V této ukázce se dozvíte, jak kopírovat data ze serveru FTP do úložiště objektů BLOB v Azure. Data je ale možné zkopírovat přímo do kterékoli z jímky uvedených v [podporovaných úložištích a formátech dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats)pomocí aktivity kopírování v Data Factory.

V následujících příkladech jsou uvedeny ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)nebo [PowerShellu](data-factory-copy-activity-tutorial-using-powershell.md):

* Propojená služba typu [FTPserver](#linked-service-properties)
* Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Vstupní [datová sada](data-factory-create-datasets.md) typu [sdílení](#dataset-properties) souborů
* Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties)
* [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [FileSystemSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

Ukázka kopíruje data ze serveru FTP do objektu blob Azure každou hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

### <a name="ftp-linked-service"></a>Propojená služba FTP

V tomto příkladu se používá základní ověřování s uživatelským jménem a heslem v prostém textu. Můžete také použít některý z následujících způsobů:

* Anonymní ověření
* Základní ověřování pomocí šifrovaných přihlašovacích údajů
* FTP přes SSL/TLS (FTPS)

Různé typy ověřování, které můžete použít, najdete v části [propojená služba FTP](#linked-service-properties) .

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
### <a name="ftp-input-dataset"></a>Vstupní datová sada FTP

Tato datová sada odkazuje na složku `mysharedfolder` a soubor FTP `test.csv` . Kanál zkopíruje soubor do cílového umístění.

Když se nastaví **vlastnost** **External** na true, informuje Data Factory službu o tom, že datová sada je externí pro objekt pro vytváření dat, a není vytvořená aktivitou v datové továrně.

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

Data se zapisují do nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt BLOB je dynamicky vyhodnocena na základě počátečního času zpracovávaného řezu. Cesta ke složce používá část roku, měsíce, dne a hodiny počátečního času.

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


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Aktivita kopírování v kanálu se zdrojem systému souborů a jímkou objektů BLOB

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady, a je naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **FileSystemSource** a typ **jímky** je nastavený na **BlobSink**.

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
> Chcete-li mapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, přečtěte si téma [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="next-steps"></a>Další kroky
Viz následující články:

* Další informace o klíčových faktorech, které mají vliv na výkon pohybu dat (aktivita kopírování) v Data Factory a různé způsoby jejich optimalizace, najdete v [Průvodci výkonem a laděním aktivity kopírování](data-factory-copy-activity-performance.md).

* Podrobné pokyny pro vytvoření kanálu s aktivitou kopírování najdete v [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
