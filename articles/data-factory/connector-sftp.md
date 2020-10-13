---
title: Kopírování dat z a do serveru SFTP
description: Naučte se, jak kopírovat data z a do serveru SFTP pomocí Azure Data Factory.
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
ms.date: 08/28/2020
ms.openlocfilehash: f4b78c6cb2af8d18dc761e9bfc78740a845f54fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91252931"
---
# <a name="copy-data-from-and-to-the-sftp-server-by-using-azure-data-factory"></a>Kopírování dat z a do serveru SFTP pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-sftp-connector.md)
> * [Aktuální verze](connector-sftp.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak kopírovat data z a na server zabezpečeného FTP (SFTP). Pokud se chcete dozvědět o Azure Data Factory, přečtěte si [úvodní článek](introduction.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Konektor SFTP se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Odstranit aktivitu](delete-activity.md)

Konkrétně konektor SFTP podporuje:

- Kopírování souborů z a do serveru SFTP pomocí *základního* nebo *SshPublicKey* ověřování.
- Kopírování souborů tak, jak jsou, nebo analýzou nebo generováním souborů s [podporovanými formáty souborů a kompresními kodeky](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které se používají k definování Data Factory entit specifických pro SFTP.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu SFTP jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na *SFTP*. |Yes |
| Hostitel | Název nebo IP adresa serveru SFTP. |Yes |
| port | Port, na kterém naslouchá server SFTP.<br/>Povolená hodnota je celé číslo a výchozí hodnota je *22*. |No |
| skipHostKeyValidation | Určete, zda se má přeskočit ověření klíče hostitele.<br/>Povolené hodnoty jsou *true* a *false* (výchozí).  | No |
| hostKeyFingerprint | Zadejte otisk prstu hostitelského klíče. | Ano, pokud je hodnota "skipHostKeyValidation" nastavená na false.  |
| authenticationType | Zadejte typ ověřování.<br/>Povolené hodnoty jsou *Basic* a *SshPublicKey*. Další vlastnosti najdete v části [použití základního ověřování](#use-basic-authentication) . Příklady JSON najdete v části [použití ověření veřejného klíče SSH](#use-ssh-public-key-authentication) . |Yes |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Další informace najdete v části [požadavky](#prerequisites) . Pokud modul runtime integrace neurčíte, služba použije výchozí Azure Integration Runtime. |No |

### <a name="use-basic-authentication"></a>Použít základní ověřování

Chcete-li použít základní ověřování, nastavte vlastnost *AuthenticationType* na hodnotu *Basic*a kromě obecných vlastností konektoru SFTP, které byly představené v předchozí části, zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| userName | Uživatel, který má přístup k serveru SFTP. |Yes |
| heslo | Heslo pro uživatele (uživatelské jméno). Označte toto pole jako SecureString a bezpečně ho uložte do své datové továrny nebo [vytvořte odkaz na tajný kód uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Yes |

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
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-ssh-public-key-authentication"></a>Použít ověřování pomocí veřejného klíče SSH

Chcete-li použít ověřování pomocí veřejného klíče SSH, nastavte vlastnost "authenticationType" jako **SshPublicKey**a zadejte následující vlastnosti kromě obecných typů KONEKTORů SFTP zavedených v poslední části:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| userName | Uživatel, který má přístup k serveru SFTP. |Yes |
| privateKeyPath | Zadejte absolutní cestu k souboru privátního klíče, ke kterému má prostředí Integration runtime přístup. To platí pouze v případě, že je typ místního prostředí Integration runtime uveden v části "connectVia". | Zadejte buď `privateKeyPath` nebo `privateKeyContent` .  |
| privateKeyContent | Obsah privátního klíče SSH kódovaný v kódování Base64. Privátní klíč SSH by měl být ve formátu OpenSSH. Označte toto pole jako SecureString a bezpečně ho uložte do své datové továrny nebo [vytvořte odkaz na tajný kód uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Zadejte buď `privateKeyPath` nebo `privateKeyContent` . |
| Hesel | Zadejte heslo a heslo pro dešifrování privátního klíče, pokud je soubor klíče nebo obsah klíče chráněn heslem. Označte toto pole jako SecureString a bezpečně ho uložte do své datové továrny nebo [vytvořte odkaz na tajný kód uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano, pokud soubor privátního klíče nebo obsah klíče jsou chráněny pomocí hesla. |

> [!NOTE]
> Konektor SFTP podporuje OpenSSH klíč RSA/DSA. Ujistěte se, že obsah souboru klíče začíná na "-----BEGIN [RSA/DSA] PRIVATE KEY-----". Pokud je soubor privátního klíče PPK formátem, použijte nástroj pro výstupy k převodu z PPK na OpenSSH. 

**Příklad 1: ověřování SshPublicKey pomocí filePath klíče privátního klíče**

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
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad 2: ověřování SshPublicKey pomocí obsahu privátního klíče**

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
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam sekcí a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro SFTP v části `location` nastavení v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Povinné |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Vlastnost *Type* v rámci `location` datové sady musí být nastavená na *SftpLocation*. | Yes      |
| folderPath | Cesta ke složce Pokud chcete použít zástupný znak k filtrování složky, přeskočte toto nastavení a zadejte cestu v nastavení zdroje aktivity. | No       |
| fileName   | Název souboru pod zadaným folderPath. Pokud chcete k filtrování souborů použít zástupný znak, přeskočte toto nastavení a zadejte název souboru v nastavení zdroje aktivity. | No       |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností, které jsou podporovány zdrojem SFTP.

### <a name="sftp-as-source"></a>SFTP jako zdroj

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

V `storeSettings` nastaveních ve zdroji kopírování založeném na formátu jsou podporovány následující vlastnosti protokolu SFTP:

| Vlastnost                 | Popis                                                  | Povinné                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | Vlastnost *Type* v poli `storeSettings` musí být nastavená na *SftpReadSettings*. | Yes                                           |
| ***Vyhledejte soubory ke zkopírování.*** |  |  |
| MOŽNOST 1: statická cesta<br> | Zkopírujte ze složky nebo cesty k souboru, který je zadaný v datové sadě. Pokud chcete zkopírovat všechny soubory ze složky, zadejte také `wildcardFileName` jako `*` . |  |
| MOŽNOST 2: zástupný znak<br>- wildcardFolderPath | Cesta ke složce se zástupnými znaky pro filtrování zdrojových složek. <br>Povolené zástupné znaky jsou `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku); `^` Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak je v rámci, použijte k Escape. <br>Další příklady najdete v tématu [příklady složek a filtru souborů](#folder-and-file-filter-examples). | No                                            |
| MOŽNOST 2: zástupný znak<br>- wildcardFileName | Název souboru se zástupnými znaky v zadaném folderPath/wildcardFolderPath pro filtrování zdrojových souborů. <br>Povolené zástupné znaky jsou `*` (odpovídá žádnému nebo více znakům) a `?` (se shoduje s žádným nebo jedním znakem); `^` Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte k jeho Escape.  Další příklady najdete v tématu [příklady složek a filtru souborů](#folder-and-file-filter-examples). | Yes |
| MOŽNOST 3: seznam souborů<br>- fileListPath | Určuje, že se má zkopírovat zadaná sada souborů. Najeďte na textový soubor, který obsahuje seznam souborů, které chcete zkopírovat (jeden soubor na řádek, přičemž relativní cesta k cestě je nakonfigurovaná v datové sadě).<br/>Když použijete tuto možnost, nezadávejte název souboru v datové sadě. Další příklady najdete v tématu [Příklady seznamů souborů](#file-list-examples). |No |
| ***Další nastavení*** |  | |
| zahrnout | Určuje, zda mají být data rekurzivně čtena z podsložek nebo pouze ze zadané složky. Pokud je rekurzivní nastavení nastaveno na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není kopírována ani vytvořena v jímky. <br>Povolené hodnoty jsou *true* (výchozí) a *false*.<br>Tato vlastnost se při konfiguraci nepoužívá `fileListPath` . |No |
| deleteFilesAfterCompletion | Uvádí, zda budou binární soubory po úspěšném přesunutí do cílového úložiště odstraněny ze zdrojového úložiště. Odstranění souboru je vázané na soubor, takže když aktivita kopírování selže, uvidíte, že některé soubory se už zkopírovaly do cílového umístění a odstranily ze zdroje, zatímco ostatní jsou pořád ve zdrojovém úložišti. <br/>Tato vlastnost je platná pouze ve scénáři kopírování binárních souborů. Výchozí hodnota: false. |No |
| modifiedDatetimeStart    | Soubory jsou filtrovány na základě *Poslední změny*atributu. <br>Soubory jsou vybrány, pokud čas poslední změny spadá do rozsahu `modifiedDatetimeStart` až `modifiedDatetimeEnd` . Čas se použije na časové pásmo UTC ve formátu *2018-12-01T05:00:00Z*. <br> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu není použit žádný filtr atributů souboru.  Když `modifiedDatetimeStart` má hodnotu DateTime, ale má hodnotu `modifiedDatetimeEnd` null, znamená, že jsou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime.  Když `modifiedDatetimeEnd` má hodnotu DateTime, ale `modifiedDatetimeStart` je null, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je menší, než hodnota DateTime.<br/>Tato vlastnost se při konfiguraci nepoužívá `fileListPath` . | No                                            |
| modifiedDatetimeEnd      | Stejné jako výše.                                               | No                                            |
| enablePartitionDiscovery | U souborů, které jsou rozdělené na oddíly, určete, jestli se mají analyzovat oddíly z cesty k souboru, a přidejte je jako další zdrojové sloupce.<br/>Povolené hodnoty jsou **false** (výchozí) a **true**. | No                                            |
| partitionRootPath | Pokud je povoleno zjišťování oddílů, zadejte absolutní kořenovou cestu, aby bylo možné číst rozdělené složky jako sloupce dat.<br/><br/>Pokud není zadaný, ve výchozím nastavení<br/>– Pokud použijete cestu k souboru v datové sadě nebo v seznamu souborů na zdroji, je kořenová cesta oddílu cestou nakonfigurovanou v datové sadě.<br/>– Když použijete filtr složky se zástupnými znaky, kořenová cesta oddílu je dílčí cesta před prvním zástupným znakem.<br/><br/>Předpokládejme například, že nakonfigurujete cestu v datové sadě jako kořen/složka/rok = 2020/měsíc = 08/Day = 27:<br/>– Pokud zadáte kořenovou cestu oddílu jako "root/složka/Year = 2020", aktivita kopírování vygeneruje další dva sloupce `month` a `day` hodnoty "08" a "27" společně se sloupci uvnitř souborů.<br/>-Pokud není zadána kořenová cesta oddílu, nebude vygenerován žádný sloupec navíc. | No                                            |
| maxConcurrentConnections | Počet připojení, která se můžou souběžně připojit k úložišti úložiště. Zadejte hodnotu pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | No                                            |

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

### <a name="sftp-as-a-sink"></a>SFTP jako jímka

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

Následující vlastnosti jsou podporovány pro SFTP v `storeSettings` nastavení v jímky kopírování na základě formátu:

| Vlastnost                 | Popis                                                  | Povinné |
| ------------------------ | ------------------------------------------------------------ | -------- |
| typ                     | Vlastnost *Type* v poli `storeSettings` musí být nastavená na *SftpWriteSettings*. | Yes      |
| copyBehavior             | Definuje chování kopírování, pokud je zdrojem soubory z úložiště dat založeného na souborech.<br/><br/>Povolené hodnoty jsou následující:<br/><b>-PreserveHierarchy (výchozí)</b>: zachovává hierarchii souborů v cílové složce. Relativní cesta ke zdrojovému souboru ke zdrojové složce je shodná s relativní cestou cílového souboru k cílové složce.<br/><b>-FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>-MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Je-li zadán název souboru, Název sloučeného souboru je zadaný název. V opačném případě se jedná o automaticky vygenerovaný název souboru. | No       |
| maxConcurrentConnections | Počet připojení, která se můžou souběžně připojit k úložišti úložiště. Zadejte hodnotu pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | No       |
| useTempFileRename | Určete, zda se mají nahrávat do dočasných souborů a přejmenovat je, nebo přímo zapisovat do cílové složky nebo umístění souboru. Ve výchozím nastavení Azure Data Factory nejprve zapisovat do dočasných souborů a po dokončení nahrávání je přejmenuje. Tato sekvence pomáhá (1) vyhnout se konfliktům, které by mohly vést k poškození souboru, pokud máte jiné procesy zapsané do stejného souboru a (2) zajistěte, aby během přenosu existovala původní verze souboru. Pokud váš server SFTP nepodporuje operaci přejmenování, zakažte tuto možnost a ujistěte se, že nemáte souběžný zápis do cílového souboru. Další informace najdete v tipu Poradce při potížích na konci této tabulky. | Ne. Výchozí hodnota je *true*. |
| operationTimeout | Doba čekání před vypršením časového limitu každého požadavku na zápis na server SFTP Výchozí hodnota je 60 min (01:00:00).|No |

>[!TIP]
>Pokud se zobrazí chyba "UserErrorSftpPathNotFound", "UserErrorSftpPermissionDenied" nebo "SftpOperationFail" při psaní dat do SFTP a uživatel SFTP, *který používáte, má správná* oprávnění, zkontrolujte, zda váš server SFTP podporuje operaci přejmenování souborů. Pokud není, zakažte možnost **Odeslat s dočasným souborem** ( `useTempFileRename` ) a zkuste to znovu. Další informace o této vlastnosti naleznete v předchozí tabulce. Pokud pro aktivitu kopírování použijete místní prostředí Integration runtime, nezapomeňte použít verzi 4,6 nebo novější.

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

### <a name="folder-and-file-filter-examples"></a>Příklady filtru složek a souborů

Tato část popisuje chování, které je výsledkem použití filtrů zástupných znaků s cestami ke složkám a názvy souborů.

| folderPath | fileName | zahrnout | Struktura zdrojové složky a výsledek filtru (jsou načteny soubory **tučně** )|
|:--- |:--- |:--- |:--- |
| `Folder*` | (prázdné, použít výchozí) | false (nepravda) | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (prázdné, použít výchozí) | true | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false (nepravda) | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Příklady seznamů souborů

Tato tabulka popisuje chování, které je výsledkem použití cesty seznamu souborů ve zdroji aktivity kopírování. Předpokládá, že máte následující strukturu zdrojové složky a chcete zkopírovat soubory, které jsou tučným typem:

| Ukázka zdrojové struktury                                      | Obsah v FileListToCopy.txt                             | Konfigurace Azure Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| kořen<br/>&nbsp;&nbsp;&nbsp;&nbsp;Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mezipaměť<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **V datové sadě:**<br>– Cesta ke složce: `root/FolderA`<br><br>**Ve zdroji aktivity kopírování:**<br>– Cesta k seznamu souborů: `root/Metadata/FileListToCopy.txt` <br><br>Cesta k seznamu souborů odkazuje na textový soubor ve stejném úložišti dat, který obsahuje seznam souborů, které chcete zkopírovat (jeden soubor na řádek, přičemž relativní cesta k cestě je nakonfigurovaná v datové sadě). |

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Informace o vlastnostech aktivity vyhledávání najdete v tématu [aktivita vyhledávání v Azure Data Factory](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Informace o vlastnostech aktivity GetMetadata najdete v tématu [aktivita GetMetadata v Azure Data Factory](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Odstranit vlastnosti aktivity

Informace o vlastnostech aktivity odstranění najdete v tématu [Odstranění aktivity v Azure Data Factory](delete-activity.md).

## <a name="legacy-models"></a>Starší modely

>[!NOTE]
>Následující modely jsou stále podporovány, protože jsou z důvodu zpětné kompatibility. Doporučujeme použít dříve popisovaný nový model, protože uživatelské rozhraní pro vytváření Azure Data Factory bylo přepnuto na generování nového modelu.

### <a name="legacy-dataset-model"></a>Model zastaralé sady dat

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost *Type* datové sady musí být nastavená na *Shared*. |Yes |
| folderPath | Cesta ke složce Je podporován filtr zástupných znaků. Povolené zástupné znaky jsou `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku); `^` Pokud má skutečný název souboru zástupný znak nebo znak escape v rámci, použijte k ukončení. <br/><br/>Příklady: RootFolder/podsložce/, další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). |Yes |
| fileName |  **Název nebo zástupný filtr** pro soubory v zadaném "FolderPath". Pokud nezadáte hodnotu pro tuto vlastnost, datová sada bude ukazovat na všechny soubory ve složce. <br/><br/>Pro filtr je povolený zástupný znak `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku).<br/>-Příklad 1: `"fileName": "*.csv"`<br/>-Příklad 2: `"fileName": "???20180427.txt"`<br/>`^`Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte k ukončení. |No |
| modifiedDatetimeStart | Soubory jsou filtrovány na základě *Poslední změny*atributu. Soubory jsou vybrány, pokud čas poslední změny spadá do rozsahu `modifiedDatetimeStart` až `modifiedDatetimeEnd` . Čas se použije na časové pásmo UTC ve formátu *2018-12-01T05:00:00Z*. <br/><br/> Celkový výkon přesunu dat bude ovlivněn tím, že toto nastavení povolíte, pokud chcete provést filtr souborů z velkého počtu souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu není použit žádný filtr atributů souboru.  Když `modifiedDatetimeStart` má hodnotu DateTime, ale má hodnotu `modifiedDatetimeEnd` null, znamená, že jsou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime.  Když `modifiedDatetimeEnd` má hodnotu DateTime, ale `modifiedDatetimeStart` je null, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je menší, než hodnota DateTime.| No |
| modifiedDatetimeEnd | Soubory jsou filtrovány na základě *Poslední změny*atributu. Soubory jsou vybrány, pokud čas poslední změny spadá do rozsahu `modifiedDatetimeStart` až `modifiedDatetimeEnd` . Čas se použije na časové pásmo UTC ve formátu *2018-12-01T05:00:00Z*. <br/><br/> Celkový výkon přesunu dat bude ovlivněn tím, že toto nastavení povolíte, pokud chcete provést filtr souborů z velkého počtu souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu není použit žádný filtr atributů souboru.  Když `modifiedDatetimeStart` má hodnotu DateTime, ale má hodnotu `modifiedDatetimeEnd` null, znamená, že jsou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime.  Když `modifiedDatetimeEnd` má hodnotu DateTime, ale `modifiedDatetimeStart` je null, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je menší, než hodnota DateTime.| No |
| formát | Pokud chcete kopírovat soubory mezi úložišti na základě souborů (binární kopie), přeskočte oddíl formát v definicích vstupní i výstupní datové sady.<br/><br/>Chcete-li analyzovat soubory s konkrétním formátem, jsou podporovány následující typy formátu souboru: *TextFormat*, *JsonFormat*, *AvroFormat*, *OrcFormat*a *ParquetFormat*. V části formát nastavte vlastnost *typ* na jednu z těchto hodnot. Další informace najdete v částech [Formát textu](supported-file-formats-and-compression-codecs-legacy.md#text-format), [formát JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formát Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Formát ORC](supported-file-formats-and-compression-codecs-legacy.md#orc-format)a formátování [Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Ne (jenom pro binární scénář kopírování) |
| komprese | Zadejte typ a úroveň komprese dat. Další informace najdete v tématu [podporované formáty souborů a kompresní kodeky](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Podporované typy jsou *gzip*, *Deflate*, *bzip2*a *ZipDeflate*.<br/>Podporované úrovně jsou *optimální* a *nejrychlejší*. |No |

>[!TIP]
>Chcete-li zkopírovat všechny soubory ve složce, zadejte pouze *FolderPath* .<br>Chcete-li zkopírovat jeden soubor se zadaným názvem, zadejte *FolderPath* s částí *složky a názvem souboru s* názvem souboru.<br>Pokud chcete zkopírovat podmnožinu souborů ve složce, zadejte *FolderPath* s částí složky a *názvem souboru* s filtrem Wildcard.

>[!NOTE]
>Pokud jste pro filtr souboru použili vlastnost *Filter* , je tato funkce stále podporovaná, ale doporučujeme použít novou funkci filtru přidanou do *souboru filename* z tohoto okna.

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

### <a name="legacy-copy-activity-source-model"></a>Starší zdrojový model aktivity kopírování

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost *Type* zdroje aktivity kopírování musí být nastavená na *FileSystemSource* . |Yes |
| zahrnout | Určuje, zda mají být data rekurzivně čtena z podsložek nebo pouze ze zadané složky. Pokud je rekurzivní nastavení nastaveno na *hodnotu true* a jímka je úložiště založené na souborech, prázdné složky a podsložky se nebudou kopírovat ani vytvářet v jímky.<br/>Povolené hodnoty jsou *true* (výchozí) a *false* . | No |
| maxConcurrentConnections | Počet připojení, která se můžou souběžně připojit k úložišti úložiště. Zadejte číslo pouze v případě, že chcete omezit souběžná připojení k úložišti dat. | No |

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
Seznam úložišť dat, která jsou podporovaná jako zdroje a jímky, pomocí aktivity kopírování v Azure Data Factory najdete v tématu [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
