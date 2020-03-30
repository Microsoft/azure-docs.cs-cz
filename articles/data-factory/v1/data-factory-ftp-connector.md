---
title: Přesunutí dat ze serveru FTP pomocí Azure Data Factory
description: Přečtěte si, jak přesunout data ze serveru FTP pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 55c8bf2210eb0990a91aeff1f90e4af4db2c22ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281402"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Přesunutí dat ze serveru FTP pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-ftp-connector.md)
> * [Verze 2 (aktuální verze)](../connector-ftp.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si [téma FTP konektor ve verzi V2](../connector-ftp.md).

Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunutí dat ze serveru FTP. Vychází z článku [Aktivity přesunu dat,](data-factory-data-movement-activities.md) který představuje obecný přehled pohybu dat s aktivitou kopírování.

Data ze serveru FTP můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako jímky naleznete v [tabulce podporovaných úložišť dat.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Data Factory v současné době podporuje pouze přesouvání dat ze serveru FTP do jiných úložišť dat, ale ne přesouvání dat z jiných úložišť dat na server FTP. Podporuje místní i cloudové servery FTP.

> [!NOTE]
> Aktivita kopírování neodstraní zdrojový soubor po úspěšném zkopírování do cílového umístění. Pokud potřebujete po úspěšné kopii odstranit zdrojový soubor, vytvořte vlastní aktivitu k odstranění souboru a použijte aktivitu v kanálu.

## <a name="enable-connectivity"></a>Povolení připojení
Pokud přesouváte data z **místního** serveru FTP do cloudového úložiště dat (například do úložiště objektů Blob Azure), nainstalujte a použijte bránu pro správu dat. Brána pro správu dat je klientský agent, který je nainstalovaný ve vašem místním počítači a umožňuje cloudovým službám připojení k místnímu prostředku. Podrobnosti naleznete v [tématu Brána pro správu dat](data-factory-data-management-gateway.md). Podrobné pokyny k nastavení brány a její používání najdete v tématu [Přesouvání dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md). Bránu se používá k připojení k serveru FTP, i když je server na infrastruktuře Azure jako virtuální počítač (VM) služby (IaaS).

Bránu je možné nainstalovat do stejného místního počítače nebo virtuálního počítače IaaS jako server FTP. Doporučujeme však nainstalovat bránu na samostatný počítač nebo virtuální počítač IaaS, abyste se vyhnuli konfliktům prostředků a pro lepší výkon. Při instalaci brány do samostatného počítače by měl mít počítač přístup k serveru FTP.

## <a name="get-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesune data ze zdroje FTP pomocí různých nástrojů nebo api.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním do datové továrny**. Viz [kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) pro rychlý návod.

K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio**, **PowerShell**, **Šablona Azure Resource Manager**, Rozhraní **.NET API**a ROZHRANÍ REST **API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aktivity.

Bez ohledu na to, zda používáte nástroje nebo api, proveďte následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat.
2. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup.

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory (propojené služby, datové sady a kanál). Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON. Ukázka s definicemi JSON pro entity Data Factory, které se používají ke kopírování dat z úložiště dat FTP, najdete v [příkladu JSON: Kopírování dat ze serveru FTP do objektu blob Azure](#json-example-copy-data-from-ftp-server-to-azure-blob) v tomto článku.

> [!NOTE]
> Podrobnosti o podporovaných formátech souborů a kompresních formátů, které chcete použít, najdete [v tématu Soubory a kompresní formáty v Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Factory dat specifických pro FTP.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb
Následující tabulka popisuje prvky JSON specifické pro propojenou službu FTP.

| Vlastnost | Popis | Požaduje se | Výchozí |
| --- | --- | --- | --- |
| type |Nastavte to na FtpServer. |Ano |&nbsp; |
| host |Zadejte název nebo adresu IP serveru FTP. |Ano |&nbsp; |
| authenticationType |Zadejte typ ověřování. |Ano |Základní, Anonymní |
| uživatelské jméno |Zadejte uživatele, který má přístup k serveru FTP. |Ne |&nbsp; |
| heslo |Zadejte heslo pro uživatele (uživatelské jméno). |Ne |&nbsp; |
| šifrované pověření |Zadejte šifrovaná pověření pro přístup k serveru FTP. |Ne |&nbsp; |
| název brány |Zadejte název brány v bráně pro správu dat pro připojení k místnímu serveru FTP. |Ne |&nbsp; |
| port |Určete port, na kterém server FTP naslouchá. |Ne |21 |
| enableSsl |Určete, zda se má použít FTP přes kanál SSL/TLS. |Ne |true |
| enableServerCertificateValidation |Určete, zda chcete povolit ověření certifikátu SSL serveru, pokud používáte FTP přes kanál SSL/TLS. |Ne |true |

>[!NOTE]
>Konektor FTP podporuje přístup k serveru FTP bez šifrování nebo explicitního šifrování SSL/TLS. nepodporuje implicitní šifrování SSL/TLS.

### <a name="use-anonymous-authentication"></a>Použití anonymního ověřování

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

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Použití uživatelského jména a hesla ve formátu prostého textu pro základní ověřování

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

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Použít port, enableSsl, enableServerCertificateValidation

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

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>Použití šifrovaných přihlašovacích údajů pro ověřování a bránu

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
Úplný seznam oddílů a vlastností dostupných pro definování datových sad naleznete v [tématu Vytváření datových sad](data-factory-create-datasets.md). Oddíly, jako je struktura, dostupnost a zásady datové sady JSON jsou podobné pro všechny typy datových sad.

Oddíl **typeProperties** se liší pro každý typ datové sady. Poskytuje informace, které jsou specifické pro typ datové sady. Oddíl **typeProperties** pro datovou sadu typu **FileShare** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Podcesta ke složce. Pro speciální znaky v řetězci použijte řídicí znak \ ' . Viz Ukázka propojené služby a definice datových sad pro příklady.<br/><br/>Tuto vlastnost můžete kombinovat s **partitionBy** mít cesty ke složkám na základě počátečnía koncové datum řezu. |Ano |
| fileName |Zadejte název souboru ve **složceCesta,** pokud chcete, aby tabulka odkazovala na určitý soubor ve složce. Pokud pro tuto vlastnost nezadáte žádnou hodnotu, tabulka odkazuje na všechny soubory ve složce.<br/><br/>Pokud není pro výstupní datovou sadu zadán název **souboru file,** je název generovaného souboru v následujícím formátu: <br/><br/>`Data.<Guid>.txt`(Příklad: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Ne |
| filtr souboru |Určete filtr, který se použije k výběru podmnožiny souborů ve **složcePath**, nikoli všechny soubory.<br/><br/>Povolené hodnoty `*` jsou: (více `?` znaků) a (jeden znak).<br/><br/>Příklad 1:`"fileFilter": "*.log"`<br/>Příklad 2:`"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** je použitelný pro vstupní datovou sadu FileShare. Tato vlastnost není podporována hadoopovým distribuovaným systémem souborů (HDFS). |Ne |
| partitionedBy |Slouží k určení dynamické **složkyCesta** a **název souboru** pro data časových řad. Můžete například zadat **složkuPath,** která je parametrizována pro každou hodinu dat. |Ne |
| formát | Podporovány jsou následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte vlastnost **type** ve formátu na jednu z těchto hodnot. Další informace naleznete v části [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), [Formát Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formát orků](data-factory-supported-file-and-compression-formats.md#orc-format)a [Formát parket.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Pokud chcete kopírovat soubory tak, jak jsou mezi obchody založenými na souborech (binární kopie), přeskočte oddíl formátu v definicích vstupníi i výstupní datové sady. |Ne |
| komprese | Určete typ a úroveň komprese dat. Podporované typy jsou **GZip**, **Deflate**, **BZip2**a **ZipDeflate**a podporované úrovně jsou **optimální** a **nejrychlejší**. Další informace najdete [v tématu Soubory a kompresní formáty v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |
| useBinaryTransfer |Určete, zda se má použít binární režim přenosu. Hodnoty platí pro binární režim (toto je výchozí hodnota) a false pro ASCII. Tuto vlastnost lze použít pouze v případě, že přidružený typ propojené služby je typu: FtpServer. |Ne |

> [!NOTE]
> **fileName** a **fileFilter** nelze použít současně.

### <a name="use-the-partionedby-property"></a>Použít vlastnost partionedBy
Jak je uvedeno v předchozí části, můžete zadat dynamickou **složkuPath** a **fileName** pro data časových řad s **vlastností partitionedBy.**

Informace o datových sadách časových řad, plánování a řezech naleznete v [tématech Vytváření datových sad](data-factory-create-datasets.md), [Plánování a spuštění](data-factory-scheduling-and-execution.md)a Vytváření [kanálů](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Ukázka 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
V tomto příkladu je {Slice} nahrazen hodnotou systémové proměnné Factory SliceStart v určeném formátu (YYYYMMDDHH). ŘezStart odkazuje na čas zahájení řezu. Cesta ke složce se pro každý řez liší. (Například wikidatagateway/wikisampledataout/2014100103 nebo wikidatagateway/wikisampledataout/2014100104.)

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
V tomto příkladu jsou rok, měsíc, den a čas SliceStart extrahovány do samostatných proměnných, které jsou používány vlastnostmi **folderPath** a **fileName.**

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů a vlastností dostupných pro definování aktivit naleznete v [tématu Vytváření kanálů](data-factory-create-pipelines.md). Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

Vlastnosti, které jsou k dispozici v části **typeProperties** aktivity, se naopak liší podle jednotlivých typů aktivit. Pro aktivitu kopírování se vlastnosti typu liší v závislosti na typech zdrojů a jímek.

V aktivitě kopírování, pokud je zdroj typu **FileSystemSource**, je v části **typeProperties** k dispozici následující vlastnost:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| Rekurzivní |Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. |Pravda, Nepravda (výchozí) |Ne |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>Příklad JSON: Kopírování dat ze serveru FTP do objektu Blob Azure
Tato ukázka ukazuje, jak zkopírovat data ze serveru FTP do úložiště objektů blob Azure. Data však lze zkopírovat přímo do libovolného z jímky uvedené v [podporovaných úložišť dat a formátech](data-factory-data-movement-activities.md#supported-data-stores-and-formats), pomocí aktivity kopírování v datové továrně.

Následující příklady poskytují ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)nebo prostředí [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md):

* Propojená služba typu [FtpServer](#linked-service-properties)
* Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Vstupní [datová sada](data-factory-create-datasets.md) typu [FileShare](#dataset-properties)
* Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [FileSystemSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

Ukázka zkopíruje data ze serveru FTP do objektu blob Azure každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

### <a name="ftp-linked-service"></a>Propojená služba FTP

Tento příklad používá základní ověřování s uživatelským jménem a heslem ve formátu prostého textu. Můžete také použít jeden z následujících způsobů:

* Anonymní ověření
* Základní ověřování se šifrovanými přihlašovacími údaji
* FTP přes SSL/TLS (FTPS)

Různé typy ověřování, které můžete použít, naleznete v části [propojené služby FTP.](#linked-service-properties)

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

Tato datová sada odkazuje na `mysharedfolder` složku `test.csv`a soubor FTP . Kanál zkopíruje soubor do cíle.

Nastavení **externí** **na hodnotu true** informuje službu Data Factory, že datová sada je externí pro datovou továrnu a není vytvářena aktivitou v datové továrně.

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

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části počátečního času rok, měsíc, den a hodiny.

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


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Aktivita kopírování v kanálu se zdrojem systému souborů a jímkou objektů blob

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánována na každou hodinu. V definici Kanálu JSON je **typ zdroje** nastaven na **FileSystemSource**a typ **jímky** je nastaven na **objekt BlobSink**.

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
> Pokud chcete mapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, přečtěte si témat [mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).

## <a name="next-steps"></a>Další kroky
Viz následující články:

* Informace o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivity kopírování) v datové továrně, a o různých způsobech jeho optimalizace najdete v [tématu Průvodce sledováním aktivity kopírování a laděním](data-factory-copy-activity-performance.md).

* Podrobné pokyny k vytvoření kanálu s aktivitou kopírování naleznete v [tématu Kopírování aktivity kurzu](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
