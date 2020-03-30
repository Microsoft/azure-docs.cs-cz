---
title: Kopírování dat do/ze systému souborů pomocí Azure Data Factory
description: Zjistěte, jak kopírovat data do a z místního systému souborů pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: ce19f1ae-358e-4ffc-8a80-d802505c9c84
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d298c83c0c1a0f33f28644e2e467ad5035300221
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265932"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Kopírování dat do a z místního systému souborů pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-onprem-file-system-connector.md)
> * [Verze 2 (aktuální verze)](../connector-file-system.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte [si téma Konektor systému souborů ve verzi 2](../connector-file-system.md).


Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat do nebo z místního systému souborů. Vychází z článku [Aktivity přesunu dat,](data-factory-data-movement-activities.md) který představuje obecný přehled přesunu dat s aktivitou kopírování.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Podporované scénáře
Data z **místního systému souborů** můžete zkopírovat do následujících úložišť dat:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Data z následujících úložišť dat můžete kopírovat **do místního systému souborů**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Aktivita kopírování neodstraní zdrojový soubor po úspěšném zkopírování do cílového umístění. Pokud potřebujete po úspěšné kopii odstranit zdrojový soubor, vytvořte vlastní aktivitu, která soubor odstraní a použije aktivitu v kanálu.

## <a name="enabling-connectivity"></a>Povolení připojení
Data Factory podporuje připojení k místnímu systému souborů a z jeho místního systému prostřednictvím **brány pro správu dat**. Chcete-li se připojit k jakémukoli podporovanému místnímu úložišti dat včetně systému souborů, musíte nainstalovat bránu pro správu dat v místním prostředí. Další informace o službě Data Management Gateway a podrobných pokynech k nastavení brány najdete v tématu [Přesun dat mezi místními zdroji a cloudem pomocí brány pro správu dat](data-factory-move-data-between-onprem-and-cloud.md). Kromě brány pro správu dat není nutné instalovat žádné další binární soubory pro komunikaci s místním systémem souborů a z místního systému souborů. Bránu pro správu dat je nutné nainstalovat a používat i v případě, že je systém souborů ve virtuálním počítači Azure IaaS. Podrobné informace o bráně naleznete v [tématu Brána pro správu dat](data-factory-data-management-gateway.md).

Chcete-li použít sdílenou složku Linuxu, nainstalujte [Sambu](https://www.samba.org/) na svůj linuxový server a nainstalujte bránu pro správu dat na serveru se systémem Windows. Instalace brány pro správu dat na server Linuxu není podporována.

## <a name="getting-started"></a>Začínáme
Pomocí různých nástrojů nebo api můžete vytvořit kanál s aktivitou kopírování, která přesouvá data do/ze systému souborů.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Viz [Kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) pro rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat.

K vytvoření kanálu můžete taky použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Šablona Azure Resource Manager**, Rozhraní **.NET API**a REST **API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aktivity.

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **datovou továrnu**. Továrna dat může obsahovat jeden nebo více kanálů.
2. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat. Například pokud kopírujete data z úložiště objektů blob Azure do místního systému souborů, vytvoříte dvě propojené služby, které propojí místní systém souborů a účet úložiště Azure s vaší továrně dat. Vlastnosti propojené služby, které jsou specifické pro místní systém souborů, naleznete v části [Vlastnosti propojené služby.](#linked-service-properties)
3. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování. V příkladu uvedeném v posledním kroku vytvoříte datovou sadu, která určí kontejner objektů blob a složku, která obsahuje vstupní data. A vytvoříte další datovou sadu, která určí název složky a souboru (volitelné) v systému souborů. Vlastnosti datové sady, které jsou specifické pro místní systém souborů, naleznete v části [Vlastnosti datové sady.](#dataset-properties)
4. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. V příkladu uvedeném výše použijete Objekt BlobSource jako zdroj a FileSystemSink jako jímku pro aktivitu kopírování. Podobně pokud kopírujete z místního systému souborů do úložiště objektů blob Azure, použijete v aktivitě kopírování FileSystemSource a BlobSink. Informace o vlastnostech aktivity kopírování, které jsou specifické pro místní systém souborů, naleznete v části [Kopírování vlastností aktivity.](#copy-activity-properties) Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímku, klikněte na odkaz v předchozí části úložiště dat.

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory (propojené služby, datové sady a kanál). Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON.  Ukázky s definicemi JSON pro entity Data Factory, které se používají ke kopírování dat do/ze systému souborů, naleznete v části [příklady JSON](#json-examples-for-copying-data-to-and-from-file-system) v tomto článku.

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Factory dat specifických pro systém souborů:

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb
Místní souborový systém můžete propojit s toovou továrně dat Azure s **propojenou službou Místní souborový server.** Následující tabulka obsahuje popisy prvků JSON, které jsou specifické pro propojenou službu místního souborového serveru.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Ujistěte se, že vlastnost type je nastavena **na OnPremisesFileServer**. |Ano |
| host |Určuje kořenovou cestu složky, kterou chcete zkopírovat. Pro speciální znaky v řetězci použijte řídicí znak \ ' . Viz [Ukázka propojené služby a definice datových sad](#sample-linked-service-and-dataset-definitions) pro příklady. |Ano |
| Userid |Zadejte ID uživatele, který má přístup k serveru. |Ne (pokud zvolíte encryptedCredential) |
| heslo |Zadejte heslo pro uživatele (userid). |Ne (pokud zvolíte encryptedCredential |
| šifrované pověření |Zadejte šifrovaná pověření, která můžete získat spuštěním rutiny New-AzDataFactoryEncryptValue. |Ne (pokud se rozhodnete zadat userid a heslo ve formátu prostého textu) |
| název brány |Určuje název brány, kterou by měla data factory použít pro připojení k místnímu souborovému serveru. |Ano |


### <a name="sample-linked-service-and-dataset-definitions"></a>Ukázkové definice propojených služeb a datových sad
| Scénář | Hostitel v definici propojené služby | folderPath v definici datové sady |
| --- | --- | --- |
| Místní složka v počítači brány pro správu dat: <br/><br/>Příklady: D:\\ \* nebo D:\folder\subfolder\\\* |D:\\ \\ (pro bránu pro správu dat 2.0 a novější verze) <br/><br/> localhost (pro starší verze než Brána pro správu dat 2.0) |. nebo\\\\podsložkou složky (pro bránu Pro správu dat 2.0 a novější verze) \\ \\ <br/><br/>D:\\ \\ nebo\\\\D: podsložka (pro\\\\verzi brány pod 2.0) |
| Vzdálená sdílená složka: <br/><br/>Příklady: \\ \\podsložka\\ \* \\ \\myserver\\\\\\share\\nebo myserver share folder\\\* |\\\\\\\\myserver\\\\sdílet |. nebo podsložka složky\\\\ \\ \\ |

>[!NOTE]
>Při vytváření pomocí ui, nemusíte zadávat dvojité zpětné`\\`lomítko ( ) uniknout, jako to děláte přes JSON, zadejte jedno zpětné lomítko.

### <a name="example-using-username-and-password-in-plain-text"></a>Příklad: Použití uživatelského jména a hesla ve formátu prostého textu

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

### <a name="example-using-encryptedcredential"></a>Příklad: Použití šifrovaného pověření

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
Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete [v tématu Vytváření datových sad](data-factory-create-datasets.md). Oddíly, jako je struktura, dostupnost a zásady datové sady JSON jsou podobné pro všechny typy datových sad.

Oddíl typeProperties se liší pro každý typ datové sady. Poskytuje informace, jako je umístění a formát dat v úložišti dat. Oddíl typeProperties pro datovou sadu typu **FileShare** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| folderPath |Určuje podcestu ke složce. Použijte řídicí znak\' ' pro speciální znaky v řetězci. Filtr se zástupnými symboly není podporován. Viz [Ukázka propojené služby a definice datových sad](#sample-linked-service-and-dataset-definitions) pro příklady.<br/><br/>Tuto vlastnost můžete kombinovat s **partitionBy** mít cesty ke složkám na základě počátečního a koncového data řezu. |Ano |
| fileName |Zadejte název souboru ve **složceCesta,** pokud chcete, aby tabulka odkazovala na určitý soubor ve složce. Pokud pro tuto vlastnost nezadáte žádnou hodnotu, tabulka odkazuje na všechny soubory ve složce.<br/><br/>Pokud **fileName** není zadán pro výstupní datovou sadu a **preserveHierarchy** není zadán v jímce aktivity, název generovaného souboru je v následujícím formátu: <br/><br/>`Data.<Guid>.txt`(Příklad: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Ne |
| filtr souboru |Určete filtr, který se použije k výběru podmnožiny souborů ve složceCesta, nikoli všechny soubory. <br/><br/>Povolené hodnoty `*` jsou: (více `?` znaků) a (jeden znak).<br/><br/>Příklad 1: "fileFilter": "*.log"<br/>Příklad 2: "fileFilter": 2014-1-?. txt"<br/><br/>Všimněte si, že fileFilter je použitelný pro vstupní datovou sadu FileShare. |Ne |
| partitionedBy |Pomocí funkce partitionedBy můžete určit dynamickou složkuCesta/název_souboru pro data časových řad. Příkladem je folderPath parametrizovaný pro každou hodinu dat. |Ne |
| formát | Podporovány jsou následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte vlastnost **type** ve formátu na jednu z těchto hodnot. Další informace naleznete v části [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), Formát [Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), Formát [orků](data-factory-supported-file-and-compression-formats.md#orc-format)a [Formát parket.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Pokud chcete **kopírovat soubory jako -je** mezi obchody založenými na souborech (binární kopie), přeskočte oddíl formátu v definicích vstupní i výstupní datové sady. |Ne |
| komprese | Určete typ a úroveň komprese dat. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**a **ZipDeflate**. Podporované úrovně jsou: **Optimální** a **nejrychlejší**. viz [Formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

> [!NOTE]
> FileName a fileFilter nelze použít současně.

### <a name="using-partitionedby-property"></a>Použití vlastnosti partitionedBy
Jak je uvedeno v předchozí části, můžete zadat dynamickou složkuPath a název souboru pro data časových řad s **vlastností partitionedBy,** [funkcemi Data Factory a systémovými proměnnými](data-factory-functions-variables.md).

Další podrobnosti o datových sadách časových řad, plánování a řezech naleznete v [tématech Vytváření datových sad](data-factory-create-datasets.md), [Plánování a spuštění](data-factory-scheduling-and-execution.md)a Vytváření [kanálů](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Vzorek 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

V tomto příkladu je {Slice} nahrazen hodnotou systémové proměnné Data Factory SliceStart ve formátu (YYYYMMDDHH). SliceStart odkazuje na počáteční čas řezu. SložkaPath se liší pro každý řez. Například: wikidatagateway/wikisampledataout/2014100103 nebo wikidatagateway/wikisampledataout/2014100104.

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

V tomto příkladu jsou rok, měsíc, den a čas SliceStart extrahovány do samostatných proměnných, které používají vlastnosti folderPath a fileName.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) Vlastnosti, jako je název, popis, vstupní a výstupní datové sady a zásady jsou k dispozici pro všechny typy aktivit. Vzhledem k tomu, vlastnosti, které jsou k dispozici v **typeProperties** části aktivity se liší s každým typem aktivity.

U aktivity kopírování se liší v závislosti na typech zdrojů a propadů. Pokud přesouváte data z místního systému souborů, nastavte typ zdroje v aktivitě kopírování na **FileSystemSource**. Podobně pokud přesouváte data do místního systému souborů, nastavíte typ jímky v aktivitě kopírování na **FileSystemSink**. Tato část obsahuje seznam vlastností podporovaných filesystemsource a FileSystemSink.

**FileSystemSource** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| Rekurzivní |Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. |Pravda, Nepravda (výchozí) |Ne |

**FileSystemSink** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| copyBehavior |Definuje chování kopírování, pokud je zdrojem Objekt BlobSource nebo FileSystem. |**Zachovat hierarchii:** Zachová hierarchii souborů v cílové složce. To znamená, že relativní cesta zdrojového souboru ke zdrojové složce je stejná jako relativní cesta cílového souboru do cílové složky.<br/><br/>**Sloučení hierarchie:** Všechny soubory ze zdrojové složky jsou vytvořeny v první úrovni cílové složky. Cílové soubory jsou vytvořeny s automaticky generovaným názvem.<br/><br/>**MergeFiles:** Sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru nebo objektblou, je zadanýnázev sloučeného souboru. V opačném případě se jedná o automaticky generovaný název souboru. |Ne |

### <a name="recursive-and-copybehavior-examples"></a>rekurzivní a copyBehavior příklady
Tato část popisuje výsledné chování operace Copy pro různé kombinace hodnot pro rekurzivní a copyBehavior vlastnosti.

| rekurzivní hodnota | hodnota copyBehavior | Výsledné chování |
| --- | --- | --- |
| true |zachovat hierarchii |U zdrojové složky Folder1 s následující strukturou<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5<br/><br/>cílová složka Folder1 je vytvořena se stejnou strukturou jako zdroj:<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 |
| true |sloučení hierarchie |U zdrojové složky Folder1 s následující strukturou<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5<br/><br/>cílová složka Folder1 je vytvořena s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název souboru1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název pro Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název souboru3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název souboru4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název souboru5 |
| true |mergeFiles |U zdrojové složky Folder1 s následující strukturou<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5<br/><br/>cílová složka Folder1 je vytvořena s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 obsah jsou sloučeny do jednoho souboru s automaticky generovaným názvem souboru. |
| false (nepravda) |zachovat hierarchii |U zdrojové složky Folder1 s následující strukturou<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5<br/><br/>cílová složka Folder1 je vytvořena s následující strukturou:<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/><br/>Podsložka1 s File3, File4 a File5 není zvedl. |
| false (nepravda) |sloučení hierarchie |U zdrojové složky Folder1 s následující strukturou<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5<br/><br/>cílová složka Folder1 je vytvořena s následující strukturou:<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název souboru1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název pro Soubor2<br/><br/>Podsložka1 s File3, File4 a File5 není zvedl. |
| false (nepravda) |mergeFiles |U zdrojové složky Folder1 s následující strukturou<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5<br/><br/>cílová složka Folder1 je vytvořena s následující strukturou:<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Obsah File1 + File2 se sloučí do jednoho souboru s automaticky generovaným názvem souboru.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automaticky generovaný název souboru1<br/><br/>Podsložka1 s File3, File4 a File5 není zvedl. |

## <a name="supported-file-and-compression-formats"></a>Podporované formáty souborů a komprese
V článku [Soubory a kompresní formáty v Azure Data Factory](data-factory-supported-file-and-compression-formats.md) najdete podrobnosti.

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>Příklady JSON pro kopírování dat do a ze systému souborů
Následující příklady poskytují ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí [Sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo Azure [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazují, jak kopírovat data do a z místního systému souborů a úložiště objektů blob Azure. Můžete však zkopírovat data *přímo* z libovolného zdroje do některého z jímky uvedené v [podporované zdroje a jímky](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Příklad: Kopírování dat z místního systému souborů do úložiště objektů Blob Azure
Tato ukázka ukazuje, jak zkopírovat data z místního systému souborů do úložiště objektů blob Azure. Ukázka má následující entity Data Factory:

* Propojená služba typu [OnPremisesFileServer](#linked-service-properties).
* Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní [datová sada](data-factory-create-datasets.md) typu [FileShare](#dataset-properties).
* Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, který používá [FileSystemSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Následující ukázka zkopíruje data časových řad z místního systému souborů do úložiště objektů Blob Azure každou hodinu. Vlastnosti JSON, které se používají v těchto ukázkách jsou popsány v částech za vzorky.

Jako první krok nastavte bránu pro správu dat podle pokynů v části [Přesun dat mezi místními zdroji a cloudem pomocí brány pro správu dat](data-factory-move-data-between-onprem-and-cloud.md).

**Propojená služba místního souborového serveru:**

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

Doporučujeme použít vlastnost **encryptedCredential** místo **vlastností userid** a **password.** Podrobnosti o této propojené službě naleznete v [tématu Propojená služba souborového serveru.](#linked-service-properties)

**Propojené služby Azure Storage:**

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

**Vstupní datová sada místního systému souborů:**

Data jsou vyzvednuta z nového souboru každou hodinu. Vlastnosti folderPath a fileName jsou určeny na základě počátečního času řezu.

Nastavení `"external": "true"` informuje Data Factory, že datová sada je externí pro datové továrny a není vyroben a aktivity v datové továrně.

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

**Výstupní datová sada úložiště objektů blob Azure:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části počátečního času rok, měsíc, den a hodinu.

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

**Aktivita kopírování v kanálu se zdrojem systému souborů a jímkou blob:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánována na každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **FileSystemSource**a typ **jímky** je nastaven na **objekt BlobSink**.

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

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Příklad: Kopírování dat z Azure SQL Database do místního systému souborů
Následující ukázka ukazuje:

* Propojená služba typu [AzureSqlDatabase.](data-factory-azure-sql-connector.md#linked-service-properties)
* Propojená služba typu [OnPremisesFileServer](#linked-service-properties).
* Vstupní datová sada typu [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* Výstupní datová sada typu [FileShare](#dataset-properties).
* Kanál s aktivitou kopírování, která používá [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) a [FileSystemSink](#copy-activity-properties).

Ukázka zkopíruje data časových řad z tabulky Azure SQL do místního systému souborů každou hodinu. Vlastnosti JSON, které se používají v těchto ukázkách jsou popsány v částech za vzorky.

**Propojená služba Azure SQL Database:**

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

**Propojená služba místního souborového serveru:**

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

Doporučujeme použít **vlastnost encryptedCredential** namísto použití vlastností **userid** a **password.** Podrobnosti o této propojené službě naleznete [v tématu Propojená služba systému souborů.](#linked-service-properties)

**Vstupní datová sada Azure SQL:**

Ukázka předpokládá, že jste vytvořili tabulku "MyTable" v Azure SQL a obsahuje sloupec s názvem "sloupec časového razítka" pro data časových řad.

Nastavení ``“external”: ”true”`` informuje Data Factory, že datová sada je externí pro datové továrny a není vyroben a aktivity v datové továrně.

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

**Výstupní datová sada místního systému souborů:**

Data se zkopírují do nového souboru každou hodinu. SložkaCesta a název_souboru pro objekt blob jsou určeny na základě počátečního času řezu.

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

**Aktivita kopírování v kanálu se zdrojem SQL a jímkou systému souborů:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánována na každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **SqlSource**a typ **jímky** je nastaven na **FileSystemSink**. Dotaz SQL, který je určen pro vlastnost **SqlReaderQuery** vybere data za poslední hodinu ke kopírování.

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

Můžete také mapovat sloupce ze zdrojové datové sady do sloupců z datové sady jímky v definici aktivity kopírování. Podrobnosti najdete [v tématu Mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a ladění
 Informace o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jeho optimalizace, najdete v [tématu Sledování kopírování a ladění průvodce](data-factory-copy-activity-performance.md).
