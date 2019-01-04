---
title: Přesun dat pomocí aktivity kopírování | Dokumentace Microsoftu
description: 'Další informace o přesouvání dat ve službě Data Factory kanálů: migrace dat mezi cloudovými úložišti a mezi místním úložištěm a cloudovým úložištěm. Použije aktivitu kopírování.'
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38299856"
---
# <a name="move-data-by-using-copy-activity"></a>Přesun dat pomocí aktivity kopírování
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-data-movement-activities.md)
> * [Verze 2 (aktuální verze)](../copy-activity-overview.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [aktivita kopírování ve verzi V2](../copy-activity-overview.md).

## <a name="overview"></a>Přehled
Ve službě Azure Data Factory můžete použít aktivitu kopírování ke kopírování dat mezi místním prostředím a cloudem datových úložišť. Po zkopírování dat, může být dále transformují a analyzují. Aktivitu kopírování, která můžete použít také k publikování, transformace a analýza výsledků pro business intelligence (BI) a využití aplikace.

![Pro dané aktivity kopírování](media/data-factory-data-movement-activities/copy-activity.png)

Aktivitu kopírování, která využívá k tomu zabezpečenou, spolehlivou a škálovatelnou, a [globálně dostupnou službu](#global). Tento článek poskytuje podrobné informace o přesouvání dat ve službě Data Factory a aktivitu kopírování.

Nejprve Podíváme se, jak dojde k migraci dat mezi dvěma cloudovými úložišti dat a mezi úložištěm dat v místním a cloudovým úložištěm dat.

> [!NOTE]
> Obecné informace o aktivitách najdete v tématu [Principy kanály a aktivity](data-factory-create-pipelines.md).
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Kopírovat data mezi dvěma cloudovými úložišti dat
Když zdroje a jímky úložiště dat v cloudu, aktivita kopírování prochází následující fáze ke kopírování dat ze zdroje do jímky. Služba, kterou využívá aktivitu kopírování:

1. Přečte data ze zdrojového úložiště dat.
2. Provede serializaci/deserializaci, komprese nebo dekomprese, mapování sloupců a převod typu. Provádí tyto operace konfigurace vstupní datové sady, výstupní datovou sadu a aktivitu kopírování.
3. Zapíše data do cílového úložiště dat.

Služba se automaticky vybere optimální oblasti, kterou chcete provést přesun dat. Tato oblast je obvykle ten nejbližší úložiště dat jímky.

![Kopírování cloud-to-cloud](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopírování dat mezi úložištěm dat v místním a cloudovým úložištěm dat
Bezpečně přesun dat mezi úložišti dat v místním a cloudovým úložištěm dat, instalace brány pro správu dat na místním počítači. Brána správy dat je agent, který umožňuje přesun hybridních dat a zpracování. Nainstalujete ho na stejném počítači jako samotné úložiště dat nebo na samostatném počítači, který má přístup k úložišti dat.

Brána správy dat v tomto scénáři, provede serializaci/deserializaci, komprese nebo dekomprese, mapování sloupců a převod typu. Není toku dat pomocí služby Azure Data Factory. Brána správy dat místo toho přímo zapisuje data do cílového úložiště.

![Kopie na místě – na cloud](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Zobrazit [přesun dat mezi místním prostředím a cloudem úložišť dat](data-factory-move-data-between-onprem-and-cloud.md) úvod a návod. Zobrazit [brána správy dat](data-factory-data-management-gateway.md) podrobné informace o tomto agentovi.

Můžete také přesun dat z/do podporované úložišť dat, které jsou hostovány na virtuálních počítačích (VM) Azure IaaS pomocí brány správy dat. Brána správy dat v takovém případě můžete nainstalovat na stejný virtuální počítač, samotného úložiště dat nebo na samostatném virtuálním počítači, který má přístup k úložišti dat.

## <a name="supported-data-stores-and-formats"></a>Podporované zdroje dat a formáty
Aktivita kopírování ve službě Data Factory kopíruje data ze zdrojového úložiště dat do úložiště dat jímky. Data Factory podporuje následující typy úložišť dat. Data z libovolného zdroje lze zapsat do libovolné jímky. Kliknutím na úložiště dat se dozvíte, jak kopírovat data z a do daného úložiště.

> [!NOTE] 
> Pokud potřebujete přesunout data do nebo z úložiště dat, které aktivita kopírování nepodporuje, použijte ke kopírování nebo přesunu **vlastní aktivitu** ve službě Data Factory s vaší vlastní logikou. Podrobnosti o vytvoření a používání vlastní aktivity najdete v tématu [Použití vlastních aktivit v kanálu služby Azure Data Factory](data-factory-use-custom-activities.md).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Úložiště dat s * mohou být místní nebo v Azure IaaS a vyžadují nainstalování [Brány správy dat](data-factory-data-management-gateway.md) na místním počítači nebo na počítači Azure IaaS.

### <a name="supported-file-formats"></a>Podporované formáty souborů
Můžete použít aktivitu kopírování, která **kopírovat soubory jako-je** mezi dvě souborové úložiště dat, můžete přeskočit [formátovat oddíl](data-factory-create-datasets.md) v definici vstupní a výstupní datové sady. Data se zkopírují efektivně bez serializaci/deserializaci.

Aktivita kopírování také načteme a zapisuje do souborů v určených formátů: **Text JSON, Avro, ORC a Parquet**a kompresní kodek **GZip, Deflate, BZip2 a ZipDeflate** jsou podporovány. Zobrazit [podporované formáty souborů a komprese](data-factory-supported-file-and-compression-formats.md) s podrobnostmi.

Například můžete provést následující aktivity kopírování:

* Kopírování dat v místním SQL serveru a ve formátu ORC zápis do Azure Data Lake Store.
* Zkopírujte soubory ve formátu textu (CSV) v místním systému souborů a zápis do objektu Blob Azure ve formátu Avro.
* Kopírování souborů ZIP v místním systému souborů a pak dekomprimovat pozemního do Azure Data Lake Store.
* Kopírování dat z objektů Blob v Azure ve formátu GZip komprimované text (CSV) a zápis do služby Azure SQL Database.

## <a name="global"></a>Přesun globálně dostupné dat
Azure Data Factory je dostupná jenom v oblastech USA – Západ, USA – východ a Severní Evropa. Ale služba, která využívá aktivitu kopírování, která je dostupná globálně v následujících území a oblastí. Globálně dostupné topologie zajišťuje přesun efektivní dat, které se obvykle vyhýbají segmentů směrování mezi oblastmi. Zobrazit [služby v jednotlivých oblastech](https://azure.microsoft.com/regions/#services) dostupnosti služby Data Factory a přesun dat v oblasti.

### <a name="copy-data-between-cloud-data-stores"></a>Kopírování dat mezi cloudovými úložišti dat
Když jsou zdroje a jímky úložiště dat v cloudu, datová továrna používá nasazení služby v oblasti, která je nejblíže k jímce ve stejné zeměpisné oblasti pro přesun dat. Informace o mapování najdete v této tabulce:

| Geografie cílové úložiště dat | Oblast cílového úložiště dat. | Oblasti používané pro přesun dat |
|:--- |:--- |:--- |
| Spojené státy | Východ USA | Východ USA |
| &nbsp; | Východní USA 2 | Východní USA 2 |
| &nbsp; | Střední USA | Střední USA |
| &nbsp; | Středoseverní USA | Středoseverní USA |
| &nbsp; | Středojižní USA | Středojižní USA |
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

Alternativně můžete explicitně uvedla oblasti služby Data Factory, který se má použít ke kopírování zadáním `executionLocation` vlastnost v aktivitě kopírování `typeProperties`. Podporované hodnoty pro tuto vlastnost výše uvedených v **oblasti použít v případě přesunu dat** sloupce. Všimněte si, že vaše data prochází tuto oblast přenášených během kopírování. Například pro kopírování mezi Azure ukládá v Koreji, můžete zadat `"executionLocation": "Japan East"` trasu přes oblast Japonsko (naleznete v tématu [ukázkový JSON](#by-using-json-scripts) jako odkaz).

> [!NOTE]
> Pokud se oblast cílového úložiště dat není v předchozím seznamu nebo jiným způsobem nezjistitelné, ve výchozím nastavení aktivity kopírování nezdaří, místo přes alternativní oblast, není-li `executionLocation` je zadán. Seznam podporovaných oblastí se bude rozšiřovat v čase.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopírování dat mezi úložištěm dat v místním a cloudovým úložištěm dat
Při kopírování dat mezi místním (nebo Azure virtual machines nebo IaaS) a cloudových úložišť, [brána správy dat](data-factory-data-management-gateway.md) provádí přesun dat na místním počítači nebo virtuálním počítači. Není toku dat ve službě v cloudu, pokud nechcete použít [fázovaného kopírování](data-factory-copy-activity-performance.md#staged-copy) funkce. V takovém případě data protékají pracovní úložiště objektů Blob v Azure před zápisem do úložiště dat jímky.

## <a name="create-a-pipeline-with-copy-activity"></a>Vytvoření kanálu s aktivitou kopírování
Vytvoření kanálu s aktivitou kopírování několika způsoby:

### <a name="by-using-the-copy-wizard"></a>Pomocí Průvodce kopírováním
Průvodce kopírováním služby Data Factory vám pomůže vytvořit kanál s aktivitou kopírování. Tento kanál umožňuje kopírovat data z podporovaných zdrojů do cílů *aniž byste museli napsat JSON* definice propojené služby, datové sady a kanály. Zobrazit [Průvodce kopírováním služby Data Factory](data-factory-copy-wizard.md) podrobné informace o průvodci.  

### <a name="by-using-json-scripts"></a>Pomocí skriptů JSON
Editor služby Data Factory webu Azure portal, sady Visual Studio nebo prostředí Azure PowerShell slouží k vytvoření definici JSON kanálu (pomocí aktivity kopírování). Pak můžete nasadit ho k vytvoření kanálu ve službě Data Factory. Zobrazit [kurz: Použití aktivitu kopírování v kanálu Azure Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) kurz s podrobnými pokyny.    

Vlastnosti JSON (například název, popis, vstupní a výstupní tabulky a zásady) jsou k dispozici pro všechny typy aktivit. Vlastnosti, které jsou k dispozici v `typeProperties` části aktivity se liší s jednotlivými typu aktivity.

Pro aktivitu kopírování `typeProperties` oddílu se liší v závislosti na typech zdroje a jímky. Klikněte na zdroj/jímka v [zdroje a jímky podporované](#supported-data-stores-and-formats) části nabízejí informace o vlastnostech typu podporovaných aktivitou kopírování pro toto úložiště dat.

Tady je ukázková definice JSON:

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
Určuje plán, který je definovaný ve vstupní sadě při spuštění aktivity (například: **denní**, četnosti, jak **den**a jako interval **1**). Aktivita kopíruje data ze vstupní datovou sadu (**zdroj**) na výstupní datovou sadu (**jímky**).

Můžete zadat více než jednu vstupní datovou sadu aktivita kopírování. Používají se k ověření závislostí, před spuštěním aktivity. Ale jenom data z datové sady první zkopírován do cílové datové sadě. Další informace najdete v tématu [plánování a provádění](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Výkon a ladění
Zobrazit [Průvodce laděním a výkonem aktivity kopírování](data-factory-copy-activity-performance.md), která popisuje klíčové faktory, které ovlivňují výkon přesun dat (aktivita kopírování) ve službě Azure Data Factory. Také uvádí zjištěnou výkon při interní testování a tento článek popisuje různé způsoby, jak optimalizovat výkon aktivitu kopírování.

## <a name="fault-tolerance"></a>Odolnost proti chybám
Ve výchozím nastavení, aktivita kopírování se zastaví kopírování dat a vrátí selhání dojde při nekompatibilní data mezi zdrojem a jímkou; Když explicitně konfigurací můžete přeskočit a protokolování nekompatibilních řádků a pouze kopie byla úspěšná kompatibilní data k vytvoření kopie. Zobrazit [aktivity kopírování odolnost proti chybám](data-factory-copy-activity-fault-tolerance.md) na další podrobnosti.

## <a name="security-considerations"></a>Aspekty zabezpečení
Najdete v článku [aspekty zabezpečení](data-factory-data-movement-security-considerations.md), která popisuje zabezpečení infrastruktury, služby pro přesun dat ve službě Azure Data Factory můžete zabezpečit vaše data.

## <a name="scheduling-and-sequential-copy"></a>Plánování a sekvenční kopírování
Zobrazit [plánování a provádění](data-factory-scheduling-and-execution.md) podrobné informace o tom, jak funguje plánování a provádění ve službě Data Factory. Je možné spustit více operací kopírování jeden po druhém sekvenční/seřazené způsobem. Zobrazit [kopírovat postupně](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) části.

## <a name="type-conversions"></a>Převody typu
Různých úložišť dat mají různé nativní typ systémy. Aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typy s následující dvoukrokový přístup:

1. Převeďte na typ .NET typy nativní zdroje.
2. Převeďte na typ jímky nativní typ formátu .NET.

Mapování ze systému nativní typ na typ .NET pro úložiště dat je v článku příslušné datové úložiště. (Klikněte na odkaz v [podporovanými úložišti dat](#supported-data-stores) tabulky). Tato mapování můžete použít k určení odpovídající typy při vytváření tabulek, takže aktivita kopírování provádí převody vpravo.

## <a name="next-steps"></a>Další postup
* Další informace o aktivitě kopírování najdete v tématu [kopírování dat z Azure Blob storage do Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Další informace o přesun dat z do místního úložiště dat do cloudového úložiště dat, naleznete v tématu [přesun dat z místních úložišť dat v cloudu](data-factory-move-data-between-onprem-and-cloud.md).
