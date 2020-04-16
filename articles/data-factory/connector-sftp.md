---
title: Kopírování dat ze serveru SFTP a na server SFTP
description: Přečtěte si, jak kopírovat data ze serveru SFTP a na server SFTP pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/02/2020
ms.openlocfilehash: e6d29f73716b04699e0cd250396df7f7d744d4c4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415252"
---
# <a name="copy-data-from-and-to-sftp-server-using-azure-data-factory"></a>Kopírování dat ze serveru SFTP a na server SFTP pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-sftp-connector.md)
> * [Aktuální verze](connector-sftp.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak kopírovat data ze serveru SFTP a na server SFTP. Další informace o Azure Data Factory najdete v [úvodním článku](introduction.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor SFTP je podporován pro následující činnosti:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)
- [Aktivita getMetadata](control-flow-get-metadata-activity.md)
- [Odstranit aktivitu](delete-activity.md)

Konkrétně tento konektor SFTP podporuje:

- Kopírování souborů z/do SFTP pomocí **základního** ověřování nebo ověřování **SshPublicKey.**
- Kopírování souborů tak, jak je nebo analýza / generování souborů s [podporovanými formáty souborů a kompresními kodeky](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Factory dat specifických pro SFTP.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu SFTP jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **Sftp**. |Ano |
| host | Název nebo IP adresa serveru SFTP. |Ano |
| port | Port, na kterém naslouchá server SFTP.<br/>Povolené hodnoty jsou: celé číslo, výchozí hodnota je **22**. |Ne |
| skipHostKeyValidation | Určete, zda má být ověření klíče hostitele přeskočte.<br/>Povolené hodnoty jsou: **true**, **false** (výchozí).  | Ne |
| hostKeyFingerprint | Určete otisk prstu klíče hostitele. | Ano, pokud je "skipHostKeyValidation" nastavena na false.  |
| authenticationType | Zadejte typ ověřování.<br/>Povolené hodnoty jsou: **Základní**, **SshPublicKey**. Odkazovat [na použití základní ověřování](#using-basic-authentication) a pomocí [SSH veřejného klíče ověřování](#using-ssh-public-key-authentication) oddíly na více vlastností a ukázky JSON v uvedeném pořadí. |Ano |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Další informace naleznete v části [Požadavky.](#prerequisites) Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

### <a name="using-basic-authentication"></a>Použití základního ověřování

Chcete-li použít základní ověřování, nastavte vlastnost "authenticationType" na **základní**a zadejte následující vlastnosti kromě obecných vlastností konektoru SFTP zavedených v poslední části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| userName | Uživatel, který má přístup k serveru SFTP. |Ano |
| heslo | Heslo pro uživatele (userName). Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano |

**Příklad:**

```json
{
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

### <a name="using-ssh-public-key-authentication"></a>Použití ověřování pomocí veřejného klíče SSH

Chcete-li použít ověřování pomocí veřejného klíče SSH, nastavte vlastnost authenticationType jako **SshPublicKey**a zadejte následující vlastnosti kromě obecných vlastností konektoru SFTP zavedeného v poslední části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| userName | Uživatel, který má přístup k serveru SFTP |Ano |
| privátnícesta | Zadejte absolutní cestu k souboru soukromého klíče, ke kterému má přístup modul Integration Runtime. Platí pouze v případě, že je v "connectVia" zadán typ integration runtime s vlastním hostitelem. | Zadejte `privateKeyPath` buď `privateKeyContent`nebo .  |
| privateKeyContent | Base64 kódovaný obsah soukromého klíče SSH. Soukromý klíč SSH by měl být ve formátu OpenSSH. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Zadejte `privateKeyPath` buď `privateKeyContent`nebo . |
| Heslo | Zadejte heslo/heslo pro dešifrování soukromého klíče, pokud je soubor klíče chráněn přístupovou frází. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano, pokud je soubor soukromého klíče chráněn příchytnou frází. |

> [!NOTE]
> Konektor SFTP podporuje klíč RSA/DSA OpenSSH. Ujistěte se, že obsah souboru klíče začíná "-----BEGIN [RSA/DSA] PRIVATE KEY-----". Pokud je soubor soukromého klíče souborve formátu ppk, použijte nástroj Putty k převodu z .ppk do formátu OpenSSH. 

**Příklad 1: Ověřování SshPublicKey pomocí filePath soukromého klíče**

```json
{
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

**Příklad 2: Ověřování SshPublicKey pomocí obsahu soukromého klíče**

```json
{
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Datové sady.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány `location` pro SFTP v nastavení v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Požaduje se |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Vlastnost type `location` under v datové sadě musí být nastavena na **SftpLocation**. | Ano      |
| folderPath | Cesta ke složce. Chcete-li k filtrování složky použít zástupný znak, přeskočte toto nastavení a zadejte v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru pod danou složkouPath. Chcete-li k filtrování souborů použít zástupný znak, přeskočte toto nastavení a určete v nastavení zdroje aktivity. | Ne       |

**Příklad:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "SftpLocation",
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

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem SFTP.

### <a name="sftp-as-source"></a>SFTP jako zdroj

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány `storeSettings` pro SFTP v rámci nastavení ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Vlastnost type `storeSettings` pod musí být nastavena na **SftpReadSettings**. | Ano                                           |
| Rekurzivní                | Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. Všimněte si, že pokud je rekurzivní nastavena na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není zkopírována nebo vytvořena v jímce. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne                                            |
| program se zástupnými_znakem       | Cesta ke složce se zástupnými znaky pro filtrování zdrojových složek. <br>Povolené zástupné `*` znaky jsou: (odpovídá `?` nula nebo více znaků) a (odpovídá nula nebo jeden znak); použijte `^` k útěku, pokud váš skutečný název složky má zástupný znak nebo tento escape char uvnitř. <br>Další příklady naleznete v [příkladech filtrů složek a souborů](#folder-and-file-filter-examples). | Ne                                            |
| název souboru se zástupnými_znakové_         | Název souboru se zástupnými znaky pod danou složkouPath/wildcardFolderPath pro filtrování zdrojových souborů. <br>Povolené zástupné `*` znaky jsou: (odpovídá `?` nula nebo více znaků) a (odpovídá nula nebo jeden znak); použijte `^` k útěku, pokud váš skutečný název složky má zástupný znak nebo tento escape char uvnitř.  Další příklady naleznete v [příkladech filtrů složek a souborů](#folder-and-file-filter-examples). | Ano, `fileName` pokud není zadán v datové sadě |
| modifiedDatetimeStart    | Filtr souborů založený na atributu: Poslední změna. Soubory budou vybrány, pokud jejich poslední upravený `modifiedDatetimeStart` čas `modifiedDatetimeEnd`je v časovém rozmezí mezi a . Čas je použit pro časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti mohou být NULL, což znamená, že žádný filtr atributů souboru bude použit pro datovou sadu.  Pokud `modifiedDatetimeStart` má hodnotu `modifiedDatetimeEnd` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je větší nebo roven hodnotě datetime.  Pokud `modifiedDatetimeEnd` má hodnotu `modifiedDatetimeStart` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je menší než hodnota datetime. | Ne                                            |
| modifiedDatetimeEnd      | Stejně jako výše.                                               | Ne                                            |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti úložiště současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne                                            |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "SftpReadSettings",
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

### <a name="sftp-as-sink"></a>SFTP jako umyvadlo

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány `storeSettings` pro SFTP v rámci nastavení v jímce kopírování založené na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Vlastnost type `storeSettings` pod musí být nastavena na **SftpWriteSettings**. | Ano      |
| copyBehavior             | Definuje chování kopírování, když zdroj jsou soubory z úložiště dat založeného na souborech.<br/><br/>Povolené hodnoty jsou následující:<br/><b>- PreserveHierarchy (výchozí)</b>: Zachová hierarchii souborů v cílové složce. Relativní cesta zdrojového souboru ke zdrojové složce je shodná s relativní cestou cílového souboru k cílové složce.<br/><b>- Sloučení :</b>Všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>- MergeFiles</b>: Sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru, je zadaným názvem sloučený název souboru. V opačném případě se jedná o název automaticky generovaného souboru. | Ne       |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti dat současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne       |
| useTempFileRename | Určete, zda se má nahrát do dočasných souborů a přejmenovat, nebo přímo zapisovat do cílové složky nebo umístění souboru. Ve výchozím nastavení ADF nejprve zapisovat do dočasných souborů (y) pak se soubor přejmenovat po dokončení nahrávání, aby se zabránilo konfliktu zápisu za následek poškozený soubor, pokud máte jiný proces zápis u stejného souboru, a 2) zajistit původní verze souboru existuje během celého přenosu. Pokud server SFTP operaci přejmenování nepodporuje, zakažte tuto možnost a ujistěte se, že nemáte souběžný zápis do cílového souboru. Viz tip pro řešení potíží pod touto tabulkou. | Ne. Výchozí hodnota je true. |
| operationTimeout | Čekací doba před každým požadavkem na zápis na server SFTP časový mj. Výchozí hodnota je 60 min (01:00:00).|Ne |

>[!TIP]
>Pokud při zápisu dat do sftp stisknete chybu "UserErrorSftpPathNotFound", "UserErrorSftpPermissionDenied" nebo "SftpOperationFail" a uživatel SFTP, který používáte, má správná oprávnění, zkontrolujte, zda váš server SFTP podporuje operaci přejmenování - pokud ne, zakažte možnost "Nahrát s dočasným souborem" (`useTempFileRename`) a zkuste to znovu. Další informace o této vlastnosti z výše uvedené tabulky. Pokud používáte pro kopírování prostředí Runtime integrace s vlastním hostitelem, ujistěte se, že používáte verzi 4.6 nebo vyšší.

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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
                "type": "<source type>"
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings":{
                    "type": "SftpWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Příklady filtrů složek a souborů

Tato část popisuje výsledné chování cesty ke složce a názvu souboru pomocí filtrů se zástupnými kódy.

| folderPath | fileName | Rekurzivní | Struktura zdrojové složky a výsledek filtru (soubory **tučně** jsou načteny)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (prázdný, použijte výchozí) | false (nepravda) | SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5.csv<br/>Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |
| `Folder*` | (prázdný, použijte výchozí) | true | SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor5.csv**<br/>Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |
| `Folder*` | `*.csv` | false (nepravda) | SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5.csv<br/>Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |
| `Folder*` | `*.csv` | true | SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor5.csv**<br/>Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Odstranit vlastnosti aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zaškrtněte [políčko Odstranit aktivitu.](delete-activity.md)

## <a name="legacy-models"></a>Starší modely

>[!NOTE]
>Následující modely jsou stále podporovány jako-je pro zpětnou kompatibilitu. Doporučujeme použít nový model uvedený ve výše uvedených oddílech do budoucna a vývojové uontové pole ADF přešlo na generování nového modelu.

### <a name="legacy-dataset-model"></a>Starší model datové sady

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **FileShare** |Ano |
| folderPath | Cesta ke složce. Filtr se zástupnými znaky je `*` podporován, povolené zástupné `?` znaky jsou: (odpovídá nule nebo více znaků) a (odpovídá nule nebo jeden znak); použijte `^` k útěku, pokud váš skutečný název souboru má zástupný znak nebo tento escape char uvnitř. <br/><br/>Příklady: kořenová složka/podsložka/, další příklady naleznete v [příkladech filtrů složek a souborů](#folder-and-file-filter-examples). |Ano |
| fileName |  **Filtr názvu nebo zástupných symbolů** pro soubor (soubory) pod zadanou "folderPath". Pokud pro tuto vlastnost nezadáte hodnotu, datová sada odkazuje na všechny soubory ve složce. <br/><br/>Pro filtr jsou povoleny `*` zástupné znaky: (odpovídá nula nebo více znaků) a `?` (odpovídá nule nebo jeden znak).<br/>- Příklad 1:`"fileName": "*.csv"`<br/>- Příklad 2:`"fileName": "???20180427.txt"`<br/>Použijte `^` k útěku, pokud váš skutečný název složky má zástupný znak nebo tento escape char uvnitř. |Ne |
| modifiedDatetimeStart | Filtr souborů založený na atributu: Poslední změna. Soubory budou vybrány, pokud jejich poslední upravený `modifiedDatetimeStart` čas `modifiedDatetimeEnd`je v časovém rozmezí mezi a . Čas je použit pro časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Uvědomte si, že celkový výkon přesunu dat bude ovlivněn povolením tohoto nastavení, pokud chcete provést filtrování souborů z velkého množství souborů. <br/><br/> Vlastnosti mohou být NULL, což znamená, že žádný filtr atributů souboru bude použit pro datovou sadu.  Pokud `modifiedDatetimeStart` má hodnotu `modifiedDatetimeEnd` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je větší nebo roven hodnotě datetime.  Pokud `modifiedDatetimeEnd` má hodnotu `modifiedDatetimeStart` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je menší než hodnota datetime.| Ne |
| modifiedDatetimeEnd | Filtr souborů založený na atributu: Poslední změna. Soubory budou vybrány, pokud jejich poslední upravený `modifiedDatetimeStart` čas `modifiedDatetimeEnd`je v časovém rozmezí mezi a . Čas je použit pro časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Uvědomte si, že celkový výkon přesunu dat bude ovlivněn povolením tohoto nastavení, pokud chcete provést filtrování souborů z velkého množství souborů. <br/><br/> Vlastnosti mohou být NULL, což znamená, že žádný filtr atributů souboru bude použit pro datovou sadu.  Pokud `modifiedDatetimeStart` má hodnotu `modifiedDatetimeEnd` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je větší nebo roven hodnotě datetime.  Pokud `modifiedDatetimeEnd` má hodnotu `modifiedDatetimeStart` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je menší než hodnota datetime.| Ne |
| formát | Pokud chcete **kopírovat soubory jako -je** mezi obchody založenými na souborech (binární kopie), přeskočte oddíl formátu v definicích vstupní i výstupní datové sady.<br/><br/>Chcete-li analyzovat soubory v určitém formátu, jsou podporovány následující typy formátů souborů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte vlastnost **type** ve formátu na jednu z těchto hodnot. Další informace naleznete v části [Formát textu](supported-file-formats-and-compression-codecs-legacy.md#text-format), Formát [Json](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formát Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), Formát [orků](supported-file-formats-and-compression-codecs-legacy.md#orc-format)a [Formát parket.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Ne (pouze pro binární scénář kopírování) |
| komprese | Určete typ a úroveň komprese dat. Další informace naleznete [v tématu Podporované formáty souborů a kompresní kodeky](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Podporované typy jsou: **GZip**, **Deflate**, **BZip2**a **ZipDeflate**.<br/>Podporované úrovně jsou: **Optimální** a **nejrychlejší**. |Ne |

>[!TIP]
>Chcete-li zkopírovat všechny soubory pod složku, zadejte pouze **složkuCesta.**<br>Chcete-li zkopírovat jeden soubor s daným názvem, zadejte **složkuCesta** s částí složky a **název_souboru** s názvem souboru.<br>Chcete-li zkopírovat podmnožinu souborů pod složku, zadejte **složkuCesta** s částí složky a **název_souboru** s filtrem se zástupnými symboly.

>[!NOTE]
>Pokud jste pro filtr souborů používali vlastnost "fileFilter", je stále podporována tak, jak je, zatímco se doporučuje použít novou funkci filtru přidanou do "fileName" do budoucna.

**Příklad:**

```json
{
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
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
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

### <a name="legacy-copy-activity-source-model"></a>Starší model zdroje aktivity kopírování

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena **na: FileSystemSource** |Ano |
| Rekurzivní | Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. Poznámka: Pokud je rekurzivní nastavena na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka nebudou zkopírovány nebo vytvořeny na jímce.<br/>Povolené hodnoty jsou: **true** (výchozí), **false** | Ne |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti úložiště současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

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

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
