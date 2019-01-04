---
title: Kopírování dat do a ze systému souborů pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data do a z v místním systému souborů pomocí služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: ce19f1ae-358e-4ffc-8a80-d802505c9c84
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1cd7e504a614203218cb06b337becf36b992cf1d
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54018224"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Kopírování dat do a z v místním systému souborů pomocí služby Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-onprem-file-system-connector.md)
> * [Verze 2 (aktuální verze)](../connector-file-system.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektoru systému souborů ve verzi V2](../connector-file-system.md).


Tento článek vysvětluje, jak použít aktivitu kopírování ke kopírování dat do a z v místním systému souborů ve službě Azure Data Factory. Je nástavbou [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který nabízí obecný přehled o přesun dat pomocí aktivity kopírování.

## <a name="supported-scenarios"></a>Podporované scénáře
Může kopírovat data **systému souborů v místním** ukládá následující data:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Může kopírovat data z následujících datových skladů **na systém souborů v místním**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Aktivita kopírování nedojde k odstranění zdrojového souboru, co se úspěšně zkopíruje do cíle. Pokud je potřeba odstranit zdrojový soubor po úspěšném kopírování, vytvoření vlastní aktivity a stejný soubor odstranit také pomocí aktivity v kanálu. 

## <a name="enabling-connectivity"></a>Povolení připojení
Data Factory podporuje připojení do a z v místním systému souborů prostřednictvím **brána správy dat**. Musíte nainstalovat bránu správy dat v místním prostředí pro službu Data Factory se připojit k jakékoli úložiště dat podporovaných místních včetně systému souborů. Další informace o bráně pro správu dat a zobrazit podrobné pokyny pro nastavení brány najdete v tématu [přesun dat mezi místním zdrojům a cloudem pomocí brány správy dat](data-factory-move-data-between-onprem-and-cloud.md). Kromě Brána pro správu dat není třeba žádné binární soubory ke komunikaci do a z v místním systému souborů. Musíte nainstalovat a používat bránu správy dat i v případě, že systém souborů je na virtuálním počítači Azure IaaS. Podrobné informace o bráně, naleznete v tématu [brána správy dat](data-factory-data-management-gateway.md).

Použití sdílené složky Linux, instalace [Samba](https://www.samba.org/) na serveru Linux a instalace brány pro správu dat na Windows serveru. Instalace brány pro správu dat na Linuxovém serveru se nepodporuje.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, která přesunuje data ze systému souborů pomocí různých nástrojů a rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Zobrazit [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **Azure portal**, **sady Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **rozhraní .NET API**a  **Rozhraní REST API**. Zobrazit [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování.

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvoření **služby data factory**. Datová továrna může obsahovat jeden nebo víc kanálů. 
2. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do služby data factory. Pokud jsou kopírování dat z Azure blob storage na systém souborů na místě, například vytvoříte dvě propojené služby k propojení vaší místní systém souborů a účet úložiště Azure pro vytváření dat. Vlastnosti propojené služby, které jsou specifické pro systém souborů v místním, naleznete v tématu [vlastnostem propojených služeb](#linked-service-properties) oddílu.
3. Vytvoření **datových sad** k představují vstupní a výstupní data pro operaci kopírování. V příkladu uvedených v posledním kroku vytvoříte datovou sadu, která zadejte kontejner objektů blob a složku obsahující vstupní data. A vytvořte jinou datovou sadu, která zadejte složku a název souboru (volitelné) v systému souborů. Vlastnosti datové sady, které jsou specifické pro systém souborů v místním, naleznete v tématu [vlastnosti datové sady](#dataset-properties) oddílu.
4. Vytvoření **kanálu** s aktivitou kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup. V příkladu již bylo zmíněno dříve pomocí BlobSource jako zdroj a FileSystemSink jako jímka pro aktivitu kopírování. Podobně pokud kopírujete z místního systému souborů do úložiště objektů Blob Azure, můžete použít FileSystemSource a BlobSink v aktivitě kopírování. Kopírovat vlastnosti aktivity, které jsou specifické pro systém souborů v místním, naleznete v tématu [vlastnosti aktivity kopírování](#copy-activity-properties) oddílu. Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímku klikněte na odkaz v předchozí části datového úložiště.

Při použití Průvodce definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál) se automaticky vytvoří za vás. Při použití nástroje a rozhraní API (s výjimkou rozhraní .NET API), můžete definovat tyto entity služby Data Factory ve formátu JSON.  Ukázky s definicemi JSON entit služby Data Factory, které se používají ke kopírování dat do a ze systému souborů najdete v tématu [JSON příklady](#json-examples-for-copying-data-to-and-from-file-system) části tohoto článku.

Následující části obsahují podrobnosti o vlastnostech JSON, které se používají k definování entit služby Data Factory, které jsou specifické pro systém souborů:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Systém souborů v místním můžete propojit s Azure data factory s **s místními souborového serveru** propojenou službu. Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro souborový Server On-Premises propojenou službu.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Zkontrolujte, že vlastnost type je nastavená na **OnPremisesFileServer**. |Ano |
| hostitel |Určuje kořenová cesta ke složce, která chcete kopírovat. Použijte řídicí znak "\" pro zvláštní znaky v řetězci. Zobrazit [ukázka propojené služby a datové sady definice](#sample-linked-service-and-dataset-definitions) příklady. |Ano |
| ID uživatele |Zadejte ID uživatele, který má přístup k serveru. |Ne (když zvolíte encryptedCredential) |
| heslo |Zadejte heslo pro uživatele (ID uživatele). |Ne (když zvolíte encryptedCredential |
| encryptedCredential |Zadejte zašifrované přihlašovací údaje, které můžete získat spuštěním rutiny New-AzureRmDataFactoryEncryptValue. |Ne (když se rozhodnete zadat ID uživatele a heslo jako prostý text) |
| Název brány |Určuje název brány, které služby Data Factory měla použít pro připojení k serveru v místním souboru. |Ano |


### <a name="sample-linked-service-and-dataset-definitions"></a>Ukázkové propojené služby a definicích datových sad
| Scénář | Hostování v definici propojené služby | folderPath v definici datové sady |
| --- | --- | --- |
| Místní složka na počítači brány pro správu dat: <br/><br/>Příklady: D:\\ \* nebo D:\folder\subfolder\\* |D:\\ \\ (pro Data Management Gateway 2.0 a novější) <br/><br/> místního hostitele (pro starší verze než Data Management Gateway 2.0) |. \\ \\ nebo složky\\\\podsložku (pro Data Management Gateway 2.0 a novější) <br/><br/>D:\\ \\ nebo D:\\\\složky\\\\podsložku (pro brány verze nižší než 2.0) |
| Vzdálené sdílené složky: <br/><br/>Příklady: \\ \\myserver\\sdílet\\ \* nebo \\ \\myserver\\sdílet\\složky\\podsložky\\* |\\\\\\\\myserver\\\\sdílet |. \\ \\ nebo složky\\\\podsložky |

>[!NOTE]
>Při vytváření prostřednictvím uživatelského rozhraní, není nutné zadat dvojité zpětné lomítko (`\\`) k uvození stejným způsobem jako prostřednictvím formátu JSON, zadejte jedno zpětné lomítko.

### <a name="example-using-username-and-password-in-plain-text"></a>Příklad: Pomocí uživatelského jména a hesla v prostém textu

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

### <a name="example-using-encryptedcredential"></a>Příklad: Pomocí encryptedcredential

```JSON
{
  "Name": " OnPremisesFileServerLinkedService ",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "D:\\",
      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
      "gatewayName": "mygateway"
    }
  }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, naleznete v tématu [vytváření datových sad](data-factory-create-datasets.md). Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datové sady.

V části typeProperties se liší pro každý typ datové sady. Poskytuje informace, jako je například umístění a formát dat v úložišti. TypeProperties části datové sady typu **sdílení souborů** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Určuje dílčí cestou ke složce. Použijte řídicí znak "\' pro zvláštní znaky v řetězci. Filtr zástupných znaků není podporován. Zobrazit [ukázka propojené služby a datové sady definice](#sample-linked-service-and-dataset-definitions) příklady.<br/><br/>Můžete zkombinovat tato vlastnost se **partitionBy** mít složku cesty založené na řez počátečním/koncovém data a časy. |Ano |
| fileName |Zadejte název souboru **folderPath** Pokud má tabulka, která má odkazovat na konkrétní soubor ve složce. Pokud je nezadávejte žádnou hodnotu pro tuto vlastnost, v tabulce odkazuje na všechny soubory ve složce.<br/><br/>Když **fileName** pro výstupní datovou sadu není zadána a **preserveHierarchy** není zadán v aktivita jímky název generovaného souboru je v následujícím formátu: <br/><br/>`Data.<Guid>.txt` (Příklad: Data.0a405f8a-93ff-4C6F-B3BE-f69616f1df7a.txt) |Ne |
| fileFilter |Určete filtr, který slouží k výběru podmnožinu souborů v cestě folderPath, nikoli všech souborů. <br/><br/>Povolené hodnoty jsou: `*` (více znaků) a `?` (jeden znak).<br/><br/>Příklad 1: "fileFilter": "* .log"<br/>Příklad 2: "fileFilter": 2014 - 1-?. TXT"<br/><br/>Všimněte si, že fileFilter se dá použít pro vstupní datovou sadu sdílení souborů. |Ne |
| partitionedBy |PartitionedBy můžete použít k určení dynamické folderPath/fileName pro data časových řad. Příkladem je folderPath s parametry pro každou hodinu data. |Ne |
| formát | Jsou podporovány následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formát](data-factory-supported-file-and-compression-formats.md#orc-format), a [formát Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete **kopírovat soubory jako-je** mezi souborové úložištěm (binární kopie) a přeskočit část o formátu v definicích oba vstupní a výstupní datové sady. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimální** a **nejrychlejší**. Zobrazit [formáty souborů a komprese ve službě Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

> [!NOTE]
> Název souboru a fileFilter nelze současně použít.

### <a name="using-partitionedby-property"></a>Vlastnost partitionedBy
Jak je uvedeno v předchozí části, můžete zadat dynamické folderPath a název souboru pro data časových řad s **partitionedBy** vlastnost [funkce Data Factory a systémové proměnné](data-factory-functions-variables.md).

Další informace o časových řad datové sady, plánování a řezy najdete v tématu [vytváření datových sad](data-factory-create-datasets.md), [plánování a provádění](data-factory-scheduling-and-execution.md), a [vytváření kanálů](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Příklad 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

V tomto příkladu {řez} se nahradí hodnotu proměnné systému služby Data Factory SliceStart ve formátu (YYYYMMDDHH). Vlastnosti SliceStart odkazuje na počáteční čas řezu. V cestě folderPath se liší pro každý řez. Příklad: wikidatagateway/wikisampledataout/2014100103 nebo wikidatagateway/wikisampledataout/2014100104.

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

V tomto příkladu rok, měsíc, den a čas z vlastnosti SliceStart extrahován do samostatných proměnné, které používají vlastnosti folderPath a název souboru.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, jako je název, popis, vstupní a výstupní datové sady a zásady jsou k dispozici pro všechny typy aktivit. Vzhledem k tomu, k dispozici ve vlastnosti **typeProperties** části aktivity se liší s jednotlivými typu aktivity.

Pro aktivitu kopírování se liší v závislosti na typy zdroje a jímky. Pokud přesouváte data z v místním systému souborů, nastavíte typ zdroje v aktivitě kopírování do **FileSystemSource**. Podobně pokud přesouváte data v místním systému souborů, nastavíte typ jímky v aktivitě kopírování do **FileSystemSink**. Tato část obsahuje seznam podporovaných FileSystemSource a FileSystemSink vlastností.

**FileSystemSource** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| rekurzivní |Určuje, jestli se data číst rekurzivně z podsložky nebo pouze z určené složky. |Hodnota TRUE, False (výchozí) |Ne |

**FileSystemSink** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| copyBehavior |Definuje chování kopírování, pokud je zdroj BlobSource nebo systému souborů. |**PreserveHierarchy:** Zachová hierarchií souborů v cílové složce. To znamená relativní cestu ke zdrojové složce zdrojový soubor je stejný jako relativní cesta cílový soubor do cílové složky.<br/><br/>**FlattenHierarchy:** Všechny soubory ze zdrojové složky vytvořené v první úroveň cílové složky. Cílové soubory se vytvoří s automaticky generovaným názvem.<br/><br/>**MergeFiles:** Sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název nebo objekt blob název souboru, název sloučený soubor je zadaný název. V opačném případě je název automaticky generovaného souboru. |Ne |

### <a name="recursive-and-copybehavior-examples"></a>rekurzivní a copyBehavior příklady
Tato část popisuje výsledné chování pro různé kombinace hodnot pro vlastnosti rekurzivní a copyBehavior operace kopírování.

| rekurzivní hodnota | Hodnota copyBehavior | Výsledné chování |
| --- | --- | --- |
| true (pravda) |preserveHierarchy |Pro zdrojové složky složku1 s následující strukturou,<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>cílové složky složku1 se vytvoří s stejnou strukturu jako zdroj:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true (pravda) |flattenHierarchy |Pro zdrojové složky složku1 s následující strukturou,<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automaticky generovaný název File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File5 |
| true (pravda) |mergeFiles |Pro zdrojové složky složku1 s následující strukturou,<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 File2 + soubor3 + File4 + 5 souboru obsahu jsou sloučeny do jednoho souboru s názvem automaticky generovaný soubor. |
| false (nepravda) |preserveHierarchy |Pro zdrojové složky složku1 s následující strukturou,<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>cílové složky složku1 se vytvoří s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Není Subfolder1 s soubor3 File4 a File5 neexistoval. |
| false (nepravda) |flattenHierarchy |Pro zdrojové složky složku1 s následující strukturou,<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>cílové složky složku1 se vytvoří s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automaticky generovaný název File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File2<br/><br/>Není Subfolder1 s soubor3 File4 a File5 neexistoval. |
| false (nepravda) |mergeFiles |Pro zdrojové složky složku1 s následující strukturou,<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>cílové složky složku1 se vytvoří s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 obsah jsou sloučeny do jednoho souboru s názvem automaticky generovaný soubor.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automaticky generovaný název File1<br/><br/>Není Subfolder1 s soubor3 File4 a File5 neexistoval. |

## <a name="supported-file-and-compression-formats"></a>Podporované formáty souborů a komprese
Zobrazit [formáty souborů a komprese ve službě Azure Data Factory](data-factory-supported-file-and-compression-formats.md) článek věnovaný tomu podrobnosti.

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>Příklady JSON pro kopírování dat do a ze systému souborů
Následující příklady popisují ukázkový JSON definice, které můžete použít k vytvoření kanálu pomocí [webu Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Zobrazí se kopírování dat do a z úložiště objektů Blob v Azure a služby v místním systému souborů. Však můžete zkopírovat data *přímo* z libovolného zdroje do libovolné jímky uvedené v [zdroje a jímky podporované](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování ve službě Azure Data Factory.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Příklad: Kopírování dat z v místním systému souborů do úložiště objektů Blob v Azure
Tato ukázka předvádí, jak kopírovat data ze v místním systému souborů do úložiště objektů Blob v Azure. Ukázka obsahuje následující entity služby Data Factory:

* Propojené služby typu [OnPremisesFileServer](#linked-service-properties).
* Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [sdílení souborů](#dataset-properties).
* Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [FileSystemSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Následující příklad zkopíruje dat časových řad ze v místním systému souborů do úložiště objektů Blob v Azure každou hodinu. Vlastnosti JSON, které se používají v těchto ukázkách jsou popsané v částech po ukázky.

Jako první krok, nastavte si bránu správy dat, podle pokynů v [přesun dat mezi místním zdrojům a cloudem pomocí brány správy dat](data-factory-move-data-between-onprem-and-cloud.md).

**Souborový Server On-Premises propojené služby:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Doporučujeme použít **encryptedCredential** vlastnost místo toho **userid** a **heslo** vlastnosti. Zobrazit [souborového serveru propojená služba](#linked-service-properties) podrobnosti o této propojené službě.

**Propojená služba Azure Storage:**

```JSON
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

**V místním souboru systému vstupní datové sady:**

Data je převzata z nového souboru každou hodinu. Vlastnosti folderPath a název souboru se určují podle času spuštění řezu.  

Nastavení `"external": "true"` služby Data Factory informuje, že datová sada je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

```JSON
{
  "name": "OnpremisesFileSystemInput",
  "properties": {
    "type": " FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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

**Azure Blob storage výstupní datovou sadu:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob se dynamicky vyhodnocuje na základě doby spuštění řez, který se právě zpracovává. Cesta ke složce používá rok, měsíc, den a hodina části čas spuštění.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Aktivita kopírování v kanálu pomocí systému souborů zdroje a jímky objektu Blob:**

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **FileSystemSource**, a **jímky** je typ nastaven na **BlobSink**.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T19:00:00",
    "description":"Pipeline for copy activity",
    "activities":[  
      {
        "name": "OnpremisesFileSystemtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "OnpremisesFileSystemInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "FileSystemSource"
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

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Příklad: Kopírování dat ze služby Azure SQL Database v místním systému souborů
Následující příklad ukazuje:

* Propojené služby typu [AzureSqlDatabase.](data-factory-azure-sql-connector.md#linked-service-properties)
* Propojené služby typu [OnPremisesFileServer](#linked-service-properties).
* Vstupní datová sada typu [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* Výstupní datová sada typu [sdílení souborů](#dataset-properties).
* Kanál s aktivitou kopírování, která používá [SqlSource](data-factory-azure-sql-connector.md##copy-activity-properties) a [FileSystemSink](#copy-activity-properties).

Ukázka zkopíruje data časových řad z tabulky Azure SQL na systém souborů v místním každou hodinu. Vlastnosti JSON, které se používají v těchto ukázkách jsou popsány v částech po ukázky.

**Azure SQL Database propojené služby:**

```JSON
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

**Souborový Server On-Premises propojené služby:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Doporučujeme použít **encryptedCredential** vlastnosti namísto použití **userid** a **heslo** vlastnosti. Zobrazit [systému souborů propojená služba](#linked-service-properties) podrobnosti o této propojené službě.

**Vstupní datová sada Azure SQL:**

Ukázka předpokládá, že jste vytvořili tabulku "MyTable" ve službě Azure SQL a obsahuje sloupec s názvem "timestampcolumn" pro data časových řad.

Nastavení ``“external”: ”true”`` služby Data Factory informuje, že datová sada je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

```JSON
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

**V místním souboru systému výstupní datovou sadu:**

Data zkopírována do nového souboru každou hodinu. FolderPath a název souboru pro tento objekt blob se určují podle času spuštění řezu.

```JSON
{
  "name": "OnpremisesFileSystemOutput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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

**Aktivita kopírování v kanálu s SQL zdroje a jímky systému souborů:**

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **SqlSource**a **jímky** je typ nastaven na **FileSystemSink**. Dotaz SQL, který je určen pro **SqlReaderQuery** vlastnost vybere data za poslední hodinu pro kopírování.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T20:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "AzureSQLtoOnPremisesFile",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "OnpremisesFileSystemOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "FileSystemSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 3,
          "timeout": "01:00:00"
        }
      }
     ]
   }
}
```


Můžete také namapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky v definici aktivity kopírování. Podrobnosti najdete v tématu [mapování sloupců v datové sadě ve službě Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a ladění
 Další informace o klíčových faktorů, které ovlivňují výkon přesouvání dat (aktivita kopírování) ve službě Azure Data Factory a různé způsoby, jak optimalizovat, najdete v článku [Průvodce laděním a výkonem aktivity kopírování](data-factory-copy-activity-performance.md).
