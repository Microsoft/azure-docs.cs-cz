---
title: Přesunutí dat ze serveru SFTP pomocí Azure Data Factory
description: Přečtěte si, jak přesouvat data z místního nebo cloudového serveru SFTP pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3f78934fb11dd4f9e34bf27d565d471d47f250b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265802"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Přesunutí dat ze serveru SFTP pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-sftp-connector.md)
> * [Verze 2 (aktuální verze)](../connector-sftp.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [SFTPconnector ve verzi 2](../connector-sftp.md).

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory přesunout data z místního/cloudového serveru SFTP do podporovaného úložiště dat jímky. Tento článek vychází z článku [aktivity přesundat dat,](data-factory-data-movement-activities.md) který představuje obecný přehled přesunu dat s aktivitou kopírování a seznam úložišť dat podporovaných jako zdroje/jímky.

Data Factory v současné době podporuje pouze přesunutí dat ze serveru SFTP do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat na server SFTP. Podporuje místní i cloudové servery SFTP.

> [!NOTE]
> Aktivita kopírování neodstraní zdrojový soubor po úspěšném zkopírování do cílového umístění. Pokud potřebujete po úspěšné kopii odstranit zdrojový soubor, vytvořte vlastní aktivitu, která soubor odstraní a použije aktivitu v kanálu.

## <a name="supported-scenarios-and-authentication-types"></a>Podporované scénáře a typy ověřování
Tento konektor SFTP můžete použít ke kopírování dat z **cloudových serverů SFTP i z místních serverů SFTP**. **Základní** typy ověřování a **SshPublicKey** jsou podporovány při připojování k serveru SFTP.

Při kopírování dat z místního serveru SFTP je potřeba nainstalovat bránu pro správu dat v místním prostředí/virtuálním počítači Azure. Podrobnosti o bráně najdete v [tématu Brána pro správu dat.](data-factory-data-management-gateway.md) Podrobné pokyny k nastavení brány a jeho používání najdete v článku [přesunutí dat mezi místními lokalitami a](data-factory-move-data-between-onprem-and-cloud.md) článkem cloudu.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesune data ze zdroje SFTP pomocí různých nástrojů nebo api.

- Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Viz [Kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) pro rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat.

- K vytvoření kanálu můžete taky použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Šablona Azure Resource Manager**, Rozhraní **.NET API**a REST **API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aktivity. Ukázky JSON ke kopírování dat ze serveru SFTP do úložiště objektů blob Azure najdete v tématu [Příklad JSON: Kopírování dat ze serveru SFTP do oddílu objektu blob Azure](#json-example-copy-data-from-sftp-server-to-azure-blob) v tomto článku.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb
V následující tabulce je uveden popis prvků JSON specifických pro propojenou službu FTP.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type | Vlastnost type musí být `Sftp`nastavena na . |Ano |
| host | Název nebo IP adresa serveru SFTP. |Ano |
| port |Port, na kterém naslouchá server SFTP. Výchozí hodnota je: 21 |Ne |
| authenticationType |Zadejte typ ověřování. Povolené hodnoty: **Základní**, **SshPublicKey**. <br><br> Odkazovat [na použití základní ověřování](#using-basic-authentication) a pomocí [SSH veřejného klíče ověřování](#using-ssh-public-key-authentication) oddíly na více vlastností a ukázky JSON v uvedeném pořadí. |Ano |
| skipHostKeyValidation | Určete, zda má být ověření klíče hostitele přeskočte. | Ne. Výchozí hodnota: false |
| hostKeyFingerprint | Určete otisk prstu klíče hostitele. | Ano, `skipHostKeyValidation` pokud je nastavena na false.  |
| název brány |Název brány pro správu dat pro připojení k místnímu serveru SFTP. | Ano, pokud kopírování dat z místního serveru SFTP. |
| šifrované pověření | Šifrovaná pověření pro přístup k serveru SFTP. Automaticky generované při zadání základního ověřování (uživatelské jméno + heslo) nebo ověřování SshPublicKey (uživatelské jméno + cesta soukromého klíče nebo obsah) v průvodci kopírováním nebo v dialogovém okně ClickOnce. | Ne. Použít pouze při kopírování dat z místního serveru SFTP. |

### <a name="using-basic-authentication"></a>Použití základního ověřování

Chcete-li použít `authenticationType` základní `Basic`ověřování, nastavte jako , a zadejte následující vlastnosti kromě obecných vlastností konektoru SFTP zavedených v poslední části:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| uživatelské jméno | Uživatel, který má přístup k serveru SFTP. |Ano |
| heslo | Heslo pro uživatele (uživatelské jméno). | Ano |

#### <a name="example-basic-authentication"></a>Příklad: Základní ověřování
```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Příklad: Základní ověřování se šifrovanými přihlašovacími údaji

```JSON
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
      }
}
```

### <a name="using-ssh-public-key-authentication"></a>Použití ověřování pomocí veřejného klíče SSH

Chcete-li použít ověřování pomocí `authenticationType` `SshPublicKey`veřejného klíče SSH, nastavte jako , a zadejte následující vlastnosti kromě obecných vlastností konektoru SFTP zavedených v poslední části:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| uživatelské jméno |Uživatel, který má přístup k serveru SFTP |Ano |
| privátnícesta | Zadejte absolutní cestu k souboru soukromého klíče, ke kterému má brána přístup. | Zadejte `privateKeyPath` buď `privateKeyContent`nebo . <br><br> Použít pouze při kopírování dat z místního serveru SFTP. |
| privateKeyContent | Serializovaný řetězec obsahu soukromého klíče. Průvodce kopírováním může číst soubor soukromého klíče a automaticky extrahovat obsah soukromého klíče. Pokud používáte jiný nástroj/sadu SDK, použijte místo toho vlastnost privateKeyPath. | Zadejte `privateKeyPath` buď `privateKeyContent`nebo . |
| Heslo | Zadejte heslo/heslo pro dešifrování soukromého klíče, pokud je soubor klíče chráněn přístupovou frází. | Ano, pokud je soubor soukromého klíče chráněn příchytnou frází. |

> [!NOTE]
> Konektor SFTP podporuje klíč RSA/DSA OpenSSH. Ujistěte se, že obsah souboru klíče začíná "-----BEGIN [RSA/DSA] PRIVATE KEY-----". Pokud je soubor soukromého klíče souborve formátu ppk, použijte nástroj Putty k převodu z .ppk do formátu OpenSSH.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Příklad: Ověřování SshPublicKey pomocí filePath soukromého klíče

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Příklad: Ověřování SshPublicKey pomocí obsahu soukromého klíče

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datových sad, naleznete v článku [Vytváření datových sad.](data-factory-create-datasets.md) Oddíly, jako je struktura, dostupnost a zásady datové sady JSON jsou podobné pro všechny typy datových sad.

Oddíl **typeProperties** se liší pro každý typ datové sady. Poskytuje informace, které jsou specifické pro typ datové sady. Oddíl typeProperties pro datovou sadu datové sady **FileShare** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Dílčí cesta ke složce. Pro speciální znaky v řetězci použijte řídicí znak \ ' . Viz Ukázka propojené služby a definice datových sad pro příklady.<br/><br/>Tuto vlastnost můžete kombinovat s **partitionBy** mít cesty ke složkám na základě počátečního a koncového data řezu. |Ano |
| fileName |Zadejte název souboru ve **složceCesta,** pokud chcete, aby tabulka odkazovala na určitý soubor ve složce. Pokud pro tuto vlastnost nezadáte žádnou hodnotu, tabulka odkazuje na všechny soubory ve složce.<br/><br/>Pokud není pro výstupní datovou sadu zadán název souboru file, bude název generovaného souboru v následujícím formátu: <br/><br/>`Data.<Guid>.txt`(Příklad: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Ne |
| filtr souboru |Určete filtr, který se použije k výběru podmnožiny souborů ve složceCesta, nikoli všechny soubory.<br/><br/>Povolené hodnoty `*` jsou: (více `?` znaků) a (jeden znak).<br/><br/>Příklady 1:`"fileFilter": "*.log"`<br/>Příklad 2:`"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter je použitelný pro vstupní datovou sadu FileShare. Tato vlastnost není podporována pomocí hdfs. |Ne |
| partitionedBy |partitionedBy lze použít k určení dynamické složkyPath, název souboru pro data časových řad. Například folderPath parametrizovánpro každou hodinu dat. |Ne |
| formát | Podporovány jsou následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte vlastnost **type** ve formátu na jednu z těchto hodnot. Další informace naleznete v části [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), Formát [Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), Formát [orků](data-factory-supported-file-and-compression-formats.md#orc-format)a [Formát parket.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Pokud chcete **kopírovat soubory jako -je** mezi obchody založenými na souborech (binární kopie), přeskočte oddíl formátu v definicích vstupní i výstupní datové sady. |Ne |
| komprese | Určete typ a úroveň komprese dat. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**a **ZipDeflate**. Podporované úrovně jsou: **Optimální** a **nejrychlejší**. Další informace najdete [v tématu Soubory a kompresní formáty v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |
| useBinaryTransfer |Určete, zda je nutné použít režim binárního přenosu. True pro binární režim a false ASCII. Výchozí hodnota: True. Tuto vlastnost lze použít pouze v případě, že přidružený typ propojené služby je typu: FtpServer. |Ne |

> [!NOTE]
> název souboru a fileFilter nelze použít současně.

### <a name="using-partionedby-property"></a>Použití partionedBy vlastnost
Jak je uvedeno v předchozí části, můžete zadat dynamickou složkuPath, název souboru pro data časových řad s partitionedBy. Můžete tak učinit s makry Datové továrny a systémovou proměnnou SliceStart, SliceEnd, které označují logické časové období pro daný datový řez.

Informace o datových sadách časových řad, plánování a řezech naleznete v [tématech Vytváření datových sad](data-factory-create-datasets.md), [Plánování & spuštění](data-factory-scheduling-and-execution.md)a Vytváření článků [kanálů.](data-factory-create-pipelines.md)

#### <a name="sample-1"></a>Vzorek 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
V tomto příkladu {Slice} je nahrazen hodnotou data factory systémové proměnné SliceStart ve formátu (YYYYMMDDHH) zadaný. ŘezStart odkazuje na čas zahájení řezu. SložkaPath se liší pro každý řez. Příklad: wikidatagateway/wikisampledataout/2014100103 nebo wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Ukázka 2:

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
V tomto příkladu jsou rok, měsíc, den a čas SliceStart extrahovány do samostatných proměnných, které jsou používány vlastnostmi folderPath a fileName.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

Vzhledem k tomu, vlastnosti, které jsou k dispozici v typeProperties části aktivity se liší podle každého typu aktivity. U aktivity Kopírování se vlastnosti typu liší v závislosti na typech zdrojů a jímek.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Podporované formáty souborů a komprese
V článku [Soubory a kompresní formáty v Azure Data Factory](data-factory-supported-file-and-compression-formats.md) najdete podrobnosti.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>Příklad JSON: Kopírování dat ze serveru SFTP do objektu blob Azure
Následující příklad obsahuje ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí [Sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo Azure [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazují, jak zkopírovat data ze zdroje SFTP do úložiště objektů blob Azure. Data však můžete zkopírovat **přímo** z libovolného zdroje do libovolného [jímky uvedené zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

> [!IMPORTANT]
> Tato ukázka obsahuje úryvky JSON. Neobsahuje podrobné pokyny pro vytvoření datové továrny. Podrobné pokyny najdete v článku [přesunutí dat mezi místními umístěními a článkem cloudu.](data-factory-move-data-between-onprem-and-cloud.md)

Ukázka má následující entity datové továrny:

* Propojená služba typu [sftp](#linked-service-properties).
* Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní [datová sada](data-factory-create-datasets.md) typu [FileShare](#dataset-properties).
* Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, který používá [FileSystemSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data ze serveru SFTP do objektu blob Azure každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

**Propojená služba SFTP**

Tento příklad používá základní ověřování s uživatelským jménem a heslem ve formátu prostého textu. Můžete také použít jeden z následujících způsobů:

* Základní ověřování se šifrovanými přihlašovacími údaji
* Ověřování pomocí veřejného klíče SSH

Různé typy ověřování, které můžete použít, naleznete v části Propojené [služby FTP.](#linked-service-properties)

```JSON

{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "myuser",
            "password": "mypassword",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```
**Propojená služba Azure Storage**

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
**Vstupní datová sada SFTP**

Tato datová sada odkazuje na `mysharedfolder` složku `test.csv`a soubor SFTP . Kanál zkopíruje soubor do cíle.

Nastavení "externí": "true" informuje službu Data Factory, že datová sada je externí pro datové továrny a není vyráběna aktivitou v datové továrně.

```JSON
{
  "name": "SFTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "SftpLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Výstupní datová sada Azure Blob**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části počátečního času rok, měsíc, den a hodiny.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Kanál s aktivitou Kopírování**

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánována na každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **FileSystemSource** a typ **jímky** je nastaven na **Objekt BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
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
        "start": "2017-02-20T18:00:00Z",
        "end": "2017-02-20T19:00:00Z"
    }
}
```

## <a name="performance-and-tuning"></a>Výkon a ladění
[V tématu Průvodce sledováním výkonu & optimalizací se](data-factory-copy-activity-performance.md) dozvíte o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivita kopírování) ve Službě Azure Data Factory, a o různých způsobech jeho optimalizace.

## <a name="next-steps"></a>Další kroky
Viz následující články:

* [Zkopírovat kurz aktivity](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pro podrobné pokyny pro vytvoření kanálu s aktivitou kopírování.
