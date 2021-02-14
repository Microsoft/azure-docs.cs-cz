---
title: Přesunout data z místního HDFS
description: Přečtěte si, jak přesunout data z místního HDFS pomocí Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 52e176e0fed85b649d482614667d695db539e5d1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383071"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Přesunutí dat z místního HDFS pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-hdfs-connector.md)
> * [Verze 2 (aktuální verze)](../connector-hdfs.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [HDFS Connector ve verzi v2](../connector-hdfs.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory přesouvat data z místního HDFS. Sestavuje se podle článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který prezentuje obecný přehled přesunu dat s aktivitou kopírování.

Data ze HDFS můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která aktivita kopírování podporuje jako jímky, najdete v tabulce [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Data Factory aktuálně podporuje jenom přesun dat z místního HDFS do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do místního HDFS.

> [!NOTE]
> Aktivita kopírování neodstraní zdrojový soubor po úspěšném zkopírování do cíle. Pokud potřebujete zdrojový soubor po úspěšné kopii odstranit, vytvořte vlastní aktivitu k odstranění tohoto souboru a použijte aktivitu v kanálu. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Povolení připojení
Služba Data Factory podporuje připojení k místnímu HDFS pomocí brány Správa dat. Další informace o Správa dat bráně a podrobné pokyny k nastavení brány najdete v tématu [přesun dat mezi místními umístěními a v cloudovém](data-factory-move-data-between-onprem-and-cloud.md) článku. Bránu můžete použít pro připojení k HDFS i v případě, že je hostovaná na virtuálním počítači Azure s IaaS.

> [!NOTE]
> Ujistěte se, že Správa dat brána má přístup ke **všem** [názvový server Node]: [název uzlu port] a [servery datových uzlů]: [port datového uzlu] clusteru Hadoop. Výchozí [název uzlu port] je 50070 a výchozí [port datového uzlu] je 50075.

I když můžete nainstalovat bránu na stejný místní počítač nebo virtuální počítač Azure jako HDFS, doporučujeme nainstalovat bránu do samostatného počítače nebo virtuálního počítače Azure s IaaS. Brána na samostatném počítači snižuje spory prostředků a zvyšuje výkon. Když bránu instalujete do samostatného počítače, počítač by měl mít přístup k počítači se službou HDFS.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data ze zdroje HDFS pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) .

K vytvoření kanálu můžete také použít následující nástroje: **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager Template**, **.NET API** a **REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou.
2. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup.

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje/rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON.  Ukázku s definicemi JSON pro Data Factory entity, které se používají ke kopírování dat z úložiště dat HDFS, najdete v části [příklad JSON: kopírování dat z místního HDFS do Azure Blob](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) tohoto článku.

Následující části obsahují podrobné informace o vlastnostech JSON, které se používají k definování Data Factory entit specifických pro HDFS:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Propojená služba propojuje úložiště dat s datovou továrnou. Vytvoříte propojenou službu typu **HDFS** a propojíte místní HDFS s vaší datovou továrnou. Následující tabulka uvádí popis pro prvky JSON specifické pro propojenou službu HDFS.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ |Vlastnost Type musí být nastavená na: **HDFS** . |Yes |
| url |Adresa URL k HDFS |Yes |
| authenticationType |Anonymní nebo Windows. <br><br> Pokud chcete pro konektor HDFS použít **ověřování pomocí protokolu Kerberos** , v [této části](#use-kerberos-authentication-for-hdfs-connector) si odpovídajícím způsobem nastavte místní prostředí. |Yes |
| userName |Uživatelské jméno pro ověřování systému Windows. Pro ověřování protokolem Kerberos zadejte `<username>@<domain>.com` . |Ano (pro ověřování systému Windows) |
| heslo |Heslo pro ověřování systému Windows. |Ano (pro ověřování systému Windows) |
| gatewayName |Název brány, kterou by služba Data Factory měla použít pro připojení ke HDFS. |Yes |
| encryptedCredential |Výstup [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) přihlašovacích údajů pro přístup |No |

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
Úplný seznam sekcí & vlastností dostupných pro definování datových sad naleznete v článku [vytvoření datových sad](data-factory-create-datasets.md) . Oddíly, jako je například struktura, dostupnost a zásada pro datovou sadu JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure Blob, tabulka Azure atd.).

Oddíl **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl typeProperties pro datovou sadu typu **Shared** (zahrnující datovou sadu HDFS) má následující vlastnosti.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| folderPath |Cesta ke složce Příklad: `myfolder`<br/><br/>Pro speciální znaky v řetězci použijte řídicí znak ' \ '. Například: pro folder\subfolder, zadejte podsložek složky \\ \\ a pro d:\samplefolder zadejte d: \\ \\ samplefolder.<br/><br/>Tuto vlastnost můžete kombinovat s **partitionBy** a mít tak cesty ke složkám na základě data a času začátku a konce řezu. |Yes |
| fileName |Pokud chcete, aby tabulka odkazovala na konkrétní soubor ve složce, zadejte název souboru do **FolderPath** . Pokud pro tuto vlastnost nezadáte žádnou hodnotu, odkazuje tabulka na všechny soubory ve složce.<br/><br/>Pokud pro výstupní datovou sadu není zadán název souboru, bude název vygenerovaného souboru v následujícím formátu: <br/><br/>`Data.<Guid>.txt` (například:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |partitionedBy lze použít k zadání dynamického názvu souboru folderPath pro data časové řady. Příklad: folderPath parametrizované pro každou hodinu dat. |No |
| formát | Podporovány jsou následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. V části formát nastavte vlastnost **typ** na jednu z těchto hodnot. Další informace najdete v částech [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), [formát JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formát ORC](data-factory-supported-file-and-compression-formats.md#orc-format)a formátování [Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Pokud chcete **Kopírovat soubory** mezi úložišti na základě souborů (binární kopie), přeskočte oddíl formát v definicích vstupní i výstupní datové sady. |No |
| komprese | Zadejte typ a úroveň komprese dat. Podporované typy jsou: **gzip**, **Deflate**, **bzip2** a **ZipDeflate**. Podporované úrovně: **optimální** a **nejrychlejší**. Další informace naleznete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

> [!NOTE]
> filename a FileFilter nelze použít současně.

### <a name="using-partionedby-property"></a>Použití vlastnosti partionedBy
Jak je uvedeno v předchozí části, můžete zadat dynamický folderPath a filename pro data časových řad pomocí vlastnosti **partitionedBy** , [Data Factory funkcí a systémových proměnných](data-factory-functions-variables.md).

Další informace o datových sadách, plánování a řezech časových řad najdete v článcích [vytváření datových sad](data-factory-create-datasets.md), [plánování & provádění](data-factory-scheduling-and-execution.md)a [vytváření kanálů](data-factory-create-pipelines.md) .

#### <a name="sample-1"></a>Ukázka 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
V tomto příkladu {Slice} se nahradí hodnotou Data Factory systémové proměnné vlastnosti slicestart v zadaném formátu (YYYYMMDDHH). Vlastnosti slicestart odkazuje na počáteční čas řezu. FolderPath se u každého řezu liší. Příklad: wikidatagateway/wikisampledataout/2014100103 nebo wikidatagateway/wikisampledataout/2014100104.

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
V tomto příkladu jsou extrahovány roky, měsíc, den a čas vlastnosti slicestart do samostatných proměnných, které jsou používány vlastnostmi folderPath a fileName.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam sekcí & vlastností dostupných pro definování aktivit najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . Pro všechny typy aktivit jsou k dispozici vlastnosti, jako je název, popis, vstupní a výstupní tabulka a zásady.

V takovém případě se vlastnosti dostupné v části typeProperties v aktivitě liší podle typu aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a jímky.

Pro aktivitu kopírování, pokud je zdroj typu **FileSystemSource** , jsou v oddílu typeProperties k dispozici následující vlastnosti:

**FileSystemSource** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| zahrnout |Určuje, zda mají být data rekurzivně čtena z dílčích složek nebo pouze ze zadané složky. |True, false (výchozí) |No |

## <a name="supported-file-and-compression-formats"></a>Podporované formáty souborů a komprese
Podrobnosti najdete v článku o [formátech souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md) .

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Příklad JSON: kopírování dat z místního HDFS do Azure Blob
V této ukázce se dozvíte, jak kopírovat data z místního HDFS do Azure Blob Storage. Data se ale dají zkopírovat **přímo** do kterékoli z těchto umyvadel, které jsou [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) uvedené, pomocí aktivity kopírování v Azure Data Factory.  

Ukázka poskytuje definice JSON pro následující Data Factory entity. Tyto definice můžete použít k vytvoření kanálu ke kopírování dat z HDFS do Azure Blob Storage pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

1. Propojená služba typu [OnPremisesHdfs](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [sdílení](#dataset-properties)souborů.
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [FileSystemSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka kopíruje data z místního HDFS do objektu blob Azure každou hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

Jako první krok nastavte bránu pro správu dat. Pokyny v tématu [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md) .

**Propojená služba HDFS:** V tomto příkladu se používá ověřování systému Windows. Různé typy ověřování, které můžete použít, najdete v části [HDFS propojená služba](#linked-service-properties) .

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

**Vstupní datová sada HDFS:** Tato datová sada odkazuje na složku HDFS DataTransfer/UnitTest/. Kanál zkopíruje všechny soubory v této složce do cílového umístění.

Nastavení "externí": "true" informuje službu Data Factory o tom, že datová sada je externí pro objekt pro vytváření dat, a není vytvořena aktivitou v datové továrně.

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

Data se zapisují do nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt BLOB je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části rok, měsíc, den a hodiny v počátečním čase.

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

**Aktivita kopírování v kanálu se zdrojem systému souborů a jímkou objektů BLOB:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala tyto vstupní a výstupní datové sady a má naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **FileSystemSource** a typ **jímky** je nastavený na **BlobSink**. Dotaz SQL zadaný pro vlastnost **dotazu** vybere data během uplynulé hodiny ke zkopírování.

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
Existují dvě možnosti nastavení místního prostředí, aby se používalo ověřování pomocí protokolu Kerberos v konektoru HDFS. Můžete si vybrat, který lépe vyhovuje vašemu případu.
* Možnost 1: [připojení počítače brány ve sféře protokolu Kerberos](#kerberos-join-realm)
* Možnost 2: [Povolení vzájemné důvěry mezi doménou systému Windows a sférou protokolu Kerberos](#kerberos-mutual-trust)

### <a name="option-1-join-gateway-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>Možnost 1: připojení počítače brány ve sféře protokolu Kerberos

#### <a name="requirement"></a>Požadavek

* Počítač brány se musí připojit k sféře protokolu Kerberos a nemůže se připojit k žádné doméně systému Windows.

#### <a name="how-to-configure"></a>Postup konfigurace:

**Na počítači brány:**

1. Spusťte nástroj **Ksetup** a nakonfigurujte server a sféru protokolu Kerberos.

   Počítač musí být nakonfigurován jako člen pracovní skupiny, protože se liší sféra protokolu Kerberos od domény systému Windows. Toho je možné dosáhnout nastavením sféry protokolu Kerberos a přidáním serveru KDC následujícím způsobem. V případě potřeby nahraďte *REALM.com* vlastním odpovídajícím sférou.

   ```cmd
   Ksetup /setdomain REALM.COM
   Ksetup /addkdc REALM.COM <your_kdc_server_address>
   ```

   Po provedení těchto dvou příkazů **restartujte** počítač.

2. Ověřte konfiguraci pomocí příkazu **Ksetup** . Výstup by měl vypadat takto:

   ```cmd
   Ksetup
   default realm = REALM.COM (external)
   REALM.com:
      kdc = <your_kdc_server_address>
   ```

**V Azure Data Factory:**

* Nakonfigurujte konektor HDFS pomocí **ověřování systému Windows** společně s vaším hlavním jménem a heslem protokolu Kerberos pro připojení ke zdroji dat HDFS. Podrobnosti o konfiguraci najdete v části [Vlastnosti propojené služby HDFS](#linked-service-properties) .

### <a name="option-2-enable-mutual-trust-between-windows-domain-and-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Možnost 2: povolení vzájemné důvěry mezi doménou systému Windows a sférou protokolu Kerberos

#### <a name="requirement"></a>Požadavek
*    Počítač brány se musí připojit k doméně systému Windows.
*    K aktualizaci nastavení řadiče domény potřebujete oprávnění.

#### <a name="how-to-configure"></a>Postup konfigurace:

> [!NOTE]
> V následujícím kurzu nahraďte REALM.COM a AD.COM podle potřeby vlastní odpovídající sférou a řadičem domény.

**Na serveru KDC:**

1. Upravte konfiguraci služby KDC v souboru **krb5. conf** , aby se doména Windows vztahu důvěryhodnosti služby KDC odkazovala na následující šablonu konfigurace. Ve výchozím nastavení se konfigurace nachází na adrese **/etc/krb5.conf**.

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

   Po dokončení konfigurace **restartujte** službu KDC.

2. Připravte objekt zabezpečení s názvem **KRBTGT/REALM. COM \@ AD.com** na serveru KDC pomocí následujícího příkazu:

   ```cmd
   Kadmin> addprinc krbtgt/REALM.COM@AD.COM
   ```

3. Do konfiguračního souboru služby **Hadoop.Security.auth_to_local** HDFS přidejte `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//` .

**Na řadiči domény:**

1. Chcete-li přidat položku sféry, spusťte následující příkazy **Ksetup** :

   ```cmd
   Ksetup /addkdc REALM.COM <your_kdc_server_address>
   ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

2. Vytvořte vztah důvěryhodnosti mezi doménou systému Windows a sférou protokolu Kerberos. [heslo] je heslo pro objekt zabezpečení **KRBTGT/REALM. COM \@ AD.com**.

   ```cmd
   netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]
   ```

3. Vyberte šifrovací algoritmus používaný v protokolu Kerberos.

   1. Přejít na Správce serveru > Zásady skupiny Správa > doméně > zásady skupiny objekty > výchozí nebo aktivní zásady domény a upravit.

   2. V místním okně **Editor pro správu zásad skupiny** přejít na konfigurace počítače > zásady > nastavení systému Windows > nastavení zabezpečení > místní zásady > možnosti zabezpečení a konfigurace **zabezpečení sítě: Konfigurace typů šifrování povolených pro protokol Kerberos**.

   3. Vyberte šifrovací algoritmus, který chcete použít pro připojení ke službě KDC. Obvykle můžete jednoduše vybrat všechny možnosti.

      ![Typy šifrování konfigurace pro Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

   4. Pomocí příkazu **Ksetup** určete šifrovací algoritmus, který se má použít pro konkrétní sféru.

      ```cmd
      ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96
      ```

4. Vytvořte mapování mezi účtem domény a hlavním objektem Kerberos, aby bylo možné v doméně systému Windows použít zabezpečení Kerberos.

   1. Spusťte nástroje pro správu > **Uživatelé a počítače služby Active Directory**.

   2. Nakonfigurujete pokročilé funkce kliknutím na **Zobrazit**  >  **Pokročilé funkce**.

   3. Vyhledejte účet, pro který chcete vytvořit mapování, a kliknutím pravým tlačítkem zobrazte **mapování názvů** > klikněte na kartu **názvy protokolu Kerberos** .

   4. Přidejte objekt zabezpečení ze sféry.

      ![Mapovat identitu zabezpečení](media/data-factory-hdfs-connector/map-security-identity.png)

**Na počítači brány:**

* Spuštěním následujících příkazů **Ksetup** přidejte položku sféry.

   ```cmd
   Ksetup /addkdc REALM.COM <your_kdc_server_address>
   ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

**V Azure Data Factory:**

* Nakonfigurujte konektor HDFS pomocí **ověřování systému Windows** společně s účtem domény nebo objektem Kerberos pro připojení ke zdroji dat HDFS. Podrobnosti o konfiguraci najdete v části [Vlastnosti propojené služby HDFS](#linked-service-properties) .

> [!NOTE]
> Chcete-li mapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, přečtěte si téma [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Další informace o klíčových faktorech, které mají vliv na výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jejich optimalizace, najdete v tématu [Průvodce optimalizací aktivity kopírování &](data-factory-copy-activity-performance.md) .
