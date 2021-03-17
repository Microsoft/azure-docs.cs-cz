---
title: Kopírování dat ze HDFS pomocí Azure Data Factory
description: Naučte se, jak kopírovat data z cloudu nebo místního zdroje HDFS do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: jingwang
ms.openlocfilehash: 3ee1b1f48d91ba1245c0173d2e00a20778932d35
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367080"
---
# <a name="copy-data-from-the-hdfs-server-by-using-azure-data-factory"></a>Kopírování dat ze serveru HDFS pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-hdfs-connector.md)
> * [Aktuální verze](connector-hdfs.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak kopírovat data ze serveru Hadoop systém souborů DFS (Distributed File System) (HDFS). Pokud se chcete dozvědět o Azure Data Factory, přečtěte si [úvodní článek](introduction.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Konektor HDFS je podporovaný pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje a jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Odstranit aktivitu](delete-activity.md)

Konkrétně konektor HDFS podporuje:

- Kopírování souborů pomocí *Windows* (Kerberos) nebo *anonymního* ověřování.
- Kopírování souborů pomocí protokolu *webhdfs* nebo integrované podpory *DistCp* .
- Kopírování souborů tak, jak jsou, nebo analýzou nebo generováním souborů s [podporovanými formáty souborů a kompresními kodeky](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Ujistěte se, že prostředí Integration runtime má přístup ke *všem* [názvový server Node]: [název uzlu port] a [servery datových uzlů]: [port datového uzlu] clusteru Hadoop. Výchozí [název portu uzlu] je 50070 a výchozí [port pro datový uzel] je 50075.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které se používají k definování Data Factory entit specifických pro HDFS.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu HDFS jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost *Type* musí být nastavena na *HDFS*. | Yes |
| url |Adresa URL k HDFS |Yes |
| authenticationType | Povolené hodnoty jsou *anonymní* nebo *Windows*. <br><br> Pokud chcete nastavit místní prostředí, přečtěte si část [použití ověřování protokolem Kerberos pro konektor HDFS](#use-kerberos-authentication-for-the-hdfs-connector) . |Yes |
| userName |Uživatelské jméno pro ověřování systému Windows. Pro ověřování protokolem Kerberos zadejte **\<username> @ \<domain> . com**. |Ano (pro ověřování systému Windows) |
| heslo |Heslo pro ověřování systému Windows. Označte toto pole jako SecureString a bezpečně ho uložte do své datové továrny nebo [vytvořte odkaz na tajný kód uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). |Ano (pro ověřování systému Windows) |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Další informace najdete v části [požadavky](#prerequisites) . Pokud modul runtime integrace neurčíte, služba použije výchozí Azure Integration Runtime. |No |

**Příklad: použití anonymního ověřování**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad: použití ověřování systému Windows**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
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

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam sekcí a vlastností, které jsou k dispozici pro definování datových sad, naleznete [v tématu datové sady v Azure Data Factory](concepts-datasets-linked-services.md). 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

V části `location` nastavení v datové sadě založené na formátu jsou podporovány následující vlastnosti HDFS:

| Vlastnost   | Popis                                                  | Povinné |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Vlastnost *Type* v rámci `location` datové sady musí být nastavená na *HdfsLocation*. | Yes      |
| folderPath | Cesta ke složce Pokud chcete použít zástupný znak k filtrování složky, přeskočte toto nastavení a zadejte cestu v nastavení zdroje aktivity. | No       |
| fileName   | Název souboru pod zadaným folderPath. Pokud chcete k filtrování souborů použít zástupný znak, přeskočte toto nastavení a zadejte název souboru v nastavení zdroje aktivity. | No       |

**Příklad:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v tématu [kanály a aktivity v Azure Data Factory](concepts-pipelines-activities.md). V této části najdete seznam vlastností, které jsou podporovány zdrojem HDFS.

### <a name="hdfs-as-source"></a>HDFS jako zdroj

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro HDFS v `storeSettings` nastavení ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Povinné                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | Vlastnost *Type* v poli `storeSettings` musí být nastavená na **HdfsReadSettings**. | Yes                                           |
| ***Vyhledejte soubory ke zkopírování.*** |  |  |
| MOŽNOST 1: statická cesta<br> | Zkopírujte ze složky nebo cesty k souboru, který je zadaný v datové sadě. Pokud chcete zkopírovat všechny soubory ze složky, zadejte také `wildcardFileName` jako `*` . |  |
| MOŽNOST 2: zástupný znak<br>- wildcardFolderPath | Cesta ke složce se zástupnými znaky pro filtrování zdrojových složek. <br>Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku). Použijte `^` k ukončení, jestli má vlastní název složky zástupný znak nebo tento řídicí znak uvnitř. <br>Další příklady najdete v tématu [příklady složek a filtru souborů](#folder-and-file-filter-examples). | No                                            |
| MOŽNOST 2: zástupný znak<br>- wildcardFileName | Název souboru se zástupnými znaky v zadaném folderPath/wildcardFolderPath pro filtrování zdrojových souborů. <br>Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku); použijte `^` k Escape, jestli má skutečný název souboru zástupný znak nebo tento řídicí znak uvnitř.  Další příklady najdete v tématu [příklady složek a filtru souborů](#folder-and-file-filter-examples). | Yes |
| MOŽNOST 3: seznam souborů<br>- fileListPath | Určuje, že se má zkopírovat zadaná sada souborů. Najeďte na textový soubor, který obsahuje seznam souborů, které chcete zkopírovat (jeden soubor na řádek, přičemž relativní cesta k cestě je nakonfigurovaná v datové sadě).<br/>Když použijete tuto možnost, nezadávejte název souboru v datové sadě. Další příklady najdete v tématu [Příklady seznamů souborů](#file-list-examples). |No |
| ***Další nastavení*** |  | |
| zahrnout | Určuje, zda mají být data rekurzivně čtena z podsložek nebo pouze ze zadané složky. Když `recursive` je nastaveno na *hodnotu true* a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není kopírována ani vytvořena v jímky. <br>Povolené hodnoty jsou *true* (výchozí) a *false*.<br>Tato vlastnost se při konfiguraci nepoužívá `fileListPath` . |No |
| deleteFilesAfterCompletion | Uvádí, zda budou binární soubory po úspěšném přesunutí do cílového úložiště odstraněny ze zdrojového úložiště. Odstranění souboru je vázané na soubor, takže když aktivita kopírování selže, uvidíte, že některé soubory se už zkopírovaly do cílového umístění a odstranily ze zdroje, zatímco ostatní jsou pořád ve zdrojovém úložišti. <br/>Tato vlastnost je platná pouze ve scénáři kopírování binárních souborů. Výchozí hodnota: false. |No |
| modifiedDatetimeStart    | Soubory jsou filtrovány na základě *Poslední změny* atributu. <br>Soubory jsou vybrány, pokud čas poslední změny spadá do rozsahu `modifiedDatetimeStart` až `modifiedDatetimeEnd` . Čas se použije na časové pásmo UTC ve formátu *2018-12-01T05:00:00Z*. <br> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu není použit žádný filtr atributů souboru.  Když `modifiedDatetimeStart` má hodnotu DateTime, ale má hodnotu `modifiedDatetimeEnd` null, znamená, že jsou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime.  Když `modifiedDatetimeEnd` má hodnotu DateTime, ale `modifiedDatetimeStart` je null, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je menší, než hodnota DateTime.<br/>Tato vlastnost se při konfiguraci nepoužívá `fileListPath` . | No                                            |
| modifiedDatetimeEnd      | Platí to samé jako výše.  
| enablePartitionDiscovery | U souborů, které jsou rozdělené na oddíly, určete, jestli se mají analyzovat oddíly z cesty k souboru, a přidejte je jako další zdrojové sloupce.<br/>Povolené hodnoty jsou **false** (výchozí) a **true**. | No                                            |
| partitionRootPath | Pokud je povoleno zjišťování oddílů, zadejte absolutní kořenovou cestu, aby bylo možné číst rozdělené složky jako sloupce dat.<br/><br/>Pokud není zadaný, ve výchozím nastavení<br/>– Pokud použijete cestu k souboru v datové sadě nebo v seznamu souborů na zdroji, je kořenová cesta oddílu cestou nakonfigurovanou v datové sadě.<br/>– Když použijete filtr složky se zástupnými znaky, kořenová cesta oddílu je dílčí cesta před prvním zástupným znakem.<br/><br/>Předpokládejme například, že nakonfigurujete cestu v datové sadě jako kořen/složka/rok = 2020/měsíc = 08/Day = 27:<br/>– Pokud zadáte kořenovou cestu oddílu jako "root/složka/Year = 2020", aktivita kopírování vygeneruje další dva sloupce `month` a `day` hodnoty "08" a "27" společně se sloupci uvnitř souborů.<br/>-Pokud není zadána kořenová cesta oddílu, nebude vygenerován žádný sloupec navíc. | No                                            |
| maxConcurrentConnections | Počet připojení, která se můžou souběžně připojit k úložišti úložiště. Zadejte hodnotu pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | No                                            |
| ***Nastavení DistCp*** |  | |
| distcpSettings | Skupina vlastností, která se má použít při použití HDFS DistCp | No |
| resourceManagerEndpoint | Nekonečný koncový bod (ještě jiný projednání prostředků) | Ano, pokud se používá DistCp |
| tempScriptPath | Cesta ke složce, která se používá k uložení skriptu příkazu Temp DistCp Soubor skriptu je vygenerován nástrojem Data Factory a po dokončení úlohy kopírování bude odebrán. | Ano, pokud se používá DistCp |
| distcpOptions | Další možnosti, které jsou k dispozici pro příkaz DistCp | No |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
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
                    "type": "HdfsReadSettings",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Příklady filtru složek a souborů

Tato část popisuje výsledné chování, pokud použijete filtr zástupných znaků s cestou ke složce a názvem souboru.

| folderPath | fileName             | zahrnout | Struktura zdrojové složky a výsledek filtru (jsou načteny soubory **tučně** ) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (prázdné, použít výchozí) | false (nepravda)     | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | (prázdné, použít výchozí) | true      | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | false (nepravda)     | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | true      | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Příklady seznamů souborů

Tato část popisuje chování, které je výsledkem použití cesty seznamu souborů ve zdroji aktivity kopírování. Předpokládá, že máte následující strukturu zdrojové složky a chcete zkopírovat soubory, které jsou tučným typem:

| Ukázka zdrojové struktury                                      | Obsah v FileListToCopy.txt                             | Konfigurace Azure Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| kořen<br/>&nbsp;&nbsp;&nbsp;&nbsp;Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mezipaměť<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **V datové sadě:**<br>– Cesta ke složce: `root/FolderA`<br><br>**Ve zdroji aktivity kopírování:**<br>– Cesta k seznamu souborů: `root/Metadata/FileListToCopy.txt` <br><br>Cesta k seznamu souborů odkazuje na textový soubor ve stejném úložišti dat, který obsahuje seznam souborů, které chcete zkopírovat (jeden soubor na řádek, přičemž relativní cesta k cestě je nakonfigurovaná v datové sadě). |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Kopírování dat ze HDFS pomocí DistCp

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) je nativní nástroj příkazového řádku Hadoop, který slouží k distribuované kopii v clusteru Hadoop. Při spuštění příkazu v DistCp nejprve vypíše všechny soubory, které mají být zkopírovány, a poté vytvoří několik úloh mapování v clusteru Hadoop. Každá úloha mapy provádí binární kopírování ze zdroje do jímky.

Aktivita kopírování podporuje použití DistCp ke kopírování souborů do úložiště objektů BLOB v Azure (včetně [připravené kopie](copy-activity-performance.md)) nebo Azure Data Lake Store. V takovém případě může DistCp využít výkon vašeho clusteru místo spuštění v místním prostředí Integration runtime. Použití DistCp poskytuje lepší propustnost kopírování, zejména v případě, že je cluster velmi výkonný. Na základě konfigurace ve vaší datové továrně aktivita kopírování automaticky vytvoří příkaz DistCp, odešle ho do vašeho clusteru Hadoop a monitoruje stav kopírování.

### <a name="prerequisites"></a>Požadavky

Pokud chcete pomocí DistCp kopírovat soubory z HDFS do úložiště objektů BLOB v Azure (včetně připravené kopie) nebo Azure Data Lake Store, ujistěte se, že cluster Hadoop splňuje následující požadavky:

* Služby MapReduce a PŘÍZ jsou povoleny.  
* Verze PŘÍZe je 2,5 nebo novější.  
* HDFS Server je integrovaný s vaším cílovým úložištěm dat: **Azure Blob Storage** nebo **Azure Data Lake Store (adls Gen1)**: 

    - Systém souborů BLOB v Azure je nativně podporovaný od Hadoop 2,7. Stačí jenom zadat cestu JAR v konfiguraci prostředí Hadoop.
    - Systém souborů Azure Data Lake Store je zabalený počínaje systémem Hadoop 3.0.0-Alpha1. Pokud je verze clusteru Hadoop starší než tato verze, bude nutné ručně naimportovat Azure Data Lake Store balíčky JAR (Azure-datalake-Store. jar) do clusteru z [tohoto místa](https://hadoop.apache.org/releases.html)a zadat cestu k souboru jar v konfiguraci prostředí Hadoop.

* Připravte dočasnou složku v HDFS. Tato dočasná složka se používá k uložení skriptu prostředí DistCp, takže bude zabírat místo na úrovni KB.
* Ujistěte se, že uživatelský účet, který je k dispozici v rámci propojené služby HDFS, má oprávnění k těmto akcím:
   * Odešlete aplikaci v PŘÍZi.
   * V dočasné složce vytvořte podsložku a soubory pro čtení a zápis.

### <a name="configurations"></a>Konfigurace

V případě konfigurací a příkladů souvisejících s DistCp můžete přejít do části [HDFS as source](#hdfs-as-source) .

## <a name="use-kerberos-authentication-for-the-hdfs-connector"></a>Použití ověřování protokolem Kerberos pro konektor HDFS

K dispozici jsou dvě možnosti nastavení místního prostředí pro použití ověřování protokolem Kerberos pro konektor HDFS. Můžete zvolit ten, který lépe vyhovuje vaší situaci.
* Možnost 1: [připojení počítače s místním prostředím Integration runtime ve sféře protokolu Kerberos](#kerberos-join-realm)
* Možnost 2: [Povolení vzájemné důvěry mezi doménou systému Windows a sférou protokolu Kerberos](#kerberos-mutual-trust)

U obou možností se ujistěte, že jste zapnuli webhdfs pro cluster Hadoop:

1. Vytvořte objekt zabezpečení HTTP a keytab pro webhdfs.

    > [!IMPORTANT]
    > Objekt zabezpečení protokolu HTTP Kerberos musí začínat řetězcem **http/**, podle specifikace protokolu Kerberos http SPNEGO. Další informace najdete [tady](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#HDFS_Configuration_Options).

    ```bash
    Kadmin> addprinc -randkey HTTP/<namenode hostname>@<REALM.COM>
    Kadmin> ktadd -k /etc/security/keytab/spnego.service.keytab HTTP/<namenode hostname>@<REALM.COM>
    ```

2. Možnosti konfigurace HDFS: přidejte následující tři vlastnosti v `hdfs-site.xml` .
    ```xml
    <property>
        <name>dfs.webhdfs.enabled</name>
        <value>true</value>
    </property>
    <property>
        <name>dfs.web.authentication.kerberos.principal</name>
        <value>HTTP/_HOST@<REALM.COM></value>
    </property>
    <property>
        <name>dfs.web.authentication.kerberos.keytab</name>
        <value>/etc/security/keytab/spnego.service.keytab</value>
    </property>
    ```

### <a name="option-1-join-a-self-hosted-integration-runtime-machine-in-the-kerberos-realm"></a><a name="kerberos-join-realm"></a>Možnost 1: připojení počítače s místním prostředím Integration runtime ve sféře protokolu Kerberos

#### <a name="requirements"></a>Požadavky

* Počítač místního prostředí Integration runtime se musí připojit k sféře protokolu Kerberos a nemůže se připojit k žádné doméně systému Windows.

#### <a name="how-to-configure"></a>Jak nakonfigurovat

**Na serveru KDC:**

Vytvořte objekt zabezpečení, který Azure Data Factory použít, a zadejte heslo.

> [!IMPORTANT]
> Uživatelské jméno by nemělo obsahovat název hostitele.

```bash
Kadmin> addprinc <username>@<REALM.COM>
```

**Na počítači místního prostředí Integration Runtime:**

1.  Spusťte nástroj Ksetup a nakonfigurujte server a sféru protokolu Kerberos služba KDC (Key Distribution Center) (KDC).

    Počítač musí být nakonfigurován jako člen pracovní skupiny, protože se liší sféra protokolu Kerberos od domény systému Windows. Tuto konfiguraci můžete dosáhnout nastavením sféry protokolu Kerberos a přidáním serveru služby KDC spuštěním následujících příkazů. Nahraďte *REALM.com* názvem vaší sféry.

    ```cmd
    C:> Ksetup /setdomain REALM.COM
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    ```

    Po spuštění těchto příkazů restartujte počítač.

2.  Ověřte konfiguraci pomocí `Ksetup` příkazu. Výstup by měl vypadat takto:

    ```cmd
    C:> Ksetup
    default realm = REALM.COM (external)
    REALM.com:
        kdc = <your_kdc_server_address>
    ```

**Ve vaší datové továrně:**

* Nakonfigurujte konektor HDFS pomocí ověřování systému Windows společně s vaším hlavním jménem a heslem protokolu Kerberos pro připojení ke zdroji dat HDFS. Podrobnosti o konfiguraci najdete v části [Vlastnosti propojené služby HDFS](#linked-service-properties) .

### <a name="option-2-enable-mutual-trust-between-the-windows-domain-and-the-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Možnost 2: povolení vzájemné důvěry mezi doménou systému Windows a sférou protokolu Kerberos

#### <a name="requirements"></a>Požadavky

*   Počítač místního prostředí Integration runtime se musí připojit k doméně systému Windows.
*   K aktualizaci nastavení řadiče domény potřebujete oprávnění.

#### <a name="how-to-configure"></a>Jak nakonfigurovat

> [!NOTE]
> V následujícím kurzu nahraďte REALM.COM a AD.COM vlastním názvem sféry a řadičem domény.

**Na serveru KDC:**

1. V souboru *krb5. conf* Upravte konfiguraci služby KDC tak, aby služba KDC důvěřovala doméně systému Windows odkazem na následující šablonu konfigurace. Ve výchozím nastavení se konfigurace nachází na adrese */etc/krb5.conf*.

   ```config
   [logging]
    default = FILE:/var/log/krb5libs.log
    kdc = FILE:/var/log/krb5kdc.log
    admin_server = FILE:/var/log/kadmind.log
            
   [libdefaults]
    default_realm = REALM.COM
    dns_lookup_realm = false
    dns_lookup_kdc = false
    ticket_lifetime = 24h
    renew_lifetime = 7d
    forwardable = true
            
   [realms]
    REALM.COM = {
     kdc = node.REALM.COM
     admin_server = node.REALM.COM
    }
   AD.COM = {
    kdc = windc.ad.com
    admin_server = windc.ad.com
   }
            
   [domain_realm]
    .REALM.COM = REALM.COM
    REALM.COM = REALM.COM
    .ad.com = AD.COM
    ad.com = AD.COM
            
   [capaths]
    AD.COM = {
     REALM.COM = .
    }
    ```

   Po konfiguraci souboru restartujte službu KDC.

2. Připravte objekt zabezpečení s názvem *KRBTGT/REALM. COM \@ AD.com* na serveru KDC pomocí následujícího příkazu:

    ```cmd
    Kadmin> addprinc krbtgt/REALM.COM@AD.COM
    ```

3. Do konfiguračního souboru služby *Hadoop.Security.auth_to_local* HDFS přidejte `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//` .

**Na řadiči domény:**

1.  Chcete-li `Ksetup` Přidat položku sféry, spusťte následující příkazy:

    ```cmd
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
    ```

2.  Vytvořte vztah důvěryhodnosti mezi doménou systému Windows a sférou protokolu Kerberos. [heslo] je heslo pro objekt zabezpečení *KRBTGT/REALM. COM \@ AD.com*.

    ```cmd
    C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /password:[password]
    ```

3.  Vyberte šifrovací algoritmus, který se používá v protokolu Kerberos.

    a. Vyberte **Správce serveru**  >  **Zásady skupiny doméně pro správu**  >    >  **Zásady skupiny objekty**  >  **výchozí nebo aktivní zásady domény** a pak vyberte **Upravit**.

    b. V podokně **Editor pro správu zásad skupiny** vyberte zásady **Konfigurace počítače**  >    >  **nastavení systému Windows** nastavení  >  **zabezpečení**  >  **místní zásady**  >  a pak konfigurace **zabezpečení sítě: Konfigurace typů šifrování povolených pro protokol Kerberos**.

    c. Vyberte šifrovací algoritmus, který chcete použít při připojování k serveru KDC. Můžete vybrat všechny možnosti.

    ![Snímek obrazovky s podoknem zabezpečení sítě: Konfigurace typů šifrování povolených pro protokol Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    d. Pomocí `Ksetup` příkazu určete šifrovací algoritmus, který se má použít pro zadanou sféru.

    ```cmd
    C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96
    ```

4.  Vytvořte mapování mezi účtem domény a hlavním objektem Kerberos, abyste mohli použít objekt Kerberos v doméně systému Windows.

    a. Vyberte **Nástroje pro správu**  >  **Uživatelé a počítače služby Active Directory**.

    b. Pomocí možnosti **Zobrazit**  >  **Pokročilé funkce** Nakonfigurujte rozšířené funkce.

    c. V podokně **Pokročilé funkce** klikněte pravým tlačítkem na účet, na který chcete vytvořit mapování, a v podokně **mapování názvů** vyberte kartu **názvy protokolu Kerberos** .

    d. Přidejte objekt zabezpečení ze sféry.

       ![Podokno "mapování identity zabezpečení"](media/connector-hdfs/map-security-identity.png)

**Na počítači místního prostředí Integration Runtime:**

* Spuštěním následujících `Ksetup` příkazů přidejte položku sféry.

   ```cmd
   C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
   C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

**Ve vaší datové továrně:**

* Nakonfigurujte konektor HDFS pomocí ověřování systému Windows společně s účtem domény nebo objektem Kerberos pro připojení ke zdroji dat HDFS. Podrobnosti o konfiguraci najdete v části [Vlastnosti propojené služby HDFS](#linked-service-properties) .

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Informace o vlastnostech aktivity vyhledávání najdete v tématu [aktivita vyhledávání v Azure Data Factory](control-flow-lookup-activity.md).

## <a name="delete-activity-properties"></a>Odstranit vlastnosti aktivity

Informace o vlastnostech aktivity odstranění najdete v tématu [Odstranění aktivity v Azure Data Factory](delete-activity.md).

## <a name="legacy-models"></a>Starší modely

>[!NOTE]
>Následující modely jsou stále podporovány, protože jsou z důvodu zpětné kompatibility. Doporučujeme použít dříve popisovaný nový model, protože uživatelské rozhraní pro vytváření Azure Data Factory bylo přepnuto na generování nového modelu.

### <a name="legacy-dataset-model"></a>Model zastaralé sady dat

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost *Type* datové sady musí být nastavená na *Shared* . |Yes |
| folderPath | Cesta ke složce Je podporován filtr zástupných znaků. Povolené zástupné znaky jsou `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku). `^` Pokud má skutečný název souboru zástupný znak nebo tento řídicí znak v rámci, použijte k Escape. <br/><br/>Příklady: RootFolder/podsložce/, další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). |Yes |
| fileName |  Název nebo zástupný filtr pro soubory v zadaném "folderPath". Pokud nezadáte hodnotu pro tuto vlastnost, datová sada bude ukazovat na všechny soubory ve složce. <br/><br/>Pro filtr je povolený zástupný znak `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá nule nebo jednomu znaku).<br/>-Příklad 1: `"fileName": "*.csv"`<br/>-Příklad 2: `"fileName": "???20180427.txt"`<br/>Použijte `^` k ukončení, jestli má vlastní název složky zástupný znak nebo tento řídicí znak uvnitř. |No |
| modifiedDatetimeStart | Soubory jsou filtrovány na základě *Poslední změny* atributu. Soubory jsou vybrány, pokud čas poslední změny spadá do rozsahu `modifiedDatetimeStart` až `modifiedDatetimeEnd` . Čas se použije na časové pásmo UTC ve formátu *2018-12-01T05:00:00Z*. <br/><br/> Počítejte s tím, že pokud chcete použít filtr souborů pro velký počet souborů, je nutné povolit toto nastavení, pokud bude mít vliv na celkový výkon přesunu dat. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu není použit žádný filtr atributů souboru.  Když `modifiedDatetimeStart` má hodnotu DateTime, ale má hodnotu `modifiedDatetimeEnd` null, znamená, že jsou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime.  Když `modifiedDatetimeEnd` má hodnotu DateTime, ale `modifiedDatetimeStart` je null, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je menší, než hodnota DateTime.| No |
| modifiedDatetimeEnd | Soubory jsou filtrovány na základě *Poslední změny* atributu. Soubory jsou vybrány, pokud čas poslední změny spadá do rozsahu `modifiedDatetimeStart` až `modifiedDatetimeEnd` . Čas se použije na časové pásmo UTC ve formátu *2018-12-01T05:00:00Z*. <br/><br/> Počítejte s tím, že pokud chcete použít filtr souborů pro velký počet souborů, je nutné povolit toto nastavení, pokud bude mít vliv na celkový výkon přesunu dat. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu není použit žádný filtr atributů souboru.  Když `modifiedDatetimeStart` má hodnotu DateTime, ale má hodnotu `modifiedDatetimeEnd` null, znamená, že jsou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime.  Když `modifiedDatetimeEnd` má hodnotu DateTime, ale `modifiedDatetimeStart` je null, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je menší, než hodnota DateTime.| No |
| formát | Pokud chcete kopírovat soubory mezi úložišti na základě souborů (binární kopie), přeskočte oddíl formát jak v definicích vstupní, tak i ve výstupní datové sadě.<br/><br/>Chcete-li analyzovat soubory s konkrétním formátem, jsou podporovány následující typy formátu souboru: *TextFormat*, *JsonFormat*, *AvroFormat*, *OrcFormat*, *ParquetFormat*. V části formát nastavte vlastnost *typ* na jednu z těchto hodnot. Další informace najdete v oddílech [Formát textu](supported-file-formats-and-compression-codecs-legacy.md#text-format), [formát JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formát Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), formát [ORC](supported-file-formats-and-compression-codecs-legacy.md#orc-format)a formát [Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Ne (jenom pro binární scénář kopírování) |
| komprese | Zadejte typ a úroveň komprese dat. Další informace najdete v tématu [podporované formáty souborů a kompresní kodeky](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Podporované typy jsou: *gzip*, *Deflate*, *bzip2* a *ZipDeflate*.<br/>Podporované úrovně: *optimální* a *nejrychlejší*. |No |

>[!TIP]
>Chcete-li zkopírovat všechny soubory ve složce, zadejte pouze **FolderPath** .<br>Chcete-li zkopírovat jeden soubor se zadaným názvem, zadejte **FolderPath** s částí **složky a názvem souboru s** názvem.<br>Chcete-li zkopírovat podmnožinu souborů ve složce, zadejte **FolderPath** s částí složky a **názvem souboru** s filtrem zástupných znaků.

**Příklad:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
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
| typ | Vlastnost *Type* zdroje aktivity kopírování musí být nastavená na *HdfsSource*. |Yes |
| zahrnout | Určuje, zda mají být data rekurzivně čtena z podsložek nebo pouze ze zadané složky. Pokud je rekurzivní nastavení nastaveno na *hodnotu true* a jímka je úložiště založené na souborech, prázdná složka nebo podsložka nebude kopírována ani vytvořena v jímky.<br/>Povolené hodnoty jsou *true* (výchozí) a *false*. | No |
| distcpSettings | Skupina vlastností při použití HDFS DistCp. | No |
| resourceManagerEndpoint | Správce prostředků koncový bod PŘÍZe | Ano, pokud se používá DistCp |
| tempScriptPath | Cesta ke složce, která se používá k uložení skriptu příkazu Temp DistCp Soubor skriptu je vygenerován nástrojem Data Factory a po dokončení úlohy kopírování bude odebrán. | Ano, pokud se používá DistCp |
| distcpOptions | Další možnosti jsou k dispozici pro příkaz DistCp. | No |
| maxConcurrentConnections | Počet připojení, která se můžou souběžně připojit k úložišti úložiště. Zadejte hodnotu pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | No |

**Příklad: HDFS zdroj v aktivitě kopírování pomocí DistCp**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat, která jsou podporovaná jako zdroje a jímky, pomocí aktivity kopírování v Azure Data Factory najdete v tématu [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
