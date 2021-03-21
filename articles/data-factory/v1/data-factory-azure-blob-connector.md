---
title: Kopírování dat do/z Azure Blob Storage
description: 'Přečtěte si, jak kopírovat data objektů BLOB v Azure Data Factory. Využijte naši ukázku: Jak kopírovat data z a do Azure Blob Storage a Azure SQL Database.'
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f1343f900e12bff09c0436ca52d8b091fe48a181
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100393543"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Kopírování dat do nebo z Azure Blob Storage pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-azure-blob-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-blob-storage.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Azure Blob Storage Connector v v2](../connector-azure-blob-storage.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z a do Azure Blob Storage. Sestavuje se podle článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který prezentuje obecný přehled přesunu dat s aktivitou kopírování.

## <a name="overview"></a>Přehled
Data z libovolného podporovaného zdrojového úložiště dat můžete zkopírovat do Azure Blob Storage nebo z Azure Blob Storage do libovolného podporovaného úložiště dat jímky. Následující tabulka obsahuje seznam úložišť dat podporovaných jako zdroje nebo jímky aktivity kopírování. Data můžete například přesunout **z** databáze SQL Server nebo databáze v Azure SQL Database **do** úložiště objektů BLOB v Azure. A můžete kopírovat data **ze služby** Azure Blob Storage **do** služby Azure synapse Analytics nebo do kolekce Azure Cosmos DB.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Podporované scénáře
Data **z Azure Blob Storage** můžete kopírovat do následujících úložišť dat:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Data z následujících úložišť dat můžete zkopírovat **do Azure Blob Storage**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> Aktivita kopírování podporuje kopírování dat z/do účtů pro obecné účely Azure Storage a horké nebo studené Blobové úložiště. Aktivita podporuje **čtení z bloků, připojení nebo objektů blob stránky**, podporuje však **zápis pouze do objektů blob bloku**. Služba Azure Premium Storage není podporována jako jímka, protože je zajištěna objekty blob stránky.
>
> Aktivita kopírování neodstraní data ze zdroje poté, co byla data úspěšně zkopírována do cílového umístění. Pokud potřebujete odstranit zdrojová data po úspěšné kopii, vytvořte [vlastní aktivitu](data-factory-use-custom-activities.md) k odstranění dat a aktivitu použijte v kanálu. Příklad najdete v tématu věnovaném [ukázce odstranit objekt BLOB nebo složku na GitHubu](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity).

