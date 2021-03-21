---
title: Přesunout data ze serveru SFTP pomocí Azure Data Factory
description: Přečtěte si, jak přesunout data z místního nebo cloudového serveru SFTP pomocí Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2018
robots: noindex
ms.openlocfilehash: b40e9dc83629362da899d2b5ff29ad42e21c4e32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382646"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Přesunutí dat ze serveru SFTP pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-sftp-connector.md)
> * [Verze 2 (aktuální verze)](../connector-sftp.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [SFTPconnector v v2](../connector-sftp.md).

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory přesouvat data z místního nebo cloudového serveru SFTP do úložiště dat jímky. Tento článek se týká článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který představuje obecný přehled přesunu dat s aktivitou kopírování a seznam úložišť dat podporovaných jako zdroje a jímky.

Data Factory aktuálně podporuje jenom přesun dat ze serveru SFTP do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat na server SFTP. Podporuje místní i cloudové servery SFTP.

> [!NOTE]
> Aktivita kopírování neodstraní zdrojový soubor po úspěšném zkopírování do cíle. Pokud potřebujete zdrojový soubor po úspěšné kopii odstranit, vytvořte vlastní aktivitu k odstranění tohoto souboru a použijte aktivitu v kanálu.

## <a name="supported-scenarios-and-authentication-types"></a>Podporované scénáře a typy ověřování
Pomocí tohoto konektoru SFTP můžete kopírovat data ze **cloudových serverů SFTP i místních serverů SFTP**. Typy ověřování **Basic** a **SshPublicKey** jsou podporované při připojování k serveru SFTP.

Při kopírování dat z místního serveru SFTP potřebujete nainstalovat bránu Správa dat do místního prostředí nebo virtuálního počítače Azure. Podrobnosti o bráně najdete v tématu [Správa dat Gateway](data-factory-data-management-gateway.md) . Podrobné pokyny k nastavení brány a jejímu použití najdete v článku [přesun dat mezi místními umístěními a v cloudu](data-factory-move-data-between-onprem-and-cloud.md) .

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data ze zdroje SFTP pomocí různých nástrojů nebo rozhraní API.

- Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) .

