---
title: Kopírování dat do a ze systému souborů pomocí Azure Data Factory
description: Naučte se, jak kopírovat data do a z místního systému souborů pomocí Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 9acd2adb68347978bb123d2239eedbb3e5595bc3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387389"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Kopírování dat do a z místního systému souborů pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-onprem-file-system-connector.md)
> * [Verze 2 (aktuální verze)](../connector-file-system.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor systému souborů ve verzi v2](../connector-file-system.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data do nebo z místního systému souborů. Sestavuje se podle článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který prezentuje obecný přehled přesunu dat s aktivitou kopírování.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Podporované scénáře
Data **z místního systému souborů** můžete kopírovat do následujících úložišť dat:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Data z následujících úložišť dat můžete kopírovat **do místního systému souborů**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Aktivita kopírování neodstraní zdrojový soubor po úspěšném zkopírování do cíle. Pokud potřebujete zdrojový soubor po úspěšné kopii odstranit, vytvořte vlastní aktivitu k odstranění tohoto souboru a použijte aktivitu v kanálu.

## <a name="enabling-connectivity"></a>Povolení připojení
Data Factory podporuje připojení k místnímu systému souborů přes **bránu Správa dat** a z něj. Bránu Správa dat musíte nainstalovat do místního prostředí, aby se služba Data Factory připojovala k jakémukoli podporovanému místnímu úložišti dat, včetně systému souborů. Další informace o službě Správa dat Gateway a podrobné pokyny k nastavení brány najdete v tématu [přesun dat mezi místními zdroji a cloudem pomocí Správa dat brány](data-factory-move-data-between-onprem-and-cloud.md). Kromě Správa dat brány není nutné instalovat žádné další binární soubory pro komunikaci s místním systémem souborů a z něj. Bránu Správa dat musíte nainstalovat a používat i v případě, že je systém souborů na virtuálním počítači Azure s IaaS. Podrobné informace o bráně najdete v tématu [Správa dat Gateway](data-factory-data-management-gateway.md).

Pokud chcete použít sdílenou složku se systémem Linux, nainstalujte na server Linux Server [Samba](https://www.samba.org/) a nainstalujte Správa dat bránu na Windows Server. Instalace brány Správa dat na serveru Linux není podporována.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data do nebo ze systému souborů pomocí různých nástrojů/rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) .

K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** a **REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **datovou továrnu**. Datová továrna může obsahovat jeden nebo více kanálů.
2. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou. Pokud například kopírujete data z úložiště objektů BLOB v Azure do místního systému souborů, vytvoříte dvě propojené služby, které propojí místní systém souborů a účet Azure Storage s datovou továrnou. Vlastnosti propojené služby, které jsou specifické pro místní systém souborů, najdete v části [vlastnosti propojených služeb](#linked-service-properties) .
3. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování. V příkladu uvedeném v posledním kroku vytvoříte datovou sadu pro určení kontejneru objektů BLOB a složky, která obsahuje vstupní data. A vytvoříte další datovou sadu pro určení složky a názvu souboru (volitelné) v systému souborů. Vlastnosti datové sady, které jsou specifické pro místní systém souborů, najdete v části [Vlastnosti datové sady](#dataset-properties) .
4. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup. V předchozím příkladu použijete jako jímku aktivity kopírování BlobSource jako zdroj a FileSystemSink. Podobně pokud kopírujete z místního systému souborů do služby Azure Blob Storage, v aktivitě kopírování použijete FileSystemSource a BlobSink. Vlastnosti aktivity kopírování, které jsou specifické pro místní systém souborů, najdete v části [vlastnosti aktivity kopírování](#copy-activity-properties) . Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímku, získáte kliknutím na odkaz v předchozí části úložiště dat.

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje/rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON.  Ukázky s definicemi JSON pro Entity Data Factory, které se používají ke kopírování dat do a ze systému souborů, najdete v části [Příklady JSON](#json-examples-for-copying-data-to-and-from-file-system) tohoto článku.

Následující části obsahují podrobné informace o vlastnostech JSON, které se používají k definování Data Factorych entit specifických pro systém souborů:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Místní systém souborů můžete propojit s objektem pro vytváření dat Azure pomocí místní propojené služby **souborového serveru** . Následující tabulka uvádí popisy pro prvky JSON, které jsou specifické pro propojenou službu místního souborového serveru.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ |Ujistěte se, že vlastnost Type je nastavená na **OnPremisesFileServer**. |Yes |
| Hostitel |Určuje kořenovou cestu ke složce, kterou chcete zkopírovat. Pro speciální znaky v řetězci použijte řídicí znak ' \ '. Příklady najdete v tématu [Ukázka propojené služby a definice datových sad](#sample-linked-service-and-dataset-definitions) . |Yes |
| UserID |Zadejte ID uživatele, který má přístup k serveru. |Ne (Pokud zvolíte encryptedCredential) |
| heslo |Zadejte heslo uživatele (UserID). |Ne (Pokud zvolíte encryptedCredential |
| encryptedCredential |Zadejte šifrované přihlašovací údaje, které můžete získat spuštěním rutiny New-AzDataFactoryEncryptValue. |Ne (Pokud se rozhodnete zadat ID uživatele a heslo jako prostý text) |
| gatewayName |Určuje název brány, kterou Data Factory použít pro připojení k místnímu souborovému serveru. |Yes |


### <a name="sample-linked-service-and-dataset-definitions"></a>Ukázka propojené služby a definic datových sad
| Scenario | Hostitel v definici propojené služby | folderPath v definici datové sady |
| --- | --- | --- |
| Místní složka na počítači Správa dat brány: <br/><br/>Příklady: D: \\ \* nebo D:\folder\subfolder\\\* |D: \\ \\ (pro Správa dat Gateway 2,0 a novější verze) <br/><br/> localhost (pro starší verze než Správa dat brána 2,0) |.\\\\ nebo \\ \\ podsložek složky (pro Správa dat bránu 2,0 a novější verze) <br/><br/>D: \\ \\ nebo d: \\ \\ \\ \\ podsložka složky (pro verzi brány nižší než 2,0) |
| Vzdálená sdílená složka: <br/><br/>Příklady: \\ \\ MyServer \\ share nebo sdílená \\ \* \\ \\ \\ Složka MyServer sdílené \\ složky \\\\\* |\\\\\\\\MyServer \\ \\ sdílená složka |.\\\\ nebo \\ \\ podsložek složky |

>[!NOTE]
>Při vytváření pomocí uživatelského rozhraní nemusíte zadávat dvojité zpětné lomítko ( `\\` ) pro řídicí panel, jako je například pomocí JSON, zadejte jednoduché zpětné lomítko.

### <a name="example-using-username-and-password-in-plain-text"></a>Příklad: použití uživatelského jména a hesla v prostém textu

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

### <a name="example-using-encryptedcredential"></a>Příklad: použití encryptedcredential

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
Úplný seznam sekcí a vlastností, které jsou k dispozici pro definování datových sad, najdete v tématu [vytváření datových sad](data-factory-create-datasets.md). Oddíly, jako jsou struktura, dostupnost a zásady pro datovou sadu JSON, jsou podobné pro všechny typy datových sad.

Oddíl typeProperties se liší pro každý typ datové sady. Poskytuje informace, jako je umístění a formát dat v úložišti dat. Oddíl typeProperties pro datovou sadu typu **Shared** má následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| folderPath |Určuje podcestu ke složce. Použijte řídicí znak \' pro speciální znaky v řetězci. Filtr zástupných znaků se nepodporuje. Příklady najdete v tématu [Ukázka propojené služby a definice datových sad](#sample-linked-service-and-dataset-definitions) .<br/><br/>Tuto vlastnost můžete kombinovat s **partitionBy** a mít tak cesty ke složkám na základě data a času začátku a konce řezu. |Yes |
| fileName |Pokud chcete, aby tabulka odkazovala na konkrétní soubor ve složce, zadejte název souboru do **FolderPath** . Pokud pro tuto vlastnost nezadáte žádnou hodnotu, odkazuje tabulka na všechny soubory ve složce.<br/><br/>Pokud není zadán **název souboru** pro výstupní datovou sadu a **preserveHierarchy** není zadán v jímky aktivity, název generovaného souboru je v následujícím formátu: <br/><br/>`Data.<Guid>.txt` (Příklad: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |No |
| fileFilter |Určete filtr, který se použije k výběru podmnožiny souborů v folderPath, nikoli všech souborů. <br/><br/>Povolené hodnoty jsou: `*` (více znaků) a `?` (jeden znak).<br/><br/>Příklad 1: "FileFilter": "*. log"<br/>Příklad 2: "FileFilter": 2014-1-?. txt<br/><br/>Všimněte si, že tento filtr souborů je použitelný pro sadu vstupních souborů Shared. |No |
| partitionedBy |Pomocí partitionedBy můžete zadat dynamický folderPath/fileName pro data časových řad. Příkladem je folderPath parametrizované pro každou hodinu dat. |No |
| formát | Podporovány jsou následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. V části formát nastavte vlastnost **typ** na jednu z těchto hodnot. Další informace najdete v částech [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), [formát JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formát ORC](data-factory-supported-file-and-compression-formats.md#orc-format)a formátování [Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Pokud chcete **Kopírovat soubory** mezi úložišti na základě souborů (binární kopie), přeskočte oddíl formát v definicích vstupní i výstupní datové sady. |No |
| komprese | Zadejte typ a úroveň komprese dat. Podporované typy jsou: **gzip**, **Deflate**, **bzip2** a **ZipDeflate**. Podporované úrovně: **optimální** a **nejrychlejší**. zobrazení [souborů a kompresních formátů v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

> [!NOTE]
> Nemůžete současně použít fileName a FileFilter.

### <a name="using-partitionedby-property"></a>Použití vlastnosti partitionedBy
Jak je uvedeno v předchozí části, můžete zadat dynamický folderPath a filename pro data časových řad pomocí vlastnosti **partitionedBy** , [Data Factory funkcí a systémových proměnných](data-factory-functions-variables.md).

Další informace o datových sadách časových řad, plánování a řezech najdete v tématu [vytváření datových sad](data-factory-create-datasets.md), [plánování a spouštění](data-factory-scheduling-and-execution.md)a [vytváření kanálů](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Ukázka 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

V tomto příkladu je {Slice} nahrazen hodnotou Data Factory systémové proměnné vlastnosti slicestart ve formátu (YYYYMMDDHH). Vlastnosti slicestart odkazuje na počáteční čas řezu. FolderPath se u každého řezu liší. Příklad: wikidatagateway/wikisampledataout/2014100103 nebo wikidatagateway/wikisampledataout/2014100104.

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

V tomto příkladu jsou roky, měsíc, den a čas vlastnosti slicestart extrahovány do samostatných proměnných, které používají vlastnosti folderPath a fileName.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam sekcí & vlastností dostupných pro definování aktivit najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . Pro všechny typy aktivit jsou k dispozici vlastnosti, jako je název, popis, vstupní a výstupní datové sady a zásady. V takovém případě se vlastnosti dostupné v části **typeProperties** v aktivitě liší podle typu aktivity.

U aktivity kopírování se liší v závislosti na typech zdrojů a jímky. Pokud přesouváte data z místního systému souborů, nastavte typ zdroje v aktivitě kopírování na **FileSystemSource**. Podobně Pokud přesouváte data do místního systému souborů, nastavíte typ jímky v aktivitě kopírování na **FileSystemSink**. V této části najdete seznam vlastností podporovaných FileSystemSource a FileSystemSink.

**FileSystemSource** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| zahrnout |Určuje, zda mají být data rekurzivně čtena z podsložek nebo pouze ze zadané složky. |True, false (výchozí) |No |

**FileSystemSink** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| copyBehavior |Definuje chování kopírování, pokud je zdroj BlobSource nebo FileSystem. |**PreserveHierarchy:** Zachová hierarchii souborů v cílové složce. To znamená, že relativní cesta ke zdrojovému souboru ke zdrojové složce je stejná jako relativní cesta cílového souboru k cílové složce.<br/><br/>**FlattenHierarchy:** Všechny soubory ze zdrojové složky se vytvoří v první úrovni cílové složky. Cílové soubory se vytvoří s automaticky generovaným názvem.<br/><br/>**MergeFiles:** Sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadaný název souboru nebo název objektu blob, Název sloučeného souboru je zadaný název. V opačném případě se jedná o automaticky generovaný název souboru. |No |

### <a name="recursive-and-copybehavior-examples"></a>Příklady rekurzivních a copyBehavior
Tato část popisuje výsledné chování operace kopírování pro různé kombinace hodnot pro rekurzivní a copyBehavior vlastnosti.

| rekurzivní hodnota | hodnota copyBehavior | Výsledné chování |
| --- | --- | --- |
| true |preserveHierarchy |Pro zdrojovou složku Složku1 s následující strukturou,<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cílová složka Složku1 se vytvoří se stejnou strukturou jako zdroj:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy |Pro zdrojovou složku Složku1 s následující strukturou,<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>cílový Složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro File5 |
| true |mergeFiles |Pro zdrojovou složku Složku1 s následující strukturou,<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>cílový Složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Obsah soubor1 + soubor2 + file3 + file4 + soubor 5 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. |
| false (nepravda) |preserveHierarchy |Pro zdrojovou složku Složku1 s následující strukturou,<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cílová složka Složku1 se vytvoří s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 s file3, file4 a File5 se nezvednuty. |
| false (nepravda) |flattenHierarchy |Pro zdrojovou složku Složku1 s následující strukturou,<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cílová složka Složku1 se vytvoří s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro soubor2<br/><br/>Subfolder1 s file3, file4 a File5 se nezvednuty. |
| false (nepravda) |mergeFiles |Pro zdrojovou složku Složku1 s následující strukturou,<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Cílová složka Složku1 se vytvoří s následující strukturou:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Obsah soubor1 + soubor2 se sloučí do jednoho souboru s automaticky generovaným názvem souboru.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automaticky vygenerovaný název pro Soubor1<br/><br/>Subfolder1 s file3, file4 a File5 se nezvednuty. |

## <a name="supported-file-and-compression-formats"></a>Podporované formáty souborů a komprese
Podrobnosti najdete v článku o [formátech souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md) .

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>Příklady JSON pro kopírování dat do a ze systému souborů
V následujících příkladech jsou uvedeny ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazují, jak kopírovat data z místního systému souborů a do úložiště objektů BLOB v Azure. Data však můžete kopírovat *přímo* z libovolného zdroje do kterékoli z jímky uvedených v části [podporované zdroje a jímky](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Příklad: kopírování dat z místního systému souborů do Azure Blob Storage
V této ukázce se dozvíte, jak kopírovat data z místního systému souborů do úložiště objektů BLOB v Azure. Ukázka má následující Data Factory entit:

* Propojená služba typu [OnPremisesFileServer](#linked-service-properties).
* Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní [datová sada](data-factory-create-datasets.md) typu [sdílení](#dataset-properties)souborů.
* Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
* [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [FileSystemSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Následující ukázka kopíruje data časových řad z místního systému souborů do úložiště objektů BLOB v Azure každou hodinu. Vlastnosti JSON používané v těchto ukázkách jsou popsány v částech po ukázkách.

Jako první krok nastavte Správa dat bránu podle pokynů v tématu [přesun dat mezi místními zdroji a cloudem pomocí Správa dat brány](data-factory-move-data-between-onprem-and-cloud.md).

**Místní propojená služba souborového serveru:**

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

Místo vlastností **UserID** a **Password** doporučujeme použít vlastnost **encryptedCredential** . Podrobnosti o této propojené službě najdete v tématu [propojená služba souborového serveru](#linked-service-properties) .

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

**Místní vstupní datová sada systému souborů:**

Data se při každé hodinu vybírají z nového souboru. Vlastnosti folderPath a fileName jsou určeny na základě počátečního času řezu.

Nastavení `"external": "true"` informs Data Factory, že datová sada je externí pro objekt pro vytváření dat a není vytvořená aktivitou v datové továrně.

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

**Výstupní datová sada Azure Blob Storage:**

Data se zapisují do nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt BLOB je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá část roku, měsíce, dne a hodiny počátečního času.

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

**Aktivita kopírování v kanálu se zdrojem systému souborů a jímkou objektů BLOB:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady, a je naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **FileSystemSource** a typ **jímky** je nastavený na **BlobSink**.

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

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Příklad: kopírování dat z Azure SQL Database do místního systému souborů
Následující příklad ukazuje:

* Propojená služba typu [AzureSqlDatabase.](data-factory-azure-sql-connector.md#linked-service-properties)
* Propojená služba typu [OnPremisesFileServer](#linked-service-properties).
* Vstupní datová sada typu [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* Výstupní datová sada typu [sdílení](#dataset-properties)souborů.
* Kanál s aktivitou kopírování, která používá [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) a [FileSystemSink](#copy-activity-properties).

Ukázka kopíruje data časových řad z tabulky Azure SQL do místního systému souborů každou hodinu. Vlastnosti JSON, které jsou používány v těchto ukázkách, jsou popsány v částech po ukázkách.

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

**Místní propojená služba souborového serveru:**

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

Namísto použití vlastností **UserID** a **Password** doporučujeme použít vlastnost **encryptedCredential** . Podrobnosti o této propojené službě najdete v tématu [propojená služba systému souborů](#linked-service-properties) .

**Vstupní datová sada Azure SQL:**

Ukázka předpokládá, že jste v Azure SQL vytvořili tabulku "MyTable" a obsahuje sloupec s názvem "timestampcolumn" pro data časových řad.

Nastavení ``"external": "true"`` informs Data Factory, že datová sada je externí pro objekt pro vytváření dat a není vytvořená aktivitou v datové továrně.

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

Data se zkopírují do nového souboru každou hodinu. FolderPath a fileName pro objekt BLOB se určují na základě počátečního času řezu.

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

**Aktivita kopírování v kanálu se zdrojovým objektem SQL a jímkuí systému souborů:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady, a je naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **SqlSource** a typ **jímky** je nastavený na **FileSystemSink**. Dotaz SQL, který je zadán pro vlastnost **SqlReaderQuery** , vybere data za uplynulou hodinu ke zkopírování.

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

Sloupce můžete také namapovat ze zdrojové datové sady na sloupce z datové sady jímky v definici aktivity kopírování. Podrobnosti najdete v tématu [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a ladění
 Další informace o klíčových faktorech, které mají vliv na výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jejich optimalizace, najdete v [Průvodci výkonem a optimalizací aktivity kopírování](data-factory-copy-activity-performance.md).
