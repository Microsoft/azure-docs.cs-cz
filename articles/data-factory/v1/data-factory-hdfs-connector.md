---
title: Přesun dat z místní HDFS | Dokumentace Microsoftu
description: Další informace o tom, jak přesunout data z místní HDFS pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e8af817c942a28cfd28d1b13303aebfcc10d31ba
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016042"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Přesun dat z místní HDFS pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-hdfs-connector.md)
> * [Verze 2 (aktuální verze)](../connector-hdfs.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [HDFS konektor ve verzi V2](../connector-hdfs.md).

Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunu dat z v místním HDFS. Je nástavbou [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který nabízí obecný přehled o přesun dat pomocí aktivity kopírování.

Kopírování dat z HDFS do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako jímky, najdete v článku [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. Data factory aktuálně podporuje pouze přesouvá data z v místním HDFS do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do místní HDFS.

> [!NOTE]
> Aktivita kopírování nedojde k odstranění zdrojového souboru, co se úspěšně zkopíruje do cíle. Pokud je potřeba odstranit zdrojový soubor po úspěšném kopírování, vytvoření vlastní aktivity a stejný soubor odstranit také pomocí aktivity v kanálu. 

## <a name="enabling-connectivity"></a>Povolení připojení
Služba data Factory podporuje připojení k místní HDFS pomocí brány správy dat. Zobrazit [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku se dozvíte o brána správy dat a podrobné pokyny o nastavení brány. Použití brány pro připojení k HDFS, i když je hostované ve Virtuálním počítači Azure IaaS.

> [!NOTE]
> Ujistěte se, že brána pro správu dat přístup k **všechny** [název uzlu serveru]: [název uzlu port] a [data uzlu servery]: [data uzlu port] clusteru Hadoop. Výchozí [název uzlu port] je 50070 a výchozí [data uzlu port] je 50075.

Když bránu můžete nainstalovat na stejnou místní počítač nebo virtuální počítač Azure, jako HDFS, doporučujeme nainstalovat bránu na samostatný počítač nebo Azure IaaS VM. Budete mít bránu pro samostatný počítač snižuje kolize prostředků a zvyšuje výkon. Když bránu instalujete na samostatný počítač, na počítači měli mít přístup k počítači s HDFS.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z HDFS zdroje pomocí různých nástrojů a rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Zobrazit [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **Azure portal**, **sady Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **rozhraní .NET API**a  **Rozhraní REST API**. Zobrazit [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování.

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do služby data factory.
2. Vytvoření **datových sad** k představují vstupní a výstupní data pro operaci kopírování.
3. Vytvoření **kanálu** s aktivitou kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup.

Při použití Průvodce definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál) se automaticky vytvoří za vás. Při použití nástroje a rozhraní API (s výjimkou rozhraní .NET API), můžete definovat tyto entity služby Data Factory ve formátu JSON.  Tady je příklad s definice JSON entit služby Data Factory, které se používají ke kopírování dat z úložiště dat HDFS najdete v části [příklad JSON: Kopírování dat z místní HDFS do objektů Blob v Azure](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) části tohoto článku.

Následující části obsahují podrobnosti o vlastnostech JSON, které se používají k definování entit služby Data Factory konkrétní HDFS:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Propojená služba propojuje úložiště dat do služby data factory. Vytvoření propojené služby typu **Hdfs** k propojení místní HDFS se svou datovou továrnou. Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro HDFS propojenou službu.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavená na: **Hdfs** |Ano |
| URL |Adresa URL HDFS |Ano |
| authenticationType. |Anonymní, nebo Windows. <br><br> Použití **ověřování protokolem Kerberos** konektor HDFS najdete v tématu [v této části](#use-kerberos-authentication-for-hdfs-connector) odpovídajícím způsobem nastavit v místním prostředí. |Ano |
| uživatelské jméno |Ověřování uživatelského jména pro Windows. Pro ověřování protokolem Kerberos, zadejte `<username>@<domain>.com`. |Ano (pro ověřování systému Windows) |
| heslo |Heslo pro ověřování Windows. |Ano (pro ověřování systému Windows) |
| Název brány |Název brány, který služba Data Factory měla použít pro připojení k HDFS. |Ano |
| encryptedCredential |[Nové AzureRMDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/azurerm.datafactories/new-azurermdatafactoryencryptvalue) výstup přihlašovacích údajů pro přístup. |Ne |

### <a name="using-anonymous-authentication"></a>Anonymní ověřování

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-windows-authentication"></a>Ověřování Windows

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```
## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny datové sady typy (Azure SQL, Azure blob, tabulky Azure, atd.).

**TypeProperties** oddílu se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti. TypeProperties části datové sady typu **sdílení souborů** (což zahrnuje datová sada HDFS) má následující vlastnosti

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Cesta ke složce. Příklad: `myfolder`<br/><br/>Použijte řídicí znak "\" pro zvláštní znaky v řetězci. Příklad: folder\subfolder, určete složku\\\\podsložky a d:\samplefolder, zadejte d:\\\\ukázková_složka.<br/><br/>Můžete zkombinovat tato vlastnost se **partitionBy** mít složku cesty založené na řez počátečním/koncovém data a časy. |Ano |
| fileName |Zadejte název souboru **folderPath** Pokud má tabulka, která má odkazovat na konkrétní soubor ve složce. Pokud je nezadávejte žádnou hodnotu pro tuto vlastnost, v tabulce odkazuje na všechny soubory ve složce.<br/><br/>Pokud není zadán název souboru pro výstupní datovou sadu, název generovaného souboru by měl být v následujícím tento formát: <br/><br/>Data. <Guid>.txt (například:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Ne |
| partitionedBy |partitionedBy slouží k určení dynamické folderPath, název souboru pro data časových řad. Příklad: folderPath s parametry pro každou hodinu data. |Ne |
| formát | Jsou podporovány následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formát](data-factory-supported-file-and-compression-formats.md#orc-format), a [formát Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete **kopírovat soubory jako-je** mezi souborové úložištěm (binární kopie) a přeskočit část o formátu v definicích oba vstupní a výstupní datové sady. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimální** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese ve službě Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

> [!NOTE]
> Název souboru a fileFilter nelze použít současně.

### <a name="using-partionedby-property"></a>Pomocí vlastnosti partionedBy
Jak je uvedeno v předchozí části, můžete zadat dynamické folderPath a název souboru pro data časových řad s **partitionedBy** vlastnost [funkce Data Factory a systémové proměnné](data-factory-functions-variables.md).

Další informace o čase řady datových sad, plánování a řezů, naleznete v tématu [vytváření datových sad](data-factory-create-datasets.md), [plánování a provádění](data-factory-scheduling-and-execution.md), a [vytváření kanálů](data-factory-create-pipelines.md) článků.

#### <a name="sample-1"></a>Příklad 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
V tomto příkladu {řez} se nahradí hodnotu proměnné objektu pro vytváření dat systému SliceStart ve formátu (YYYYMMDDHH) zadaná. Vlastnosti SliceStart odkazuje na počáteční čas řezu. V cestě folderPath se liší pro každý řez. Příklad: wikidatagateway/wikisampledataout/2014100103 nebo wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Příklad 2:

```JSON
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

Vzhledem k tomu, vlastnosti v části typeProperties aktivity se liší s jednotlivými typu aktivity. Pro aktivitu kopírování se liší v závislosti na typy zdroje a jímky.

Pro aktivitu kopírování, pokud je zdroj typu **FileSystemSource** v části typeProperties jsou k dispozici následující vlastnosti:

**FileSystemSource** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| rekurzivní |Určuje, jestli se data ze složek sub nebo pouze z určené složky Číst rekurzivně. |Hodnota TRUE, False (výchozí) |Ne |

## <a name="supported-file-and-compression-formats"></a>Podporované formáty souborů a komprese
Zobrazit [formáty souborů a komprese ve službě Azure Data Factory](data-factory-supported-file-and-compression-formats.md) článek věnovaný tomu podrobnosti.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Příklad JSON: Kopírování dat z místní HDFS do objektů Blob v Azure
Tato ukázka předvádí, jak kopírovat data do Azure Blob Storage z v místním HDFS. Nicméně je možné zkopírovat data **přímo** do libovolné jímky uvedeno [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivit kopírování ve službě Azure Data Factory.  

Tento vzorový definice JSON pro následující entity služby Data Factory. Tyto definice můžete použít k vytvoření kanálu pro kopírování dat z HDFS do Azure Blob Storage pomocí [webu Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

1. Propojené služby typu [OnPremisesHdfs](#linked-service-properties).
2. Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [sdílení souborů](#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [FileSystemSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázce kopíruje data z HDFS místní do objektu blob Azure každou hodinu. Vlastnostech JSON použitých v tyto ukázky jsou popsány v části podle ukázky.

Jako první krok nastavte si bránu správy dat. Podle pokynů [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku.

**HDFS propojené služby:** Tento příklad používá ověřování Windows. Zobrazit [HDFS propojená služba](#linked-service-properties) oddílu pro různé typy ověřování můžete použít.

```JSON
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

**Propojená služba Azure Storage:**

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

**HDFS vstupní datové sady:** Tato datová sada odkazuje na složku HDFS DataTransfer/UnitTest /. Kanál kopíruje do cíle všechny soubory v této složce.

Nastavení "externí": "PRAVDA" informuje služby Data Factory, že datová sada je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

```JSON
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Výstupní datová sada Azure Blob:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob se dynamicky vyhodnocuje na základě doby spuštění řez, který se právě zpracovává. Cesta ke složce používá rok, měsíc, den a části hodin čas spuštění.

```JSON
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Aktivita kopírování v kanálu pomocí systému souborů zdroje a jímky objektu Blob:**

Kanálu obsahujícího aktivitu kopírování, kterou Pokud chcete použít tyto vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **FileSystemSource** a **jímky** je typ nastaven na **BlobSink**. Zadaná pro dotaz SQL **dotazu** vlastnost vybere data za poslední hodinu pro kopírování.

```JSON
{
    "name": "pipeline",
    "properties":
    {
        "activities":
        [
            {
                "name": "HdfsToBlobCopy",
                "inputs": [ {"name": "InputDataset"} ],
                "outputs": [ {"name": "OutputDataset"} ],
                "type": "Copy",
                "typeProperties":
                {
                    "source":
                    {
                        "type": "FileSystemSource"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "policy":
                {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Používat ověřování protokolem Kerberos pro konektor HDFS
Existují dvě možnosti, jak nastavit v místním prostředí tak, aby používala ověřování protokolu Kerberos v konektoru HDFS. Můžete zvolit, že je lépe vyhovuje vašemu případu.
* Možnost 1: [Připojte se k počítači brány ve sféře Kerberos](#kerberos-join-realm)
* Možnost 2: [Povolit vzájemné důvěry mezi Windows doménu a sféru protokolu Kerberos](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Možnost 1: Připojte se k počítači brány ve sféře Kerberos

#### <a name="requirement"></a>Požadavek:

* Počítači brány potřebuje k připojení sféry Kerberos a nejde připojit k libovolné doméně Windows.

#### <a name="how-to-configure"></a>Postup konfigurace:

**Na počítači brány:**

1.  Spustit **Ksetup** nástroj a nakonfigurujte server KDC protokolu Kerberos a sféry.

    Na počítači musí být nakonfigurován jako člen pracovní skupiny, protože Sféra Kerberos se liší od domény Windows. Toho lze dosáhnout nastavením sféry Kerberos a přidání serveru služby KDC následujícím způsobem. Nahraďte *REALM.COM* s vlastním příslušných sféra podle potřeby.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Restartujte** počítač po spuštění těchto příkazů 2.

2.  Ověřte konfiguraci pomocí **Ksetup** příkazu. Výstup by měl být jako:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Ve službě Azure Data Factory:**

* Nakonfigurujte pomocí konektoru HDFS **ověřování Windows** společně s vaší hlavní název Kerberos a heslo pro připojení ke zdroji dat HDFS. Zkontrolujte [propojená služba HDFS vlastnosti](#linked-service-properties) části na podrobnosti o konfiguraci.

### <a name="kerberos-mutual-trust"></a>Možnost 2: Povolit vzájemné důvěry mezi Windows doménu a sféru protokolu Kerberos

#### <a name="requirement"></a>Požadavek:
*   Počítači brány musí připojit k doméně Windows.
*   Budete potřebovat oprávnění k aktualizaci nastavení řadiče domény.

#### <a name="how-to-configure"></a>Postup konfigurace:

> [!NOTE]
> Nahraďte REALM.COM a AD.COM v následujícím kurzu s příslušnými sféry a řadič domény, podle potřeby.

**Na serveru služby KDC:**

1.  Upravit konfiguraci služby KDC v **krb5.conf** souboru chcete, aby služba KDC důvěřovat odkazující na následující šablonu konfigurace domény Windows. Ve výchozím nastavení, se nachází v konfiguraci **/etc/krb5.conf**.

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

  **Restartujte** služba KDC po konfiguraci.

2.  Příprava objekt zabezpečení s názvem **krbtgt/REALM.COM@AD.COM** na serveru služby KDC pomocí následujícího příkazu:

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.  V **hadoop.security.auth_to_local**  konfigurace služby HDFS přidejte `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`.

**Na řadiči domény:**

1.  Spusťte následující příkaz **Ksetup** příkazy, které chcete přidat položku sféry:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Navázání vztahu důvěryhodnosti z domény Windows do sféry Kerberos. [heslo] je heslo pro objekt **krbtgt/REALM.COM@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Vyberte šifrovací algoritmus použitý v protokolu Kerberos.

    1. Přejděte do správce serveru > Správa zásad skupiny > domény > objekty zásad skupiny > výchozí nebo zásady aktivní domény a upravit.

    2. V **Editor správy zásad skupiny** automaticky otevíraném okně, přejděte na konfigurace počítače > zásady > Nastavení Windows > Nastavení zabezpečení > Místní zásady > Možnosti zabezpečení a nakonfigurujte **sítě zabezpečení: Konfigurovat typy šifrování povolené pro protokol Kerberos**.

    3. Vyberte šifrovací algoritmus, který chcete použít při připojení k služby KDC. Běžně můžete jednoduše vybrat všechny možnosti.

        ![Typy šifrování konfiguračním pro protokol Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. Použití **Ksetup** příkazu zadejte šifrovací algoritmus, který se má použít na konkrétní SFÉRY.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Vytvoření mapování mezi účet domény a zabezpečení protokolu Kerberos, aby bylo možné používat instanční objekt pomocí protokolu Kerberos v doméně Windows.

    1. Spuštění nástroje pro správu > **Active Directory Users and Computers**.

    2. Konfigurace rozšířených funkcí kliknutím **zobrazení** > **pokročilé funkce**.

    3. Najděte účet, ke kterému chcete vytvořit mapování a klikněte pravým tlačítkem na zobrazení **mapování názvů** > klikněte na tlačítko **Kerberos názvy** kartu.

    4. Přidáte objekt zabezpečení ze sféry.

        ![Identita zabezpečení mapy](media/data-factory-hdfs-connector/map-security-identity.png)

**Na počítači brány:**

* Spusťte následující příkaz **Ksetup** příkazy, které chcete přidat položku sféry.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Ve službě Azure Data Factory:**

* Nakonfigurujte pomocí konektoru HDFS **ověřování Windows** společně se váš účet domény nebo objekt zabezpečení protokolu Kerberos pro připojení ke zdroji dat HDFS. Zkontrolujte [propojená služba HDFS vlastnosti](#linked-service-properties) části na podrobnosti o konfiguraci.

> [!NOTE]
> Pokud chcete namapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, najdete v článku [mapování sloupců v datové sadě ve službě Azure Data Factory](data-factory-map-columns.md).


## <a name="performance-and-tuning"></a>Výkon a optimalizace
Zobrazit [výkonem aktivity kopírování & Průvodci optimalizací](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů této ovlivnit výkon přesouvání dat (aktivita kopírování) ve službě Azure Data Factory a různé způsoby, jak optimalizovat.
