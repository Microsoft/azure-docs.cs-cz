---
title: Přesouvání dat ze serveru SFTP pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Další informace o tom, jak přesunout data z místní nebo server SFTP cloudu pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 2aa272c126e06b758dc3903a8ec71b7043491057
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017646"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Přesun dat z server SFTP pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-sftp-connector.md)
> * [Verze 2 (aktuální verze)](../connector-sftp.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [SFTPconnector ve V2](../connector-sftp.md).

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory pro přesun dat z místní/Cloudová server SFTP do úložiště dat jímky podporované. Tento článek vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který nabízí obecný přehled o přesun dat pomocí aktivity kopírování a seznam úložišť dat podporovaných jako zdroje a jímky.

Data factory aktuálně podporuje pouze přesouvá data z SFTP server do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat na SFTP server. Podporuje i s místními a cloudovými servery SFTP.

> [!NOTE]
> Aktivita kopírování nedojde k odstranění zdrojového souboru, co se úspěšně zkopíruje do cíle. Pokud je potřeba odstranit zdrojový soubor po úspěšném kopírování, vytvoření vlastní aktivity a stejný soubor odstranit také pomocí aktivity v kanálu. 

## <a name="supported-scenarios-and-authentication-types"></a>Podporované scénáře a typy ověřování
Můžete použít ke zkopírování dat z tohoto konektoru SFTP **i cloudové servery SFTP a SFTP v místním**. **Základní** a **SshPublicKey** typy ověřování jsou podporovány při připojování k serveru SFTP.

Kopírování dat ze na místním serveru SFTP, třeba instalace brány pro správu dat v prostředí/Azure v místním virtuálním počítači. Zobrazit [brána správy dat](data-factory-data-management-gateway.md) podrobné informace o bráně. V tématu [přesouvání dat mezi místními umístěními a cloudu](data-factory-move-data-between-onprem-and-cloud.md) najdete podrobné pokyny k nastavením brány a jeho použití.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z SFTP zdroje pomocí různých nástrojů a rozhraní API.

- Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Zobrazit [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním data.

- Tyto nástroje můžete také použít k vytvoření kanálu: **Azure portal**, **sady Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **rozhraní .NET API**a  **Rozhraní REST API**. Zobrazit [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. Ukázky JSON pro kopírování dat ze serveru SFTP do Azure Blob Storage najdete v tématu [příklad JSON: Kopírování dat ze serveru SFTP do objektů blob v Azure](#json-example-copy-data-from-sftp-server-to-azure-blob) části tohoto článku.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro propojenou službu FTP.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- | --- |
| type | Vlastnost type musí být nastavená na `Sftp`. |Ano |
| hostitel | Název nebo IP adresa serveru SFTP. |Ano |
| port |Port, na kterém naslouchá SFTP server. Výchozí hodnota je: 21 |Ne |
| authenticationType. |Zadejte typ ověřování. Povolené hodnoty: **Základní**, **SshPublicKey**. <br><br> Odkazovat na [použití základního ověřování](#using-basic-authentication) a [pomocí SSH ověření veřejného klíče](#using-ssh-public-key-authentication) oddíly na více vlastností a ukázky JSON v uvedeném pořadí. |Ano |
| skipHostKeyValidation | Určete, jestli chcete přeskočit ověřování klíče hostitele. | Ne. Výchozí hodnota: NEPRAVDA |
| Mělo | Zadejte hlas klíče hostitele. | Ano, pokud `skipHostKeyValidation` je nastavena na hodnotu false.  |
| Název brány |Název brány správy dat pro připojení místnímu serveru SFTP. | Ano, pokud se kopírování dat z místní server SFTP. |
| encryptedCredential | Šifrované přihlašovací údaje pro přístup k serveru SFTP. Automaticky generované při zadání základní ověřování (uživatelské jméno a heslo) nebo SshPublicKey ověřování (uživatelské jméno + privátní cesta ke klíči nebo obsah) v Průvodci kopírovat nebo dialogové okno automaticky otevírané okno ClickOnce. | Ne. Platí pouze při kopírování dat z místní server SFTP. |

### <a name="using-basic-authentication"></a>Použití základního ověřování

Chcete-li použít základní ověřování, nastavte `authenticationType` jako `Basic`a zadejte následující požadované vlastnosti kromě konektoru SFTP obecných představíme v poslední části:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- | --- |
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

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Příklad: Základní ověřování s šifrovaném přihlašovacím údaji

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

Chcete-li použít ověřování pomocí veřejného klíče SSH, nastavte `authenticationType` jako `SshPublicKey`a zadejte následující požadované vlastnosti kromě konektoru SFTP obecných představíme v poslední části:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- | --- |
| uživatelské jméno |Uživatel, který má přístup k serveru SFTP |Ano |
| privateKeyPath | Zadejte absolutní cestu k souboru privátního klíče můžete přístup k této brány. | Zadejte, jestli `privateKeyPath` nebo `privateKeyContent`. <br><br> Platí pouze při kopírování dat z místní server SFTP. |
| privateKeyContent | Serializovaný řetězec soukromého klíče obsahu. Průvodce kopírováním může číst soubor privátního klíče a automaticky extrahovat obsah privátního klíče. Pokud používáte žádné další nástroj nebo sadu SDK, použijte vlastnost privateKeyPath. | Zadejte, jestli `privateKeyPath` nebo `privateKeyContent`. |
| přístupové heslo | Zadejte pass frázi/heslo k dešifrování privátního klíče, pokud soubor klíče je chráněn heslo. | Ano, pokud se soubor privátního klíče je chráněn heslo. |

> [!NOTE]
> Konektor SFTP podporuje RSA/DSA OpenSSH key. Ujistěte se, že obsah souboru klíče začíná řetězcem "---začátek [RSA/DSA] PRIVÁTNÍ klíč---". Pokud soubor privátního klíče je ppk formátový soubor, použijte prosím Putty nástroj pro převod z .ppk s formátem OpenSSH.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Příklad: SshPublicKey ověřování pomocí privátního klíče filePath

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

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Příklad: SshPublicKey ověřování pomocí privátního klíče obsahu

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
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datové sady.

**TypeProperties** oddílu se liší pro každý typ datové sady. Poskytuje informace, které jsou specifické pro daný typ datové sady. V části datové sady typu typeProperties **sdílení souborů** datovou sadu má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Podřízená cesta ke složce. Použijte řídicí znak "\" pro zvláštní znaky v řetězci. Zobrazit [ukázka propojené služby a datové sady definice](#sample-linked-service-and-dataset-definitions) příklady.<br/><br/>Můžete zkombinovat tato vlastnost se **partitionBy** mít složku cesty založené na řez počátečním/koncovém data a časy. |Ano |
| fileName |Zadejte název souboru **folderPath** Pokud má tabulka, která má odkazovat na konkrétní soubor ve složce. Pokud je nezadávejte žádnou hodnotu pro tuto vlastnost, v tabulce odkazuje na všechny soubory ve složce.<br/><br/>Pokud není zadán název souboru pro výstupní datovou sadu, název generovaného souboru by měl být v následujícím tento formát: <br/><br/>Data. <Guid>.txt (například: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Ne |
| fileFilter |Určete filtr, který slouží k výběru podmnožinu souborů v cestě folderPath, nikoli všech souborů.<br/><br/>Povolené hodnoty jsou: `*` (více znaků) a `?` (jeden znak).<br/><br/>Příklady 1: `"fileFilter": "*.log"`<br/>Příklad 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter platí pro vstupní datovou sadu sdílení souborů. Tato vlastnost není podporována s HDFS. |Ne |
| partitionedBy |partitionedBy slouží k určení dynamické folderPath, název souboru pro data časových řad. Například folderPath s parametry pro každou hodinu data. |Ne |
| formát | Jsou podporovány následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formát](data-factory-supported-file-and-compression-formats.md#orc-format), a [formát Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete **kopírovat soubory jako-je** mezi souborové úložištěm (binární kopie) a přeskočit část o formátu v definicích oba vstupní a výstupní datové sady. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimální** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese ve službě Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |
| useBinaryTransfer |Určete, jestli použít režim binární přenos. Hodnota true pro binárním režimu a false ASCII. Výchozí hodnota: Hodnota TRUE. Tuto vlastnost lze použít pouze v případě typu přidružené propojené služby typu: Server_ftp. |Ne |

> [!NOTE]
> Název souboru a fileFilter nelze použít současně.

### <a name="using-partionedby-property"></a>Pomocí vlastnosti partionedBy
Jak je uvedeno v předchozí části, můžete zadat dynamické folderPath, název souboru pro data časových řad s partitionedBy. Lze provést pomocí makra objektu pro vytváření dat a systémové proměnné SliceStart, SliceEnd, které označují logické časové období pro daný datový řez.

Další informace o čase řady datových sad, plánování a řezů, naleznete v tématu [vytváření datových sad](data-factory-create-datasets.md), [plánování a provádění](data-factory-scheduling-and-execution.md), a [vytváření kanálů](data-factory-create-pipelines.md) článků.

#### <a name="sample-1"></a>Příklad 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
V tomto příkladu {řez} se nahradí hodnotu proměnné objektu pro vytváření dat systému SliceStart ve formátu (YYYYMMDDHH) zadaná. Vlastnosti SliceStart odkazuje na počáteční čas řezu. V cestě folderPath se liší pro každý řez. Příklad: wikidatagateway/wikisampledataout/2014100103 nebo wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Příklad 2:

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
V tomto příkladu rok, měsíc, den a čas z vlastnosti SliceStart extrahován do samostatných proměnné, které jsou používány vlastnosti folderPath a název souboru.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

Vzhledem k tomu, k dispozici vlastnosti v části typeProperties aktivity se liší s jednotlivými typu aktivity. Pro aktivitu kopírování typ vlastnosti lišit v závislosti na typy zdroje a jímky.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Podporované formáty souborů a komprese
Zobrazit [formáty souborů a komprese ve službě Azure Data Factory](data-factory-supported-file-and-compression-formats.md) článek věnovaný tomu podrobnosti.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>Příklad JSON: Kopírování dat ze serveru SFTP do objektů blob v Azure
Následující příklad obsahuje ukázky JSON definice, které můžete použít k vytvoření kanálu pomocí [webu Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Zobrazí se kopírování dat ze zdroje SFTP do Azure Blob Storage. Nicméně je možné zkopírovat data **přímo** z libovolného zdroje do libovolné jímky uvedeno [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivit kopírování ve službě Azure Data Factory.

> [!IMPORTANT]
> Tato ukázka poskytuje fragmenty kódu JSON. Neobsahuje podrobné pokyny pro vytvoření datové továrny. Zobrazit [přesun dat mezi místními umístěními a cloudu](data-factory-move-data-between-onprem-and-cloud.md) najdete podrobné pokyny.

Ukázka obsahuje následující entit datové továrny:

* Propojené služby typu [sftp](#linked-service-properties).
* Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [sdílení souborů](#dataset-properties).
* Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [FileSystemSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázce kopíruje data ze serveru SFTP do objektu blob Azure každou hodinu. Vlastnostech JSON použitých v tyto ukázky jsou popsány v části podle ukázky.

**SFTP propojené služby**

Tento příklad používá základní ověřování pomocí uživatelského jména a hesla v prostém textu. Můžete také použít jednu z následujících způsobů:

* Základní ověřování se zašifrovanými přihlašovacími údaji
* Ověření veřejného klíče SSH

Zobrazit [FTP propojená služba](#linked-service-properties) oddílu pro různé typy ověřování můžete použít.

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

Tato datová sada odkazuje na složku SFTP `mysharedfolder` a soubor `test.csv`. Kanál kopíruje soubor do cílového umístění.

Nastavení "externí": "PRAVDA" informuje služby Data Factory, že datová sada je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

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

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob se dynamicky vyhodnocuje na základě doby spuštění řez, který se právě zpracovává. Cesta ke složce používá rok, měsíc, den a části hodin čas spuštění.

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

**Kanál s aktivitou kopírování**

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **FileSystemSource** a **jímky** je typ nastaven na **BlobSink**.

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

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Zobrazit [výkonem aktivity kopírování & Průvodci optimalizací](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů této ovlivnit výkon přesouvání dat (aktivita kopírování) ve službě Azure Data Factory a různé způsoby, jak optimalizovat.

## <a name="next-steps"></a>Další kroky
Viz následující články:

* [Kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny pro vytvoření kanálu s aktivitou kopírování.
