---
title: Kopírování dat do/z Azure Blob Storage | Microsoft Docs
description: 'Zjistěte, jak zkopírovat data objektů blob v Azure Data Factory. Použijte naše ukázka: kopírování dat do a z Azure Blob Storage a Azure SQL Database.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ec3c87c39cd8b8d1dafa8ad062776171c602135b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37047056"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Kopírovat data do nebo z Azure Blob Storage pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-azure-blob-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-blob-storage.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [konektor Azure Blob Storage v V2](../connector-azure-blob-storage.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat do a z Azure Blob Storage. Vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který představuje obecný přehled přesun dat s aktivitou kopírování.

## <a name="overview"></a>Přehled
Data můžete zkopírovat z jakékoli podporované zdrojové úložiště dat do úložiště objektů Blob Azure nebo z úložiště objektů Blob Azure do úložiště dat žádné podporované jímky. Následující tabulka obsahuje seznam úložiště dat, které jsou podporované jako zdroje nebo jímky pomocí aktivity kopírování. Například můžete přesunout data **z** databázi systému SQL Server nebo Azure SQL database **k** Azure blob storage. A může kopírovat data **z** úložiště objektů blob Azure **k** Azure SQL Data Warehouse nebo kolekci Azure Cosmos DB. 

## <a name="supported-scenarios"></a>Podporované scénáře
Může kopírovat data **z Azure Blob Storage** ukládá do následující data:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Může kopírovat data z následujících datových úložišť **do úložiště objektů Blob Azure**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]
 
> [!IMPORTANT]
> Aktivita kopírování podporuje kopírování dat z/do úložiště objektů Blob v horké nebo nástrojů i pro obecné účely účty Azure Storage. Aktivita podporuje **čtení z bloku, připojte, nebo objekty BLOB stránky**, ale podporuje **zápis do pouze objekty BLOB bloků**. Azure Storage úrovně Premium není podporován jako jímka, protože je zálohovaný díky objekty BLOB stránky.
> 
> Aktivita kopírování nedojde k odstranění dat ze zdroje po dat byl úspěšně zkopírován do cílové. Pokud potřebujete odstranit zdroj dat po úspěšné kopie, vytvořte [vlastní aktivity](data-factory-use-custom-activities.md) k odstranění dat a použijte aktivitu v kanálu. Příklad, naleznete v části [odstranění objektů blob nebo složky ukázce na Githubu](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity). 

## <a name="get-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z Azure Blob Storage pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním**. Tento článek má [návod](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) pro vytvoření kanálu pro zkopírování dat z umístění úložiště objektů Blob Azure do jiného umístění úložiště objektů Blob Azure. Kurz týkající se vytváření kanál ke zkopírování dat z Azure Blob Storage do Azure SQL Database, najdete v části [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru** , **.NET API**, a **rozhraní REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování.

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený:

