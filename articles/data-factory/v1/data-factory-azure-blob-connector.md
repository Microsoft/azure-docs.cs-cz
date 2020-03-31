---
title: Kopírování dat do úložiště objektů blob Azure nebo z ní
description: 'Zjistěte, jak kopírovat data objektů blob v Azure Data Factory. Použijte naši ukázku: Jak zkopírovat data do a z Azure Blob Storage a Azure SQL Database.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: eab332f102b9e39981e2d8ed6e84f73fada87a1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282130"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Kopírování dat do nebo z Azure Blob Storage pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-azure-blob-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-blob-storage.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Konektor úložiště objektů blob Azure ve Verzi 2](../connector-azure-blob-storage.md).


Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat do a z Azure Blob Storage. Vychází z článku [Aktivity přesunu dat,](data-factory-data-movement-activities.md) který představuje obecný přehled přesunu dat s aktivitou kopírování.

## <a name="overview"></a>Přehled
Data z libovolného úložiště dat podporovaného zdroje můžete zkopírovat do úložiště objektů blob Azure nebo z úložiště objektů blob Azure do libovolného podporovaného úložiště dat jímky. Následující tabulka obsahuje seznam úložišť dat podporovaných jako zdroje nebo jímky aktivity kopírování. Můžete například přesunout data **z** databáze SERVERU SQL nebo databáze Azure SQL **do** úložiště objektů blob Azure. A můžete zkopírovat data **z** úložiště objektů blob Azure **do** Azure SQL Data Warehouse nebo kolekce Azure Cosmos DB.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Podporované scénáře
Data z **Azure Blob Storage** můžete zkopírovat do následujících úložišť dat:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Data z následujících úložišť dat můžete zkopírovat **do úložiště objektů blob Azure**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> Aktivita kopírování podporuje kopírování dat z nebo do běžných účtů Azure Storage a hot/cool úložiště objektů blob. Aktivita podporuje **čtení z objektů blob bloku, připojení nebo stránky**, ale podporuje zápis pouze do objektů **BLOB bloku**. Azure Premium Storage není podporována jako jímky, protože je zálohována objekty BLOB stránky.
>
> Aktivita kopírování neodstraní data ze zdroje po úspěšném zkopírování dat do cílového umístění. Pokud potřebujete odstranit zdrojová data po úspěšné kopii, vytvořte [vlastní aktivitu](data-factory-use-custom-activities.md) k odstranění dat a použijte aktivitu v kanálu. Například najdete v tématu [Odstranění objektu blob nebo ukázky složky na GitHubu](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity).

## <a name="get-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data do nebo z úložiště objektů blob Azure pomocí různých nástrojů nebo api.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Tento článek obsahuje [návod](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) pro vytvoření kanálu pro kopírování dat z umístění úložiště objektů blob Azure do jiného umístění úložiště objektů blob Azure. Kurz na vytvoření kanálu pro kopírování dat z úložiště objektů blob Azure do databáze Azure SQL [najdete v tématu Kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

