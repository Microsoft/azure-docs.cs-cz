---
title: Přesun dat pomocí aktivity kopírování | Microsoft Docs
description: 'Další informace o přesun dat v kanálů služby Data Factory: migrace dat mezi cloudové úložiště a mezi úložišti místního a cloudového úložiště. Pomocí aktivity kopírování.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 67543a20-b7d5-4d19-8b5e-af4c1fd7bc75
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6b13c70d86af195e50190083aa562811236cdd4b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054254"
---
# <a name="move-data-by-using-copy-activity"></a>Přesun dat pomocí aktivity kopírování
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-data-movement-activities.md)
> * [Verze 2 (aktuální verze)](../copy-activity-overview.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [aktivitu kopírování v V2](../copy-activity-overview.md).

## <a name="overview"></a>Přehled
V Azure Data Factory můžete aktivitu kopírování ke kopírování dat mezi místními a cloudovými datová úložiště. Po zkopírování dat, může být další transformovat a analyzovat. Aktivita kopírování můžete taky publikovat transformaci a analýzu výsledky pro business intelligence (BI) a využití aplikace.

![Role aktivity kopírování](media/data-factory-data-movement-activities/copy-activity.png)

Aktivita kopírování používá technologii zabezpečený, spolehlivou a škálovatelnou, a [globálně dostupnou službu,](#global). Tento článek poskytuje podrobné informace o přesouvání dat ve službě Data Factory a aktivita kopírování.

Nejprve si ukážeme, jak dojde k migraci dat mezi dvě úložiště dat cloudu a mezi místnímu úložišti dat a úložiště dat cloudu.

> [!NOTE]
> Obecné informace o aktivitách najdete v tématu [Principy kanály a aktivity](data-factory-create-pipelines.md).
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Kopírovat data mezi dvěma cloudové úložiště dat
Pokud jsou zdrojový a podřízený datová úložiště v cloudu, aktivity kopírování projde těchto fází ke zkopírování dat ze zdroje do jímky. Služba, která pohání aktivity kopírování:

1. Čte data z úložiště dat zdroje.
2. Provede serializaci nebo deserializaci, kompresi nebo dekompresi, mapování sloupců a typ – převod. Dělá tyto operace na základě konfigurací vstupní datové sady, výstupní datovou sadu a kopie aktivity.
3. Zapisuje data do cílového úložiště dat.

Služba automaticky vybere optimální oblasti, kterou chcete provést přesun dat. Tato oblast je obvykle nejblíže k úložišti dat podřízený jeden.

![Cloud cloudové kopírování](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopírování dat mezi úložišti dat místní a cloudové úložiště dat
Bezpečně přesun dat mezi úložišti dat místní a cloudové úložiště dat, nainstalujte Brána pro správu dat na místním počítači. Brána pro správu dat je agenta, který umožňuje přesun dat hybridní a zpracování. Nainstalujete ho na stejném počítači jako data uložit sám sebe, nebo na samostatný počítač, má přístup k úložišti dat.

V tomto scénáři Brána pro správu dat provede serializaci nebo deserializaci, kompresi nebo dekompresi, mapování sloupců a typ – převod. Není toku dat pomocí služby Azure Data Factory. Místo toho brána pro správu dat přímo zapisuje data do cílové úložiště.

![Kopírování na místní--cloud](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

V tématu [přesun dat mezi místní a cloudové úložiště dat](data-factory-move-data-between-onprem-and-cloud.md) úvod a návod. V tématu [Brána pro správu dat](data-factory-data-management-gateway.md) podrobné informace o tohoto agenta.

Také můžete přesunout data z/do podporované úložiště dat, které jsou hostované na virtuální počítače Azure IaaS (VM) s použitím brány pro správu dat. Brána pro správu dat je v takovém případě můžete nainstalovat na stejný virtuální počítač jako data uložit sám sebe, nebo na samostatný virtuální počítač, má přístup k úložišti dat.

## <a name="supported-data-stores-and-formats"></a>Podporované datové úložiště a formáty
Aktivita kopírování ve službě Data Factory kopíruje data ze zdrojového úložiště dat do úložiště dat jímky. Data Factory podporuje následující typy úložišť dat. Data z libovolného zdroje lze zapsat do libovolné jímky. Kliknutím na úložiště dat se dozvíte, jak kopírovat data z a do daného úložiště.

> [!NOTE] 
> Pokud potřebujete přesunout data do nebo z úložiště dat, které aktivita kopírování nepodporuje, použijte ke kopírování nebo přesunu **vlastní aktivitu** ve službě Data Factory s vaší vlastní logikou. Podrobnosti o vytvoření a používání vlastní aktivity najdete v tématu [Použití vlastních aktivit v kanálu služby Azure Data Factory](data-factory-use-custom-activities.md).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Úložiště dat s * mohou být místní nebo v Azure IaaS a vyžadují nainstalování [Brány správy dat](data-factory-data-management-gateway.md) na místním počítači nebo na počítači Azure IaaS.

### <a name="supported-file-formats"></a>Podporované formáty souborů
Můžete použít aktivitu kopírování **zkopírujte soubory jako-je** mezi dvě souborové úložiště dat, můžete přeskočit [formátovat oddíl](data-factory-create-datasets.md) v definicích vstupní a výstupní datové sady. Data budou zkopírována efektivně bez serializaci nebo deserializaci.

Aktivita kopírování také čte z a zapíše do souborů v zadané formáty: **Text JSON, Avro, ORC a Parquet**a kompresní kodek **GZip, Deflate, BZip2 a ZipDeflate** jsou podporovány. V tématu [podporované formáty souborů a komprese](data-factory-supported-file-and-compression-formats.md) s podrobnostmi.

Například můžete provést následující kopie aktivity:

* Kopírování dat na místním serveru SQL a zapisovat do Azure Data Lake Store ve formátu ORC.
* Zkopírujte soubory ve formátu textu (CSV) z místní systém souborů a zápis do objektu Blob Azure ve formátu Avro.
* Kopírování komprimované soubory ze systému souborů na místě a pak dekomprimovat krajině do Azure Data Lake Store.
* Kopírování dat z Azure Blob ve formátu GZip komprimované textu (CSV) a zapsat do databáze SQL Azure.

## <a name="global"></a>Přesun globálně dostupnou dat
Azure Data Factory je k dispozici pouze v oblastech severní Evropa, západní USA a východní USA. Službu, která pohání aktivity kopírování je však k dispozici globálně v následujících oblastech a zeměpisných oblastí. Globálně dostupnou topologie zajišťuje přesun efektivní dat, která obvykle zabraňuje směrování mezi oblastmi. V tématu [služby podle oblasti](https://azure.microsoft.com/regions/#services) dostupnost služby Data Factory a přesun dat v oblasti.

### <a name="copy-data-between-cloud-data-stores"></a>Kopírování dat mezi úložišti dat cloudu
Pokud jsou zdrojový a podřízený datová úložiště v cloudu, objekt pro vytváření dat používá nasazení služby v oblasti, které je nejblíže jímky ve stejné geography přesouvat data. Informace o mapování najdete v této tabulce:

| Geography cílového úložiště dat | Oblast cílového úložiště dat | Oblast pro přesun dat |
|:--- |:--- |:--- |
| Spojené státy | Východ USA | Východ USA |
| &nbsp; | Východní USA 2 | Východní USA 2 |
| &nbsp; | Střed USA | Střed USA |
| &nbsp; | Střed USA – sever | Střed USA – sever |
| &nbsp; | Střed USA – jih | Střed USA – jih |
| &nbsp; | Západní střed USA | Západní střed USA |
| &nbsp; | Západní USA | Západní USA |
| &nbsp; | Západní USA 2 | Západní USA 2 |
| Kanada | Východní Kanada | Střední Kanada |
| &nbsp; | Střední Kanada | Střední Kanada |
| Brazílie | Brazílie – jih | Brazílie – jih |
| Evropa | Severní Evropa | Severní Evropa |
| &nbsp; | Západní Evropa | Západní Evropa |
| Spojené království | Spojené království – západ | Spojené království – jih |
| &nbsp; | Spojené království – jih | Spojené království – jih |
| Asie a Tichomoří | Jihovýchodní Asie | Jihovýchodní Asie |
| &nbsp; | Východní Asie | Jihovýchodní Asie |
| Austrálie | Austrálie – východ | Austrálie – východ |
| &nbsp; | Austrálie – jihovýchod | Austrálie – jihovýchod |
| Indie | Střed Indie | Střed Indie |
| &nbsp; | Indie – západ | Střed Indie |
| &nbsp; | Indie – jih | Střed Indie |
| Japonsko | Japonsko – východ | Japonsko – východ |
| &nbsp; | Japonsko – západ | Japonsko – východ |
| Jižní Korea | Jižní Korea – střed | Jižní Korea – střed |
| &nbsp; | Jižní Korea – jih | Jižní Korea – střed |

Alternativně můžete explicitně určit oblasti služby Data Factory, který se má použít k provedení kopie zadáním `executionLocation` vlastnost v aktivitě kopírování `typeProperties`. Podporované hodnoty této vlastnosti jsou nahoře uvedené v **používá oblast pro přesun dat** sloupce. Všimněte si, že vaše data prochází danou oblast přenášených v síti během kopírování. Například pro kopírování mezi Azure ukládá v Koreji, můžete zadat `"executionLocation": "Japan East"` trasu prostřednictvím Japonsko oblast (najdete v části [ukázkové JSON](#by-using-json-scripts) jako odkaz).

> [!NOTE]
> Pokud oblast cílového úložiště dat není v předchozím seznamu nebo nezjistitelný, ve výchozím nastavení aktivity kopírování selže místo alternativní oblast, pokud `executionLocation` je zadán. Rozbalí se seznamu podporovaných oblastí v čase.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopírování dat mezi úložišti dat místní a cloudové úložiště dat
Při kopírování dat mezi místními (nebo Azure virtuální počítače nebo IaaS) a cloudových úložišť [Brána pro správu dat](data-factory-data-management-gateway.md) provádí přesun dat na místní počítač nebo virtuální počítač. Data nejsou procházet skrz služba v cloudu, pokud nechcete použít [připravený kopie](data-factory-copy-activity-performance.md#staged-copy) schopností. V takovém případě toky dat prostřednictvím pracovní úložiště objektů Azure Blob před zápisem do úložiště dat jímky.

## <a name="create-a-pipeline-with-copy-activity"></a>Vytvoření kanálu s aktivitou kopírování
Vytvoření kanálu s aktivitou kopírování v několika způsoby:

### <a name="by-using-the-copy-wizard"></a>Pomocí Průvodce kopírováním
Průvodce kopírováním objekt pro vytváření dat umožňuje vytvoření kanálu s aktivitou kopírování. Tento kanál umožňuje zkopírování dat z podporovaných zdrojů do cíle *bez nutnosti psaní JSON* definice pro propojené služby, datové sady a kanály. V tématu [Průvodce kopírováním služby Data Factory](data-factory-copy-wizard.md) podrobnosti o průvodce.  

### <a name="by-using-json-scripts"></a>Pomocí skriptů JSON
Editoru služby Data Factory můžete v portálu Azure, Visual Studio nebo Azure PowerShell k vytvoření definice JSON pro kanál (pomocí aktivity kopírování). Pak můžete nasadit v Data Factory vytvořit kanál. V tématu [kurz: použijte aktivitu kopírování v kanál služby Azure Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) kurz s podrobnými pokyny.    

Jsou dostupné pro všechny typy aktivit JSON vlastnosti (například název, popis, vstupní a výstupní tabulky a zásady). Vlastnosti, které jsou k dispozici v `typeProperties` části aktivity se liší podle každý typ aktivity.

Pro aktivitu kopírování `typeProperties` části se liší v závislosti na typech zdrojů a jímky. Klikněte na tlačítko zdroj/jímka v [podporované zdroje a jímky](#supported-data-stores-and-formats) části nabízejí informace o typu vlastnosti, které aktivity kopírování podporuje pro toto datové úložiště.

Zde je ukázka definice JSON:

```json
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from Azure blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputBlobTable"
          }
        ],
        "outputs": [
          {
            "name": "OutputSQLTable"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          },
          "executionLocation": "Japan East"          
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```
Určuje plán, který je definován v výstupní datovou sadu při spuštění aktivity (například: **denní**, frekvence jako **den**a intervalu jako **1**). Aktivity kopíruje data ze vstupní datové sady (**zdroj**) do výstupní datové sady (**podřízený**).

Můžete zadat více než jednu vstupní datovou sadu, která aktivita kopírování. Používají se k ověření závislosti před spuštěním aktivity. Ale pouze data z první datovou sadu zkopírován do cílové datové sadě. Další informace najdete v tématu [plánování a provádění](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Výkon a ladění
Najdete v článku [výkonu kopie aktivity a vyladění průvodce](data-factory-copy-activity-performance.md), který popisuje klíčové faktory ovlivňující výkon přesun dat (aktivita kopírování) v Azure Data Factory. Také uvádí pozorovaný výkon při interním testování a popisuje různé způsoby za účelem optimalizace výkonu kopie aktivity.

## <a name="fault-tolerance"></a>Odolnost proti chybám
Ve výchozím nastavení, bude aktivity kopírování zastavíte kopírování dat a vraťte se selhání když dojde k nekompatibilní data mezi zdroj a jímka; explicitně nakonfigurovat přeskočit a protokolování nekompatibilní řádků a pouze kopie těchto kompatibilní data tak, aby kopie bylo úspěšně dokončeno. Najdete v článku [aktivity kopírování odolnost proti chybám](data-factory-copy-activity-fault-tolerance.md) na další podrobnosti.

## <a name="security-considerations"></a>Aspekty zabezpečení
Najdete v článku [aspekty zabezpečení](data-factory-data-movement-security-considerations.md), který popisuje infrastrukturu zabezpečení, která pomocí služby pro přesun dat v Azure Data Factory zabezpečit data.

## <a name="scheduling-and-sequential-copy"></a>Plánování a sekvenčních kopie
V tématu [plánování a provádění](data-factory-scheduling-and-execution.md) podrobné informace o plánování a provádění fungování ve službě Data Factory. Je možné spustit více operací kopírování jedna po druhé způsobem sekvenční/řazení. Najdete v článku [zkopírujte postupně](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) části.

## <a name="type-conversions"></a>Převody typu
Různé datové úložiště mají jiný typ nativní systémy. Aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typů s následující postup ve dvou krocích:

1. Převeďte na typ .NET typy nativní zdrojů.
2. Typ formátu .NET převeďte na typ nativní jímky.

Mapování z nativní typ systému na typ .NET pro úložiště dat, které je v článku příslušných dat úložiště. (Klikněte na odkaz v [podporovanými úložišti dat](#supported-data-stores) tabulky). Tato mapování můžete použít k určení odpovídající typy při vytváření tabulek, tak, aby aktivita kopírování provádí převody správné.

## <a name="next-steps"></a>Další postup
* Další informace o aktivitě kopírování najdete v tématu [kopírování dat z Azure Blob storage do Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Další informace o přesouvání dat z místního úložiště dat do úložiště dat cloudu najdete v tématu [přesun dat z cloudových úložišť dat místní](data-factory-move-data-between-onprem-and-cloud.md).
