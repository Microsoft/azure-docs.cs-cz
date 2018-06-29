---
title: Přesun dat ze serveru pomocí protokolu SFTP pomocí Azure Data Factory | Microsoft Docs
description: Další informace o tom, jak přesunout data z místní nebo serveru pomocí protokolu SFTP cloudu pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c22d2cba23e8bae965fa7c5746c9fff69ad3fa9e
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054411"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Přesunutí dat ze serveru pomocí protokolu SFTP pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-sftp-connector.md)
> * [Verze 2 (aktuální verze)](../connector-sftp.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [SFTPconnector v V2](../connector-sftp.md).

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory pro přesun dat ze serveru pomocí protokolu SFTP lokální/Cloudová do úložiště dat podporovaných jímky. Tento článek vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který uvádí obecný přehled přesun dat s aktivitou kopírování a seznam úložiště dat, které jsou podporované jako zdroje nebo jímky.

Objekt pro vytváření dat aktuálně podporuje pouze přesunutí dat ze serveru pomocí protokolu SFTP k jiným úložištím dat, ale ne pro přesun dat z jiných úložišť dat k serveru pomocí protokolu SFTP. Podporuje místní a cloudové servery pomocí protokolu SFTP.

> [!NOTE]
> Aktivita kopírování nedojde k odstranění zdrojového souboru po byl úspěšně zkopírován do cílové. Pokud potřebujete odstranit zdrojový soubor po úspěšné kopie, vytvořte vlastní aktivity odstranit soubor a použijte aktivitu v kanálu. 

## <a name="supported-scenarios-and-authentication-types"></a>Podporované scénáře a typy ověřování
Tento konektor SFTP můžete použít ke zkopírování dat z **i v cloudu pomocí protokolu SFTP servery a servery pomocí protokolu SFTP místní**. **Základní** a **parametru SshPublicKey** typy ověřování jsou podporovány při připojování k serveru pomocí protokolu SFTP.

Při kopírování dat z místního serveru pomocí protokolu SFTP, je nutné nainstalovat brána pro správu dat v prostředí nebo Azure místní počítač. V tématu [Brána pro správu dat](data-factory-data-management-gateway.md) podrobnosti na bráně. V tématu [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku podrobné pokyny k nastavení brány a jeho použití.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z protokolu SFTP zdroje pomocí různých nástrojů nebo rozhraní API.

- Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním**. V tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) podrobný rychlé vytvoření kanálu pomocí Průvodce kopírováním data.

- Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru** , **.NET API**, a **rozhraní REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. JSON ukázky ke zkopírování dat z protokolu SFTP serveru do Azure Blob Storage, najdete v části [JSON příklad: kopírování dat ze serveru pomocí protokolu SFTP do objektu blob Azure](#json-example-copy-data-from-sftp-server-to-azure-blob) tohoto článku.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro propojenou službu FTP.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- | --- |
| type | Vlastnost typu musí být nastavená na `Sftp`. |Ano |
| hostitel | Název nebo IP adresa serveru pomocí protokolu SFTP. |Ano |
| port |Port, na kterém naslouchá server pomocí protokolu SFTP. Výchozí hodnota je: 21 |Ne |
| authenticationType. |Zadejte typ ověřování. Povolené hodnoty: **základní**, **parametru SshPublicKey**. <br><br> Odkazovat na [základní ověřování pomocí](#using-basic-authentication) a [pomocí SSH ověření veřejného klíče](#using-ssh-public-key-authentication) částech na další vlastnosti a ukázky JSON v uvedeném pořadí. |Ano |
| skipHostKeyValidation | Určete, zda chcete přeskočit ověření klíče hostitele. | Ne. Výchozí hodnota: false |
| hostKeyFingerprint | Zadejte prstu klíče hostitele. | Ano, pokud `skipHostKeyValidation` nastaven na hodnotu false.  |
| gatewayName |Název brány pro správu dat pro připojení k serveru pomocí protokolu SFTP místně. | Ano, pokud kopírování dat z místního serveru pomocí protokolu SFTP. |
| encryptedCredential | Šifrovaný přihlašovací údaje pro přístup k serveru pomocí protokolu SFTP. Automaticky generovaný když zadáte v Průvodci kopírovat nebo dialogové okno místní ClickOnce základní ověřování (uživatelské jméno a heslo) nebo ověřování parametru SshPublicKey (uživatelské jméno + cesta privátního klíče nebo obsah). | Ne. Platí jenom v případě, že kopírování dat z místního serveru pomocí protokolu SFTP. |

### <a name="using-basic-authentication"></a>Použití základního ověřování

Chcete-li základní ověřování použijte, nastavte `authenticationType` jako `Basic`a zadejte následující vlastnosti kromě konektor SFTP obecné ty, které jsou zavedené v poslední části:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- | --- |
| uživatelské jméno | Uživatel, který má přístup k serveru pomocí protokolu SFTP. |Ano |
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

### <a name="using-ssh-public-key-authentication"></a>Pomocí ověření veřejného klíče SSH

Chcete-li použít ověření veřejného klíče SSH, nastavte `authenticationType` jako `SshPublicKey`a zadejte následující vlastnosti kromě konektor SFTP obecné ty, které jsou zavedené v poslední části:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- | --- |
| uživatelské jméno |Uživatel, který má přístup k serveru pomocí protokolu SFTP |Ano |
| privateKeyPath | Zadejte absolutní cestu k souboru privátního klíče můžete přístup k této brány. | Zadejte buď `privateKeyPath` nebo `privateKeyContent`. <br><br> Platí jenom v případě, že kopírování dat z místního serveru pomocí protokolu SFTP. |
| privateKeyContent | Serializovaná řetězec privátní klíče obsahu. Průvodce kopírováním můžete číst soubor privátního klíče a automaticky extrahování privátní klíče obsahu. Pokud používáte jakékoli jiné nástroje nebo SDK, použijte vlastnost privateKeyPath. | Zadejte buď `privateKeyPath` nebo `privateKeyContent`. |
| přístupové heslo | Zadejte průchodu fráze nebo hesla k dešifrování privátního klíče, pokud soubor klíče je chráněn heslo. | Ano, pokud heslo je chráněný soubor privátního klíče. |

> [!NOTE]
> Pomocí protokolu SFTP konektor podporuje klíč RSA/DSA OpenSSH. Ujistěte se, že obsah souboru klíče začíná "---BEGIN [RSA/DSA] PRIVÁTNÍ klíč,". Pokud soubor privátního klíče je soubor ve formátu ppk, použijte prosím Putty nástroj pro převod z .ppk OpenSSH formátu.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Příklad: Parametru SshPublicKey ověřování pomocí privátního klíče filePath

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

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Příklad: Parametru SshPublicKey ověřování pomocí privátního klíče obsahu

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
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu.

**Rámci typeProperties** části se liší pro jednotlivé typy datovou sadu. Poskytuje informace, které jsou specifické pro daný typ datové sady. Rámci typeProperties část datové sady typu **sdílení souborů** datová sada má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Sub – cesta ke složce. Použít řídicí znak ' \ ' pro speciální znaky v řetězci. V tématu [ukázka propojené definice služby a datovou sadu](#sample-linked-service-and-dataset-definitions) příklady.<br/><br/>Tato vlastnost se můžete kombinovat **partitionBy** tak, aby měl složky cesty založené na řez počáteční nebo koncové hodnoty data a času. |Ano |
| fileName |Zadejte název souboru do **folderPath** Pokud chcete, aby v tabulce odkazovat na konkrétní soubor ve složce. Pokud nezadáte žádnou hodnotu pro tuto vlastnost, tabulka odkazuje na všechny soubory ve složce.<br/><br/>Pokud není zadán název souboru pro datovou sadu výstupů, název vygenerovaný soubor bude v následujícím tento formát: <br/><br/>Data. <Guid>.txt (například: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Ne |
| fileFilter |Zadejte filtr pro umožňuje vybrat podmnožinu souborů v folderPath, nikoli všech souborů.<br/><br/>Povolené hodnoty jsou: `*` (více znaků) a `?` (jeden znak).<br/><br/>Příklady 1: `"fileFilter": "*.log"`<br/>Příklad 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter se vztahuje vstupní datové sady sdílení souborů. Tato vlastnost není podporována s HDFS. |Ne |
| partitionedBy |partitionedBy slouží k určení dynamické folderPath, název souboru pro data časové řady. Například folderPath parametry pro každou hodinu data. |Ne |
| Formát | Jsou podporovány následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Nastavte **typ** vlastnost pod formát na jednu z těchto hodnot. Další informace najdete v tématu [textovém formátu](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátu](data-factory-supported-file-and-compression-formats.md#orc-format), a [Parquet formát](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete **zkopírujte soubory jako-je** mezi souborové úložiště (binární kopie), přeskočte část formátu v obou definice vstupní a výstupní datové sady. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |
| useBinaryTransfer |Určit, jestli použít režim binární přenosu. Platí pro binárního režimu a false ASCII. Výchozí hodnota: True. Tuto vlastnost lze použít pouze v případě typu přidružené propojené služby typu: Server_ftp. |Ne |

> [!NOTE]
> Název souboru a fileFilter nelze použít současně.

### <a name="using-partionedby-property"></a>Pomocí vlastnosti partionedBy
Jak je uvedeno v předchozí části, můžete zadat dynamické folderPath, název souboru pro data časové řady s partitionedBy. Můžete tak učinit pomocí makra pro vytváření dat a systémové proměnné SliceStart, SliceEnd, který označuje logické časové období pro daný datový řez.

Další informace o datové sady času řady, plánování a řezy najdete v tématu [vytváření datových sad](data-factory-create-datasets.md), [plánování a provádění](data-factory-scheduling-and-execution.md), a [vytváření kanálů](data-factory-create-pipelines.md) články.

#### <a name="sample-1"></a>Příklad 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
V tomto příkladu {řez} se nahradí hodnotu objektu pro vytváření dat systému proměnné SliceStart ve formátu (YYYYMMDDHH) zadán. Vlastnosti SliceStart odkazuje na spuštění řezu. FolderPath se liší pro každý řez. Příklad: wikidatagateway/wikisampledataout/2014100103 nebo wikidatagateway/wikisampledataout/2014100104.

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
V tomto příkladu jsou extrahován rok, měsíc, den a čas SliceStart do samostatné proměnné, které jsou používány folderPath a název vlastnosti.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, například název, popis, vstupní a výstupní tabulky a zásad jsou dostupné pro všechny typy aktivit.

Vzhledem k tomu, vlastnosti dostupné v rámci typeProperties části aktivity se liší podle každý typ aktivity. Pro aktivitu kopírování vlastnosti typu lišit v závislosti na typech zdrojů a jímky.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Podporované formáty souborů a komprese
V tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md) článek na podrobnosti.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>Příklad JSON: Kopírování dat ze serveru pomocí protokolu SFTP do objektů blob v Azure
Následující příklad uvádí ukázka JSON definice, které můžete použít k vytvoření kanálu pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Se ukazují, jak zkopírovat data ze zdroje SFTP do Azure Blob Storage. Nicméně je možné zkopírovat data **přímo** ze všech zdrojů do jakéhokoli z jímky uvádí [sem](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

> [!IMPORTANT]
> Tato ukázka obsahuje fragmenty kódu JSON. Podrobné pokyny pro vytvoření objektu pro vytváření dat neobsahuje. V tématu [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md) podrobné pokyny najdete v článku.

Ukázka má následující entity objektu pro vytváření dat:

* Propojené služby typu [sftp](#linked-service-properties).
* Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstup [datovou sadu](data-factory-create-datasets.md) typu [sdílení souborů](#dataset-properties).
* Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [FileSystemSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka kopíruje data ze serveru pomocí protokolu SFTP do objektu blob Azure každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

**SFTP propojené služby**

Tento příklad používá základní ověřování s uživatelské jméno a heslo v prostém textu. Můžete také použít jednu z následujících způsobů:

* Základní ověřování s zašifrované přihlašovací údaje
* Ověření veřejného klíče SSH

V tématu [FTP propojená služba](#linked-service-properties) části pro různé typy ověřování můžete použít.

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
**Vstupní datové sady pomocí protokolu SFTP**

Tato datová sada odkazuje na složku SFTP `mysharedfolder` a soubor `test.csv`. Kanál zkopíruje soubor do cílového umístění.

Nastavení "externí": "PRAVDA" informuje služba Data Factory, datová sada je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

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

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Cesta ke složce pro tento objekt blob je vyhodnocován dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc, den a čas částí čas spuštění.

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

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **FileSystemSource** a **podřízený** je typ nastaven na **BlobSink**.

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
V tématu [výkonu kopie aktivity & ladění průvodce](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů, že dopad výkon přesun dat (aktivita kopírování) v Azure Data Factory a různé způsoby, jak optimalizovat ho.

## <a name="next-steps"></a>Další kroky
Viz následující články:

* [Kopie aktivity kurzu](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny pro vytvoření kanálu s aktivitou kopírování.
