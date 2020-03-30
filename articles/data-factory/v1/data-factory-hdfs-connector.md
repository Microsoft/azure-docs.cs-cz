---
title: Přesunutí dat z místního HDFS
description: Přečtěte si, jak přesunout data z místního HDFS pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7652ab72fb972230d98913c2d7e2601737982532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74924354"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Přesunutí dat z místního HDFS pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-hdfs-connector.md)
> * [Verze 2 (aktuální verze)](../connector-hdfs.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si [téma HDFS konektor ve verzi 2](../connector-hdfs.md).

Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunutí dat z místníhdfs. Vychází z článku [Aktivity přesunu dat,](data-factory-data-movement-activities.md) který představuje obecný přehled přesunu dat s aktivitou kopírování.

Můžete zkopírovat data z HDFS do libovolného úložiště dat podporovaných jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako jímky naleznete v tabulce [Podporovaná úložiště dat.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Data Factory v současné době podporuje pouze přesunutí dat z místní HDFS do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do místníhdfs.

> [!NOTE]
> Aktivita kopírování neodstraní zdrojový soubor po úspěšném zkopírování do cílového umístění. Pokud potřebujete po úspěšné kopii odstranit zdrojový soubor, vytvořte vlastní aktivitu, která soubor odstraní a použije aktivitu v kanálu. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Povolení připojení
Služba Data Factory podporuje připojení k místnímu HDFS pomocí brány pro správu dat. Podívejte [se na pohybující se data mezi místními lokacemi a cloudovým](data-factory-move-data-between-onprem-and-cloud.md) článkem, kde najdete informace o bráně pro správu dat a podrobné pokyny k nastavení brány. Pomocí brány se můžete připojit k HDFS, i když je hostovaná ve virtuálním počítači Azure IaaS.

> [!NOTE]
> Ujistěte se, že brána pro správu dat má přístup ke **všem** [název uzlu serveru]:[název uzlu port] a [servery datových uzlů]:[port datového uzlu] clusteru Hadoop. Výchozí [port uzlu názvu] je 50070 a výchozí [datový uzel] je 50075.

Zatímco bránu můžete nainstalovat do stejného místního počítače nebo virtuálního počítače Azure jako HDFS, doporučujeme nainstalovat bránu do samostatného počítače/virtuálního počítače Azure IaaS. S brána na samostatném počítači snižuje konflikty prostředků a zlepšuje výkon. Při instalaci brány na samostatném počítači by měl být počítač schopen přistupovat k počítači pomocí hdfs.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data ze zdroje HDFS pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Viz [Kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) pro rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat.

K vytvoření kanálu můžete taky použít následující nástroje: **Portál Azure**, **Visual Studio**, Azure **PowerShell**, **Šablona Azure Resource Manager**, Rozhraní API **.NET**a **ROZHRANÍ REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aktivity.

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat.
2. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup.

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory (propojené služby, datové sady a kanál). Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON.  Ukázka s definicemi JSON pro entity Data Factory, které se používají ke kopírování dat z úložiště dat HDFS, najdete v [tématu JSON příklad: Kopírování dat z místníhdfs do Azure Blob](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) části tohoto článku.

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Factory dat specifických pro HDFS:

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb
Propojená služba propojuje úložiště dat s továrnou na data. Vytvoříte propojenou službu typu **HDFS** pro propojení místníhdfs s vaší datové továrny. V následující tabulce je uveden popis prvků JSON specifických pro propojenou službu HDFS.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavena na: **Hdfs** |Ano |
| url |Adresa URL systému HDFS |Ano |
| authenticationType |Anonymní, nebo Windows. <br><br> Chcete-li použít **ověřování protokolem Kerberos** pro konektor HDFS, v [této části](#use-kerberos-authentication-for-hdfs-connector) nastavte místní prostředí odpovídajícím způsobem. |Ano |
| userName |Uživatelské jméno pro ověřování systému Windows. Pro ověřování protokolem `<username>@<domain>.com`Kerberos zadejte . |Ano (pro ověřování systému Windows) |
| heslo |Heslo pro ověřování systému Windows. |Ano (pro ověřování systému Windows) |
| název brány |Název brány, kterou by měla služba Data Factory použít pro připojení k hdfs. |Ano |
| šifrované pověření |[New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) výstup přístupového pověření. |Ne |

### <a name="using-anonymous-authentication"></a>Použití anonymního ověřování

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

### <a name="using-windows-authentication"></a>Použití ověřování systému Windows

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
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datových sad, naleznete v článku [Vytváření datových sad.](data-factory-create-datasets.md) Oddíly, jako je struktura, dostupnost a zásady datové sady JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure blob, Tabulka Azure atd.).

Sekce **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Sekce typeProperties pro datovou sadu typu **FileShare** (která zahrnuje datovou sadu HDFS) má následující vlastnosti

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Cesta ke složce. Příklad: `myfolder`<br/><br/>Pro speciální znaky v řetězci použijte řídicí znak \ ' . Příklad: pro složku\podsložka zadejte podsložku složky\\\\a pro složku\\\\d:\samplefolder zadejte d: samplefolder.<br/><br/>Tuto vlastnost můžete kombinovat s **partitionBy** mít cesty ke složkám na základě počátečního a koncového data řezu. |Ano |
| fileName |Zadejte název souboru ve **složceCesta,** pokud chcete, aby tabulka odkazovala na určitý soubor ve složce. Pokud pro tuto vlastnost nezadáte žádnou hodnotu, tabulka odkazuje na všechny soubory ve složce.<br/><br/>Pokud není pro výstupní datovou sadu zadán název souboru file, bude název generovaného souboru v následujícím formátu: <br/><br/>`Data.<Guid>.txt`(například: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Ne |
| partitionedBy |partitionedBy lze použít k určení dynamické složkyPath, název souboru pro data časových řad. Příklad: folderPath parametrizovaný pro každou hodinu dat. |Ne |
| formát | Podporovány jsou následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte vlastnost **type** ve formátu na jednu z těchto hodnot. Další informace naleznete v části [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), Formát [Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), Formát [orků](data-factory-supported-file-and-compression-formats.md#orc-format)a [Formát parket.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Pokud chcete **kopírovat soubory jako -je** mezi obchody založenými na souborech (binární kopie), přeskočte oddíl formátu v definicích vstupní i výstupní datové sady. |Ne |
| komprese | Určete typ a úroveň komprese dat. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**a **ZipDeflate**. Podporované úrovně jsou: **Optimální** a **nejrychlejší**. Další informace najdete [v tématu Soubory a kompresní formáty v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

> [!NOTE]
> název souboru a fileFilter nelze použít současně.

### <a name="using-partionedby-property"></a>Použití partionedBy vlastnost
Jak je uvedeno v předchozí části, můžete zadat dynamickou složkuPath a název souboru pro data časových řad s **vlastností partitionedBy,** [funkcemi Data Factory a systémovými proměnnými](data-factory-functions-variables.md).

Další informace o datových sadách časových řad, plánování a řezech najdete [v tématech Vytváření datových sad](data-factory-create-datasets.md), [Plánování & spuštění](data-factory-scheduling-and-execution.md)a Vytváření článků [kanálů.](data-factory-create-pipelines.md)

#### <a name="sample-1"></a>Vzorek 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
V tomto příkladu {Slice} je nahrazen hodnotou data factory systémové proměnné SliceStart ve formátu (YYYYMMDDHH) zadaný. ŘezStart odkazuje na čas zahájení řezu. SložkaPath se liší pro každý řez. Například: wikidatagateway/wikisampledataout/2014100103 nebo wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Ukázka 2:

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
V tomto příkladu jsou rok, měsíc, den a čas SliceStart extrahovány do samostatných proměnných, které jsou používány vlastnostmi folderPath a fileName.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

Vzhledem k tomu, vlastnosti, které jsou k dispozici v typeProperties části aktivity se liší s každým typem aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a propadů.

V části Copy Activity jsou v části typeProperties k dispozici následující vlastnosti, pokud je zdroj typu **FileSystemSource:**

**FileSystemSource** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| Rekurzivní |Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. |Pravda, Nepravda (výchozí) |Ne |

## <a name="supported-file-and-compression-formats"></a>Podporované formáty souborů a komprese
V článku [Soubory a kompresní formáty v Azure Data Factory](data-factory-supported-file-and-compression-formats.md) najdete podrobnosti.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Příklad JSON: Kopírování dat z místního HDFS do objektu blob Azure
Tato ukázka ukazuje, jak zkopírovat data z místníHDFS do úložiště objektů blob Azure. Data však můžete zkopírovat **přímo** do některého z jímky [uvedené zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.  

Ukázka poskytuje definice JSON pro následující entity Data Factory. Pomocí těchto definic můžete vytvořit kanál ke kopírování dat z HDFS do azure blob storage pomocí [Visual Studia](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShellu](data-factory-copy-activity-tutorial-using-powershell.md).

1. Propojená služba typu [OnPremisesHdfs](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [FileShare](#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, který používá [FileSystemSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data z místníhdfs do objektu blob Azure každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

Jako první krok nastavte bránu pro správu dat. Pokyny v [přesunutí dat mezi místními umístěními a článkem cloudu.](data-factory-move-data-between-onprem-and-cloud.md)

**Služba propojená s HDFS:** Tento příklad používá ověřování systému Windows. Různé typy ověřování, které můžete použít, naleznete v části [propojené služby HDFS.](#linked-service-properties)

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

**Propojené služby Azure Storage:**

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

**Vstupní datová sada HDFS:** Tato datová sada odkazuje na složku HDFS DataTransfer/UnitTest/. Kanál zkopíruje všechny soubory v této složce do cíle.

Nastavení "externí": "true" informuje službu Data Factory, že datová sada je externí pro datové továrny a není vyráběna aktivitou v datové továrně.

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

**Výstupní datová sada objektu Blob Azure:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části počátečního času rok, měsíc, den a hodiny.

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

**Aktivita kopírování v kanálu se zdrojem systému souborů a jímkou blob:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití těchto vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **FileSystemSource** a typ **jímky** je nastaven na **Objekt BlobSink**. Dotaz SQL zadaný pro vlastnost **dotazu** vybere data za poslední hodinu ke kopírování.

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

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Použití ověřování protokolem Kerberos pro konektor HDFS
Existují dvě možnosti nastavení místního prostředí tak, aby bylo použití ověřování protokolem Kerberos v konektoru HDFS. Můžete si vybrat ten, který lépe vyhovuje vašemu případu.
* Možnost 1: [Připojení k počítači brány v oblasti Protokolu Kerberos](#kerberos-join-realm)
* Možnost 2: [Povolení vzájemné důvěryhodnosti mezi doménou systému Windows a sférou protokolu Kerberos](#kerberos-mutual-trust)

### <a name="option-1-join-gateway-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>Možnost 1: Připojení k počítači brány v oblasti Protokolu Kerberos

#### <a name="requirement"></a>Požadavek:

* Počítač brány se musí připojit k sabě Protokolu Kerberos a nemůže se připojit k žádné doméně systému Windows.

#### <a name="how-to-configure"></a>Jak konfigurovat:

**Na stroji brány:**

1.  Spusťte nástroj **Ksetup** a nakonfigurujte server a sféru protokolu Kerberos KDC.

    Počítač musí být nakonfigurován jako člen pracovní skupiny, protože sféra protokolu Kerberos se liší od domény systému Windows. Toho lze dosáhnout nastavením sféry Protokolu Kerberos a přidáním serveru KDC následujícím způsobem. Podle potřeby *REALM.COM* nahraďte vlastní sférou.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Restartujte** počítač po provedení těchto 2 příkazů.

2.  Ověřte konfiguraci pomocí příkazu **Ksetup.** Výstup by měl být jako:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Ve Službě Azure Data Factory:**

* Nakonfigurujte konektor HDFS pomocí **ověřování systému Windows** spolu s hlavním jménem a heslem protokolu Kerberos pro připojení ke zdroji dat HDFS. Podrobnosti o konfiguraci najdete v části [vlastnosti propojené služby HDFS.](#linked-service-properties)

### <a name="option-2-enable-mutual-trust-between-windows-domain-and-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Možnost 2: Povolení vzájemné důvěryhodnosti mezi doménou systému Windows a sférou protokolu Kerberos

#### <a name="requirement"></a>Požadavek:
*   Počítač brány se musí připojit k doméně systému Windows.
*   K aktualizaci nastavení řadiče domény potřebujete oprávnění.

#### <a name="how-to-configure"></a>Jak konfigurovat:

> [!NOTE]
> Podle potřeby nahraďte REALM.COM a AD.COM v následujícím kurzu vlastní sférou a řadičem domény.

**Na serveru KDC:**

1. Upravte konfiguraci KDC v souboru **krb5.conf,** aby kdc důvěřoval doméně systému Windows odkazující na následující konfigurační šablonu. Ve výchozím nastavení je konfigurace umístěna na **adrese /etc/krb5.conf**.

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

   Po konfiguraci **restartujte** službu KDC.

2. Připravte objekt zabezpečení s názvem **krbtgt/REALM.COM\@AD.COM** na serveru KDC pomocí následujícího příkazu:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. V souboru konfigurace služby **hadoop.security.auth_to_local** HDFS přidejte `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**Na řadiči domény:**

1.  Spusťte následující příkazy **Ksetup** a přidejte položku sféry:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Vytvořte vztah důvěryhodnosti z domény systému Windows do sféry protokolu Kerberos. [heslo] je heslo pro hlavní **krbtgt/REALM.COM\@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Vyberte šifrovací algoritmus používaný v protokolu Kerberos.

    1. Přejděte na Správce serveru > správa zásad skupiny > objekty zásad skupiny > domény > výchozí nebo aktivní zásady domény a upravit.

    2. V místním okně **Editor správy zásad skupiny** přejděte na položku Zásady konfigurace počítače > > Nastavení systému Windows > Nastavení zabezpečení > Místní zásady > možnosti zabezpečení a konfigurace **zabezpečení sítě: Konfigurace typů šifrování povolených pro protokol Kerberos**.

    3. Vyberte šifrovací algoritmus, který chcete použít při připojení k KDC. Obvykle můžete jednoduše vybrat všechny možnosti.

        ![Typy šifrování konfigurace pro protokol Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. Pomocí příkazu **Ksetup** určete šifrovací algoritmus, který se má použít v konkrétní skutečnosti REALM.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Vytvořte mapování mezi účtem domény a objektem zabezpečení protokolu Kerberos, abyste mohli použít objekt zabezpečení protokolu Kerberos v doméně systému Windows.

    1. Spusťte nástroje pro správu > **uživatelé a počítače služby Active Directory**.

    2. Nakonfigurujte pokročilé funkce klepnutím na tlačítko **Zobrazit** > **rozšířené funkce**.

    3. Vyhledejte účet, pro který chcete vytvořit mapování, a kliknutím pravým tlačítkem myši **zobrazte položku Mapování názvů** > klikněte na kartu **Názvy kerberos.**

    4. Přidejte objekt zabezpečení z říše.

        ![Identita zabezpečení mapy](media/data-factory-hdfs-connector/map-security-identity.png)

**Na stroji brány:**

* Spusťte následující příkazy **Ksetup** a přidejte položku sféry.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Ve Službě Azure Data Factory:**

* Nakonfigurujte konektor HDFS pomocí **ověřování systému Windows** společně s vaším účtem domény nebo objektem Kerberos Pro připojení ke zdroji dat HDFS. Podrobnosti o konfiguraci najdete v části [vlastnosti propojené služby HDFS.](#linked-service-properties)

> [!NOTE]
> Pokud chcete mapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, přečtěte si témat [mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).


## <a name="performance-and-tuning"></a>Výkon a ladění
[V tématu Průvodce sledováním výkonu & optimalizací se](data-factory-copy-activity-performance.md) dozvíte o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivita kopírování) ve Službě Azure Data Factory, a o různých způsobech jeho optimalizace.