- K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** a **REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . Ukázky JSON pro kopírování dat ze serveru SFTP do Azure Blob Storage najdete v části [JSON example: kopírování dat ze serveru SFTP do objektu blob Azure](#json-example-copy-data-from-sftp-server-to-azure-blob) v tomto článku.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka uvádí popis pro prvky JSON specifické pro propojenou službu FTP.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ | Vlastnost Type musí být nastavena na hodnotu `Sftp` . |Yes |
| Hostitel | Název nebo IP adresa serveru SFTP. |Yes |
| port |Port, na kterém naslouchá server SFTP. Výchozí hodnota je: 21 |No |
| authenticationType |Zadejte typ ověřování. Povolené hodnoty: **Basic**, **SshPublicKey**. <br><br> Přečtěte si téma [použití základního ověřování](#using-basic-authentication) a části [ověřování pomocí veřejného klíče SSH](#using-ssh-public-key-authentication) na dalších vlastnostech a ukázkách JSON. |Yes |
| skipHostKeyValidation | Určete, zda se má přeskočit ověření klíče hostitele. | No. Výchozí hodnota: false |
| hostKeyFingerprint | Zadejte prst pro tisk klíče hostitele. | Ano, pokud `skipHostKeyValidation` je hodnota nastavena na false.  |
| gatewayName |Název Správa dat brány pro připojení k místnímu serveru SFTP. | Ano, pokud se kopírují data z místního serveru SFTP. |
| encryptedCredential | Šifrované přihlašovací údaje pro přístup k serveru SFTP. Automaticky generováno při zadání základního ověřování (uživatelské jméno + heslo) nebo ověřování SshPublicKey (uživatelské jméno + cesta k privátnímu klíči nebo obsah) v průvodci kopírováním nebo v překryvném okně ClickOnce. | No. Platí jenom při kopírování dat z místního serveru SFTP. |

### <a name="using-basic-authentication"></a>Použití základního ověřování

Chcete-li použít základní ověřování, nastavte `authenticationType` jako `Basic` a zadejte následující vlastnosti kromě obecných typů KONEKTORů SFTP zavedených v poslední části:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| username | Uživatel, který má přístup k serveru SFTP. |Yes |
| heslo | Heslo pro uživatele (uživatelské jméno). | Yes |

#### <a name="example-basic-authentication"></a>Příklad: základní ověřování
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

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Příklad: základní ověřování pomocí šifrovaných přihlašovacích údajů

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

Pokud chcete použít ověřování pomocí veřejného klíče SSH, nastavte `authenticationType` jako `SshPublicKey` a zadejte následující vlastnosti kromě obecných typů KONEKTORů SFTP zavedených v poslední části:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| username |Uživatel, který má přístup k serveru SFTP |Yes |
| privateKeyPath | Zadejte absolutní cestu k souboru privátního klíče, ke kterému má brána přístup. | Zadejte buď `privateKeyPath` nebo `privateKeyContent` . <br><br> Platí jenom při kopírování dat z místního serveru SFTP. |
| privateKeyContent | Serializovaný řetězec obsahu privátního klíče. Průvodce kopírováním může přečíst soubor privátního klíče a automaticky extrahovat obsah privátního klíče. Pokud používáte jiný nástroj nebo sadu SDK, použijte místo toho vlastnost privateKeyPath. | Zadejte buď `privateKeyPath` nebo `privateKeyContent` . |
| Hesel | Zadejte heslo a heslo pro dešifrování privátního klíče, je-li soubor klíče chráněn pomocí hesla. | Ano, pokud je soubor privátního klíče chráněn pomocí fráze Pass. |

> [!NOTE]
> Konektor SFTP podporuje OpenSSH klíč RSA/DSA. Ujistěte se, že obsah souboru klíče začíná na "-----BEGIN [RSA/DSA] privátní klíč-----". Pokud soubor privátního klíče je PPK soubor, použijte prosím Nástroj pro výstup k převodu z. ppk do formátu OpenSSH.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Příklad: ověřování SshPublicKey pomocí filePath privátního klíče

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

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Příklad: ověřování SshPublicKey pomocí obsahu privátního klíče

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
Úplný seznam sekcí & vlastností dostupných pro definování datových sad naleznete v článku [vytvoření datových sad](data-factory-create-datasets.md) . Oddíly, jako jsou struktura, dostupnost a zásady pro datovou sadu JSON, jsou podobné pro všechny typy datových sad.

Oddíl **typeProperties** se liší pro každý typ datové sady. Poskytuje informace, které jsou specifické pro typ datové sady. Oddíl typeProperties pro datovou sadu DataSet typu **Shared** má následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| folderPath |Dílčí cesta ke složce Pro speciální znaky v řetězci použijte řídicí znak ' \ '. Příklady najdete v tématu Ukázka propojené služby a definice datových sad.<br/><br/>Tuto vlastnost můžete kombinovat s **partitionBy** a mít tak cesty ke složkám na základě data a času začátku a konce řezu. |Yes |
| fileName |Pokud chcete, aby tabulka odkazovala na konkrétní soubor ve složce, zadejte název souboru do **FolderPath** . Pokud pro tuto vlastnost nezadáte žádnou hodnotu, odkazuje tabulka na všechny soubory ve složce.<br/><br/>Pokud pro výstupní datovou sadu není zadán název souboru, bude název vygenerovaného souboru v následujícím formátu: <br/><br/>`Data.<Guid>.txt` (Příklad: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| fileFilter |Určete filtr, který se použije k výběru podmnožiny souborů v folderPath, nikoli všech souborů.<br/><br/>Povolené hodnoty jsou: `*` (více znaků) a `?` (jeden znak).<br/><br/>Příklady 1: `"fileFilter": "*.log"`<br/>Příklad 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> pro sadu vstupních dat Shared je použitelný filtr. Tato vlastnost není u HDFS podporována. |No |
| partitionedBy |partitionedBy lze použít k zadání dynamického názvu souboru folderPath pro data časové řady. Například folderPath parametrizované pro každou hodinu dat. |No |
| formát | Podporovány jsou následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. V části formát nastavte vlastnost **typ** na jednu z těchto hodnot. Další informace najdete v částech [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), [formát JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formát ORC](data-factory-supported-file-and-compression-formats.md#orc-format)a formátování [Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Pokud chcete **Kopírovat soubory** mezi úložišti na základě souborů (binární kopie), přeskočte oddíl formát v definicích vstupní i výstupní datové sady. |No |
| komprese | Zadejte typ a úroveň komprese dat. Podporované typy jsou: **gzip**, **Deflate**, **bzip2** a **ZipDeflate**. Podporované úrovně: **optimální** a **nejrychlejší**. Další informace naleznete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |
| useBinaryTransfer |Určete, zda se má použít režim binárního přenosu. Hodnota true pro binární režim a hodnotu false ASCII. Výchozí hodnota: true. Tato vlastnost se dá použít jedině v případě, že typ přidružené propojené služby je typu: FtpServer. |No |

> [!NOTE]
> filename a FileFilter nelze použít současně.

### <a name="using-partionedby-property"></a>Použití vlastnosti partionedBy
Jak je uvedeno v předchozí části, můžete zadat dynamický folderPath, filename pro data časové řady s partitionedBy. Můžete tak učinit pomocí Data Factory makra a systémové proměnné vlastnosti slicestart, SliceEnd, která indikuje logické časové období pro daný datový řez.

Další informace o datových sadách časových řad, plánování a řezech najdete v článku [vytváření datových sad](data-factory-create-datasets.md), [plánování &ho provádění](data-factory-scheduling-and-execution.md)a [vytváření kanálů](data-factory-create-pipelines.md) .

#### <a name="sample-1"></a>Ukázka 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
V tomto příkladu {Slice} se nahradí hodnotou Data Factory systémové proměnné vlastnosti slicestart v zadaném formátu (YYYYMMDDHH). Vlastnosti slicestart odkazuje na počáteční čas řezu. FolderPath se u každého řezu liší. Příklad: wikidatagateway/wikisampledataout/2014100103 nebo wikidatagateway/wikisampledataout/2014100104.

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
V tomto příkladu jsou extrahovány roky, měsíc, den a čas vlastnosti slicestart do samostatných proměnných, které jsou používány vlastnostmi folderPath a fileName.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam sekcí & vlastností dostupných pro definování aktivit najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . Pro všechny typy aktivit jsou k dispozici vlastnosti, jako je název, popis, vstupní a výstupní tabulka a zásady.

Vzhledem k tomu, že vlastnosti, které jsou k dispozici v části typeProperties v aktivitě, se liší podle typu aktivity. U aktivity kopírování se vlastnosti typu liší v závislosti na typech zdrojů a jímky.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Podporované formáty souborů a komprese
Podrobnosti najdete v článku o [formátech souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md) .

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>Příklad JSON: kopírování dat ze serveru SFTP do Azure Blob
Následující příklad poskytuje ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazují, jak kopírovat data ze zdroje SFTP do Azure Blob Storage. Data se ale dají zkopírovat **přímo** z libovolného zdroje do kterékoli z těchto umyvadel, které jsou [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) uvedené, pomocí aktivity kopírování v Azure Data Factory.

> [!IMPORTANT]
> Tato ukázka poskytuje fragmenty kódu JSON. Nezahrnuje podrobné pokyny k vytvoření datové továrny. Podrobné pokyny najdete v článku [přesun dat mezi místními umístěními a v cloudu](data-factory-move-data-between-onprem-and-cloud.md) .

Ukázka má následující Entity Data Factory:

* Propojená služba typu [SFTP](#linked-service-properties).
* Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní [datová sada](data-factory-create-datasets.md) typu [sdílení](#dataset-properties)souborů.
* Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
* [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [FileSystemSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka kopíruje data ze serveru SFTP do objektu blob Azure každou hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

**Propojená Služba SFTP**

V tomto příkladu se používá základní ověřování s uživatelským jménem a heslem v prostém textu. Můžete také použít některý z následujících způsobů:

* Základní ověřování pomocí šifrovaných přihlašovacích údajů
* Ověřování veřejného klíče SSH

Různé typy ověřování, které můžete použít, najdete v části [propojená služba FTP](#linked-service-properties) .

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

Tato datová sada odkazuje na složku `mysharedfolder` a soubor SFTP `test.csv` . Kanál zkopíruje soubor do cílového umístění.

Nastavení "externí": "true" informuje službu Data Factory o tom, že datová sada je externí pro objekt pro vytváření dat, a není vytvořena aktivitou v datové továrně.

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

Data se zapisují do nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt BLOB je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části rok, měsíc, den a hodiny v počátečním čase.

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

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady a má naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **FileSystemSource** a typ **jímky** je nastavený na **BlobSink**.

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
Další informace o klíčových faktorech, které mají vliv na výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jejich optimalizace, najdete v tématu [Průvodce optimalizací aktivity kopírování &](data-factory-copy-activity-performance.md) .

## <a name="next-steps"></a>Další kroky
Viz následující články:

* [Kurz kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pro podrobné pokyny k vytvoření kanálu s aktivitou kopírování.
