---
title: Přesun dat z místní HDFS | Microsoft Docs
description: Další informace o tom, jak přesunout data z HDFS místně pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b8a35e19628ebbe75d3f1890a6d64ab777519698
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621543"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Přesun dat z místní HDFS pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](data-factory-hdfs-connector.md)
> * [Verze 2 – Preview](../connector-hdfs.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [HDFS konektor v V2](../connector-hdfs.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory pro přesun dat z místní službě HDFS. Vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který představuje obecný přehled přesun dat s aktivitou kopírování.

Můžete zkopírovat data z HDFS do úložiště dat žádné podporované jímky. Seznam úložišť dat jako jímky nepodporuje aktivitě kopírování najdete v tématu [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. Objekt pro vytváření dat aktuálně podporuje pouze přesunutí dat z místní službě HDFS k jiným úložištím dat, ale ne pro přesun dat z jiných úložišť dat na místní službě HDFS.

> [!NOTE]
> Aktivita kopírování nedojde k odstranění zdrojového souboru po byl úspěšně zkopírován do cílové. Pokud potřebujete odstranit zdrojový soubor po úspěšné kopie, vytvořte vlastní aktivity odstranit soubor a použijte aktivitu v kanálu. 

## <a name="enabling-connectivity"></a>Povolení připojení
Služba data Factory podporuje připojení k místní službě HDFS pomocí Brána pro správu dat. V tématu [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku se dozvíte o Brána pro správu dat a podrobné pokyny o nastavení brány. Použijte bránu pro připojení k HDFS i v případě, že je hostovaná ve virtuálním počítači Azure IaaS.

> [!NOTE]
> Ujistěte se, že brána pro správu dat přístup k **všechny** [název uzlu serveru]: [name portu uzlu] a [data uzlu servery]: [datový uzel port] clusteru Hadoop. Výchozí [název uzlu port] je 50070 a výchozí hodnota [datový uzel port] je 50075.

Zatímco můžete bránu nainstalovat na stejný místní počítač nebo virtuální počítač Azure, jako HDFS, doporučujeme nainstalovat bránu na samostatný počítač nebo Azure IaaS virtuálních počítačů. S brány na samostatný počítač snižuje kolize prostředků a zvyšuje výkon. Při instalaci brány na samostatný počítač, na počítač byste měli mít přístup k počítači s HDFS.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z HDFS zdroje pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním**. V tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) podrobný rychlé vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **.NET API**, a **REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování.

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený:

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory.
2. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování.
3. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup.

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Při použití nástroje nebo rozhraní API (s výjimkou .NET API), definujete tyto entity služby Data Factory pomocí formátu JSON.  Příklad s definicemi JSON entit služby Data Factory, které se používají ke zkopírování dat z úložiště dat HDFS, naleznete v tématu [JSON příklad: kopírování dat z místní HDFS do objektu Blob Azure](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) tohoto článku.

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které slouží k definování entit služby Data Factory, které jsou specifické pro HDFS:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Propojená služba odkazuje na objekt pro vytváření dat úložiště dat. Vytvoření propojené služby typu **Hdfs** propojit místní službě HDFS do data factory. Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro HDFS propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu musí být nastavena na: **Hdfs** |Ano |
| URL |Adresa URL HDFS |Ano |
| authenticationType. |Anonymní, nebo Windows. <br><br> Použít **ověřování protokolem Kerberos** HDFS konektor, najdete v části [v této části](#use-kerberos-authentication-for-hdfs-connector) odpovídajícím způsobem nastavit v místním prostředí. |Ano |
| Uživatelské jméno |Ověřování uživatelského jména pro systém Windows. Pro ověřování pomocí protokolu Kerberos, zadejte `<username>@<domain>.com`. |Ano (pro ověřování systému Windows) |
| heslo |Heslo pro ověřování systému Windows. |Ano (pro ověřování systému Windows) |
| gatewayName |Název brány, kterou služba Data Factory měla použít pro připojení k HDFS. |Ano |
| encryptedCredential |[Nové AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) výstup pověření přístup. |Ne |

### <a name="using-anonymous-authentication"></a>Pomocí anonymní ověřování

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

### <a name="using-windows-authentication"></a>Pomocí ověřování systému Windows

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
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu (Azure SQL Azure blob, tabulky Azure, atd.).

**Rámci typeProperties** oddílu se liší pro jednotlivé typy datovou sadu a informace o umístění dat v úložišti dat. Rámci typeProperties část datové sady typ **sdílení souborů** (což zahrnuje datová sada HDFS) má následující vlastnosti

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Cesta ke složce. Příklad: `myfolder`<br/><br/>Použít řídicí znak ' \ ' pro speciální znaky v řetězci. Příklad: pro folder\subfolder, určete složku\\\\podsložky a pro d:\samplefolder, zadejte d:\\\\ukázková_složka.<br/><br/>Tato vlastnost se můžete kombinovat **partitionBy** tak, aby měl složky cesty založené na řez počáteční nebo koncové hodnoty data a času. |Ano |
| fileName |Zadejte název souboru do **folderPath** Pokud chcete, aby v tabulce odkazovat na konkrétní soubor ve složce. Pokud nezadáte žádnou hodnotu pro tuto vlastnost, tabulka odkazuje na všechny soubory ve složce.<br/><br/>Pokud není zadán název souboru pro datovou sadu výstupů, název vygenerovaný soubor bude v následujícím tento formát: <br/><br/>Data. <Guid>.txt (například:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Ne |
| partitionedBy |partitionedBy slouží k určení dynamické folderPath, název souboru pro data časové řady. Příklad: folderPath parametry pro každou hodinu data. |Ne |
| Formát | Jsou podporovány následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte **typ** vlastnost pod formát na jednu z těchto hodnot. Další informace najdete v tématu [textovém formátu](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátu](data-factory-supported-file-and-compression-formats.md#orc-format), a [Parquet formát](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete **zkopírujte soubory jako-je** mezi souborové úložiště (binární kopie), přeskočte část formátu v obou definice vstupní a výstupní datové sady. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

> [!NOTE]
> Název souboru a fileFilter nelze použít současně.

### <a name="using-partionedby-property"></a>Pomocí vlastnosti partionedBy
Jak je uvedeno v předchozí části, můžete zadat dynamické folderPath a název souboru pro data časové řady s **partitionedBy** vlastnost [funkce pro vytváření dat a systémové proměnné](data-factory-functions-variables.md).

Další informace o datové sady času řady, plánování a řezů, najdete v části [vytváření datových sad](data-factory-create-datasets.md), [plánování a provádění](data-factory-scheduling-and-execution.md), a [vytváření kanálů](data-factory-create-pipelines.md) články.

#### <a name="sample-1"></a>Příklad 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
V tomto příkladu {řez} se nahradí hodnotu objektu pro vytváření dat systému proměnné SliceStart ve formátu (YYYYMMDDHH) zadán. Vlastnosti SliceStart odkazuje na spuštění řezu. FolderPath se liší pro každý řez. Příklad: wikidatagateway/wikisampledataout/2014100103 nebo wikidatagateway/wikisampledataout/2014100104.

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
V tomto příkladu jsou extrahován rok, měsíc, den a čas SliceStart do samostatné proměnné, které jsou používány folderPath a název vlastnosti.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, například název, popis, vstupní a výstupní tabulky a zásad jsou dostupné pro všechny typy aktivit.

Vzhledem k tomu, vlastnosti dostupné v rámci typeProperties části aktivity se liší podle každý typ aktivity. Pro aktivitu kopírování budou lišit v závislosti na typech zdrojů a jímky.

Pro aktivitu kopírování, pokud je zdroj typu **FileSystemSource** následující vlastnosti jsou k dispozici v rámci typeProperties části:

**FileSystemSource** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| Rekurzivní |Označuje, zda je data načíst rekurzivně z dílčí složky nebo pouze do zadané složky. |Hodnota TRUE, False (výchozí) |Ne |

## <a name="supported-file-and-compression-formats"></a>Podporované formáty souborů a komprese
V tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md) článek na podrobnosti.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Příklad JSON: kopírování dat z místní HDFS do objektu Blob Azure
Tento příklad ukazuje, jak ke zkopírování dat z místní službě HDFS do úložiště objektů Blob Azure. Však můžete zkopírovat data **přímo** žádnému jímky uvádí [sem](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.  

Ukázka poskytuje JSON definice pro následující entity služby Data Factory. Tyto definice můžete použít k vytvoření kanálu pro zkopírování dat z HDFS do Azure Blob Storage pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

1. Propojené služby typu [OnPremisesHdfs](#linked-service-properties).
2. Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstup [datovou sadu](data-factory-create-datasets.md) typu [sdílení souborů](#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [FileSystemSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data z místní službě HDFS do objektu blob Azure každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

Jako první krok nastavte Brána pro správu dat. Podle pokynů [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku.

**Propojená služba HDFS:** tento příklad používá ověřování systému Windows. V tématu [HDFS propojená služba](#linked-service-properties) části pro různé typy ověřování můžete použít.

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

**HDFS vstupní datovou sadu:** tato datová sada odkazuje na složku HDFS DataTransfer/UnitTest /. Kanál zkopíruje všechny soubory v této složce do cílového umístění.

Nastavení "externí": "PRAVDA" informuje služba Data Factory, datová sada je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

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

**Azure Blob výstupní datovou sadu:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Cesta ke složce pro tento objekt blob je vyhodnocován dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc, den a čas částí čas spuštění.

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

**Aktivita kopírování v kanálu pomocí systému souborů zdrojový a podřízený objekt Blob:**

Kanál obsahuje aktivitu kopírování, která je konfigurovaná pro používání těchto vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **FileSystemSource** a **podřízený** je typ nastaven na **BlobSink**. Zadané pro dotaz SQL **dotazu** vlastnost vybere data za poslední hodinu pro kopírování.

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

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Ověřování pomocí protokolu Kerberos pro HDFS konektor
Existují dvě možnosti nastavit v místním prostředí tak, aby používala ověřování protokolu Kerberos v konektoru HDFS. Je možné, že byl lépe vyhovuje váš případ.
* Možnost 1: [připojení počítače brány ve sféře Kerberos](#kerberos-join-realm)
* Možnost 2: [povolit vzájemné vztah důvěryhodnosti mezi doménou systému Windows a Sféra Kerberos](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Možnost 1: Připojení počítače brány ve sféře Kerberos

#### <a name="requirement"></a>Požadavek:

* Počítač brány je potřeba připojit Sféra Kerberos a nemůže připojit k žádné doméně systému Windows.

#### <a name="how-to-configure"></a>Postup konfigurace:

**Na počítači brány:**

1.  Spustit **Ksetup** nástroj Konfigurace serveru protokolu Kerberos služby KDC a sféru.

    Počítač musí být nakonfigurován jako člena pracovní skupiny, protože Sféra Kerberos se liší od domény systému Windows. Toho lze dosáhnout nastavením Sféra Kerberos a takto přidejte server služby KDC. Nahraďte *REALM.COM* s vlastními příslušných sféry podle potřeby.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Restartujte** počítače po provedení těchto příkazů 2.

2.  Ověření konfigurace s **Ksetup** příkaz. Výstup by měl vypadat podobně jako:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**V Azure Data Factory:**

* Konfigurovat pomocí konektoru HDFS **ověřování systému Windows** společně s hlavní název protokolu Kerberos a heslo pro připojení ke zdroji dat HDFS. Zkontrolujte [vlastnosti propojené služby HDFS](#linked-service-properties) části na podrobnosti o konfiguraci.

### <a name="kerberos-mutual-trust"></a>Možnost 2: Povolení vzájemné vztah důvěryhodnosti mezi doménou systému Windows a Sféra Kerberos

#### <a name="requirement"></a>Požadavek:
*   Počítač brány musí připojit k doméně systému Windows.
*   Budete potřebovat oprávnění k aktualizaci nastavení řadiče domény.

#### <a name="how-to-configure"></a>Postup konfigurace:

> [!NOTE]
> Nahraďte REALM.COM a AD.COM v následujícím kurzu vlastní příslušných sféry a řadič domény, podle potřeby.

**Na serveru služby KDC:**

1.  Upravit konfiguraci služby KDC v **krb5.conf** souboru chcete, aby služba KDC vztahu důvěryhodnosti domény systému Windows na následující konfigurace šablony. Ve výchozím nastavení nachází ve **/etc/krb5.conf**.

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

3.  V **hadoop.security.auth_to_local** konfigurace služby HDFS souboru, přidejte `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`.

**Na řadiči domény:**

1.  Spusťte následující **Ksetup** přidejte položku sféry:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Navázání vztahu důvěryhodnosti z domény systému Windows k Sféra Kerberos. [heslo] je heslo pro objekt **krbtgt/REALM.COM@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Vyberte šifrovací algoritmus použitý v protokolu Kerberos.

    1. Přejděte do správce serveru > Správa zásad skupiny > domény > objekty zásad skupiny > výchozí nebo zásady služby Active Domain a upravit.

    2. V **Editor správy zásad skupiny** automaticky otevíraném okně, přejděte na konfigurace počítače > zásady > Nastavení systému Windows > Nastavení zabezpečení > Místní zásady > Možnosti zabezpečení a nakonfigurujte **zabezpečení sítě: typy konfigurace šifrování povolené pro protokol Kerberos**.

    3. Vyberte algoritmus šifrování, kterou chcete použít při připojení k služby KDC. Běžně můžete jednoduše vybrat všechny možnosti.

        ![Typy šifrování konfigurace pro protokol Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. Použití **Ksetup** příkazu zadejte šifrovací algoritmus, který se má použít na konkrétní SFÉRY.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Vytvoření mapování mezi účet domény a objektu zabezpečení pomocí protokolu Kerberos, aby bylo možné používat objekt zabezpečení protokolu Kerberos v doméně systému Windows.

    1. Spuštění nástroje pro správu > **Active Directory Users and Computers**.

    2. Konfigurace rozšířených funkcí kliknutím **zobrazení** > **pokročilé funkce**.

    3. Najděte účet, ke které chcete vytvořit mapování a klikněte pravým tlačítkem myši zobrazíte **mapování názvů** > klikněte na tlačítko **Kerberos názvy** kartě.

    4. Přidáte objekt z sféru.

        ![Mapování Identity zabezpečení](media/data-factory-hdfs-connector/map-security-identity.png)

**Na počítači brány:**

* Spusťte následující **Ksetup** přidejte položku sféry.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**V Azure Data Factory:**

* Konfigurovat pomocí konektoru HDFS **ověřování systému Windows** společně s buď váš účet domény nebo objekt zabezpečení protokolu Kerberos pro připojení ke zdroji dat HDFS. Zkontrolujte [vlastnosti propojené služby HDFS](#linked-service-properties) části na podrobnosti o konfiguraci.

> [!NOTE]
> Mapování sloupců z datové sady zdroje na sloupce ze sady jímku dat naleznete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).


## <a name="performance-and-tuning"></a>Výkon a ladění
V tématu [výkonu kopie aktivity & ladění průvodce](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů, že dopad výkon přesun dat (aktivita kopírování) v Azure Data Factory a různé způsoby, jak optimalizovat ho.
