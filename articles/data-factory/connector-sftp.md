---
title: Kopírování dat ze serveru pomocí protokolu SFTP pomocí Azure Data Factory | Microsoft Docs
description: Další informace o konektoru MySQL v Azure Data Factory, která umožňuje kopírování dat ze serveru pomocí protokolu SFTP do úložiště dat jako jímka podporované.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jingwang
ms.openlocfilehash: 5d6e66104e602d7e5cbfadab004a4f9547c9b6c7
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="copy-data-from-sftp-server-using-azure-data-factory"></a>Kopírování dat ze serveru pomocí protokolu SFTP pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-sftp-connector.md)
> * [Verze 2 – Preview](connector-sftp.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z serveru pomocí protokolu SFTP. Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [SFTP konektoru V1](v1/data-factory-sftp-connector.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Ze serveru pomocí protokolu SFTP může kopírovat data do úložiště dat žádné podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor SFTP podporuje:

- Kopírování souborů pomocí **základní** nebo **parametru SshPublicKey** ověřování.
- Kopírování souborů jako-je nebo analýza souborů pomocí [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení konkrétní entity služby Data Factory k protokolu SFTP.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro SFTP propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požadované |
|:--- |:--- |:--- |
| typ | Vlastnost typu musí být nastavena na: **Sftp**. |Ano |
| hostitel | Název nebo IP adresa serveru pomocí protokolu SFTP. |Ano |
| Port | Port, na kterém naslouchá server pomocí protokolu SFTP.<br/>Povolené hodnoty jsou: výchozí hodnota je celé číslo, **22**. |Ne |
| skipHostKeyValidation | Určete, zda chcete přeskočit ověření klíče hostitele.<br/>Povolené hodnoty jsou: **true**, **false** (výchozí).  | Ne |
| hostKeyFingerprint | Zadejte prstu klíče hostitele. | Ano, pokud "skipHostKeyValidation" je nastavena na hodnotu false.  |
| authenticationType. | Zadejte typ ověřování.<br/>Povolené hodnoty jsou: **základní**, **parametru SshPublicKey**. Odkazovat na [základní ověřování pomocí](#using-basic-authentication) a [pomocí SSH ověření veřejného klíče](#using-ssh-public-key-authentication) částech na další vlastnosti a ukázky JSON v uvedeném pořadí. |Ano |
| connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít modul Runtime integrace Azure nebo Self-hosted integrace Runtime. Pokud není zadaný, použije výchozí Runtime integrace Azure. |Ne |

### <a name="using-basic-authentication"></a>Použití základního ověřování

Chcete-li základní ověřování použijte, nastavte vlastnost "authenticationType" na **základní**a zadejte následující vlastnosti kromě konektor SFTP obecné ty, které jsou zavedené v poslední části:

| Vlastnost | Popis | Požadované |
|:--- |:--- |:--- |
| Uživatelské jméno | Uživatel, který má přístup k serveru pomocí protokolu SFTP. |Ano |
| heslo | Heslo pro uživatele (uživatelské jméno). Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |

**Příklad:**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SftpLinkedService",
    "type": "linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
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

### <a name="using-ssh-public-key-authentication"></a>Pomocí ověření veřejného klíče SSH

Chcete-li použít ověření veřejného klíče SSH, nastavte vlastnost "authenticationType" jako **parametru SshPublicKey**a zadejte následující vlastnosti kromě konektor SFTP obecné ty, které jsou zavedené v poslední části:

| Vlastnost | Popis | Požadované |
|:--- |:--- |:--- |
| Uživatelské jméno | Uživatel, který má přístup k serveru pomocí protokolu SFTP |Ano |
| privateKeyPath | Zadejte absolutní cestu k souboru privátního klíče, který přístup integrace modulu Runtime. Platí jenom v případě, že je zadán vlastním hostováním typ integrace Runtime v "connectVia". | Zadejte buď `privateKeyPath` nebo `privateKeyContent`.  |
| privateKeyContent | Kódováním base64, pomocí SSH privátní klíče obsahu. Privátní klíč SSH musí být ve formátu OpenSSH. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Zadejte buď `privateKeyPath` nebo `privateKeyContent`. |
| přístupové heslo | Zadejte průchodu fráze nebo hesla k dešifrování privátního klíče, pokud soubor klíče je chráněn heslo. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ano, pokud heslo je chráněný soubor privátního klíče. |

> [!NOTE]
> Pomocí protokolu SFTP konektor podporuje klíč RSA/DSA OpenSSH. Ujistěte se, že obsah souboru klíče začíná "---BEGIN [RSA/DSA] PRIVÁTNÍ klíč,". Pokud soubor privátního klíče je soubor ve formátu ppk, použijte prosím Putty nástroj pro převod z .ppk OpenSSH formátu. 

**Příklad 1: Ověření parametru SshPublicKey pomocí privátního klíče filePath**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad 2: Ověření parametru SshPublicKey pomocí privátního klíče obsahu**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností nepodporuje SFTP datovou sadu.

Ke zkopírování dat z protokolu SFTP, nastavte vlastnost typu datové sady, která **sdílení souborů**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požadované |
|:--- |:--- |:--- |
| typ | Vlastnost typu datové sady musí být nastavena na: **sdílení souborů** |Ano |
| folderPath | Cesta ke složce. Zástupný filtr není podporován. Příklad: složku nebo podsložku / |Ano |
| fileName |  **Název nebo zástupný filtr** pro soubory v zadané "folderPath". Pokud nezadáte hodnotu pro tuto vlastnost, datová sada odkazuje na všechny soubory ve složce. <br/><br/>Pro filtr, povoleny zástupné znaky jsou: `*` (více znaků) a `?` (jeden znak).<br/>– Příklad 1: `"fileName": "*.csv"`<br/>-Příklad 2: `"fileName": "???20180427.txt"`<br/>Použití `^` abyste se vyhnuli, pokud jejich název zástupných znaků nebo tento řídicí znak uvnitř. |Ne |
| Formát | Pokud chcete **zkopírujte soubory jako-je** mezi souborové úložiště (binární kopie), přeskočte část formátu v obou definice vstupní a výstupní datové sady.<br/><br/>Pokud chcete analyzovat soubory s konkrétním formátu, jsou podporovány následující typy souboru formátu: **TextFormat**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. Nastavte **typ** vlastnost pod formát na jednu z těchto hodnot. Další informace najdete v tématu [textovém formátu](supported-file-formats-and-compression-codecs.md#text-format), [formátu Json](supported-file-formats-and-compression-codecs.md#json-format), [Avro formát](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátu](supported-file-formats-and-compression-codecs.md#orc-format), a [Parquet formát](supported-file-formats-and-compression-codecs.md#parquet-format) oddíly. |Ne (pouze pro scénář binární kopie) |
| Komprese | Zadejte typ a úroveň komprese pro data. Další informace najdete v tématu [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**.<br/>Jsou podporované úrovně: **Optimal** a **nejrychlejší**. |Ne |

>[!TIP]
>Pokud chcete zkopírovat všechny soubory ve složce, zadejte **folderPath** pouze.<br>Pokud chcete zkopírovat jeden soubor s daným názvem, zadejte **folderPath** s částí složky a **fileName** s názvem souboru.<br>Pokud chcete zkopírovat podmnožinu souborů ve složce, zadejte **folderPath** s částí složky a **fileName** s filtrem zástupný znak.

>[!NOTE]
>Pokud jste používali vlastnost "fileFilter" pro filtr souborů, je stále podporovány jako-se, když jsou navrhované nová funkce filtru, který byl přidán do "název souboru" do budoucna.

**Příklad:**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností nepodporuje SFTP zdroje.

### <a name="sftp-as-source"></a>Pomocí protokolu SFTP jako zdroj

Ke zkopírování dat z protokolu SFTP, nastavte typ zdroje v aktivitě kopírování do **FileSystemSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požadované |
|:--- |:--- |:--- |
| typ | Vlastnost typ zdroje kopie aktivity musí být nastavena na: **FileSystemSource** |Ano |
| Rekurzivní | Označuje, zda je data načíst rekurzivně z dílčí složky nebo pouze do zadané složky. Poznámka: když rekurzivní nastavena na hodnotu true a jímka je na základě souborů úložiště, prázdné složky nebo dílčí-folder nebudou zkopírovat nebo vytvořit v jímky.<br/>Povolené hodnoty jsou: **true** (výchozí), **false** | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
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


## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