1. Vytvoření **objekt pro vytváření dat**. Objekt pro vytváření dat může obsahovat jeden nebo víc kanálů. 
2. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory. Pokud jsou kopírování dat z Azure blob storage do Azure SQL database, například vytvoříte dvě propojené služby pro vytváření dat. propojení účtu úložiště Azure a Azure SQL database. Vlastnosti propojené služby, které jsou specifické pro Azure Blob Storage, najdete v části [propojené vlastnosti služby](#linked-service-properties) části. 
2. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování. V příkladu uvedených v posledním kroku vytvoříte datové sady a zadat kontejner objektů blob a složky, která obsahuje vstupní data. A vytvořte jinou datovou sadu, která zadejte tabulku SQL ve službě Azure SQL database, který obsahuje data zkopírovat z úložiště objektů blob. Vlastnosti datové sady, které jsou specifické pro Azure Blob Storage, najdete v části [vlastnosti datové sady](#dataset-properties) části.
3. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. V příkladu již bylo zmíněno dříve použijete BlobSource jako zdroj a SqlSink jako jímku pro aktivitu kopírování. Podobně pokud kopírujete z databáze SQL Azure do Azure Blob Storage, můžete použít SqlSource a BlobSink v aktivitě kopírování. Kopírovat vlastnosti aktivity, které jsou specifické pro Azure Blob Storage, najdete v části [zkopírovat vlastnosti aktivity](#copy-activity-properties) části. Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímka klikněte na odkaz v předchozí části pro data store.  

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Při použití nástroje nebo rozhraní API (s výjimkou .NET API), definujete tyto entity služby Data Factory pomocí formátu JSON.  Ukázky s definicemi JSON entit služby Data Factory, které se používají ke zkopírování dat do/z Azure Blob Storage, najdete v části [JSON příklady](#json-examples-for-copying-data-to-and-from-blob-storage  ) tohoto článku.

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které slouží k určení entit služby Data Factory konkrétní do úložiště objektů Blob Azure.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Existují dva typy propojené služby, které můžete použít k propojení Azure Storage do Azure data factory. Jsou: **azurestorage** propojená služba a **AzureStorageSas** propojené služby. Propojenou službu úložiště Azure poskytuje objekt pro vytváření dat s globálním přístupem k úložišti Azure. Zatímco úložiště SAS Azure (sdíleného přístupového podpisu) propojená služba poskytuje objekt pro vytváření dat s přístupem omezený nebo časově vázaných do úložiště Azure. Nejsou žádné další rozdíly mezi tyto dvě propojené služby. Zvolte propojené služby, která vyhovuje vašim potřebám. Následující části obsahují další podrobnosti na tyto dvě propojené služby.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Vlastnosti datové sady
Pokud chcete zadat datové sady představují vstupních nebo výstupních dat v Azure Blob Storage, nastavte vlastnost type datové sady, která: **AzureBlob**. Nastavte **linkedServiceName** vlastnosti datové sady, která název Azure Storage nebo Azure úložiště SAS propojené služby.  Zadejte typ vlastnosti datové sady **kontejner objektů blob** a **složky** ve službě blob storage.

Úplný seznam části JSON & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu (Azure SQL Azure blob, tabulky Azure, atd.).

Objekt pro vytváření dat podporuje následující hodnoty typu kompatibilní se specifikací CLS .NET na základě poskytnutí informací o typu "struktury" zdroje dat schématu na čtení jako objekt blob systému Azure: Int16, Int32, Int64, jeden, Double, Decimal, Byte [], Bool, String, Guid, Datetime, DateTimeOffset, Timespan. Objekt pro vytváření dat automaticky provede převody typů, při přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky.

**Rámci typeProperties** oddílu se liší pro jednotlivé typy datovou sadu a informace o umístění, formátování atd, dat v úložišti. Rámci typeProperties část datové sady typ **AzureBlob** datová sada má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Cesta ke kontejneru a složce v úložišti objektů blob. Příklad: myblobcontainer\myblobfolder\ |Ano |
| fileName |Název objektu blob. Název souboru je volitelné a velká a malá písmena.<br/><br/>Pokud zadáte název souboru, na konkrétní objekt Blob funguje aktivitu (včetně kopie).<br/><br/>Pokud není zadán název souboru, zahrnuje kopírování všech objektů BLOB v folderPath pro vstupní datové sady.<br/><br/>Když **fileName** pro datovou sadu výstupů není zadána a **preserveHierarchy** není zadané v aktivity podřízený název vygenerovaný soubor bude v následujícím tento formát: Data<Guid>. TXT (například: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Ne |
| partitionedBy |partitionedBy vlastnost je volitelná. Můžete ji k určení dynamické folderPath a název souboru pro data časové řady. Například folderPath lze nastavit parametry pro každou hodinu data. Najdete v článku [pomocí části vlastnost partitionedBy](#using-partitionedBy-property) podrobnosti a příklady. |Ne |
| Formát | Jsou podporovány následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Nastavte **typ** vlastnost pod formát na jednu z těchto hodnot. Další informace najdete v tématu [textovém formátu](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátu](data-factory-supported-file-and-compression-formats.md#orc-format), a [Parquet formát](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete **zkopírujte soubory jako-je** mezi souborové úložiště (binární kopie), přeskočte část formátu v obou definice vstupní a výstupní datové sady. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

### <a name="using-partitionedby-property"></a>Pomocí vlastnost partitionedBy
Jak je uvedeno v předchozí části, můžete zadat dynamické folderPath a název souboru pro data časové řady s **partitionedBy** vlastnost [funkce pro vytváření dat a systémové proměnné](data-factory-functions-variables.md).

Další informace o datové sady času řady, plánování a řezy najdete v tématu [vytváření datových sad](data-factory-create-datasets.md) a [plánování a provádění](data-factory-scheduling-and-execution.md) články.

#### <a name="sample-1"></a>Ukázka 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

V tomto příkladu {řez} se nahradí hodnotu objektu pro vytváření dat systému proměnné SliceStart ve formátu (YYYYMMDDHH) zadán. Vlastnosti SliceStart odkazuje na spuštění řezu. FolderPath se liší pro každý řez. Příklad: wikidatagateway/wikisampledataout/2014100103 nebo wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>Ukázka 2

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
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, například název, popis, vstupní a výstupní datové sady a zásad jsou dostupné pro všechny typy aktivit. Vzhledem k tomu, vlastnosti dostupné ve **rámci typeProperties** části aktivity se liší podle každý typ aktivity. Pro aktivitu kopírování budou lišit v závislosti na typech zdrojů a jímky. Pokud přesouváte data z Azure Blob Storage, nastavíte typ zdroje v aktivitě kopírování do **BlobSource**. Podobně pokud přesouváte data do Azure Blob Storage, nastavíte typ jímky v aktivitě kopírování do **BlobSink**. Tato část obsahuje seznam vlastností, které jsou podporované BlobSource a BlobSink.

**BlobSource** podporuje následující vlastnosti v **rámci typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| rekurzivní |Označuje, zda je data načíst rekurzivně z dílčí složky nebo pouze do zadané složky. |True (výchozí hodnota), False. |Ne |

**BlobSink** podporuje následující vlastnosti **rámci typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| copyBehavior |Definuje chování kopie, pokud je zdroj BlobSource nebo systému souborů. |<b>PreserveHierarchy</b>: zachovává hierarchii souborů v cílové složce. Relativní cesta zdrojového souboru do zdrojové složky je stejný jako relativní cestu k souboru cíl k cílové složce.<br/><br/><b>FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úroveň cílové složce. Cílové soubory mít název automaticky generovány. <br/><br/><b>MergeFiles</b>: sloučí všechny soubory ze zdrojové složky pro jeden soubor. Pokud je zadán název souboru nebo objekt Blob, název souboru sloučené by být zadaný název; jinak by automaticky generovaný soubor název. |Ne |

**BlobSource** také podporuje tyto dvě vlastnosti z důvodu zpětné kompatibility.

* **treatEmptyAsNull**: Určuje, jestli má hodnotu null nebo prázdný řetězec považovat za hodnotu null.
* **skipHeaderLineCount** -Určuje, kolik řádků potřebovat přeskočen. Vztahuje se pouze pokud vstupní datové sady používá TextFormat.

Podobně **BlobSink** podporuje následující vlastnost z důvodu zpětné kompatibility.

* **blobWriterAddHeader**: Určuje, zda chcete přidat hlavičku definice sloupců při zápisu do výstupní datové sady.

Datové sady teď podporují následující vlastnosti, které implementují stejnou funkci: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

Následující tabulka obsahuje informace o použití nové vlastnosti datové sady místo tyto vlastnosti Zdroj/jímka objektů blob.

| Vlastnost aktivity kopírování | Vlastnost DataSet |
|:--- |:--- |
| skipHeaderLineCount na BlobSource |skipLineCount a firstRowAsHeader. Řádky jsou přeskočeny první a pak je pro čtení první řádek jako záhlaví. |
| treatEmptyAsNull na BlobSource |treatEmptyAsNull na vstupní datové sady |
| blobWriterAddHeader na BlobSink |firstRowAsHeader na výstupní datové sady |

V tématu [zadání TextFormat](data-factory-supported-file-and-compression-formats.md#text-format) části Podrobné informace o těchto vlastností.    

### <a name="recursive-and-copybehavior-examples"></a>Příklady rekurzivní a copyBehavior
Tato část popisuje jejich výsledné chování pro různé kombinace hodnot rekurzivní a copyBehavior operace kopírování.

| rekurzivní | copyBehavior | Výsledné chování |
| --- | --- | --- |
| true (pravda) |preserveHierarchy |Pro zdrojové složky složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>cílové složky složku1 je vytvořena s stejná struktura jako zdroj<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true (pravda) |flattenHierarchy |Pro zdrojové složky složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cíl složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File5 |
| true (pravda) |mergeFiles |Pro zdrojové složky složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cíl složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + soubor3 + File4 + soubor 5 obsah jsou sloučeny do jednoho souboru s názvem automaticky generovaný soubor |
| false (nepravda) |preserveHierarchy |Pro zdrojové složky složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Vytvoření cílové složky složku1 s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Subfolder1 s soubor3, File4 a File5 nejsou zachyceny. |
| false (nepravda) |flattenHierarchy |Pro zdrojové složky složku1 s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Vytvoření cílové složky složku1 s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File2<br/><br/><br/>Subfolder1 s soubor3, File4 a File5 nejsou zachyceny. |
| false (nepravda) |mergeFiles |Pro zdrojové složky složku1 s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Vytvoření cílové složky složku1 s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 obsah jsou sloučeny do jednoho souboru s názvem automaticky generovaný soubor. automaticky generovaný název File1<br/><br/>Subfolder1 s soubor3, File4 a File5 nejsou zachyceny. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Návod: Použití Průvodce kopírováním ke zkopírování dat do nebo z úložiště objektů Blob
Podívejme se na tom, jak rychle kopírování dat z Azure blob storage. V tomto návodu ukládá data zdrojového a cílového typu: Azure Blob Storage. Kanál v tomto návodu kopíruje data ze složky do jiné složky ve stejném kontejneru objektů blob. Tento názorný postup je tak, aby zobrazovalo nastavení nebo vlastnosti při používání Blob Storage jako zdroj nebo podřízený záměrně jednoduchá. 

### <a name="prerequisites"></a>Požadavky
1. Vytvoření pro obecné účely **účet úložiště Azure** Pokud již nemáte. Použít úložiště objektů blob jako obě **zdroj** a **cílové** úložiště dat v tomto návodu. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-create-storage-account.md#create-a-storage-account), kde najdete kroky pro jeho vytvoření.
2. Vytvořte kontejner objektů blob s názvem **adfblobconnector** v účtu úložiště. 
4. Vytvořte složku s názvem **vstupní** v **adfblobconnector** kontejneru.
5. Vytvořte soubor s názvem **emp.txt** s následující obsahu a nahrajte ho do **vstupní** složky pomocí nástrojů, jako například [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```
### <a name="create-the-data-factory"></a>Vytvoření objektu pro vytváření dat
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **vytvořit prostředek** v levém horním rohu klikněte na **Intelligence + analýzy**a klikněte na tlačítko **Data Factory**.
3. V **nový objekt pro vytváření dat** podokně:   
    1. Zadejte **ADFBlobConnectorDF** pro **název**. Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí chyba: `*Data factory name “ADFBlobConnectorDF” is not available`, změňte název objektu pro vytváření dat (například yournameADFBlobConnectorDF) a zkuste to znovu. V tématu [Objekty pro vytváření dat – pravidla pojmenování](data-factory-naming-rules.md) najdete pravidla pojmenování artefaktů služby Data Factory.
    2. Vyberte své **předplatné** Azure.
    3. Pro skupinu prostředků, vyberte **použít existující** vyberte existující skupinu prostředků (nebo) vyberte **vytvořit nový** k zadání názvu pro skupinu prostředků.
    4. Vyberte **umístění** pro příslušný objekt pro vytváření dat.
    5. Zaškrtněte políčko **Připnout na řídicí panel** v dolní části okna.
    6. Klikněte na možnost **Vytvořit**.
3. Po dokončení vytvoření se zobrazí **Data Factory** okno, jak je znázorněno na následujícím obrázku: ![Domovská stránka objektu pro vytváření dat](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Průvodce kopírováním
1. Na domovské stránce objektu pro vytváření dat klikněte na tlačítko **kopírování dat** dlaždici spustíte **Průvodce kopírováním dat** na samostatné kartě.    
    
    > [!NOTE]
    >    Pokud se zobrazí, že webový prohlížeč zasekl ve fázi "autorizace …", zakažte/zrušte zaškrtnutí políčka **blokovat soubory cookie třetích stran a data lokality** nastavení (nebo) zachovat povolené a vytvořte výjimku pro **login.microsoftonline.com**a poté se pokuste spustit průvodce znovu.
2. Na stránce **Vlastnosti**:
    1. Zadejte **CopyPipeline** pro **název úlohy**. Název úlohy je název kanálu v datové továrně.
    2. Zadejte **popis** pro úlohu (volitelné).
    3. Pro **cadence úloh nebo plán úloh**, zachovat **spuštění pravidelně podle plánu** možnost. Pokud chcete spustit tuto úlohu jenom jednou místo spustit opakovaně podle plánu, vyberte **spustit jednou nyní**. Pokud vyberete, **spustit jednou nyní** možnost, [jednorázového kanálu](data-factory-create-pipelines.md#onetime-pipeline) je vytvořena. 
    4. Potvrďte nastavení pro **opakovaná vzor**. Tato úloha se spustí každý den mezi počáteční a koncový čas, které zadáte v dalším kroku.
    5. Změna **datum a čas zahájení** k **21/04/2017**. 
    6. Změna **datum a čas ukončení** k **04/25/2017**. Můžete zadat datum místo projdete kalendáři.     
    8. Klikněte na **Další**.
      ![Nástroj pro kopírování – stránka vlastností](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png) 
3. Na stránce **Source data store** (Zdrojové úložiště dat) klikněte na dlaždici **Azure Blob Storage**. Tato stránka slouží k zadání zdrojového úložiště dat pro úlohu kopírování. Můžete použít existující propojenou službu úložiště dat nebo zadat nové úložiště dat. Pokud chcete použít existující propojenou službu, vyberte **z existujících PROPOJENÝCH služeb** a vyberte požadovanou propojenou službu. 
    ![Nástroj pro kopírování – stránka zdrojového úložiště dat](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. Na stránce **Specify the Azure Blob storage account** (Zadejte účet Azure Blob Storage):
   1. Zachovat automaticky generovaný název pro **název připojení**. Název připojení je název propojené služby typu: Azure Storage. 
   2. Ujistěte se, že je pro položku **Metoda výběru účtu** vybrána možnost **Z předplatných Azure**.
   3. Vyberte předplatné Azure nebo ponechte **Vybrat vše** pro **předplatné**.   
   4. V seznamu účtů úložiště Azure dostupných ve zvoleném předplatném vyberte požadovaný **účet úložiště Azure**. Můžete také zadat nastavení účtu úložiště ručně tak, že vyberete **zadat ručně** možnost **účet metodu výběru**.
   5. Klikněte na **Další**. 
      ![Nástroj pro kopírování – zadání účtu Azure Blob storage](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. Na stránce **Choose the input file or folder** (Zvolte vstupní soubor nebo složku):
   1. Klikněte dvakrát na **adfblobcontainer**.
   2. Vyberte **vstupní**a klikněte na tlačítko **zvolte**. V tomto návodu vyberte vstupní složky. Můžete třeba také vybrat soubor emp.txt ve složce místo. 
      ![Nástroj pro kopírování – volba vstupního souboru nebo složky](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. Na **zvolte vstupní soubor nebo složku** stránky:
    1. Potvrďte, že **souboru nebo složky** je nastaven na **adfblobconnector/vstup**. Pokud jsou soubory do podsložek, například 2017/04/01, 2017/04/02 a tak dále, zadání adfblobconnector / / {year} / {month} / {day} pro soubor nebo složku. Po stisknutí klávesy TAB mimo textového pole, zobrazí se tří rozevíracích seznamech vyberte formáty (rrrr) rok, měsíc (MM) a den (dd). 
    2. Nenastavujte **zkopírujte soubor rekurzivně**. Tuto možnost vyberte k rekurzivnímu křížovou prostřednictvím složek souborů ke zkopírování do cílového umístění. 
    3. Nechcete **binární kopie** možnost. Vyberte tuto možnost, chcete-li provést binární kopii zdrojového souboru do cílového umístění. Nevybírejte v tomto návodu tak, aby se zobrazí další možnosti v dalších stránkách. 
    4. Potvrďte, že **typ komprese** je nastaven na **žádné**. Hodnotu pro tuto možnost vyberte, pokud jsou v některém z podporovaných formátů komprimované zdrojové soubory. 
    5. Klikněte na **Další**.
    ![Nástroj pro kopírování – volba vstupního souboru nebo složky](./media/data-factory-azure-blob-connector/chose-input-file-folder.png) 
7. Na stránce **Nastavení formátu souboru** jsou uvedeny oddělovače a schéma, které je automaticky zjištěno průvodcem při analýze souboru. 
    1. Potvrďte následující možnosti:. **Formát souboru** je nastaven na **formátu textu**. Můžete zobrazit všechny podporované formáty v rozevíracím seznamu. Příklad: formát JSON, Avro, ORC, Parquet.
        b. **Sloupec oddělovač** je nastaven na `Comma (,)`. Můžete zobrazit další sloupec oddělovače podporovaných službou Data Factory v rozevíracím seznamu. Můžete také zadat vlastní oddělovač.
        c. **Oddělovač řádků** je nastaven na `Carriage Return + Line feed (\r\n)`. Můžete zobrazit další řádek oddělovače podporovaných službou Data Factory v rozevíracím seznamu. Můžete také zadat vlastní oddělovač.
        d. **Přeskočit počet řádků** je nastaven na **0**. Pokud chcete po zadání několika řádků lze vynechat v horní části souboru, zadejte zde číslo.
        e.  **První řádek dat obsahuje názvy sloupců** není nastaven. Pokud zdrojové soubory obsahují názvy sloupců v prvním řádku, vyberte tuto možnost.
        f. **Považovat prázdný sloupec hodnota null** je možnost nastavena.
    2. Rozbalte položku **upřesňující nastavení** zobrazíte upřesňující možnost k dispozici.
    3. V dolní části stránky, najdete v článku **preview** dat ze souboru emp.txt.
    4. Klikněte na tlačítko **schématu** karta v dolní části zobrazíte schéma, které Průvodce kopírováním vyvozena na základě dat ve zdrojovém souboru.
    5. Po zkontrolování oddělovačů a náhledu dat klikněte na **Další**.
    ![Nástroj pro kopírování – nastavení formátu souboru](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)  
8. Na **úložiště dat cílové stránky**, vyberte **Azure Blob Storage**a klikněte na tlačítko **Další**. Úložiště objektů Blob Azure používají jako obou zdrojové a cílové úložištích dat v tomto návodu.    
    ![Nástroj pro kopírování – vyberte cílového úložiště dat](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. Na **zadejte účet úložiště Azure Blob** stránky:
   1. Zadejte **AzureStorageLinkedService** pro **název připojení** pole.
   2. Ujistěte se, že je pro položku **Metoda výběru účtu** vybrána možnost **Z předplatných Azure**.
   3. Vyberte své **předplatné** Azure.  
   4. Vyberte účet úložiště Azure. 
   5. Klikněte na **Další**.     
10. Na **zvolit výstupní soubor nebo složku** stránky: 
    6. Zadejte **cesta ke složce** jako **adfblobconnector výstupní / {year} / {month} / {day}**. Zadejte **KARTĚ**.
    7. Pro **roku**, vyberte **rrrr**.
    8. Pro **měsíc**, potvrďte, že je nastavena na **MM**.
    9. Pro **den**, potvrďte, že je nastavena na **dd**.
    10. Potvrďte, že **typ komprese** je nastaven na **žádné**.
    11. Potvrďte, že **zkopírujte chování** je nastaven na **sloučení souborů**. Pokud výstupní soubor s tímto názvem již existuje, je přidán nový obsah do stejného souboru na konci.
    12. Klikněte na **Další**.
    ![Nástroj pro kopírování – volba výstupního souboru nebo složky](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. Na **nastavení formátu souboru** stránka, zkontrolujte nastavení a klikněte na tlačítko **Další**. Jedním z dalších možností je přidat hlavičku do výstupního souboru. Pokud tuto možnost vyberete, se přidá řádek záhlaví s názvy sloupců ze schématu zdroje. Při zobrazení schématu zdroje, můžete přejmenovat výchozí názvy sloupců. První sloupec můžete například změnit křestní jméno a příjmení druhý sloupec. Potom výstupní soubor je vytvořen s záhlaví s těmito názvy jako názvy sloupců. 
    ![Nástroj pro kopírování – nastavení formátu souboru pro cíl](media/data-factory-azure-blob-connector/file-format-destination.png)
12. Na **nastavení výkonu** potvrďte, že **cloudu jednotky** a **paralelní kopie** jsou nastaveny na **automaticky**a klikněte na tlačítko Další. Podrobnosti o těchto nastaveních najdete v tématu [zkopírujte aktivity výkonu a vyladění průvodce](data-factory-copy-activity-performance.md#parallel-copy).
    ![Nástroj pro kopírování – nastavení výkonu](media/data-factory-azure-blob-connector/copy-performance-settings.png) 
14. Na **Souhrn** zkontrolujte všechna nastavení (Vlastnosti úlohy, nastavení pro zdrojové a cílové a Kopírovat nastavení) a klikněte na tlačítko **Další**.
    ![Nástroj pro kopírování – stránka souhrnu](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Na stránce **Souhrn** zkontrolujte informace a klikněte na **Dokončit**. Průvodce v objektu pro vytváření dat (ze kterého jste průvodce kopírováním spustili) vytvoří dvě propojené služby, dvě datové sady (vstupní a výstupní) a jeden kanál.
    ![Nástroj pro kopírování – stránka nasazení](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>Monitorování kanálu (úlohy kopie)

1. Klikněte na odkaz `Click here to monitor copy pipeline` na **nasazení** stránky. 
2. Měli byste vidět **sledovat a spravovat aplikace** na samostatné kartě.  ![Sledování a správě aplikací](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Změna **spustit** čas od nejvyšší `04/19/2017` a **end** čas k `04/27/2017`a potom klikněte na **použít**. 
4. Měli byste vidět pět oken aktivity v **aktivity WINDOWS** seznamu. **WindowStart** časy by mělo zahrnovat všechny dny od začátku kanálu do kanálu koncový čas. 
5. Klikněte na tlačítko **aktualizovat** tlačítko pro **aktivity WINDOWS** seznamu několikrát, dokud se zobrazí stav všech aktivity windows je nastaven na hodnotu Připraveno. 
6. Nyní ověřte, že jsou generovány výstupní soubory v zadané výstupní složce adfblobconnector kontejneru. Měli byste vidět následující strukturu složek do výstupní složky: 
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25    
    ```
Podrobné informace o monitorování a Správa objektů pro vytváření dat najdete v tématu [monitorování a Správa kanálů služby Data Factory](data-factory-monitor-manage-app.md) článku. 
 
### <a name="data-factory-entities"></a>Entity objektu pro vytváření dat
Nyní přejděte zpět na kartě s domovské stránce objektu pro vytváření dat. Všimněte si, že existují dvě propojené služby, dvě datové sady a jeden kanál v datové továrně teď. 

![Domovská stránka objektu pro vytváření dat s entitami](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Klikněte na tlačítko **vytvořit a nasadit** ke spuštění editoru služby Data Factory. 

![Data Factory Editor](media/data-factory-azure-blob-connector/data-factory-editor.png)

V datové továrně byste měli vidět následující entity služby Data Factory: 

 - Dvě propojené služby. Jeden pro zdroji a ostatní jeden pro cíl. Propojené služby odkazovat na stejný účet služby Azure Storage v tomto návodu. 
 - Dvě datové sady. Vstupní datové sady a datovou sadu výstupů. V tomto návodu obě používat stejný kontejner objektů blob ale odkazovat na jiné složky (vstup a výstup).
 - Kanál. Kanál obsahuje kopii aktivit, které používají zdroj objektů blob a podřízený objekt blob ke zkopírování dat z umístění objektu blob Azure do jiného umístění objektu blob Azure. 

Následující části obsahují další informace o těchto entitách. 

#### <a name="linked-services"></a>Propojené služby
Měli byste vidět dvě propojené služby. Jeden pro zdroji a ostatní jeden pro cíl. V tomto návodu podívejte se i definice stejné s výjimkou názvy. **Typ** propojené služby je nastaven na **azurestorage**. Nejdůležitější vlastnost definice propojené služby **connectionString**, který je využíván jiným objekt pro vytváření dat pro připojení k účtu úložiště Azure za běhu. Ignorujte hubName vlastnost v definici. 

##### <a name="source-blob-storage-linked-service"></a>Zdrojový objekt blob propojená služba úložiště
```json
{
    "name": "Source-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

##### <a name="destination-blob-storage-linked-service"></a>Cílový objekt blob propojená služba úložiště

```json
{
    "name": "Destination-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

Další informace o propojené služby Azure Storage najdete v tématu [propojené vlastnosti služby](#linked-service-properties) části. 

#### <a name="datasets"></a>Datové sady
Existují dvě datové sady: vstupní datové sady a datovou sadu výstupů. Typ datové sady je nastaven na **AzureBlob** pro obojí. 

Vstupní datová sada odkazuje na **vstupní** složky **adfblobconnector** kontejner objektů blob. **Externí** je nastavena na **true** pro tuto datovou sadu jako data není vytvořená v kanálu s aktivitou kopírování, která přebírá tuto datovou sadu jako vstup. 

Výstupní datovou sadu odkazuje na **výstup** složky stejné kontejneru objektů blob. Výstupní datovou sadu také používá rok, měsíc a den **SliceStart** proměnné systému pro dynamicky vyhodnocení cesta pro výstupní soubor. Seznam funkcí a systémové proměnné podporovaných službou Data Factory najdete v tématu [funkce pro vytváření dat a systémové proměnné](data-factory-functions-variables.md). **Externí** je nastavena na **false** (výchozí hodnota) protože tato datová sada je produkovaný kanálu. 

Další informace o vlastnostech podporovaných zprostředkovatelem datovou sadu objektu Blob Azure najdete v tématu [vlastnosti datové sady](#dataset-properties) části.

##### <a name="input-dataset"></a>Vstupní datové sady

```json
{
    "name": "InputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Source-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/input/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

##### <a name="output-dataset"></a>Výstupní datové sady

```json
{
    "name": "OutputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Destination-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/output/{year}/{month}/{day}",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
            "partitionedBy": [
                { "name": "year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }
            ]
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

#### <a name="pipeline"></a>Kanál
Kanál obsahuje pouze jednu aktivitu. **Typ** aktivity je nastaven na **kopie**.  Ve vlastnostech typu aktivity jsou dvě části, jeden pro zdroj a jinou pro sink. Typ zdroje je nastaven na **BlobSource** jako aktivity je kopírování dat z úložiště objektů blob. Typ jímky nastavena na **BlobSink** jako aktivita kopírování dat do úložiště objektů blob. Aktivita kopírování trvá InputDataset z4y jako vstup a OutputDataset z4y jako výstup. 

Další informace o vlastnostech podporovaných zprostředkovatelem BlobSource a BlobSink najdete v tématu [zkopírovat vlastnosti aktivity](#copy-activity-properties) části. 

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "MergeFiles",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-z4y"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-z4y"
                    }
                ],
                "policy": {
                    "timeout": "1.00:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 3,
                    "longRetry": 0,
                    "longRetryInterval": "00:00:00"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "Activity-0-Blob path_ adfblobconnector_input_->OutputDataset-z4y"
            }
        ],
        "start": "2017-04-21T22:34:00Z",
        "end": "2017-04-25T05:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>Příklady JSON pro kopírování dat do a z úložiště objektů Blob  
Následující příklady poskytují ukázka JSON definice, které můžete použít k vytvoření kanálu pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Se ukazují, jak ke zkopírování dat do a z Azure Blob Storage a Azure SQL Database. Nicméně je možné zkopírovat data **přímo** ze všech zdrojů do jakéhokoli z jímky uvádí [sem](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>Příklad JSON: Kopírování dat z úložiště objektů Blob do databáze SQL
Následující příklad ukazuje:

1. Propojené služby typu [azuresqldatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Propojené služby typu [azurestorage](#linked-service-properties).
3. Vstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](#copy-activity-properties) a [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties).

Kopie ukázka časové řady dat z Azure blob do Azure SQL tabulky každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

**Azure SQL propojené služby:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Propojená služba Azure Storage:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Podporuje dva typy Azure Storage, propojené služby Azure Data Factory: **azurestorage** a **AzureStorageSas**. Pro první zadejte připojovací řetězec, který obsahuje klíč účtu a pro novější verzi, zadejte identifikátor Uri sdíleného přístupového podpisu (SAS). V tématu [propojené služby](#linked-service-properties) podrobnosti.  

**Azure vstupní datovou sadu objektu Blob:**

Data je převzata z nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Název složky a cesta k souboru pro tento objekt blob se vyhodnocují dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc a den součástí čas spuštění a název souboru používá hodinu součástí čas spuštění. "externí": "PRAVDA" nastavení informuje objekt pro vytváření dat, v tabulce je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure SQL výstupní datovou sadu:**

Ukázková data kopie tabulku s názvem "MyTable" v Azure SQL database. Vytvoření tabulky ve vaší databázi Azure SQL s stejný počet sloupců, podle očekávání souboru CSV objektů Blob tak, aby obsahovala. Nové záznamy se přidají do tabulky každou hodinu.

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Aktivita kopírování v kanálu s Blob zdroj a jímka SQL:**

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **BlobSource** a **podřízený** je typ nastaven na **SqlSink**.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>Příklad JSON: Kopírování dat z Azure SQL do Azure Blob
Následující příklad ukazuje:

1. Propojené služby typu [azuresqldatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Propojené služby typu [azurestorage](#linked-service-properties).
3. Vstup [datovou sadu](data-factory-create-datasets.md) typu [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) a [BlobSink](#copy-activity-properties).

Ukázka kopíruje data časové řady z tabulky Azure SQL do objektu blob Azure každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

**Azure SQL propojené služby:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Propojená služba Azure Storage:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Podporuje dva typy Azure Storage, propojené služby Azure Data Factory: **azurestorage** a **AzureStorageSas**. Pro první zadejte připojovací řetězec, který obsahuje klíč účtu a pro novější verzi, zadejte identifikátor Uri sdíleného přístupového podpisu (SAS). V tématu [propojené služby](#linked-service-properties) podrobnosti.  

**Azure SQL vstupní datové sady:**

Příkladu se předpokládá, jste vytvořili tabulku "MyTable" v Azure SQL a obsahuje sloupec s názvem "timestampcolumn" pro data časové řady.

Nastavení "externí": "PRAVDA" informuje služba Data Factory, v tabulce je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

```json
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure Blob výstupní datovou sadu:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Cesta ke složce pro tento objekt blob je vyhodnocován dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc, den a čas částí čas spuštění.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": { "type": "DateTime",  "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Aktivita kopírování v kanálu s SQL zdrojový a podřízený objekt Blob:**

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **SqlSource** a **podřízený** je typ nastaven na **BlobSink**. Zadané pro dotaz SQL **SqlReaderQuery** vlastnost vybere data za poslední hodinu pro kopírování.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
              {
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureSQLInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
                      "executionPriorityOrder": "OldestFirst",
                      "retry": 0,
                      "timeout": "01:00:00"
                }
              }
         ]
    }
}
```

> [!NOTE]
> Mapování sloupců z datové sady zdroje na sloupce ze sady jímku dat naleznete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a ladění
V tématu [výkonu kopie aktivity & ladění průvodce](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů, že dopad výkon přesun dat (aktivita kopírování) v Azure Data Factory a různé způsoby, jak optimalizovat ho.