## <a name="get-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data do nebo z Azure Blob Storage pomocí různých nástrojů/rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Tento článek obsahuje [návod](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) pro vytvoření kanálu pro kopírování dat z BLOB Storage umístění Azure do jiného umístění Azure Blob Storage. Kurz týkající se vytvoření kanálu pro kopírování dat z Blob Storage Azure do Azure SQL Database najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** a **REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **datovou továrnu**. Datová továrna může obsahovat jeden nebo více kanálů.
2. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou. Pokud například kopírujete data z úložiště objektů BLOB v Azure do Azure SQL Database, vytvoříte dvě propojené služby, které propojí váš účet služby Azure Storage a Azure SQL Database k vašemu objektu pro vytváření dat. Vlastnosti propojených služeb, které jsou specifické pro Azure Blob Storage, najdete v části [vlastnosti propojených služeb](#linked-service-properties) .
2. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování. V příkladu uvedeném v posledním kroku vytvoříte datovou sadu pro určení kontejneru objektů BLOB a složky, která obsahuje vstupní data. A můžete vytvořit další datovou sadu pro určení tabulky SQL v Azure SQL Database, která obsahuje data zkopírovaná z úložiště objektů BLOB. Vlastnosti datové sady, které jsou specifické pro Azure Blob Storage, najdete v části [Vlastnosti datové sady](#dataset-properties) .
3. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup. V předchozím příkladu použijete jako jímku aktivity kopírování BlobSource jako zdroj a SqlSink. Podobně pokud kopírujete z Azure SQL Database do Azure Blob Storage, v aktivitě kopírování použijete SqlSource a BlobSink. Vlastnosti aktivity kopírování, které jsou specifické pro Azure Blob Storage, najdete v části [vlastnosti aktivity kopírování](#copy-activity-properties) . Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímku, získáte kliknutím na odkaz v předchozí části úložiště dat.

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje/rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON.  Ukázky s definicemi JSON pro Entity Data Factory, které se používají ke kopírování dat do a z Azure Blob Storage, najdete v části [Příklady JSON](#json-examples-for-copying-data-to-and-from-blob-storage  ) tohoto článku.

Následující části obsahují podrobné informace o vlastnostech JSON, které slouží k definování Data Factory entit specifických pro Azure Blob Storage.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Existují dva typy propojených služeb, které můžete použít k propojení Azure Storage s objektem pro vytváření dat Azure. Jsou to: propojená služba **AzureStorage** a propojená služba **AzureStorageSas** . Propojená služba Azure Storage poskytuje datovou továrnu s globálním přístupem k Azure Storage. Vzhledem k tomu, že propojená služba Azure Storage SAS (sdílený přístupový podpis) poskytuje objekt pro vytváření dat s omezeným/časově vázaným přístupem k Azure Storage. Mezi těmito dvěma propojenými službami neexistují žádné jiné rozdíly. Vyberte propojenou službu, která vyhovuje vašim potřebám. Následující části obsahují další podrobnosti o těchto dvou propojených službách.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Vlastnosti datové sady
Chcete-li určit datovou sadu, která bude představovat vstupní nebo výstupní data v Blob Storage Azure, nastavte vlastnost Type datové sady na: **azureblobu**. Nastavte vlastnost **linkedServiceName** datové sady na název Azure Storage nebo Azure Storage propojenou službu SAS.  Vlastnosti typu datové sady určují **kontejner objektů BLOB** a **složku** v úložišti objektů BLOB.

Úplný seznam sekcí JSON & vlastností dostupných pro definování datových sad naleznete v článku [vytvoření datových sad](data-factory-create-datasets.md) . Oddíly, jako je například struktura, dostupnost a zásada pro datovou sadu JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure Blob, tabulka Azure atd.).

Data Factory podporuje následující hodnoty typů založené na technologii .NET kompatibilní se specifikací CLS pro poskytování informací o typech v "struktuře" zdrojů dat pro čtení, jako je Azure Blob: Int16, Int32, Int64, Single, Double, Decimal, Byte [], bool, String, GUID, DateTime, DateTimeOffset, TimeSpan. Data Factory automaticky provádí převody typu při přesunu dat ze zdrojového úložiště dat do úložiště dat jímky.

Oddíl **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění, formátu atd. dat v úložišti dat. Oddíl typeProperties pro sadu dat **azureblobu** typu DataSet má následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| folderPath |Cesta ke kontejneru a složce v úložišti objektů BLOB. Příklad: myblobcontainer\myblobfolder\ |Yes |
| fileName |Název objektu BLOB Název souboru je nepovinný a rozlišuje velká a malá písmena.<br/><br/>Pokud zadáte název souboru, bude aktivita (včetně kopírování) fungovat na konkrétním objektu BLOB.<br/><br/>Pokud není zadán název souboru, příkaz Kopírovat zahrnuje všechny objekty BLOB v folderPath pro vstupní datovou sadu.<br/><br/>Pokud není zadán **název souboru** pro výstupní datovou sadu a **preserveHierarchy** není zadán v jímky aktivity, název generovaného souboru by byl v následujícím formátu: `Data.<Guid>.txt` (například:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |partitionedBy je volitelná vlastnost. Můžete ji použít k určení dynamického folderPath a názvu souboru pro data časových řad. Například folderPath může být Parametrizovaná za každou hodinu dat. Podrobnosti a příklady najdete v [části použití vlastnosti partitionedBy](#using-partitionedby-property) . |No |
| formát | Podporovány jsou následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. V části formát nastavte vlastnost **typ** na jednu z těchto hodnot. Další informace najdete v částech [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), [formát JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formát ORC](data-factory-supported-file-and-compression-formats.md#orc-format)a formátování [Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Pokud chcete **Kopírovat soubory** mezi úložišti na základě souborů (binární kopie), přeskočte oddíl formát v definicích vstupní i výstupní datové sady. |No |
| komprese | Zadejte typ a úroveň komprese dat. Podporované typy jsou: **gzip**, **Deflate**, **bzip2** a **ZipDeflate**. Podporované úrovně: **optimální** a **nejrychlejší**. Další informace naleznete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

### <a name="using-partitionedby-property"></a>Použití vlastnosti partitionedBy
Jak je uvedeno v předchozí části, můžete zadat dynamický folderPath a filename pro data časových řad pomocí vlastnosti **partitionedBy** , [Data Factory funkcí a systémových proměnných](data-factory-functions-variables.md).

Další informace o datových sadách časových řad, plánování a řezech najdete v tématu [vytváření datových sad](data-factory-create-datasets.md) a [plánování &ch](data-factory-scheduling-and-execution.md) článků o spuštění.

#### <a name="sample-1"></a>Ukázka 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

V tomto příkladu je {Slice} nahrazen hodnotou Data Factory systémové proměnné vlastnosti slicestart v zadaném formátu (YYYYMMDDHH). Vlastnosti slicestart odkazuje na počáteční čas řezu. FolderPath se u každého řezu liší. Příklad: wikidatagateway/wikisampledataout/2014100103 nebo wikidatagateway/wikisampledataout/2014100104

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

V tomto příkladu jsou extrahovány roky, měsíc, den a čas vlastnosti slicestart do samostatných proměnných, které jsou používány vlastnostmi folderPath a fileName.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam sekcí & vlastností dostupných pro definování aktivit najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . Pro všechny typy aktivit jsou k dispozici vlastnosti, jako je název, popis, vstupní a výstupní datové sady a zásady. V takovém případě se vlastnosti dostupné v části **typeProperties** v aktivitě liší podle typu aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a jímky. Pokud přesouváte data z Blob Storage Azure, nastavíte typ zdroje v aktivitě kopírování na **BlobSource**. Podobně platí, že Pokud přesouváte data do Blob Storage Azure, nastavíte typ jímky v aktivitě kopírování na **BlobSink**. V této části najdete seznam vlastností podporovaných BlobSource a BlobSink.

**BlobSource** podporuje následující vlastnosti v části **typeProperties** :

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| zahrnout |Určuje, zda mají být data rekurzivně čtena z dílčích složek nebo pouze ze zadané složky. |True (výchozí hodnota), false |No |

**BlobSink** podporuje následující vlastnosti oddílu **typeProperties** :

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| copyBehavior |Definuje chování kopírování, pokud je zdroj BlobSource nebo FileSystem. |<b>PreserveHierarchy</b>: zachová hierarchii souborů v cílové složce. Relativní cesta ke zdrojovému souboru se zdrojovou složkou je shodná s relativní cestou cílového souboru do cílové složky.<br/><br/><b>FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generovaný název. <br/><br/><b>MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadaný název souboru nebo objektu blob, bude název sloučeného souboru zadaný název. jinak by byl automaticky vygenerovaný název souboru. |No |

**BlobSource** také podporuje tyto dvě vlastnosti kvůli zpětné kompatibilitě.

* **treatEmptyAsNull**: Určuje, zda má být jako hodnota null zpracován řetězec s hodnotou null nebo prázdným řetězcem.
* **skipHeaderLineCount** – určuje, kolik řádků se musí přeskočit. Dá se použít jenom v případě, že vstupní datová sada používá TextFormat.

Podobně **BlobSink** podporuje následující vlastnost pro zpětnou kompatibilitu.

* **blobWriterAddHeader**: Určuje, zda se má při zápisu do výstupní datové sady přidat záhlaví definic sloupců.

Datové sady teď podporují následující vlastnosti, které implementují stejné funkce: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

Následující tabulka uvádí pokyny k použití vlastností nové datové sady místo těchto vlastností zdroje a jímky objektu BLOB.

| Vlastnost aktivity kopírování | Vlastnost DataSet |
|:--- |:--- |
| skipHeaderLineCount na BlobSource |skipLineCount a firstRowAsHeader. Řádky se nejdříve přeskočí a pak se první řádek přečte jako záhlaví. |
| treatEmptyAsNull na BlobSource |treatEmptyAsNull pro vstupní datovou sadu |
| blobWriterAddHeader na BlobSink |firstRowAsHeader na výstupní datovou sadu |

Podrobné informace o těchto vlastnostech najdete v tématu [určení oddílu TextFormat](data-factory-supported-file-and-compression-formats.md#text-format) .

### <a name="recursive-and-copybehavior-examples"></a>Příklady rekurzivních a copyBehavior
Tato část popisuje výsledné chování operace kopírování pro různé kombinace rekurzivních a copyBehavior hodnot.

| zahrnout | copyBehavior | Výsledné chování |
| --- | --- | --- |
| true |preserveHierarchy |Pro zdrojovou složku Složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cílová složka Složku1 se vytvoří se stejnou strukturou jako zdroj.<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |Pro zdrojovou složku Složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>cílový Složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro File5 |
| true |mergeFiles |Pro zdrojovou složku Složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>cílový Složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Obsah soubor1 + soubor2 + file3 + file4 + soubor 5 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. |
| false (nepravda) |preserveHierarchy |Pro zdrojovou složku Složku1 s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cílová složka Složku1 se vytvoří s následující strukturou.<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Subfolder1 s file3, file4 a File5 se neúčtují. |
| false (nepravda) |flattenHierarchy |Pro zdrojovou složku Složku1 s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cílová složka Složku1 se vytvoří s následující strukturou.<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro soubor2<br/><br/><br/>Subfolder1 s file3, file4 a File5 se neúčtují. |
| false (nepravda) |mergeFiles |Pro zdrojovou složku Složku1 s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cílová složka Složku1 se vytvoří s následující strukturou.<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Obsah soubor1 + soubor2 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. automaticky vygenerovaný název pro Soubor1<br/><br/>Subfolder1 s file3, file4 a File5 se neúčtují. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Návod: použití Průvodce kopírováním ke zkopírování dat do/z Blob Storage
Pojďme se podívat, jak rychle kopírovat data do nebo z úložiště objektů BLOB v Azure. V tomto návodu jsou zdrojová i cílová úložiště dat typu: Azure Blob Storage. Kanál v tomto návodu kopíruje data ze složky do jiné složky ve stejném kontejneru objektů BLOB. Tento názorný postup je záměrně jednoduchý k zobrazení nastavení nebo vlastností při použití Blob Storage jako zdroje nebo jímky.

### <a name="prerequisites"></a>Předpoklady
1. Pokud ho ještě nemáte, vytvořte účet pro obecné účely **Azure Storage** . V tomto návodu použijete úložiště objektů BLOB jako **zdrojové** i **cílové** úložiště dat. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md), kde najdete kroky pro jeho vytvoření.
2. V účtu úložiště vytvořte kontejner objektů BLOB s názvem **adfblobconnector** .
4. V kontejneru **adfblobconnector** vytvořte složku s názvem **input** .
5. Vytvořte soubor s názvem **emp.txt** s následujícím obsahem a nahrajte ho do **vstupní** složky pomocí nástrojů, jako je například [Průzkumník služby Azure Storage](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```

### <a name="create-the-data-factory"></a>Vytvoření datové továrny
1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. V levém horním rohu klikněte na **vytvořit prostředek** , klikněte na **Intelligence + Analytics** a pak klikněte na **Data Factory**.
3. V podokně **Nová datová továrna** :  
    1. Jako **název** zadejte **ADFBlobConnectorDF** . Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí chyba: `*Data factory name "ADFBlobConnectorDF" is not available` , změňte název datové továrny (například na yournameADFBlobConnectorDF) a zkuste to znovu. V tématu [Objekty pro vytváření dat – pravidla pojmenování](data-factory-naming-rules.md) najdete pravidla pojmenování artefaktů služby Data Factory.
    2. Vyberte své **předplatné** Azure.
    3. V případě skupiny prostředků vyberte **použít existující** a vyberte existující skupinu prostředků (nebo) vyberte **vytvořit novou** a zadejte název skupiny prostředků.
    4. Vyberte **umístění** pro příslušný objekt pro vytváření dat.
    5. Zaškrtněte políčko **Připnout na řídicí panel** v dolní části okna.
    6. Klikněte na **Vytvořit**.
3. Po dokončení vytváření se zobrazí okno **Data Factory** , jak je znázorněno na následujícím obrázku:  ![ Domovská stránka datové továrny](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Průvodce kopírováním
1. Na domovské stránce Data Factory klikněte na dlaždici **Kopírovat data** , na které se spustí **Průvodce kopírování dat** na samostatné kartě.  

    > [!NOTE]
    > Pokud zjistíte, že je webový prohlížeč zablokovaný při autorizaci..., zakažte nebo zrušte kontrolu **souborů cookie třetích stran a nastavení dat webu** (nebo) zachovejte povolení a vytvořte výjimku pro **Login.microsoftonline.com** a potom zkuste průvodce spustit znovu.
2. Na stránce **Vlastnosti**:
    1. Jako **název úlohy** zadejte **CopyPipeline** . Název úlohy je název kanálu ve vaší datové továrně.
    2. Zadejte **Popis** úlohy (volitelné).
    3. V případě **tempo úkolů nebo plánu úloh** Udržujte možnost **spouštět pravidelně podle plánu** . Chcete-li tuto úlohu spustit pouze jednou, místo opakovaného spuštění podle plánu, vyberte možnost **Spustit nyní**. Pokud vyberete možnost, **spustí se jednou** , vytvoří se [jednorázový kanál](data-factory-create-pipelines.md#onetime-pipeline) .
    4. Ponechte nastavení pro **Periodický vzor**. Tato úloha se spouští každý den mezi časem zahájení a ukončení, který zadáte v dalším kroku.
    5. Změňte **počáteční datum a čas** na **04/21/2017**.
    6. Změňte **koncové datum a čas** na **04/25/2017**. Možná budete chtít zadat datum místo procházení kalendářem.
    8. Klikněte na **Next** (Další).
        ![Nástroj pro kopírování – stránka Vlastnosti](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png)
3. Na stránce **Source data store** (Zdrojové úložiště dat) klikněte na dlaždici **Azure Blob Storage**. Tato stránka slouží k zadání zdrojového úložiště dat pro úlohu kopírování. Můžete použít existující propojenou službu úložiště dat nebo zadat nové úložiště dat. Pokud chcete použít existující propojenou službu, vyberte **z existujících propojených služeb** a vyberte správnou propojenou službu.
    ![Nástroj pro kopírování – stránka zdrojového úložiště dat](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. Na stránce **Specify the Azure Blob storage account** (Zadejte účet Azure Blob Storage):
    1. Pro **název připojení** nechte automaticky generovaný název. Název připojení je název propojené služby typu: Azure Storage.
    2. Ujistěte se, že je pro položku **Metoda výběru účtu** vybrána možnost **Z předplatných Azure**.
    3. Vyberte své předplatné Azure nebo nechte **možnost Vybrat vše** pro **předplatné Azure**.
    4. V seznamu účtů úložiště Azure dostupných ve zvoleném předplatném vyberte požadovaný **účet úložiště Azure**. Můžete také zadat nastavení účtu úložiště ručně tak, že pro **metodu výběru účtu** vyberete možnost **zadat ručně** .
    5. Klikněte na **Next** (Další).  
        ![Nástroj pro kopírování – zadání účtu Azure Blob Storage](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. Na stránce **Choose the input file or folder** (Zvolte vstupní soubor nebo složku):
    1. Dvakrát klikněte na **adfblobcontainer**.
    2. Vyberte **vstup** a klikněte na **zvolit**. V tomto návodu vyberete vstupní složku. Místo toho můžete také vybrat soubor emp.txt ve složce.
        ![Nástroj pro kopírování – výběr vstupního souboru nebo složky 1](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. Na stránce **zvolit vstupní soubor nebo složku** :
    1. Potvrďte, že je **soubor nebo složka** nastavené na **adfblobconnector/Input**. Pokud jsou soubory v dílčích složkách, například 2017/04/01, 2017/04/02 a tak dále, zadejte adfblobconnector/Input/{Year}/{Month}/{Day} pro soubor nebo složku. Po stisknutí klávesy TAB mimo textové pole se zobrazí tři rozevírací seznamy pro výběr formátů Year (rrrr), month (MM) a Day (DD).
    2. Nenastavuje **rekurzivní kopírování souboru**. Tuto možnost vyberte, pokud chcete rekurzivně Procházet složky, aby se soubory zkopírovaly do cílového umístění.
    3. Nejedná se o možnost **binární kopírování** . Tuto možnost vyberte, pokud chcete v cíli provést binární kopii zdrojového souboru. Nevybírejte pro tento návod, abyste viděli další možnosti na dalších stránkách.
    4. Potvrďte, že **typ komprese** je nastavený na **none**. Vyberte hodnotu pro tuto možnost, pokud jsou zdrojové soubory komprimovány v jednom z podporovaných formátů.
    5. Klikněte na **Next** (Další).
    ![Nástroj pro kopírování – výběr vstupního souboru nebo složky 2](./media/data-factory-azure-blob-connector/chose-input-file-folder.png)
7. Na stránce **Nastavení formátu souboru** jsou uvedeny oddělovače a schéma, které je automaticky zjištěno průvodcem při analýze souboru.
    1. Potvrďte následující možnosti:  
        a. **Formát souboru** je nastaven na **formát text**. Všechny podporované formáty můžete zobrazit v rozevíracím seznamu. Například: JSON, Avro, ORC, Parquet.
       b. **Oddělovač sloupců** je nastaven na hodnotu `Comma (,)` . V rozevíracím seznamu můžete zobrazit další oddělovače sloupců podporované Data Factory. Můžete také zadat vlastní oddělovač.
       c. **Oddělovač řádků** je nastaven na hodnotu `Carriage Return + Line feed (\r\n)` . Další oddělovače řádků podporované Data Factory můžete zobrazit v rozevíracím seznamu. Můžete také zadat vlastní oddělovač.
       d. **Počet řádků přeskočení** je nastaven na **hodnotu 0**. Pokud chcete v horní části souboru přeskočit několik řádků, zadejte číslo sem.
       e. **První řádek dat obsahuje názvy sloupců** , které nejsou nastaveny. Pokud zdrojové soubory obsahují názvy sloupců v prvním řádku, vyberte tuto možnost.
       f. Možnost **považovat prázdnou hodnotu sloupce jako hodnotu null** je nastavena.
    2. Rozbalením možnosti **Upřesnit nastavení** zobrazíte upřesňující možnost k dispozici.
    3. V dolní části stránky se podívejte na **Náhled** dat ze souboru emp.txt.
    4. Klikněte na kartu **schéma** v dolní části, abyste viděli schéma, které vyvodil Průvodce kopírováním, a to tak, že prohlížíte data ve zdrojovém souboru.
    5. Po zkontrolování oddělovačů a náhledu dat klikněte na **Další**.
    ![Nástroj pro kopírování – nastavení formátu souboru](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)
8. Na **stránce cílové úložiště dat** vyberte **Azure Blob Storage** a klikněte na **Další**. V tomto návodu používáte Blob Storage Azure jako zdrojové i cílové úložiště dat.  
    ![Nástroj pro kopírování – výběr cílového úložiště dat](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. Na stránce **Zadejte účet služby Azure Blob Storage** :  
    1. Do pole **název připojení** zadejte **AzureStorageLinkedService** .
    2. Ujistěte se, že je pro položku **Metoda výběru účtu** vybrána možnost **Z předplatných Azure**.
    3. Vyberte své **předplatné** Azure.
    4. Vyberte svůj účet úložiště Azure.
    5. Klikněte na **Next** (Další).
10. Na stránce **zvolit výstupní soubor nebo složku** :  
    1. Zadejte **cestu ke složce** jako **adfblobconnector/Output/{Year}/{Month}/{Day**}. Zadejte **kartu**.
    1. V **roce** vyberte **RRRR**.
    1. V **měsíci** potvrďte, že je nastavené na **mm**.
    1. Pro daný **den** potvrďte, že je nastavené na **DD**.
    1. Potvrďte, že **typ komprese** je nastavený na **none**.
    1. Ověřte, že je **chování při kopírování** nastaveno na **Sloučit soubory**. Pokud výstupní soubor se stejným názvem již existuje, nový obsah se přidá do stejného souboru na konci.
    1. Klikněte na **Next** (Další).
       ![Nástroj pro kopírování – volba výstupního souboru nebo složky](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. Na stránce **Nastavení formátu souboru** zkontrolujte nastavení a klikněte na **Další**. Jednou z dalších možností je přidat hlavičku do výstupního souboru. Pokud vyberete tuto možnost, přidá se řádek záhlaví s názvy sloupců ze schématu zdroje. Výchozí názvy sloupců můžete přejmenovat při zobrazení schématu pro zdroj. Například můžete změnit první sloupec na jméno a druhý sloupec na jméno a příjmení. Výstupní soubor se pak vygeneruje pomocí záhlaví s těmito názvy jako názvy sloupců.
    ![Nástroj pro kopírování – nastavení formátu souboru pro cíl](media/data-factory-azure-blob-connector/file-format-destination.png)
12. Na stránce **Nastavení výkonu** potvrďte, že **jednotky cloudu** a **paralelní kopie** jsou nastavené na hodnotu **automaticky**, a klikněte na další. Podrobnosti o těchto nastaveních najdete v tématu [Průvodce laděním a výkonem aktivity kopírování](data-factory-copy-activity-performance.md#parallel-copy).
    ![Nástroj pro kopírování – nastavení výkonu](media/data-factory-azure-blob-connector/copy-performance-settings.png)
14. Na stránce **Souhrn** Zkontrolujte všechna nastavení (vlastnosti úlohy, nastavení zdroje a cíle a nastavení kopírování) a klikněte na tlačítko **Další**.
    ![Nástroj pro kopírování – stránka souhrnu](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Na stránce **Souhrn** zkontrolujte informace a klikněte na **Dokončit**. Průvodce v objektu pro vytváření dat (ze kterého jste průvodce kopírováním spustili) vytvoří dvě propojené služby, dvě datové sady (vstupní a výstupní) a jeden kanál.
    ![Nástroj pro kopírování – stránka nasazení](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>Monitorování kanálu (kopírovat úlohu)

1. Klikněte na odkaz `Click here to monitor copy pipeline` na stránce **nasazení** .
2. Na samostatné kartě by se měla zobrazit **aplikace monitorování a Správa** .  ![Monitorování a Správa aplikace](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Změňte čas **spuštění** v horní části na `04/19/2017` a **koncový** čas na a `04/27/2017` pak klikněte na **použít**.
4. V seznamu **okna aktivit** by se měla zobrazit pět oken aktivit. Doba **WindowStart** by měla pokrývat všechny dny od doby od zahájení kanálu až po ukončení kanálu.
5. Klikněte několikrát na tlačítko **aktualizovat** u okna **aktivity** , dokud se nezobrazí stav všechna okna aktivity nastavená na připraveno.
6. Nyní ověřte, zda jsou výstupní soubory generovány ve výstupní složce kontejneru adfblobconnector. Ve výstupní složce by se měla zobrazit následující struktura složky:
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```
   Podrobné informace o monitorování a správě datových továren najdete v článku [monitorování a správa data Factoryho kanálu](data-factory-monitor-manage-app.md) .

### <a name="data-factory-entities"></a>Data Factory entit
Teď se přepněte zpátky na kartu pomocí Data Factory domovské stránky. Všimněte si, že teď existují dvě propojené služby, dvě datové sady a jeden kanál ve vaší datové továrně.

![Data Factory domovské stránky s entitami](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Kliknutím na tlačítko **vytvořit a nasadit** spusťte Editor Data Factory.

![Data Factory Editor](media/data-factory-azure-blob-connector/data-factory-editor.png)

Ve vaší datové továrně byste měli vidět následující Data Factory entity:

- Dvě propojené služby. Jednu pro zdroj a druhý pro cíl. Propojené služby odkazují na stejný účet Azure Storage v tomto návodu.
- Dvě datové sady. Vstupní datová sada a výstupní datová sada. V tomto návodu oba používají stejný kontejner objektů blob, ale odkazují na různé složky (vstup a výstup).
- Kanál. Kanál obsahuje aktivitu kopírování, která používá zdroj objektu BLOB a jímku objektů BLOB ke kopírování dat z umístění objektu blob Azure do jiného umístění objektu blob Azure.

Následující části obsahují další informace o těchto entitách.

#### <a name="linked-services"></a>Propojené služby
Měly by se zobrazit dvě propojené služby. Jednu pro zdroj a druhý pro cíl. V tomto návodu obě definice vypadají stejně, s výjimkou názvů. **Typ** propojené služby je nastavený na **AzureStorage**. Nejdůležitější vlastností definice propojené služby je **připojovací řetězec**, který Data Factory použít k připojení k účtu Azure Storage za běhu. Ignoruje vlastnost hubName v definici.

##### <a name="source-blob-storage-linked-service"></a>Propojená služba BLOB Storage ve zdroji
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

##### <a name="destination-blob-storage-linked-service"></a>Propojená služba BLOB Storage v cíli

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

Další informace o Azure Storage propojených službách najdete v tématu věnovaném [vlastnostem propojených služeb](#linked-service-properties) .

#### <a name="datasets"></a>Datové sady
Existují dvě datové sady: vstupní datová sada a výstupní datová sada. Typ datové sady je nastaven na **azureblobu** pro obě.

Vstupní datová sada odkazuje na **vstupní** složku **adfblobconnector** kontejneru objektů BLOB. Vlastnost **External** pro tuto datovou sadu je nastavená na **true** , protože data nejsou vytvořená kanálem s aktivitou kopírování, která tuto datovou sadu přebírá jako vstup.

Výstupní datová sada odkazuje na **výstupní** složku stejného kontejneru objektů BLOB. Výstupní datová sada používá také rok, měsíc a den systémové proměnné **vlastnosti slicestart** k dynamickému vyhodnocení cesty pro výstupní soubor. Seznam funkcí a systémových proměnných podporovaných nástrojem Data Factory naleznete v tématu [Data Factory functions a System Variables](data-factory-functions-variables.md). Vlastnost **External** je nastavena na **hodnotu false** (výchozí hodnota), protože tato datová sada je vytvořena kanálem.

Další informace o vlastnostech podporovaných datovou sadou objektů BLOB v Azure najdete v části [Vlastnosti datové sady](#dataset-properties) .

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
Kanál obsahuje pouze jednu aktivitu. **Typ** aktivity je nastavený na **Kopírovat**. Ve vlastnostech typu pro aktivitu existují dva oddíly, jeden pro zdroj a druhý pro jímku. Typ zdroje je nastavený na **BlobSource** , protože aktivita kopíruje data z úložiště objektů BLOB. Typ jímky je nastavený na **BlobSink** , protože aktivita kopíruje data do úložiště objektů BLOB. Aktivita kopírování přijímá InputDataset-Z4Y jako vstup a OutputDataset-Z4Y jako výstup.

Další informace o vlastnostech podporovaných nástrojem BlobSource a BlobSink najdete v části [vlastnosti aktivity kopírování](#copy-activity-properties) .

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

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>Příklady JSON pro kopírování dat do a z Blob Storage
V následujících příkladech jsou uvedeny ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazují, jak kopírovat data z a do Azure Blob Storage a Azure SQL Database. Data se ale dají zkopírovat **přímo** z libovolného zdroje do kterékoli z těchto umyvadel, které jsou [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) uvedené, pomocí aktivity kopírování v Azure Data Factory.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>Příklad JSON: kopírování dat z Blob Storage do SQL Database
Následující příklad ukazuje:

1. Propojená služba typu [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Propojená služba typu [AzureStorage](#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](#copy-activity-properties) a [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties).

Ukázka kopíruje data časových řad z objektu blob Azure do tabulky Azure SQL za hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

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
Azure Data Factory podporuje dva typy propojených služeb Azure Storage: **AzureStorage** a **AzureStorageSas**. Jako první zadejte připojovací řetězec, který obsahuje klíč účtu a za pozdější dobu, a zadejte identifikátor URI sdíleného přístupového podpisu (SAS). Podrobnosti najdete v části [propojené služby](#linked-service-properties) .

**Vstupní datová sada Azure Blob:**

Data se vybírají z nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce a název souboru pro objekt BLOB jsou dynamicky vyhodnocovány na základě počátečního času zpracovávaného řezu. Cesta ke složce používá část rok, měsíc a den v čase zahájení a název souboru používá hodinovou část času spuštění. "externí": nastavení "true" informuje Data Factory, že je tabulka externí pro objekt pro vytváření dat a není vytvořena aktivitou v datové továrně.

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

Ukázka zkopíruje data do tabulky s názvem "MyTable" v Azure SQL Database. Vytvořte tabulku v databázi SQL se stejným počtem sloupců, protože očekáváte, že soubor CSV BLOB bude obsahovat. Nové řádky jsou do tabulky přidány každou hodinu.

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
**Aktivita kopírování v kanálu se zdrojem objektů BLOB a jímkou SQL:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady a má naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **BlobSource** a typ **jímky** je nastavený na **SqlSink**.

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
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>Příklad JSON: kopírování dat z Azure SQL do Azure Blob
Následující příklad ukazuje:

1. Propojená služba typu [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Propojená služba typu [AzureStorage](#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) a [BlobSink](#copy-activity-properties).

Ukázka kopíruje data časových řad z tabulky Azure SQL do objektu blob Azure za hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

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
Azure Data Factory podporuje dva typy propojených služeb Azure Storage: **AzureStorage** a **AzureStorageSas**. Jako první zadejte připojovací řetězec, který obsahuje klíč účtu a za pozdější dobu, a zadejte identifikátor URI sdíleného přístupového podpisu (SAS). Podrobnosti najdete v části [propojené služby](#linked-service-properties) .

**Vstupní datová sada Azure SQL:**

Ukázka předpokládá, že jste v Azure SQL vytvořili tabulku "MyTable" a obsahuje sloupec s názvem "timestampcolumn" pro data časových řad.

Nastavení "externí": "true" informuje Data Factory služby, že tabulka je externí pro objekt pro vytváření dat, a není vytvořena aktivitou v datové továrně.

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

Data se zapisují do nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt BLOB je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části rok, měsíc, den a hodiny v počátečním čase.

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

**Aktivita kopírování v kanálu se zdrojem SQL a jímkou objektů BLOB:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady a má naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **SqlSource** a typ **jímky** je nastavený na **BlobSink**. Dotaz SQL zadaný pro vlastnost **SqlReaderQuery** vybere data během uplynulé hodiny ke zkopírování.

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
> Chcete-li mapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, přečtěte si téma [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Další informace o klíčových faktorech, které mají vliv na výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jejich optimalizace, najdete v tématu [Průvodce optimalizací aktivity kopírování &](data-factory-copy-activity-performance.md) .
