---
title: Kopírování dat do a z úložiště objektů Blob v Azure | Dokumentace Microsoftu
description: 'Zjistěte, jak kopírovat data objektů blob v Azure Data Factory. Využijte naši ukázku: kopírování dat do a z Azure Blob Storage a Azure SQL Database.'
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
ms.openlocfilehash: 2efc20d5a2248fed69f38880a9e75a6ccb2403dd
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "42057221"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Kopírování dat do nebo z Azure Blob Storage pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-azure-blob-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-blob-storage.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor Azure Blob Storage ve verzi V2](../connector-azure-blob-storage.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování ve službě Azure Data Factory pro kopírování dat do a z úložiště objektů Blob v Azure. Je nástavbou [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který nabízí obecný přehled o přesun dat pomocí aktivity kopírování.

## <a name="overview"></a>Přehled
Můžete zkopírovat data z libovolné podporované zdrojové úložiště dat do Azure Blob Storage nebo z úložiště objektů Blob Azure a všechny podporovaného úložiště dat jímky. Následující tabulka obsahuje seznam úložišť dat podporovaných jako zdroje nebo jímky v aktivitě kopírování. Například můžete přesunout data **z** databázi serveru SQL Server nebo databázi Azure SQL **k** služby Azure blob storage. A může kopírovat data **z** úložiště objektů blob v Azure **k** službu Azure SQL Data Warehouse nebo kolekci Azure Cosmos DB. 

## <a name="supported-scenarios"></a>Podporované scénáře
Data můžete kopírovat **z Azure Blob Storage** ukládá následující data:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Může kopírovat data z následujících datových skladů **do Azure Blob Storage**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]
 
> [!IMPORTANT]
> Aktivitu kopírování, která podporuje kopírování dat z/do účtech Azure Storage a Hot nebo Cool Blob storage. Aktivita podporuje **čtení z bloku, doplňovací nebo objekty BLOB stránky**, ale podporuje **zápisu do jenom objekty BLOB bloku**. Azure Premium Storage není podporován jako jímka, protože se zajištěním objekty BLOB stránky.
> 
> Aktivita kopírování neodstraní data ze zdroje po data se úspěšně zkopíroval do cíle. Pokud je potřeba odstranit zdroj dat po úspěšném kopírování, vytvoření [vlastní aktivity](data-factory-use-custom-activities.md) a odstraňte data pomocí aktivity v kanálu. Příklad najdete v tématu [odstranění objektů blob a složku ukázka na Githubu](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity). 

## <a name="get-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, která přesouvání dat do a z Azure Blob Storage pomocí různých nástrojů a rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Tento článek obsahuje [návod](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) pro vytvoření kanálu pro kopírování dat z umístění služby Azure Blob Storage do jiného umístění Azure Blob Storage. Kurz týkající se vytvoření kanálu pro kopírování dat ze služby Azure Blob Storage do služby Azure SQL Database, najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

Tyto nástroje můžete také použít k vytvoření kanálu: **webu Azure portal**, **sady Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru** , **Rozhraní .NET API**, a **rozhraní REST API**. Zobrazit [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování.

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvoření **služby data factory**. Datová továrna může obsahovat jeden nebo víc kanálů. 
2. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do služby data factory. Například pokud kopírujete data ze služby Azure blob storage do Azure SQL database, vytvoříte dvě propojené služby pro propojení účtu služby Azure storage a Azure SQL database do služby data factory. Vlastnosti propojené služby, které jsou specifické pro úložiště objektů Blob v Azure, najdete v části [vlastnostem propojených služeb](#linked-service-properties) oddílu. 
2. Vytvoření **datových sad** k představují vstupní a výstupní data pro operaci kopírování. V příkladu uvedených v posledním kroku vytvoříte datovou sadu, která zadejte kontejner objektů blob a složku obsahující vstupní data. A vytvořte jinou datovou sadu, která zadejte tabulky SQL ve službě Azure SQL database, která obsahuje data zkopírovaná z úložiště objektů blob. Vlastnosti datové sady, které jsou specifické pro Azure Blob Storage, najdete v části [vlastnosti datové sady](#dataset-properties) oddílu.
3. Vytvoření **kanálu** s aktivitou kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup. V příkladu již bylo zmíněno dříve pomocí BlobSource jako zdroj a SqlSink jako jímka pro aktivitu kopírování. Podobně pokud kopírujete z Azure SQL Database do Azure Blob Storage, můžete použít SqlSource a BlobSink v aktivitě kopírování. Kopírovat vlastnosti aktivity, které jsou specifické pro Azure Blob Storage, najdete v části [vlastnosti aktivity kopírování](#copy-activity-properties) oddílu. Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímku klikněte na odkaz v předchozí části datového úložiště.  

Při použití Průvodce definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál) se automaticky vytvoří za vás. Při použití nástroje a rozhraní API (s výjimkou rozhraní .NET API), můžete definovat tyto entity služby Data Factory ve formátu JSON.  Ukázky s definicemi JSON entit služby Data Factory, které se používají ke kopírování dat do a z Azure Blob Storage najdete v tématu [JSON příklady](#json-examples-for-copying-data-to-and-from-blob-storage  ) části tohoto článku.

Následující části obsahují podrobnosti o vlastnostech JSON, které se používají k definování konkrétní entity služby Data Factory do Azure Blob Storage.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Existují dva typy propojené služby, které lze použít k propojení služby Azure Storage do služby Azure data factory. Jsou: **AzureStorage** propojenou službu a **AzureStorageSas** propojenou službu. Propojenou službu Azure Storage poskytuje datovou továrnu s globálním přístupem ke službě Azure Storage. Vzhledem k tomu Azure úložiště SAS (sdíleným přístupovým podpisům) propojená služba poskytuje služby data factory s přístupem s omezením pomocí specifikátoru/časově omezenou do služby Azure Storage. Nejsou žádné další rozdíly mezi tyto dvě propojené služby. Zvolte propojené služby, která vyhovuje vašim potřebám. Následující oddíly poskytují další podrobnosti na tyto dvě propojené služby.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Vlastnosti datové sady
K určení datové sady reprezentující vstupní nebo výstupní data ve službě Azure Blob Storage, nastavte vlastnost typ datové sady na: **AzureBlob**. Nastavte **linkedServiceName** vlastnosti datové sady na název služby Azure Storage nebo Azure Storage SAS propojenou službu.  Zadejte typ vlastnosti datové sady **kontejner objektů blob** a **složky** ve službě blob storage.

Úplný seznam oddílů JSON a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny datové sady typy (Azure SQL, Azure blob, tabulky Azure, atd.).

Data factory podporuje následující hodnoty kompatibilní se Specifikací CLS .NET na základě typu pro poskytnutí informací o typu v "struktura" zdrojů schéma při čtení dat objektů blob v Azure: Int16, Int32, Int64, jednu, Double, Decimal, Byte [], Bool, String, Guid, Datetime, DateTimeOffset, Timespan. Data Factory automaticky provádí převody typů, při přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky.

**TypeProperties** oddílu se liší pro každý typ datové sady a informace o umístění, formátovat atd, dat v úložišti. TypeProperties části datové sady typu **AzureBlob** datovou sadu má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Cesta k kontejner a složku v úložišti objektů blob. Příklad: myblobcontainer\myblobfolder\ |Ano |
| fileName |Název objektu blob. Název souboru je volitelný a malá a velká písmena.<br/><br/>Pokud zadáte filename, aktivity (včetně kopie) funguje na konkrétní objekt Blob.<br/><br/>Pokud není zadán název souboru, zahrnuje kopírování všech objektů BLOB v folderPath pro vstupní datovou sadu.<br/><br/>Když **fileName** pro datovou sadu výstupů není zadána a **preserveHierarchy** není zadané v aktivity podřízený název vygenerovaný soubor bude v následujícím tento formát: Data<Guid>. TXT (například: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Ne |
| partitionedBy |partitionedBy vlastnost je volitelná. Slouží k určení dynamické folderPath a název souboru pro data časových řad. Například může být parametrizován folderPath pro každou hodinu data. Zobrazit [pomocí části vlastnost partitionedBy](#using-partitionedBy-property) podrobnosti a příklady. |Ne |
| Formát | Jsou podporovány následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formát](data-factory-supported-file-and-compression-formats.md#orc-format), a [formát Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete **kopírovat soubory jako-je** mezi souborové úložištěm (binární kopie) a přeskočit část o formátu v definicích oba vstupní a výstupní datové sady. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese ve službě Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

### <a name="using-partitionedby-property"></a>Vlastnost partitionedBy
Jak je uvedeno v předchozí části, můžete zadat dynamické folderPath a název souboru pro data časových řad s **partitionedBy** vlastnost [funkce Data Factory a systémové proměnné](data-factory-functions-variables.md).

Další informace o čase řady datových sad, plánování a řezů, naleznete v tématu [vytváření datových sad](data-factory-create-datasets.md) a [plánování a provádění](data-factory-scheduling-and-execution.md) článků.

#### <a name="sample-1"></a>Ukázka 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

V tomto příkladu {řez} se nahradí hodnotu proměnné objektu pro vytváření dat systému SliceStart ve formátu (YYYYMMDDHH) zadaná. Vlastnosti SliceStart odkazuje na počáteční čas řezu. V cestě folderPath se liší pro každý řez. Příklad: wikidatagateway/wikisampledataout/2014100103 nebo wikidatagateway/wikisampledataout/2014100104

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

V tomto příkladu rok, měsíc, den a čas z vlastnosti SliceStart extrahován do samostatných proměnné, které jsou používány vlastnosti folderPath a název souboru.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, jako je název, popis, vstupní a výstupní datové sady a zásady jsou k dispozici pro všechny typy aktivit. Vzhledem k tomu, k dispozici ve vlastnosti **typeProperties** části aktivity se liší s jednotlivými typu aktivity. Pro aktivitu kopírování se liší v závislosti na typy zdroje a jímky. Pokud přesouváte data ze služby Azure Blob Storage, nastavte typ zdroje v aktivitě kopírování do **BlobSource**. Podobně pokud přesouváte data do služby Azure Blob Storage, je nastavit typ jímky v aktivitě kopírování do **BlobSink**. Tato část obsahuje seznam podporovaných BlobSource a BlobSink vlastností.

**BlobSource** podporuje následující vlastnosti v **typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| rekurzivní |Určuje, jestli se data ze složek sub nebo pouze z určené složky Číst rekurzivně. |True, False (výchozí hodnota) |Ne |

**BlobSink** podporuje následující vlastnosti **typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| copyBehavior |Definuje chování kopírování, pokud je zdroj BlobSource nebo systému souborů. |<b>PreserveHierarchy</b>: zachová hierarchií souborů v cílové složce. Relativní cesta zdrojového souboru do zdrojové složky je stejný jako relativní cesta cílový soubor do cílové složky.<br/><br/><b>FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úroveň cílové složky. Cílové soubory mají název automaticky generovány. <br/><br/><b>MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadaný název souboru nebo objekt Blob, název sloučený soubor by měl být zadaný název; v opačném případě bude název automaticky generovaného souboru. |Ne |

**BlobSource** také podporuje tyto dvě vlastnosti z důvodu zpětné kompatibility.

* **treatEmptyAsNull**: Určuje, jestli se má hodnotu null nebo prázdný řetězec považovat za hodnotu null.
* **skipHeaderLineCount** – Určuje, kolik řádků musí být přeskočeny. To platí, pouze pokud vstupní datová sada používá TextFormat.

Obdobně **BlobSink** podporuje následující vlastnost z důvodu zpětné kompatibility.

* **blobWriterAddHeader**: Určuje, jestli se má přidat hlavičku definice sloupců při zápisu do výstupní datovou sadu.

Datové sady teď podporují následující vlastnosti, které implementují stejnou funkci: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

Následující tabulka uvádí pokyny k používání nové vlastnosti datové sady místo tyto vlastnosti objektu blob zdroje a jímky.

| Vlastnost aktivity kopírování | Vlastnosti datové sady |
|:--- |:--- |
| skipHeaderLineCount na BlobSource |Parametr skipLineCount a firstRowAsHeader. Nejdřív se přeskočí řádky a potom načtou první řádek jako záhlaví. |
| treatEmptyAsNull na BlobSource |treatEmptyAsNull na vstupní datové sady |
| blobWriterAddHeader na BlobSink |firstRowAsHeader na výstupní datovou sadu |

V tématu [zadání TextFormat](data-factory-supported-file-and-compression-formats.md#text-format) části Podrobné informace o těchto vlastností.    

### <a name="recursive-and-copybehavior-examples"></a>rekurzivní a copyBehavior příklady
Tato část popisuje výsledné chování pro různé kombinace hodnot rekurzivní a copyBehavior operace kopírování.

| rekurzivní | copyBehavior | Výsledné chování |
| --- | --- | --- |
| true (pravda) |preserveHierarchy |Pro zdrojové složky složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>cílové složky složku1 se vytvoří s stejnou strukturu jako zdroj<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true (pravda) |flattenHierarchy |Pro zdrojové složky složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automaticky generovaný název File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File5 |
| true (pravda) |mergeFiles |Pro zdrojové složky složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 File2 + soubor3 + File4 + 5 souboru obsahu jsou sloučeny do jednoho souboru s názvem automaticky generovaný soubor |
| false (nepravda) |preserveHierarchy |Pro zdrojové složky složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cílové složky složku1 se vytvoří s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Subfolder1 s soubor3 File4 a File5 se nenačítají. |
| false (nepravda) |flattenHierarchy |Pro zdrojové složky složku1 s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cílové složky složku1 se vytvoří s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automaticky generovaný název File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File2<br/><br/><br/>Subfolder1 s soubor3 File4 a File5 se nenačítají. |
| false (nepravda) |mergeFiles |Pro zdrojové složky složku1 s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cílové složky složku1 se vytvoří s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 obsah jsou sloučeny do jednoho souboru s názvem automaticky generovaného souboru. Automaticky generovaný název File1<br/><br/>Subfolder1 s soubor3 File4 a File5 se nenačítají. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Návod: Použití Průvodce kopírováním ke kopírování dat do a z úložiště objektů Blob
Podívejme se na tom, jak rychle zkopírovat data do a z Azure blob storage. V tomto podrobném návodu se ukládá data na zdrojovém i cílovém typu: Azure Blob Storage. Kanál v tomto názorném postupu kopíruje data ze složky do jiné složky ve stejném kontejneru objektů blob. Tento názorný postup je záměrně jednoduchá můžete zobrazit nastavení nebo vlastnosti, při použití úložiště objektů Blob jako zdroj nebo jímku. 

### <a name="prerequisites"></a>Požadavky
1. Vytvořte pro obecné účely **účet služby Azure Storage** Pokud již nemáte. Úložiště objektů blob použijete jako **zdroj** a **cílové** úložiště dat v tomto názorném postupu. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-quickstart-create-account.md), kde najdete kroky pro jeho vytvoření.
2. Vytvořte kontejner objektů blob s názvem **adfblobconnector** v účtu úložiště. 
4. Vytvořte složku s názvem **vstupní** v **adfblobconnector** kontejneru.
5. Vytvořte soubor s názvem **emp.txt** s následující obsah a nahrajte ho do **vstupní** složky pomocí nástrojů, jako [Průzkumníka služby Azure Storage](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```
### <a name="create-the-data-factory"></a>Vytvoření datové továrny
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **vytvořit prostředek** z levého horního rohu, klikněte na tlačítko **inteligence a analýza**a klikněte na tlačítko **služby Data Factory**.
3. V **nová datová továrna** podokna:   
    1. Zadejte **ADFBlobConnectorDF** pro **název**. Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí chybová zpráva: `*Data factory name “ADFBlobConnectorDF” is not available`, změňte název datové továrny (například yournameADFBlobConnectorDF) a zkuste to znovu. V tématu [Objekty pro vytváření dat – pravidla pojmenování](data-factory-naming-rules.md) najdete pravidla pojmenování artefaktů služby Data Factory.
    2. Vyberte své **předplatné** Azure.
    3. Pro skupinu prostředků, vyberte **použít existující** vyberte existující skupinu prostředků (nebo) vyberte **vytvořit nový** zadat název pro skupinu prostředků.
    4. Vyberte **umístění** pro příslušný objekt pro vytváření dat.
    5. Zaškrtněte políčko **Připnout na řídicí panel** v dolní části okna.
    6. Klikněte na možnost **Vytvořit**.
3. Po vytvoření se zobrazí, se zobrazí **služby Data Factory** okno, jak je znázorněno na následujícím obrázku: ![Domovská stránka datová továrna](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Průvodce kopírováním
1. Na domovské stránce objektu pro vytváření dat klikněte na tlačítko **kopírování dat** dlaždice **Data Průvodce kopírováním** na samostatné kartě.    
    
    > [!NOTE]
    >    Pokud zjistíte, že se webový prohlížeč zasekl ve "Autorizace …", zakažte/zrušte zaškrtnutí políčka **zablokuje soubory cookie třetích stran a data lokality** nastavení (nebo) bude povolené a vytvořte výjimku pro **login.microsoftonline.com**a potom zkuste průvodce znovu spustit.
2. Na stránce **Vlastnosti**:
    1. Zadejte **CopyPipeline** pro **název úkolu**. Název úlohy je název kanálu ve službě data factory.
    2. Zadejte **popis** pro úlohu (volitelné).
    3. Pro **tempo úkolu nebo plán úloh**, zachovat **pravidelně spouštět podle plánu** možnost. Pokud chcete spustit tuto úlohu pouze jednou místo spuštění opakovaně podle plánu, vyberte **jednou spustit**. Pokud si vyberete, **jednou spustit** možnost, [jednorázového kanálu](data-factory-create-pipelines.md#onetime-pipeline) se vytvoří. 
    4. Zachovat nastavení **periodický vzor**. Tato úloha se spustí každý den mezi počátečním a koncovým časem, který zadáte v dalším kroku.
    5. Změnit **datum a čas zahájení** k **04/21/2017**. 
    6. Změnit **datum a čas ukončení** k **04/25/2017**. Můžete zadat datum místo procházení v kalendáři.     
    8. Klikněte na **Další**.
      ![Nástroj pro kopírování – stránka Vlastnosti](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png) 
3. Na stránce **Source data store** (Zdrojové úložiště dat) klikněte na dlaždici **Azure Blob Storage**. Tato stránka slouží k zadání zdrojového úložiště dat pro úlohu kopírování. Můžete použít existující propojenou službu úložiště dat nebo zadat nové úložiště dat. Pokud chcete použít existující propojenou službu, vyberte **z existujících PROPOJENÝCH služeb** a vyberte požadovanou propojenou službu. 
    ![Nástroj pro kopírování – stránka zdrojového úložiště dat](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. Na stránce **Specify the Azure Blob storage account** (Zadejte účet Azure Blob Storage):
   1. Zachovat název automaticky generované **název připojení**. Název připojení je název propojené služby typu: Azure Storage. 
   2. Ujistěte se, že je pro položku **Metoda výběru účtu** vybrána možnost **Z předplatných Azure**.
   3. Vyberte své předplatné Azure nebo ponechat **Vybrat vše** pro **předplatného Azure**.   
   4. V seznamu účtů úložiště Azure dostupných ve zvoleném předplatném vyberte požadovaný **účet úložiště Azure**. Můžete také zadat nastavení účtu úložiště ručně tak, že vyberete **zadat ručně** možnost **metoda výběru účtu**.
   5. Klikněte na **Další**. 
      ![Nástroj pro kopírování – zadání účtu úložiště objektů Blob v Azure](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. Na stránce **Choose the input file or folder** (Zvolte vstupní soubor nebo složku):
   1. Dvakrát klikněte na panel **adfblobcontainer**.
   2. Vyberte **vstupní**a klikněte na tlačítko **zvolit**. V tomto názorném postupu vyberte vstupní složky. Můžete také vybrat soubor emp.txt ve složce místo. 
      ![Nástroj pro kopírování – volba vstupního souboru nebo složky](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. Na **zvolte vstupní soubor nebo složku** stránky:
    1. Ujistěte se, že **souboru nebo složky** je nastavena na **adfblobconnector/input**. Pokud jsou soubory do podsložek, například 2017/04/01, 2017/04/02 a tak dále, zadejte adfblobconnector/input / {year} / {month} / {day} pro soubor nebo složku. Při stisknutí klávesy TAB mimo do textového pole, se zobrazí tři rozevíracích seznamech vyberte formáty rok (rrrr), (MM) měsíce a dne (dd). 
    2. Nenastavujte **zkopírujte soubor rekurzivně**. Tato možnost Procházet rekurzivně procházet složky pro soubory, které se mají zkopírovat do cíle. 
    3. Nepodporují **binární kopie** možnost. Tuto možnost, aby binární kopii zdrojového souboru do cíle. Nesmí být zvolen pro Tento názorný postup, kde můžete zobrazit další možnosti v další stránky. 
    4. Ujistěte se, že **typ komprese** je nastavena na **žádný**. Hodnota pro tuto možnost vyberte, pokud jsou komprimované zdrojové soubory v jednom z podporovaných formátů. 
    5. Klikněte na **Další**.
    ![Nástroj pro kopírování – volba vstupního souboru nebo složky](./media/data-factory-azure-blob-connector/chose-input-file-folder.png) 
7. Na stránce **Nastavení formátu souboru** jsou uvedeny oddělovače a schéma, které je automaticky zjištěno průvodcem při analýze souboru. 
    1. Potvrďte následující možnosti:. **Formát souboru** je nastavena na **textový formát**. Uvidíte všechny podporované formáty v rozevíracím seznamu. Příklad: JSON, Avro, ORC, Parquet.
        b. **Oddělovač sloupců** je nastavena na `Comma (,)`. Můžete zobrazit jiné oddělovače sloupců v rozevíracím seznamu podporovaných službou Data Factory. Můžete také určit vlastní oddělovač.
        c. **Oddělovač řádků** je nastavena na `Carriage Return + Line feed (\r\n)`. Můžete zobrazit jiné oddělovače řádků v rozevíracím seznamu podporovaných službou Data Factory. Můžete také určit vlastní oddělovač.
        d. **Přeskočit počet řádků** je nastavena na **0**. Pokud chcete, aby několik řádků se má přeskočit v horní části souboru, zadejte číslo zde.
        e.  **První řádek dat obsahují názvy sloupců** není nastaven. Pokud zdrojové soubory obsahují názvy sloupců v prvním řádku, vyberte tuto možnost.
        f. **Považovat za prázdný sloupec hodnoty null** je možnost nastavená.
    2. Rozbalte **upřesňující nastavení** zobrazíte upřesňující možnosti, které jsou k dispozici.
    3. V dolní části stránky, najdete v článku **ve verzi preview** dat ze souboru emp.txt.
    4. Klikněte na tlačítko **schématu** karta v dolní části zobrazíte schéma, které Průvodce kopírováním vyvozena na základě podíváme na data ve zdrojovém souboru.
    5. Po zkontrolování oddělovačů a náhledu dat klikněte na **Další**.
    ![Nástroj pro kopírování – nastavení formátu souboru](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)  
8. Na **úložiště dat cílové stránky**vyberte **Azure Blob Storage**a klikněte na tlačítko **Další**. Používání úložiště objektů Blob Azure jako obě zdrojové a cílové úložiště dat v tomto názorném postupu.    
    ![Nástroj pro kopírování – výběr cílového úložiště dat.](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. Na **zadejte účet úložiště objektů Blob v Azure** stránky:
   1. Zadejte **AzureStorageLinkedService** pro **název připojení** pole.
   2. Ujistěte se, že je pro položku **Metoda výběru účtu** vybrána možnost **Z předplatných Azure**.
   3. Vyberte své **předplatné** Azure.  
   4. Vyberte svůj účet úložiště Azure. 
   5. Klikněte na **Další**.     
10. Na **zvolte výstupní soubor nebo složku** stránky: 
    6. Zadejte **cesta ke složce** jako **adfblobconnector/output / {year} / {month} / {day}**. Zadejte **kartu**.
    7. Pro **rok**vyberte **rrrr**.
    8. Pro **měsíc**, potvrďte, že je nastavena **MM**.
    9. Pro **den**, potvrďte, že je nastavena **dd**.
    10. Ujistěte se, že **typ komprese** je nastavena na **žádný**.
    11. Ujistěte se, že **zkopírujte chování** je nastavena na **sloučit soubory**. Pokud výstupní soubor se stejným názvem už existuje, je stejný soubor na konci přidal nový obsah.
    12. Klikněte na **Další**.
    ![Nástroj pro kopírování – volba výstupní soubor nebo složku](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. Na **nastavení formátu souboru** stránky, zkontrolujte nastavení a klikněte na tlačítko **Další**. Jedním z dalších možností je přidat hlavičku do výstupního souboru. Pokud tuto možnost vyberete, se přidá řádek záhlaví s názvy sloupců ze schématu zdroje. Při zobrazení schémat pro zdroj, můžete změnit výchozí názvy sloupců. Například můžete změnit na první sloupec na křestní jméno a příjmení druhý sloupec. Potom výstupní soubor je vytvořen s záhlaví s těmito názvy jako názvy sloupců. 
    ![Nástroj pro kopírování – nastavení formátu souboru pro cíl](media/data-factory-azure-blob-connector/file-format-destination.png)
12. Na **nastavení výkonu** stránky, ujistěte se, že **cloudu jednotky** a **paralelní kopie** jsou nastaveny na **automaticky**a klikněte na tlačítko Další. Podrobnosti o těchto nastaveních najdete v tématu [Průvodce laděním a výkonem aktivity kopírování](data-factory-copy-activity-performance.md#parallel-copy).
    ![Nástroj pro kopírování – nastavení výkonu](media/data-factory-azure-blob-connector/copy-performance-settings.png) 
14. Na **Souhrn** stránce zkontrolujte všechna nastavení (Vlastnosti úlohy, nastavení pro zdroj a cíl a zkopírujte nastavení) a klikněte na tlačítko **Další**.
    ![Nástroj pro kopírování – stránka souhrnu](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Na stránce **Souhrn** zkontrolujte informace a klikněte na **Dokončit**. Průvodce v objektu pro vytváření dat (ze kterého jste průvodce kopírováním spustili) vytvoří dvě propojené služby, dvě datové sady (vstupní a výstupní) a jeden kanál.
    ![Nástroj pro kopírování – stránka nasazení](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>Začněte monitorovat kanál (úlohu kopírování)

1. Klikněte na odkaz `Click here to monitor copy pipeline` na **nasazení** stránky. 
2. Měli byste vidět **monitorovat a spravovat aplikace** na samostatné kartě.  ![Monitorování a Správa aplikace](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Změnit **start** čas v horní části `04/19/2017` a **end** uvedení na `04/27/2017`a potom klikněte na tlačítko **použít**. 
4. Uvidíte pět oken aktivity v **okna aktivit** seznamu. **WindowStart** časy by mělo zahrnovat všechny dny od začátku profilace do kanálu koncovým časem. 
5. Klikněte na tlačítko **aktualizovat** tlačítko pro **okna aktivit** seznamu několikrát, dokud se nezobrazí stav všechna okna aktivity je nastaven na hodnotu Připraveno. 
6. Nyní ověřte, že výstupní soubory jsou vygenerovány ve výstupní složce adfblobconnector kontejneru. Měli byste vidět následující strukturu složek ve výstupní složce: 
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25    
    ```
Podrobné informace o monitorování a Správa datových továren najdete v tématu [monitorování a Správa kanálu služby Data Factory](data-factory-monitor-manage-app.md) článku. 
 
### <a name="data-factory-entities"></a>Entit datové továrny
Nyní přejděte zpět na kartu s Domovská stránka datové továrny. Všimněte si, že jsou dvě propojené služby, dvě datové sady a jeden kanál v datové továrně nyní. 

![Domovská stránka s entitami](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Klikněte na tlačítko **vytvořit a nasadit** ke spuštění editoru služby Data Factory. 

![Data Factory Editor](media/data-factory-azure-blob-connector/data-factory-editor.png)

Zobrazí se následující entity služby Data Factory ve službě data factory: 

 - Dvě propojené služby. Jednu pro zdroj a druhou pro cíl. Propojené služby odkazují na stejný účet Azure Storage v tomto názorném postupu. 
 - Dvě datové sady. Vstupní datová sada a výstupní datovou sadu. V tomto podrobném návodu oba použít stejný kontejner objektů blob ale odkazují do různých složek (vstup a výstup).
 - Kanál. Kanálu obsahujícího aktivitu kopírování, která používá ke kopírování dat z umístění služby objektů blob v Azure do jiného umístění objektu blob Azure blob zdroje a jímky objektu blob. 

Následující části obsahují další informace o těchto entitách. 

#### <a name="linked-services"></a>Propojené služby
Měli byste vidět dvě propojené služby. Jednu pro zdroj a druhou pro cíl. V tomto návodu vypadat obě definice stejné s výjimkou názvů. **Typ** propojené služby je nastavena na **AzureStorage**. Nejdůležitější vlastnost definici propojené služby **connectionString**, které služby Data Factory používá pro připojení k účtu Azure Storage za běhu. Ignorujte vlastnost hubName v definici. 

##### <a name="source-blob-storage-linked-service"></a>Zdrojový objekt blob propojenou službu storage
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

##### <a name="destination-blob-storage-linked-service"></a>Cílové objektů blob úložiště, propojené služby

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

Další informace o propojenou službu Azure Storage najdete v tématu [vlastnostem propojených služeb](#linked-service-properties) oddílu. 

#### <a name="datasets"></a>Datové sady
Existují dvě datové sady: vstupní datovou sadu a výstupní datovou sadu. Typ datové sady je nastaven na **AzureBlob** pro obojí. 

Vstupní datová sada odkazuje na **vstupní** složky **adfblobconnector** kontejner objektů blob. **Externí** je nastavena na **true** pro tuto datovou sadu jako data není vytvořený kanál s aktivitou kopírování, která přebírá tuto datovou sadu jako vstup. 

Výstupní datová sada odkazuje **výstup** složky stejný kontejner objektů blob. Výstupní datová sada také používá rok, měsíc a den **SliceStart** systémová proměnná dynamicky vyhodnotit cestu k výstupnímu souboru. Seznam funkcí a systémové proměnné podporovaných službou Data Factory najdete v tématu [funkce Data Factory a systémové proměnné](data-factory-functions-variables.md). **Externí** je nastavena na **false** (výchozí hodnota) protože tuto datovou sadu vytváří kanál. 

Další informace o vlastnostech podporovaných datovou sadu objektů Blob v Azure najdete v tématu [vlastnosti datové sady](#dataset-properties) oddílu.

##### <a name="input-dataset"></a>Vstupní datová sada

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

##### <a name="output-dataset"></a>Výstupní datová sada

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
Tento kanál obsahuje pouze jednu aktivitu. **Typ** aktivity nastavená na **kopírování**.  Ve vlastnostech typu aktivity jsou dva oddíly, jednu pro zdroj a druhou pro jímku. Typ zdroje je nastavený na **BlobSource** jako aktivita kopíruje data z úložiště objektů blob. Typ jímky je nastavený na **BlobSink** jako aktivita kopírování dat do úložiště objektů blob. Aktivita kopírování používá InputDataset z4y jako vstup a OutputDataset z4y jako výstup. 

Další informace o vlastnostech podporovaných BlobSource a BlobSink najdete v tématu [vlastnosti aktivity kopírování](#copy-activity-properties) oddílu. 

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
Následující příklady popisují ukázkový JSON definice, které můžete použít k vytvoření kanálu pomocí [webu Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Zobrazí se kopírování dat do a z Azure Blob Storage a Azure SQL Database. Nicméně je možné zkopírovat data **přímo** z libovolného zdroje do libovolné jímky uvedeno [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivit kopírování ve službě Azure Data Factory.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>Příklad JSON: Kopírování dat z úložiště objektů Blob do služby SQL Database
Následující příklad ukazuje:

1. Propojené služby typu [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Propojené služby typu [AzureStorage](#linked-service-properties).
3. Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](#copy-activity-properties) a [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties).

Ukázka zkopíruje časových řad dat z Azure blob do Azure SQL tabulka každou hodinu. Vlastnostech JSON použitých v tyto ukázky jsou popsány v části podle ukázky.

**Propojená služba Azure SQL:**

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
Azure Data Factory podporuje dva typy služeb propojené služby Azure Storage: **AzureStorage** a **AzureStorageSas**. Pro první z nich zadejte připojovací řetězec, který obsahuje klíč účtu a pro novější verzi, zadejte identifikátor Uri sdíleného přístupového podpisu (SAS). Zobrazit [propojené služby](#linked-service-properties) podrobné informace.  

**Azure vstupní datovou sadou objektů Blob:**

Data je převzata z nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Název složky a cesta k souboru pro tento objekt blob se dynamicky vyhodnocuje podle času spuštění řezu, který se právě zpracovává. Cesta ke složce používá rok, měsíc a den čas spuštění a název souboru používá hodinová část času zahájení. "externí": "PRAVDA" nastavení informuje služby Data Factory, že v tabulce je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

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

Ukázka zkopíruje data do tabulky s názvem "MyTable" ve službě Azure SQL database. Vytvoření tabulky ve službě Azure SQL database s stejný počet sloupců tak, jak očekáváte objektů Blob CSV soubor obsahovat. Nové řádky do tabulky přidají každou hodinu.

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
**Aktivita kopírování v kanálu s Blob zdroje a jímky SQL:**

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **BlobSource** a **jímky** je typ nastaven na **SqlSink**.

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
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>Příklad JSON: Kopírování dat z Azure SQL do objektu Blob Azure
Následující příklad ukazuje:

1. Propojené služby typu [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Propojené služby typu [AzureStorage](#linked-service-properties).
3. Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) a [BlobSink](#copy-activity-properties).

Ukázka zkopíruje data časových řad z tabulky Azure SQL do objektu blob Azure každou hodinu. Vlastnostech JSON použitých v tyto ukázky jsou popsány v části podle ukázky.

**Propojená služba Azure SQL:**

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
Azure Data Factory podporuje dva typy služeb propojené služby Azure Storage: **AzureStorage** a **AzureStorageSas**. Pro první z nich zadejte připojovací řetězec, který obsahuje klíč účtu a pro novější verzi, zadejte identifikátor Uri sdíleného přístupového podpisu (SAS). Zobrazit [propojené služby](#linked-service-properties) podrobné informace.  

**Vstupní datová sada Azure SQL:**

Ukázka předpokládá vytvoříte tabulku "MyTable" ve službě Azure SQL a obsahuje sloupec s názvem "timestampcolumn" pro data časových řad.

Nastavení "externí": "PRAVDA" informuje služby Data Factory, že v tabulce je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

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

**Výstupní datová sada Azure Blob:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob se dynamicky vyhodnocuje na základě doby spuštění řez, který se právě zpracovává. Cesta ke složce používá rok, měsíc, den a části hodin čas spuštění.

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

**Aktivita kopírování v kanálu s SQL zdroje a jímky objektu Blob:**

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **SqlSource** a **jímky** je typ nastaven na **BlobSink**. Zadaná pro dotaz SQL **SqlReaderQuery** vlastnost vybere data za poslední hodinu pro kopírování.

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
> Pokud chcete namapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, najdete v článku [mapování sloupců v datové sadě ve službě Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Zobrazit [výkonem aktivity kopírování & Průvodci optimalizací](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů této ovlivnit výkon přesouvání dat (aktivita kopírování) ve službě Azure Data Factory a různé způsoby, jak optimalizovat.