K vytvoření kanálu můžete taky použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Šablona Azure Resource Manager**, Rozhraní **.NET API**a REST **API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aktivity.

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **datovou továrnu**. Továrna dat může obsahovat jeden nebo více kanálů.
2. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat. Například pokud kopírujete data z úložiště objektů blob Azure do databáze Azure SQL, vytvoříte dvě propojené služby, které propojí váš účet úložiště Azure a databázi Azure SQL s vaší továrně dat. Propojené vlastnosti služby, které jsou specifické pro Azure Blob Storage, najdete v tématu [propojené služby vlastnosti](#linked-service-properties) části.
2. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování. V příkladu uvedeném v posledním kroku vytvoříte datovou sadu, která určí kontejner objektů blob a složku, která obsahuje vstupní data. A vytvoříte další datovou sadu k určení tabulky SQL v databázi Azure SQL, která obsahuje data zkopírovaná z úložiště objektů blob. Vlastnosti datové sady, které jsou specifické pro Azure Blob Storage, najdete v tématu [vlastnosti datové sady](#dataset-properties) části.
3. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. V příkladu uvedeném výše použijete Objekt blobSource jako zdroj a SqlSink jako jímku pro aktivitu kopírování. Podobně pokud kopírujete z Azure SQL Database do azure blob storage, použijete sqlsource a blobsink v aktivitě kopírování. Informace o vlastnostech aktivity kopírování, které jsou specifické pro Azure Blob Storage, najdete v tématu [kopírování vlastností aktivity](#copy-activity-properties) části. Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímku, klikněte na odkaz v předchozí části úložiště dat.

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory (propojené služby, datové sady a kanál). Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON.  Ukázky s definicemi JSON pro entity Data Factory, které se používají ke kopírování dat do/z úložiště objektů blob Azure, najdete v části [příklady JSON](#json-examples-for-copying-data-to-and-from-blob-storage  ) v tomto článku.

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Datové továrny specifické pro azure blob storage.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb
Existují dva typy propojených služeb, které můžete použít k propojení Azure Storage s totodatovou továrně dat Azure. Jsou: **AzureStorage** propojené služby a **AzureStorageSas** propojené služby. Propojená služba Azure Storage poskytuje datové továrně globální přístup k Úložišti Azure. Vzhledem k tomu, Azure Storage SAS (sdílený přístupový podpis) propojené služby poskytuje datové továrny s omezený/časově vázaný přístup k úložišti Azure. Mezi těmito dvěma propojenými službami nejsou žádné jiné rozdíly. Vyberte si propojenou službu, která vyhovuje vašim potřebám. V následujících částech jsou uvedeny další podrobnosti o těchto dvou propojených službách.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Vlastnosti datové sady
Chcete-li zadat datovou sadu představující vstupní nebo výstupní data v úložišti objektů blob Azure, nastavte vlastnost type datové sady na: **AzureBlob**. Nastavte vlastnost **linkedServiceName** datové sady na název propojené služby Azure Storage nebo Azure Storage SAS.  Vlastnosti typu datové sady určují **kontejner objektů blob** a **složku** v úložišti objektů blob.

Úplný seznam oddílů JSON & vlastnosti, které jsou k dispozici pro definování datových sad, naleznete v článku [Vytváření datových sad.](data-factory-create-datasets.md) Oddíly, jako je struktura, dostupnost a zásady datové sady JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure blob, Tabulka Azure atd.).

Factory podporuje následující cls kompatibilní s .NET založené hodnoty typu pro poskytování informací o typu v "struktura" pro zdroje dat schématu při čtení, jako je objekt blob Azure: Int16, Int32, Int64, Single, Double, decimal, Byte[], Bool, String, Guid, Datetime, Datetimeoffset, Timespan. Data Factory automaticky provádí převody typů při přesunu dat ze zdrojového úložiště dat do úložiště dat jímky.

Sekce **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění, formátu atd., dat v úložišti dat. Oddíl typeProperties pro datovou sadu typu **AzureBlob** dataset má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Cesta ke kontejneru a složce v úložišti objektů blob. Příklad: myblobcontainer\myblobfolder\ |Ano |
| fileName |Název objektu blob. název_souboru je volitelný a rozlišuje malá a velká písmena.<br/><br/>Pokud zadáte název souboru, aktivita (včetně kopírování) funguje na konkrétním objektu Blob.<br/><br/>Když fileName není zadán, Copy zahrnuje všechny objekty BLOB ve složceCesta pro vstupní datovou sadu.<br/><br/>Pokud **fileName** není zadán pro výstupní datovou sadu a **preserveHierarchy** není zadán v jímce aktivity, `Data.<Guid>.txt` název generovaného souboru by byl v následujícím následujícím formátu: (například: : Data.0a405f8a-93ff-4c6f-b3be-f69616f7a.txt |Ne |
| partitionedBy |partitionedBy je volitelná vlastnost. Můžete ji použít k určení dynamické složkyCesta a název souboru pro data časových řad. Například folderPath lze parametrizovat pro každou hodinu dat. Podrobnosti a příklady naleznete [v části Using partitionedBy](#using-partitionedby-property) property section. |Ne |
| formát | Podporovány jsou následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte vlastnost **type** ve formátu na jednu z těchto hodnot. Další informace naleznete v části [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), Formát [Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), Formát [orků](data-factory-supported-file-and-compression-formats.md#orc-format)a [Formát parket.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Pokud chcete **kopírovat soubory jako -je** mezi obchody založenými na souborech (binární kopie), přeskočte oddíl formátu v definicích vstupní i výstupní datové sady. |Ne |
| komprese | Určete typ a úroveň komprese dat. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**a **ZipDeflate**. Podporované úrovně jsou: **Optimální** a **nejrychlejší**. Další informace najdete [v tématu Soubory a kompresní formáty v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

### <a name="using-partitionedby-property"></a>Použití vlastnosti partitionedBy
Jak je uvedeno v předchozí části, můžete zadat dynamickou složkuPath a název souboru pro data časových řad s **vlastností partitionedBy,** [funkcemi Data Factory a systémovými proměnnými](data-factory-functions-variables.md).

Další informace o datových sadách časových řad, plánování a řezech naleznete v [tématu Vytváření datových sad](data-factory-create-datasets.md) a [plánování článků & spuštění.](data-factory-scheduling-and-execution.md)

#### <a name="sample-1"></a>Ukázka 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

V tomto příkladu je {Slice} nahrazen hodnotou systémové proměnné Factory SliceStart v zadaném formátu (YYYYMMDDHH). ŘezStart odkazuje na čas zahájení řezu. SložkaPath se liší pro každý řez. Příklad: wikidatagateway/wikisampledataout/2014100103 nebo wikidatagateway/wikisampledataout/2014100104

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

V tomto příkladu jsou rok, měsíc, den a čas SliceStart extrahovány do samostatných proměnných, které jsou používány vlastnostmi folderPath a fileName.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) Vlastnosti, jako je název, popis, vstupní a výstupní datové sady a zásady jsou k dispozici pro všechny typy aktivit. Vzhledem k tomu, vlastnosti, které jsou k dispozici v **typeProperties** části aktivity se liší s každým typem aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a propadů. Pokud přesouváte data z úložiště objektů blob Azure, nastavíte typ zdroje v aktivitě kopírování na **Objekt BlobSource**. Podobně pokud přesouváte data do úložiště objektů blob Azure, nastavíte typ jímky v aktivitě kopírování na **BlobSink**. Tato část obsahuje seznam vlastností podporovaných objekty BlobSource a BlobSink.

**Objekt BlobSource** podporuje následující vlastnosti v části **typeProperties:**

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| Rekurzivní |Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. |True (výchozí hodnota), False |Ne |

**Objekt BlobSink** podporuje následující oddíl **vlastnosti typeProperties:**

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| copyBehavior |Definuje chování kopírování, pokud je zdrojem Objekt BlobSource nebo FileSystem. |<b>Zachovat hierarchii</b>: zachová hierarchii souborů v cílové složce. Relativní cesta zdrojového souboru ke zdrojové složce je shodná s relativní cestou cílového souboru do cílové složky.<br/><br/><b>Sloučení :</b>všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generovaný název. <br/><br/><b>MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru/objektu blob, sloučený název souboru by byl zadaný název; v opačném případě by byl automaticky generovaný název souboru. |Ne |

**BlobSource** také podporuje tyto dvě vlastnosti pro zpětnou kompatibilitu.

* **treatEmptyAsNull**: Určuje, zda má být nulový nebo prázdný řetězec považován za nulovou hodnotu.
* **skipHeaderLineCount** - Určuje, kolik řádků je třeba přeskočit. Platí pouze v případě, že vstupní datová sada používá TextFormat.

Podobně **BlobSink** podporuje následující vlastnost pro zpětnou kompatibilitu.

* **blobWriterAddHeader**: Určuje, zda má být při zápisu do výstupní datové sady přidat záhlaví definic sloupců.

Datové sady nyní podporují následující vlastnosti, které implementují stejné funkce: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

Následující tabulka obsahuje pokyny k použití nové vlastnosti datové sady namísto těchto vlastností zdroj/jímka objektu blob.

| Kopírovat aktivitu, vlastnost | Vlastnost Datové sady |
|:--- |:--- |
| skipHeaderLineCount na objektu BlobSource |skipLineCount a firstRowAsHeader. Řádky jsou nejprve přeskočeny a první řádek se čte jako záhlaví. |
| treatEmptyAsNull na BlobSource |treatEmptyAsNull na vstupní datové sadě |
| objekt blobWriterAddHeader v objektu BlobSink |firstRowAsHeader ve výstupní datové sadě |

Podrobné informace o těchto vlastnostech naleznete v části [Zadání formátu Textu.](data-factory-supported-file-and-compression-formats.md#text-format)

### <a name="recursive-and-copybehavior-examples"></a>rekurzivní a copyBehavior příklady
Tato část popisuje výsledné chování operace Copy pro různé kombinace rekurzivních a copyBehavior hodnot.

| Rekurzivní | copyBehavior | Výsledné chování |
| --- | --- | --- |
| true |zachovat hierarchii |Pro zdrojovou složku Folder1 s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5<br/><br/>Cílová složka Folder1 je vytvořena se stejnou strukturou jako zdroj<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5. |
| true |sloučení hierarchie |Pro zdrojovou složku Folder1 s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5<br/><br/>cílová složka Folder1 je vytvořena s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název souboru1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název pro Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název souboru3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název souboru4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název souboru5 |
| true |mergeFiles |Pro zdrojovou složku Folder1 s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5<br/><br/>cílová složka Folder1 je vytvořena s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 obsah jsou sloučeny do jednoho souboru s automaticky generovaným názvem souboru |
| false (nepravda) |zachovat hierarchii |Pro zdrojovou složku Folder1 s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5<br/><br/>Cílová složka Folder1 je vytvořena s následující strukturou<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/><br/><br/>Podsložka1 s File3, File4 a File5 nejsou zachyceny. |
| false (nepravda) |sloučení hierarchie |Pro zdrojovou složku Folder1 s následující strukturou:<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5<br/><br/>Cílová složka Folder1 je vytvořena s následující strukturou<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název souboru1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název pro Soubor2<br/><br/><br/>Podsložka1 s File3, File4 a File5 nejsou zachyceny. |
| false (nepravda) |mergeFiles |Pro zdrojovou složku Folder1 s následující strukturou:<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5<br/><br/>Cílová složka Folder1 je vytvořena s následující strukturou<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Obsah File1 + File2 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. automaticky generovaný název souboru1<br/><br/>Podsložka1 s File3, File4 a File5 nejsou zachyceny. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Návod: Kopírování dat do úložiště objektů Blob nebo z nich pomocí Průvodce kopírováním
Podívejme se, jak rychle kopírovat data do nebo z úložiště objektů blob Azure. V tomto návodu úložiště zdrojových i cílových dat typu: Azure Blob Storage. Kanál v tomto návodu zkopíruje data ze složky do jiné složky ve stejném kontejneru objektů blob. Tento návod je záměrně jednoduché zobrazit nastavení nebo vlastnosti při použití úložiště objektů blob jako zdroj nebo jímky.

### <a name="prerequisites"></a>Požadavky
1. Pokud ho ještě nemáte, vytvořte **účet úložiště Azure** pro obecné účely. Úložiště objektů blob se používá jako **zdrojové** i **cílové** úložiště dat v tomto návodu. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md), kde najdete kroky pro jeho vytvoření.
2. Vytvořte kontejner objektů blob s názvem **adfblobconnector** v účtu úložiště.
4. Vytvořte složku s názvem **input** v kontejneru **adfblobconnector.**
5. Vytvoření souboru s názvem **emp.txt** s následujícím obsahem a jeho nahrání do **vstupní** složky pomocí nástrojů, jako je [Průzkumník úložiště Azure](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```

### <a name="create-the-data-factory"></a>Vytvoření datové továrny
1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Klikněte na **Vytvořit prostředek** v levém horním rohu, klikněte na **Intelligence + analytics**a klikněte na **Data Factory**.
3. V podokně **Nová továrna dat:**  
    1. Zadejte **ADFBlobConnectorDF** pro **název**. Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí `*Data factory name “ADFBlobConnectorDF” is not available`chyba: , změňte název datové továrny (například yournameADFBlobConnectorDF) a zkuste vytvořit znovu. V tématu [Objekty pro vytváření dat – pravidla pojmenování](data-factory-naming-rules.md) najdete pravidla pojmenování artefaktů služby Data Factory.
    2. Vyberte **předplatné**Azure .
    3. V části Skupina prostředků vyberte **Použít existující** k výběru existující skupiny prostředků (nebo) vyberte **Vytvořit nový,** chcete-li zadat název skupiny prostředků.
    4. Vyberte **umístění** pro příslušný objekt pro vytváření dat.
    5. Zaškrtněte políčko **Připnout na řídicí panel** v dolní části okna.
    6. Klikněte na **Vytvořit**.
3. Po dokončení vytváření se zobrazí okno **Data Factory,** jak ![je znázorněno na následujícím obrázku: Domovská stránka datové továrny](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Průvodce kopírováním
1. Na domovské stránce Data Factory klikněte na dlaždici **Kopírovat data** a spusťte **Průvodce kopírováním dat** na samostatné kartě.  

    > [!NOTE]
    > Pokud zjistíte, že webový prohlížeč je přilepená na "Autorizace ...", zakázat / zrušit zaškrtnutí **blokovat cookies třetích stran a** nastavení dat webu (nebo) ponechat povoleno a vytvořit výjimku pro **login.microsoftonline.com** a zkuste spustit průvodce znovu.
2. Na stránce **Vlastnosti**:
    1. Zadejte **CopyPipeline** pro **název úkolu**. Název úkolu je název kanálu v datové továrně.
    2. Zadejte **popis** úkolu (nepovinné).
    3. U **kadence úloh nebo plánu úloh**ponechte možnost Spustit pravidelně podle **plánu.** Pokud chcete tuto úlohu spustit pouze jednou namísto opakovaného spuštění podle plánu, vyberte **spustit jednou nyní**. Pokud vyberete **možnost Spustit jednou nyní,** vytvoří se jednorázový [kanál.](data-factory-create-pipelines.md#onetime-pipeline)
    4. Zachovat nastavení pro **opakovaný vzorek**. Tato úloha se spouští denně mezi počátečním a koncovým časem, který zadáte v dalším kroku.
    5. Změňte **čas počátečního data** na **04/21/2017**.
    6. Změňte **čas koncového data** na **04/25/2017**. Místo procházení kalendáře můžete zadat datum.
    8. Klikněte na **Další**.
        ![Nástroj pro kopírování – stránka Vlastnosti](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png)
3. Na stránce **Source data store** (Zdrojové úložiště dat) klikněte na dlaždici **Azure Blob Storage**. Tato stránka slouží k zadání zdrojového úložiště dat pro úlohu kopírování. Můžete použít existující propojenou službu úložiště dat nebo zadat nové úložiště dat. Chcete-li použít existující propojenou službu, vyberte **možnost EXISTUJÍCÍ PROPOJENÉ SLUŽBY** a vyberte správnou propojenou službu.
    ![Nástroj pro kopírování – stránka zdrojového úložiště dat](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. Na stránce **Specify the Azure Blob storage account** (Zadejte účet Azure Blob Storage):
    1. Ponechte si automaticky generovaný název **názvu připojení**. Název připojení je název propojené služby typu: Azure Storage.
    2. Ujistěte se, že je pro položku **Metoda výběru účtu** vybrána možnost **Z předplatných Azure**.
    3. Vyberte předplatné Azure nebo si ponechte **možnost Vybrat vše** pro předplatné **Azure**.
    4. V seznamu účtů úložiště Azure dostupných ve zvoleném předplatném vyberte požadovaný **účet úložiště Azure**. Nastavení účtu úložiště můžete také zadat ručně tak, že pro **metodu výběru účtu**vyberete ručně možnost **Zadat** .
    5. Klikněte na **Další**.  
        ![Nástroj pro kopírování – zadání účtu Azure Blob Storage](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. Na stránce **Choose the input file or folder** (Zvolte vstupní soubor nebo složku):
    1. Poklepejte na **adfblobcontainer**.
    2. Vyberte **vstup**a klepněte na **tlačítko Vybrat**. V tomto návodu vyberete vstupní složku. Místo toho můžete také vybrat soubor emp.txt ve složce.
        ![Nástroj pro kopírování – volba vstupního souboru nebo složky](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. Na stránce **Vybrat vstupní soubor nebo složku:**
    1. Zkontrolujte, zda je **soubor nebo složka** nastavena na **adfblobconnector/input**. Pokud jsou soubory v podsložkách, například 2017/04/01, 2017/04/02 a tak dále, zadejte adfblobconnector/input/{year}/{month}/{day} pro soubor nebo složku. Když stisknete klávesu TAB mimo textové pole, zobrazí se tři rozevírací seznamy pro výběr formátů pro rok (yyyy), měsíc (MM) a den (dd).
    2. Nenastavujte **kopírovat soubor rekurzivně**. Tuto možnost vyberte, chcete-li rekurzivně procházet složky pro soubory, které mají být zkopírovány do cíle.
    3. Nepoužívejte **binární možnost kopírování.** Tuto možnost vyberte, chcete-li provést binární kopii zdrojového souboru do cíle. Nevybírejte pro tento návod, abyste viděli další možnosti na dalších stránkách.
    4. Zkontrolujte, zda je **typ komprese** nastaven na **možnost Žádný**. Vyberte hodnotu pro tuto možnost, pokud jsou zdrojové soubory komprimovány v jednom z podporovaných formátů.
    5. Klikněte na **Další**.
    ![Nástroj pro kopírování – volba vstupního souboru nebo složky](./media/data-factory-azure-blob-connector/chose-input-file-folder.png)
7. Na stránce **Nastavení formátu souboru** jsou uvedeny oddělovače a schéma, které je automaticky zjištěno průvodcem při analýze souboru.
    1. Potvrďte následující možnosti:  
        a. **Formát souboru** je nastaven na **formát Text**. V rozevíracím seznamu můžete zobrazit všechny podporované formáty. Například: JSON, Avro, ORC, Parkety.
       b. **Oddělovač sloupců** je `Comma (,)`nastaven na . V rozevíracím seznamu se zobrazí další oddělovače sloupců podporované sadou dat. Můžete také zadat vlastní oddělovač.
       c. **Oddělovač řádků** je `Carriage Return + Line feed (\r\n)`nastaven na . V rozevíracím seznamu se zobrazí další oddělovače řádků podporované sadou dat. Můžete také zadat vlastní oddělovač.
       d. Počet **přeskakovat řádek** je nastaven na **0**. Pokud chcete přeskočit několik řádků v horní části souboru, zadejte číslo zde.
       e. **První řádek dat obsahuje názvy sloupců** není nastavena. Pokud zdrojové soubory obsahují názvy sloupců v prvním řádku, vyberte tuto možnost.
       f. Je **nastavena hodnota prázdného sloupce jako null.**
    2. Chcete-li zobrazit rozšířené možnosti, zobrazíte možnost **Upřesnit.**
    3. V dolní části stránky se podívejte na **náhled** dat ze souboru emp.txt.
    4. Kliknutím na kartu **SCHEMA** v dolní části zobrazíte schéma, které průvodce kopírováním odvodil při pohledu na data ve zdrojovém souboru.
    5. Po zkontrolování oddělovačů a náhledu dat klikněte na **Další**.
    ![Nástroj pro kopírování – nastavení formátu souboru](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)
8. Na **stránce Cílové úložiště dat**vyberte **Azure Blob Storage**a klikněte na **Další**. Používáte Azure Blob Storage jako zdrojové i cílové úložiště dat v tomto návodu.  
    ![Nástroj kopírovat – výběr cílového úložiště dat](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. Na **stránce Určení účtu úložiště objektů blob Azure:**  
    1. Zadejte **AzureStorageLinkedService** pro pole **Název připojení.**
    2. Ujistěte se, že je pro položku **Metoda výběru účtu** vybrána možnost **Z předplatných Azure**.
    3. Vyberte **předplatné**Azure .
    4. Vyberte svůj účet úložiště Azure.
    5. Klikněte na **Další**.
10. Na stránce **Vybrat výstupní soubor nebo složku:**  
    1. zadejte **cestu složky** jako **adfblobconnector/output/{year}/{month}/{day}**. Zadejte **TAB**.
    1. Pro **rok**vyberte **yyyy**.
    1. Pro **měsíc**potvrďte, že je nastavena na **MM**.
    1. Pro **den**, potvrďte, že je nastavena na **dd**.
    1. Zkontrolujte, zda je **typ komprese** nastaven na **možnost Žádný**.
    1. Zkontrolujte, zda je **chování kopírování** nastaveno na **sloučit soubory**. Pokud výstupní soubor se stejným názvem již existuje, nový obsah je přidán do stejného souboru na konci.
    1. Klikněte na **Další**.
       ![Nástroj kopírování – volba výstupního souboru nebo složky](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. Na stránce **Nastavení formátu souboru** zkontrolujte nastavení a klepněte na tlačítko **Další**. Jednou z dalších možností je přidání záhlaví do výstupního souboru. Pokud vyberete tuto možnost, přidá se řádek záhlaví s názvy sloupců ze schématu zdroje. Při zobrazení schématu zdroje můžete přejmenovat výchozí názvy sloupců. Můžete například změnit první sloupec na Křestní jméno a Druhý sloupec na Příjmení. Poté je výstupní soubor generován se záhlavím s těmito názvy jako názvy sloupců.
    ![Nástroj kopírovat – nastavení formátu souboru pro cíl](media/data-factory-azure-blob-connector/file-format-destination.png)
12. Na stránce **Nastavení výkonu** zkontrolujte, zda jsou **cloudové jednotky** a paralelní **kopie** nastaveny na **automaticky**, a klepněte na tlačítko Další. Podrobnosti o těchto nastaveních naleznete v [tématu Kopírování výkonu aktivity a průvodce laděním](data-factory-copy-activity-performance.md#parallel-copy).
    ![Nástroj kopírování – nastavení výkonu](media/data-factory-azure-blob-connector/copy-performance-settings.png)
14. Na stránce **Souhrn** zkontrolujte všechna nastavení (vlastnosti úlohy, nastavení pro zdroj a cíl a nastavení kopírování) a klepněte na tlačítko **Další**.
    ![Nástroj kopírování – stránka Souhrn](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Na stránce **Souhrn** zkontrolujte informace a klikněte na **Dokončit**. Průvodce v objektu pro vytváření dat (ze kterého jste průvodce kopírováním spustili) vytvoří dvě propojené služby, dvě datové sady (vstupní a výstupní) a jeden kanál.
    ![Nástroj kopírování – stránka nasazení](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>Sledování kanálu (úloha kopírování)

1. Klikněte `Click here to monitor copy pipeline` na odkaz na stránce **Nasazení.**
2. Aplikace Sledování **a správa by** se měla zobrazit na samostatné kartě.  ![Sledování a správa aplikace](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Změňte **počáteční** čas v `04/19/2017` horním a `04/27/2017` **koncovém** čase na a klepněte na tlačítko **Použít**.
4. V seznamu **ACTIVITY WINDOWS** by se mělo zobrazit pět oken aktivit. **WindowStart** časy by měly zahrnovat všechny dny od začátku kanálu do konce kanálu časy.
5. Několikrát klikněte na tlačítko **Aktualizovat** pro seznam **AKTIVITWindows,** dokud neuvidíte stav všech oken aktivit, které jsou nastaveny na Připraveno.
6. Nyní ověřte, zda jsou výstupní soubory generovány ve výstupní složce kontejneru adfblobconnector. Ve výstupní složce byste měli vidět následující strukturu složek:
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```
   Podrobné informace o monitorování a správě datových továren najdete v článku [Sledování a správa kanálu datové továrny.](data-factory-monitor-manage-app.md)

### <a name="data-factory-entities"></a>Entity data factory
Nyní přepněte zpět na kartu s domovskou stránkou Data Factory. Všimněte si, že existují dvě propojené služby, dvě datové sady a jeden kanál v datové továrně nyní.

![Domovská stránka datové továrny s entitami](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Kliknutím na **Author a nasazením** spusťte Editor datových tocích.

![Data Factory Editor](media/data-factory-azure-blob-connector/data-factory-editor.png)

V datové továrně byste měli vidět následující entity Data Factory:

- Dvě propojené služby. Jeden pro zdroj a druhý pro cíl. Obě propojené služby odkazují na stejný účet Azure Storage v tomto návodu.
- Dvě datové sady. Vstupní datová sada a výstupní datová sada. V tomto návodu oba používají stejný kontejner objektů blob, ale odkazují na různé složky (vstup a výstup).
- Potrubí. Kanál obsahuje aktivitu kopírování, která používá zdroj objektu blob a jímka objektů blob ke kopírování dat z umístění objektu blob Azure do jiného umístění objektu blob Azure.

Následující části obsahují další informace o těchto entitách.

#### <a name="linked-services"></a>Propojené služby
Měli byste vidět dvě propojené služby. Jeden pro zdroj a druhý pro cíl. V tomto návodu vypadají obě definice stejně, s výjimkou názvů. **Typ** propojené služby je nastavena na **AzureStorage**. Nejdůležitější vlastností definice propojené služby je **connectionString**, který používá Data Factory pro připojení k účtu Azure Storage za běhu. Ignorujte vlastnost hubName v definici.

##### <a name="source-blob-storage-linked-service"></a>Propojená služba zdrojového úložiště objektů blob
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

##### <a name="destination-blob-storage-linked-service"></a>Cílová služba propojené úložiště objektů blob

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

Další informace o propojené službě Azure Storage najdete v tématu Propojené [služby vlastnosti](#linked-service-properties) části.

#### <a name="datasets"></a>Datové sady
Existují dvě datové sady: vstupní datová sada a výstupní datová sada. Typ datové sady je nastavena na **AzureBlob** pro oba.

Vstupní datová sada odkazuje na **vstupní** složku kontejneru objektů blob **adfblobconnector.** **Externí** vlastnost je nastavena na **hodnotu true** pro tuto datovou sadu, protože data nejsou vytvářena kanálem s aktivitou kopírování, která přebírá tuto datovou sadu jako vstup.

Výstupní datová sada odkazuje na **výstupní** složku stejného kontejneru objektů blob. Výstupní datová sada také používá rok, měsíc a den systémové proměnné **SliceStart** k dynamickému vyhodnocení cesty pro výstupní soubor. Seznam funkcí a systémových proměnných podporovaných data factory, naleznete [v tématu Data Factory funkce a systémové proměnné](data-factory-functions-variables.md). **Externí** vlastnost je nastavena na **false** (výchozí hodnota), protože tato datová sada je vytvářena kanálem.

Další informace o vlastnostech podporovaných datovou sadou Objektů blob Azure najdete v tématu [Vlastnosti datové sady.](#dataset-properties)

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
Potrubí má pouze jednu aktivitu. **Typ** aktivity je nastaven na **Kopírovat**. Ve vlastnostech typu pro aktivitu existují dvě části, jeden pro zdroj a druhý pro jímky. Typ zdroje je nastavena na **Objekt BlobSource** jako aktivita je kopírování dat z úložiště objektů blob. Typ jímky je nastavena na **Objekt blobSink** jako aktivity kopírování dat do úložiště objektů blob. Aktivita kopírování přebírá InputDataset-z4y jako vstup a OutputDataset-z4y jako výstup.

Další informace o vlastnostech podporovaných objekty BlobSource a BlobSink naleznete v části [Kopírování vlastností aktivity.](#copy-activity-properties)

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

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>Příklady JSON pro kopírování dat do úložiště objektů Blob a z nich
Následující příklady poskytují ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí [Sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo Azure [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazují, jak kopírovat data do a z Azure Blob Storage a Azure SQL Database. Data však můžete zkopírovat **přímo** z libovolného zdroje do libovolného [jímky uvedené zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>Příklad JSON: Kopírování dat z úložiště objektů Blob do databáze SQL
Následující ukázka ukazuje:

1. Propojená služba typu [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Propojená služba typu [AzureStorage](#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou Copy, která používá [objekty BlobSource](#copy-activity-properties) a [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties).

Ukázka zkopíruje data časových řad z objektu blob Azure do tabulky Azure SQL každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

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
**Propojené služby Azure Storage:**

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
Azure Data Factory podporuje dva typy propojených služeb Azure Storage: **AzureStorage** a **AzureStorageSas**. Pro první zadáte připojovací řetězec, který obsahuje klíč účtu a pro pozdější, zadáte identifikátor Uri sdíleného přístupového podpisu (SAS). Podrobnosti najdete v části [Propojené služby.](#linked-service-properties)

**Vstupní datová sada objektu Blob Azure:**

Data se zírají z nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce a název souboru pro objekt blob jsou dynamicky vyhodnocovány na základě počátečního času zpracovávaného řezu. Cesta ke složce používá rok, měsíc a den část počátečního času a název souboru používá hodinovou část počátečního času. "externí": nastavení "true" informuje Data Factory, že tabulka je externí pro datovou továrnu a není vyráběna aktivitou v datové továrně.

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
**Výstupní datová sada Azure SQL:**

Ukázka zkopíruje data do tabulky s názvem "MyTable" v databázi Azure SQL. Vytvořte tabulku v databázi Azure SQL se stejným počtem sloupců, jako uočekáváte, že soubor BLOB CSV bude obsahovat. Nové řádky jsou přidávány do tabulky každou hodinu.

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
**Aktivita kopírování v kanálu se zdrojem objektů blob a jímkou SQL:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánována na každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **objekt BlobSource** a typ **jímky** je nastaven na **sqlsink**.

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
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>Příklad JSON: Kopírování dat z Azure SQL do objektu blob Azure
Následující ukázka ukazuje:

1. Propojená služba typu [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Propojená služba typu [AzureStorage](#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou Copy, která používá [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) a [BlobSink](#copy-activity-properties).

Ukázka zkopíruje data časových řad z tabulky Azure SQL do objektu blob Azure každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

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
**Propojené služby Azure Storage:**

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
Azure Data Factory podporuje dva typy propojených služeb Azure Storage: **AzureStorage** a **AzureStorageSas**. Pro první zadáte připojovací řetězec, který obsahuje klíč účtu a pro pozdější, zadáte identifikátor Uri sdíleného přístupového podpisu (SAS). Podrobnosti najdete v části [Propojené služby.](#linked-service-properties)

**Vstupní datová sada Azure SQL:**

Ukázka předpokládá, že jste vytvořili tabulku "MyTable" v Azure SQL a obsahuje sloupec s názvem "sloupec časového razítka" pro data časových řad.

Nastavení "externí": "true" informuje službu Data Factory, že tabulka je externí pro datovou továrnu a není vytvářena aktivitou v datové továrně.

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

**Výstupní datová sada objektu Blob Azure:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části počátečního času rok, měsíc, den a hodiny.

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
          "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
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

**Aktivita kopírování v kanálu se zdrojem SQL a jímkou blob:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánována na každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **SqlSource** a typ **jímky** je nastaven na **Objekt blobSink**. Dotaz SQL zadaný pro vlastnost **SqlReaderQuery** vybere data za poslední hodinu ke kopírování.

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
> Pokud chcete mapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, přečtěte si témat [mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a ladění
[V tématu Průvodce sledováním výkonu & optimalizací se](data-factory-copy-activity-performance.md) dozvíte o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivita kopírování) ve Službě Azure Data Factory, a o různých způsobech jeho optimalizace.
