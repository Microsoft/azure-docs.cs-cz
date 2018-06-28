---
title: Kopírování dat do nebo ze systému souborů pomocí Azure Data Factory | Microsoft Docs
description: Zjistěte, jak ke zkopírování dat do a ze v místním systému souborů pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: ce19f1ae-358e-4ffc-8a80-d802505c9c84
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7ab38c689cb6445bc85a942fc350c2a1f5de7912
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37047039"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Kopírování dat do a ze v místním systému souborů pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-onprem-file-system-connector.md)
> * [Verze 2 (aktuální verze)](../connector-file-system.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [konektor systému souborů v V2](../connector-file-system.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z v místním systému souborů. Vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který představuje obecný přehled přesun dat s aktivitou kopírování.

## <a name="supported-scenarios"></a>Podporované scénáře
Může kopírovat data **ze systému souborů na místě** ukládá do následující data:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Může kopírovat data z následujících datových úložišť **na systém souborů místní**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Aktivita kopírování nedojde k odstranění zdrojového souboru po byl úspěšně zkopírován do cílové. Pokud potřebujete odstranit zdrojový soubor po úspěšné kopie, vytvořte vlastní aktivity odstranit soubor a použijte aktivitu v kanálu. 

## <a name="enabling-connectivity"></a>Povolení připojení
Objekt pro vytváření dat podporuje připojení do a ze v místním systému souborů prostřednictvím **Brána pro správu dat**. Brána pro správu dat je nutné nainstalovat v prostředí místní služby Data Factory pro připojení k úložišti všechny podporované místní data, včetně systému souborů. Další informace o Brána pro správu dat a podrobné pokyny k nastavení brány najdete v tématu [přesun dat mezi místní zdroje a cloudu s Brána pro správu dat](data-factory-move-data-between-onprem-and-cloud.md). Kromě Brána pro správu dat musíte nainstalovat komunikace do a ze v místním systému souborů žádné binární soubory. Musíte nainstalovat a použít Brána pro správu dat, i když je systém souborů ve virtuálním počítači Azure IaaS. Podrobné informace o bráně najdete v tématu [Brána pro správu dat](data-factory-data-management-gateway.md).

Chcete-li použít sdílenou složku systému Linux, nainstalovat [Samba](https://www.samba.org/) na Linux server a instalaci brány pro správu dat v systému Windows server. Instalace brány pro správu dat na Linux server není podporována.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data ze systému souborů pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním**. V tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) podrobný rychlé vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru** , **.NET API**, a **rozhraní REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování.

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený:

1. Vytvoření **objekt pro vytváření dat**. Objekt pro vytváření dat může obsahovat jeden nebo víc kanálů. 
2. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory. Pokud jsou kopírování dat z Azure blob storage na systém souborů na místě, například vytvoříte dvě propojené služby k propojení vaší místní systém souborů a účet úložiště Azure pro vytváření dat. Vlastnosti propojené služby, které jsou specifické pro systém souborů na místě, najdete v části [propojené vlastnosti služby](#linked-service-properties) části.
3. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování. V příkladu uvedených v posledním kroku vytvoříte datové sady a zadat kontejner objektů blob a složky, která obsahuje vstupní data. A vytvořte jinou datovou sadu, která určete složku a název souboru (volitelné) v systému souborů. Vlastnosti datové sady, které jsou specifické pro systém souborů na místě, najdete v části [vlastnosti datové sady](#dataset-properties) části.
4. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. V příkladu již bylo zmíněno dříve použijete BlobSource jako zdroj a FileSystemSink jako jímku pro aktivitu kopírování. Podobně pokud kopírujete z místní systém souborů do úložiště objektů Blob Azure, můžete použít FileSystemSource a BlobSink v aktivitě kopírování. Kopírovat vlastnosti aktivity, které jsou specifické pro systém souborů na místě, najdete v části [zkopírovat vlastnosti aktivity](#copy-activity-properties) části. Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímka klikněte na odkaz v předchozí části pro data store.

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Při použití nástroje nebo rozhraní API (s výjimkou .NET API), definujete tyto entity služby Data Factory pomocí formátu JSON.  Ukázky s definicemi JSON entit služby Data Factory, které se používají ke zkopírování dat do nebo ze systému souborů naleznete v části [JSON příklady](#json-examples-for-copying-data-to-and-from-file-system) tohoto článku.

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které slouží k určení entit služby Data Factory, které jsou specifické pro systém souborů:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Systém souborů na místě můžete propojit s objektem pro vytváření dat Azure s **místní souborový Server** propojené služby. Následující tabulka obsahuje popis elementy JSON, které jsou specifické pro službu propojené místní souborový Server.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Ujistěte se, že je vlastnost Typ nastavena **OnPremisesFileServer**. |Ano |
| hostitel |Určuje cestu kořenové složky, kterou chcete zkopírovat. Použít řídicí znak ' \ ' pro speciální znaky v řetězci. V tématu [ukázka propojené definice služby a datovou sadu](#sample-linked-service-and-dataset-definitions) příklady. |Ano |
| ID uživatele |Zadejte ID uživatele, který má přístup k serveru. |Ne (když zvolíte encryptedCredential) |
| heslo |Zadejte heslo pro uživatele (ID uživatele). |Ne (když zvolíte encryptedCredential |
| encryptedCredential |Zadejte zašifrované přihlašovací údaje, které můžete získat spuštěním rutiny New-AzureRmDataFactoryEncryptValue. |Ne (když zvolíte možnost zadat ID uživatele a heslo ve formátu prostého textu) |
| gatewayName |Určuje název brány, kterou Data Factory měla použít pro připojení k souborovému serveru místně. |Ano |


### <a name="sample-linked-service-and-dataset-definitions"></a>Ukázkové propojené služby a definice datové sady
| Scénář | Hostování v definici propojené služby | folderPath v definici datové sady |
| --- | --- | --- |
| Místní složky v počítači brány pro správu dat: <br/><br/>Příklady: D:\\ \* nebo D:\folder\subfolder\\* |D:\\ \\ (pro Data Management Gateway 2.0 nebo novější) <br/><br/> localhost (pro starší verze než Data Management Gateway 2.0) |. \\ \\ nebo složky\\\\podsložky (pro Data Management Gateway 2.0 nebo novější) <br/><br/>D:\\ \\ nebo D:\\\\složky\\\\podsložky (pro brány verzi nižší než 2.0) |
| Vzdálené sdílené složce: <br/><br/>Příklady: \\ \\myserver\\sdílet\\ \* nebo \\ \\myserver\\sdílet\\složky\\podsložky\\* |\\\\\\\\myserver\\\\sdílet |. \\ \\ nebo složky\\\\podsložky |

>[!NOTE]
>Při vytváření prostřednictvím uživatelského rozhraní, nemusíte vstupní dvojité zpětné lomítko (`\\`) abyste se vyhnuli, stejně jako prostřednictvím formátu JSON, zadejte jedno zpětné lomítko.

### <a name="example-using-username-and-password-in-plain-text"></a>Příklad: Pomocí uživatelského jména a hesla ve formátu prostého textu

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
Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v části [vytváření datových sad](data-factory-create-datasets.md). Oddíly jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu.

V rámci typeProperties části se liší pro jednotlivé typy datovou sadu. Poskytuje informace, jako je například umístění a formát dat v úložišti. Rámci typeProperties část datové sady typ **sdílení souborů** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Určuje dílčí cestou ke složce. Použít řídicí znak '\' pro speciální znaky v řetězci. Zástupný filtr není podporován. V tématu [ukázka propojené definice služby a datovou sadu](#sample-linked-service-and-dataset-definitions) příklady.<br/><br/>Tato vlastnost se můžete kombinovat **partitionBy** tak, aby měl složky cesty založené na řez počáteční nebo koncové hodnoty data a času. |Ano |
| fileName |Zadejte název souboru do **folderPath** Pokud chcete, aby v tabulce odkazovat na konkrétní soubor ve složce. Pokud nezadáte žádnou hodnotu pro tuto vlastnost, tabulka odkazuje na všechny soubory ve složce.<br/><br/>Když **fileName** pro datovou sadu výstupů není zadána a **preserveHierarchy** není zadané v aktivity podřízený název vygenerovaný soubor je v následujícím formátu: <br/><br/>`Data.<Guid>.txt` (Příklad: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Ne |
| fileFilter |Zadejte filtr pro umožňuje vybrat podmnožinu souborů v folderPath, nikoli všech souborů. <br/><br/>Povolené hodnoty jsou: `*` (více znaků) a `?` (jeden znak).<br/><br/>Příklad 1: "fileFilter": "* .log"<br/>Příklad 2: "fileFilter": 2014 - 1-?. TXT"<br/><br/>Všimněte si, že fileFilter je použít pro datové sadě služby vstupní sdílení souborů. |Ne |
| partitionedBy |PartitionedBy můžete použít k určení dynamické folderPath nebo název souboru pro data časové řady. Příkladem je folderPath parametry pro každou hodinu data. |Ne |
| Formát | Jsou podporovány následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Nastavte **typ** vlastnost pod formát na jednu z těchto hodnot. Další informace najdete v tématu [textovém formátu](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátu](data-factory-supported-file-and-compression-formats.md#orc-format), a [Parquet formát](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete **zkopírujte soubory jako-je** mezi souborové úložiště (binární kopie), přeskočte část formátu v obou definice vstupní a výstupní datové sady. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimal** a **nejrychlejší**. v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

> [!NOTE]
> Název souboru a fileFilter nelze současně použít.

### <a name="using-partitionedby-property"></a>Pomocí vlastnost partitionedBy
Jak je uvedeno v předchozí části, můžete zadat dynamické folderPath a název souboru pro data časové řady s **partitionedBy** vlastnost [funkce pro vytváření dat a systémové proměnné](data-factory-functions-variables.md).

Zjistit další informace o datových sad časové řady, plánování a řezů, najdete v části [vytváření datových sad](data-factory-create-datasets.md), [plánování a provádění](data-factory-scheduling-and-execution.md), a [vytváření kanálů](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Příklad 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

V tomto příkladu {řez} se nahradí hodnoty proměnné objektu pro vytváření dat systému SliceStart ve formátu (YYYYMMDDHH). SliceStart odkazuje na spuštění řezu. FolderPath se liší pro každý řez. Příklad: wikidatagateway/wikisampledataout/2014100103 nebo wikidatagateway/wikisampledataout/2014100104.

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

V tomto příkladu se extrahují rok, měsíc, den a čas SliceStart do samostatné proměnných, které používají vlastnosti folderPath a název souboru.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, například název, popis, vstupní a výstupní datové sady a zásad jsou dostupné pro všechny typy aktivit. Vzhledem k tomu, vlastnosti dostupné ve **rámci typeProperties** části aktivity se liší podle každý typ aktivity.

Pro aktivitu kopírování budou lišit v závislosti na typech zdrojů a jímky. Pokud přesouváte data ze v místním systému souborů, nastavíte typ zdroje v aktivitě kopírování do **FileSystemSource**. Podobně pokud přesouváte data na systém souborů na místě, nastavíte typ jímky v aktivitě kopírování do **FileSystemSink**. Tato část obsahuje seznam vlastností, které jsou podporované FileSystemSource a FileSystemSink.

**FileSystemSource** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| rekurzivní |Označuje, zda je data načíst rekurzivně z podsložky nebo pouze do zadané složky. |Hodnota TRUE, False (výchozí) |Ne |

**FileSystemSink** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| copyBehavior |Definuje chování kopie, pokud je zdroj BlobSource nebo systému souborů. |**PreserveHierarchy:** zachovává hierarchii souborů v cílové složce. To znamená relativní cesta zdrojového souboru do zdrojové složky je stejný jako relativní cestu k souboru cíl k cílové složce.<br/><br/>**FlattenHierarchy:** všechny soubory ze zdrojové složky jsou vytvořené v první úroveň cílové složce. Cílové soubory jsou vytvořeny pomocí názvu objektu generován automaticky.<br/><br/>**MergeFiles:** slučuje všechny soubory ze zdrojové složky pro jeden soubor. Pokud je zadán název nebo objekt blob název souboru, název souboru sloučené je zadaný název. Jinak je název automaticky generovaný soubor. |Ne |

### <a name="recursive-and-copybehavior-examples"></a>Příklady rekurzivní a copyBehavior
Tato část popisuje jejich výsledné chování pro různé kombinace hodnot vlastností rekurzivní a copyBehavior operace kopírování.

| rekurzivní hodnota | Hodnota copyBehavior | Výsledné chování |
| --- | --- | --- |
| true (pravda) |preserveHierarchy |Pro zdrojovou složku složku1 s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cílové složky složku1 je vytvořen s stejná struktura jako zdroj:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true (pravda) |flattenHierarchy |Pro zdrojovou složku složku1 s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cíl složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File5 |
| true (pravda) |mergeFiles |Pro zdrojovou složku složku1 s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cíl složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + soubor3 + File4 + soubor 5 obsah jsou sloučeny do jednoho souboru s názvem automaticky generovaný soubor. |
| false (nepravda) |preserveHierarchy |Pro zdrojovou složku složku1 s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cílové složky složku1 je vytvořen s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 s soubor3, File4 a File5 není zachyceny. |
| false (nepravda) |flattenHierarchy |Pro zdrojovou složku složku1 s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cílové složky složku1 je vytvořen s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File2<br/><br/>Subfolder1 s soubor3, File4 a File5 není zachyceny. |
| false (nepravda) |mergeFiles |Pro zdrojovou složku složku1 s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cílové složky složku1 je vytvořen s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 obsah jsou sloučeny do jednoho souboru s názvem automaticky generovaný soubor.<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File1<br/><br/>Subfolder1 s soubor3, File4 a File5 není zachyceny. |

## <a name="supported-file-and-compression-formats"></a>Podporované formáty souborů a komprese
V tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md) článek na podrobnosti.

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>Příklady JSON pro kopírování dat do a ze systému souborů
Následující příklady poskytují ukázka JSON definice, které můžete použít k vytvoření kanálu pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Se ukazují, jak ke zkopírování dat do a z v místním systému souborů a úložiště objektů Blob v Azure. Však může kopírovat data *přímo* ze všech zdrojů do jakéhokoli z jímky uvedené v [podporované zdroje a jímky](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Příklad: Kopírování dat z v místním systému souborů do úložiště objektů Blob v Azure
Tento příklad ukazuje, jak ke kopírování dat ze v místním systému souborů do úložiště objektů Blob Azure. Ukázka má následující entity služby Data Factory:

* Propojené služby typu [OnPremisesFileServer](#linked-service-properties).
* Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstup [datovou sadu](data-factory-create-datasets.md) typu [sdílení souborů](#dataset-properties).
* Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [FileSystemSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Následující příklad zkopíruje data časové řady ze v místním systému souborů do úložiště objektů Blob Azure každou hodinu. Vlastnosti JSON, které se používají v tyto ukázky jsou popsané v částech po ukázky.

Jako první krok, nastavit Brána pro správu dat podle pokynů v [přesun dat mezi místní zdroje a cloudu s Brána pro správu dat](data-factory-move-data-between-onprem-and-cloud.md).

**Souborový Server místní propojené služby:**

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

Doporučujeme používat **encryptedCredential** vlastnost místo toho **userid** a **heslo** vlastnosti. V tématu [souborového serveru propojená služba](#linked-service-properties) podrobnosti o této propojené službě.

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

**Místní soubor vstupní datové sady systému:**

Data je převzata z nový soubor každou hodinu. Vlastnosti folderPath a název souboru jsou určeny podle času zahájení řezu.  

Nastavení `"external": "true"` informuje objekt pro vytváření dat, datová sada je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

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

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Cesta ke složce pro tento objekt blob je vyhodnocován dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc, den a hodina části čas spuštění.

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

**Aktivita kopírování v kanálu pomocí systému souborů zdrojový a podřízený objekt Blob:**

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **FileSystemSource**, a **podřízený** je typ nastaven na **BlobSink**.

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

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Příklad: Kopírování dat z databáze Azure SQL Database na systém souborů na místě
Následující příklad ukazuje:

* Propojené služby typu [azuresqldatabase..](data-factory-azure-sql-connector.md#linked-service-properties)
* Propojené služby typu [OnPremisesFileServer](#linked-service-properties).
* Vstupní datové sady typu [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* Výstupní datové typu [sdílení souborů](#dataset-properties).
* Kanál s aktivitou kopírování, která používá [SqlSource](data-factory-azure-sql-connector.md##copy-activity-properties) a [FileSystemSink](#copy-activity-properties).

Ukázka kopíruje data časové řady z tabulky Azure SQL na systém souborů na místě každou hodinu. Vlastnosti JSON, které se používají v tyto ukázky jsou popsané v částech po ukázky.

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

**Souborový Server místní propojené služby:**

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

Doporučujeme používat **encryptedCredential** vlastnost místo použití **userid** a **heslo** vlastnosti. V tématu [systém souborů propojená služba](#linked-service-properties) podrobnosti o této propojené službě.

**Azure SQL vstupní datové sady:**

Ukázka předpokládá, že jste vytvořili tabulku "MyTable" v Azure SQL, a obsahuje sloupec s názvem "timestampcolumn" pro data časové řady.

Nastavení ``“external”: ”true”`` informuje objekt pro vytváření dat, datová sada je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

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

**Místní soubor výstupní datovou sadu systému:**

Data se zkopírují do nového souboru každou hodinu. FolderPath a název souboru pro tento objekt blob jsou určeny podle času zahájení řezu.

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

**Aktivita kopírování v kanálu s SQL zdroj a jímka systému souborů:**

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **SqlSource**a **podřízený** je typ nastaven na **FileSystemSink**. Příkaz jazyka SQL, která je určená pro **SqlReaderQuery** vlastnost vybere data za poslední hodinu pro kopírování.

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


Můžete také mapovat sloupců z datové sady zdroje na sloupce ze sady jímku dat v definici aktivity kopírování. Podrobnosti najdete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a ladění
 Další informace o klíčových faktorů, které mít dopad na výkon přesun dat (aktivita kopírování) v Azure Data Factory a různé způsoby, jak optimalizovat ho najdete v tématu [výkonu kopie aktivity a vyladění průvodce](data-factory-copy-activity-performance.md).
