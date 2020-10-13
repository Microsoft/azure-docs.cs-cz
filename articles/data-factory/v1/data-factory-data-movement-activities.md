---
title: Přesun dat pomocí aktivity kopírování
description: 'Přečtěte si o přesunu dat v Data Factory kanálech: migrace dat mezi cloudových úložišť a mezi místním úložištěm a cloudovým úložištěm. Použijte aktivitu kopírování.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 67543a20-b7d5-4d19-8b5e-af4c1fd7bc75
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a48a6de406f14c5339a4e6d92cd09a12357b73f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84195966"
---
# <a name="move-data-by-using-copy-activity"></a>Přesun dat pomocí aktivity kopírování
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-data-movement-activities.md)
> * [Verze 2 (aktuální verze)](../copy-activity-overview.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [aktivita kopírování ve verzi v2](../copy-activity-overview.md).

## <a name="overview"></a>Přehled
V Azure Data Factory můžete použít aktivitu kopírování ke kopírování dat mezi místními a cloudovým úložištěm dat. Po zkopírování dat je lze dále transformovat a analyzovat. Aktivitu kopírování můžete použít také k publikování transformačních a analytických výsledků pro business intelligence (BI) a využití aplikací.

![Role aktivity kopírování](media/data-factory-data-movement-activities/copy-activity.png)

Aktivita kopírování využívá zabezpečenou, spolehlivou, škálovatelnou a [globálně dostupnou službu](#global). Tento článek poskytuje podrobné informace o přesunu dat v Data Factory a aktivitě kopírování.

Nejdřív se podívejme, jak probíhá migrace dat mezi dvěma cloudových úložišť dat a mezi místním úložištěm dat a cloudovým úložištěm dat.

> [!NOTE]
> Obecné informace o aktivitách najdete v tématu [Principy kanálů a aktivit](data-factory-create-pipelines.md).
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Kopírování dat mezi dvěma datovými úložišti cloudových dat
Pokud jsou zdrojová i jímka úložiště dat v cloudu, aktivita kopírování prochází z následujících fází a kopíruje data ze zdroje do jímky. Služba, která využívá aktivitu kopírování:

1. Načte data ze zdrojového úložiště dat.
2. Provádí serializaci/deserializaci, kompresi a dekompresi, mapování sloupců a převod typu. Provádí tyto operace na základě konfigurací vstupní datové sady, výstupní datové sady a aktivity kopírování.
3. Zapisuje data do cílového úložiště dat.

Služba automaticky zvolí optimální oblast pro pohyb dat. Tato oblast je obvykle ta nejbližší k úložišti dat jímky.

![Kopie z cloudu do cloudu](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopírování dat mezi místním úložištěm dat a cloudovým úložištěm dat
K bezpečnému přesunu dat mezi místním úložištěm dat a cloudovým úložištěm dat nainstalujte Správa dat bránu na místním počítači. Správa dat brána je agent, který umožňuje přesun a zpracování hybridních dat. Můžete ji nainstalovat na stejný počítač, jako je samotné úložiště dat, nebo na samostatném počítači, který má přístup k úložišti dat.

V tomto scénáři Správa dat brána provádí serializaci/deserializaci, kompresi/dekompresi, mapování sloupců a převod typu. Data neproudí prostřednictvím služby Azure Data Factory. Místo toho Správa dat brána přímo zapisuje data do cílového úložiště.

![Kopírování z místního prostředí do cloudu](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Úvod a návod najdete v tématu [přesun dat mezi místními a cloudových úložišť dat](data-factory-move-data-between-onprem-and-cloud.md) . Podrobné informace o tomto agentovi najdete v tématu [Správa dat Gateway](data-factory-data-management-gateway.md) .

Můžete také přesunout data z/do podporovaných úložišť dat hostovaných na virtuálních počítačích Azure s IaaS pomocí brány Správa dat. V takovém případě můžete bránu Správa dat nainstalovat na stejný virtuální počítač, jako je samotné úložiště dat, nebo na samostatném virtuálním počítači, který má přístup k úložišti dat.

## <a name="supported-data-stores-and-formats"></a>Podporovaná úložiště a formáty dat
Aktivita kopírování ve službě Data Factory kopíruje data ze zdrojového úložiště dat do úložiště dat jímky. Data Factory podporuje následující typy úložišť dat. Data z libovolného zdroje lze zapsat do libovolné jímky. Kliknutím na úložiště dat se dozvíte, jak kopírovat data z a do daného úložiště.

> [!NOTE] 
> Pokud potřebujete přesunout data do nebo z úložiště dat, které aktivita kopírování nepodporuje, použijte ke kopírování nebo přesunu **vlastní aktivitu** ve službě Data Factory s vaší vlastní logikou. Podrobnosti o vytvoření a používání vlastní aktivity najdete v tématu [Použití vlastních aktivit v kanálu služby Azure Data Factory](data-factory-use-custom-activities.md).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Úložiště dat s * mohou být místní nebo v Azure IaaS a vyžadují nainstalování [Brány správy dat](data-factory-data-management-gateway.md) na místním počítači nebo na počítači Azure IaaS.

### <a name="supported-file-formats"></a>Podporované formáty souborů
Aktivitu kopírování můžete použít ke **kopírování souborů** mezi dvěma úložišti dat založených na souborech, ale v definicích vstupní i výstupní datové sady můžete přeskočit [oddíl formát](data-factory-create-datasets.md) . Data se zkopírují efektivně bez serializace/deserializace.

Aktivita kopírování také čte a zapisuje do souborů v určených formátech: **text, JSON, Avro, orc a Parquet**a Kompresní kodek **gzip, Deflate, bzip2 a ZipDeflate** jsou podporovány. Viz [podporované formáty souborů a komprese](data-factory-supported-file-and-compression-formats.md) s podrobnostmi.

Můžete například provést následující aktivity kopírování:

* Zkopírujte data do databáze SQL Server a zapište do Azure Data Lake Store ve formátu ORC.
* Kopírování souborů ve formátu textu (CSV) z místního systému souborů a zápis do objektu blob Azure ve formátu Avro
* Zkopírujte soubory zip z místního systému souborů a dekomprimujte je na Azure Data Lake Store.
* Zkopírujte data ve formátu. komprimovaný text (CSV) z Azure Blob a zapište do Azure SQL Database.

## <a name="globally-available-data-movement"></a><a name="global"></a>Celkově dostupný pohyb dat
Azure Data Factory je k dispozici pouze v oblastech Západní USA, Východní USA a Severní Evropa. Služba, která provádí kopírování, je ale globálně dostupná v následujících oblastech a zeměpisných oblastech. Globálně dostupná topologie zajišťuje efektivní přesun dat, který obvykle brání směrování mezi oblastmi. Dostupnost Data Factory a přesunu dat v oblasti najdete v tématu [služby podle oblasti](https://azure.microsoft.com/regions/#services) .

### <a name="copy-data-between-cloud-data-stores"></a>Kopírování dat mezi úložišti cloudových dat
Pokud jsou zdrojová i jímka úložiště dat v cloudu, Data Factory používá nasazení služby v oblasti, která je nejblíže jímky ve stejné geografické oblasti pro přesun dat. Informace o mapování najdete v této tabulce:

| Zeměpis cílových úložišť dat | Oblast cílového úložiště dat | Oblast, která se používá k přesunu dat |
|:--- |:--- |:--- |
| USA | East US | East US |
| &nbsp; | USA – východ 2 | USA – východ 2 |
| &nbsp; | Střední USA | Střední USA |
| &nbsp; | USA – středosever | USA – středosever |
| &nbsp; | Středojižní USA | Středojižní USA |
| &nbsp; | USA – středozápad | USA – středozápad |
| &nbsp; | USA – západ | USA – západ |
| &nbsp; | Západní USA 2 | Západní USA 2 |
| Kanada | Kanada – východ | Střední Kanada |
| &nbsp; | Střední Kanada | Střední Kanada |
| Brazílie | Brazil South | Brazil South |
| Evropa | Severní Evropa | Severní Evropa |
| &nbsp; | West Europe | West Europe |
| Spojené království | Spojené království – západ | Spojené království – jih |
| &nbsp; | Spojené království – jih | Spojené království – jih |
| Asie a Tichomoří | Southeast Asia | Southeast Asia |
| &nbsp; | Východní Asie | Southeast Asia |
| Austrálie | Austrálie – východ | Austrálie – východ |
| &nbsp; | Austrálie – jihovýchod | Australia Southeast |
| Indie | Indie – střed | Indie – střed |
| &nbsp; | Západní Indie | Indie – střed |
| &nbsp; | Indie – jih | Indie – střed |
| Japonsko | Japan East | Japonsko – východ |
| &nbsp; | Japonsko – západ | Japan East |
| Jižní Korea | Jižní Korea – střed | Jižní Korea – střed |
| &nbsp; | Jižní Korea – jih | Jižní Korea – střed |

Případně můžete explicitně určit oblast Data Factory služby, která se má použít k provedení kopírování zadáním `executionLocation` vlastnosti v části aktivita kopírování `typeProperties` . Podporované hodnoty této vlastnosti jsou uvedeny v oblasti výše, která se **používá pro sloupec přesunu dat** . Všimněte si, že data procházejí během kopírování přes tuto oblast. Například pro kopírování mezi obchody Azure v Koreji můžete určit `"executionLocation": "Japan East"` směr směrování prostřednictvím japonské oblasti (viz [Ukázka JSON](#by-using-json-scripts) jako reference).

> [!NOTE]
> Pokud oblast cílového úložiště dat není v předchozím seznamu nebo nezjistitelná, ve výchozím nastavení se aktivita kopírování nezdařila místo přechodu přes alternativní oblast, pokud `executionLocation` není zadaná. Seznam podporovaných oblastí se rozbalí v průběhu času.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopírování dat mezi místním úložištěm dat a cloudovým úložištěm dat
Při kopírování dat mezi místními (nebo virtuálními počítači Azure nebo IaaS) a cloudovým úložištěm [Správa dat brána](data-factory-data-management-gateway.md) provádí přesun dat na místním počítači nebo virtuálním počítači. Data netoků služby v cloudu, pokud nepoužijete možnost [dvoufázové kopírování](data-factory-copy-activity-performance.md#staged-copy) . V takovém případě data před zápisem do úložiště dat jímky natéká do pracovního úložiště Azure Blob.

## <a name="create-a-pipeline-with-copy-activity"></a>Vytvoření kanálu s aktivitou kopírování
Kanál s aktivitou kopírování můžete vytvořit několika způsoby:

### <a name="by-using-the-copy-wizard"></a>Pomocí Průvodce kopírováním
Průvodce kopírováním Data Factory vám pomůže vytvořit kanál s aktivitou kopírování. Tento kanál umožňuje kopírovat data z podporovaných zdrojů do cílových umístění *bez psaní definic JSON* pro propojené služby, datové sady a kanály. Podrobnosti o průvodci najdete v tématu [Průvodce kopírováním Data Factory](data-factory-copy-wizard.md) .  

### <a name="by-using-json-scripts"></a>Pomocí skriptů JSON
Můžete použít Editor Data Factory v aplikaci Visual Studio nebo Azure PowerShell k vytvoření definice JSON pro kanál (pomocí aktivity kopírování). Pak ho můžete nasadit a vytvořit tak kanál v Data Factory. Podrobný postup najdete [v tématu Kurz: použití aktivity kopírování v kanálu Azure Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .    

Vlastnosti JSON (například název, popis, vstupní a výstupní tabulky a zásady) jsou k dispozici pro všechny typy aktivit. Vlastnosti, které jsou k dispozici v `typeProperties` části aktivity, se liší podle typu aktivity.

V případě aktivity kopírování se `typeProperties` oddíl liší v závislosti na typech zdrojů a jímky. Kliknutím na zdroj nebo jímku v části [podporované zdroje a jímky](#supported-data-stores-and-formats) získáte informace o vlastnostech typu, které aktivita kopírování podporuje pro dané úložiště dat.

Tady je ukázka definice JSON:

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
Plán, který je definován ve výstupní datové sadě, určuje, kdy se aktivita spustí (například **denně**, frekvence jako **den**a interval jako **1**). Aktivita kopíruje data ze vstupní datové sady (**zdroje**) do výstupní datové sady (**jímka**).

Pro aktivitu kopírování lze zadat více než jednu vstupní datovou sadu. Slouží k ověření závislostí před spuštěním aktivity. Pouze data z první datové sady jsou však zkopírována do cílové datové sady. Další informace najdete v tématu [plánování a provádění](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Výkon a ladění
Podívejte se na téma [Průvodce výkonem a optimalizací aktivity kopírování](data-factory-copy-activity-performance.md), který popisuje klíčové faktory ovlivňující výkon přesunu dat (aktivita kopírování) v Azure Data Factory. Také uvádí zjištěný výkon během interního testování a popisuje různé způsoby, jak optimalizovat výkon aktivity kopírování.

## <a name="fault-tolerance"></a>Odolnost proti chybám
Ve výchozím nastavení zastaví aktivita kopírovat kopírování dat a vrátí chybu, když dojde k nekompatibilním datům mezi zdrojem a jímkou. i když se dá explicitně nakonfigurovat tak, aby přeskočil a zaprotokoloval nekompatibilní řádky a kopíruje se jenom taková kompatibilní data, aby kopírování bylo úspěšné. Další podrobnosti najdete v podrobnostech o odolnosti [proti chybám aktivity kopírování](data-factory-copy-activity-fault-tolerance.md) .

## <a name="security-considerations"></a>Důležité informace o zabezpečení
Projděte si téma [požadavky na zabezpečení](data-factory-data-movement-security-considerations.md), které popisuje infrastrukturu zabezpečení, kterou služby pro přesun dat v Azure Data Factory používají k zabezpečení vašich dat.

## <a name="scheduling-and-sequential-copy"></a>Plánování a sekvenční kopírování
Podrobné informace o tom, jak plánování a spouštění fungují v Data Factory, najdete v tématu [plánování a spouštění](data-factory-scheduling-and-execution.md) . Je možné spustit více operací kopírování jeden po druhém sekvenčním a seřazeným způsobem. Viz část [kopírování postupně](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) .

## <a name="type-conversions"></a>Převody typů
Různá úložiště dat mají různé systémy nativního typu. Aktivita kopírování provádí automatické převody typů ze zdrojových typů do typů jímky s následujícím dvěma kroky:

1. Převeďte z nativního zdrojového typu na typ .NET.
2. Převod z typu .NET na nativní typ jímky.

Mapování z nativního typu systému na typ .NET pro úložiště dat je v příslušném článku úložiště dat. (Klikněte na odkaz konkrétní v tabulce podporovaná úložiště dat). Tato mapování můžete použít k určení vhodných typů při vytváření tabulek, takže aktivita kopírování provádí správné převody.

## <a name="next-steps"></a>Další kroky
* Další informace o aktivitě kopírování najdete v tématu [kopírování dat z úložiště objektů BLOB v Azure do Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Další informace o přesouvání dat z místního úložiště dat do cloudového úložiště dat najdete v tématu [přesun dat z místních úložišť do cloudových úložišť dat](data-factory-move-data-between-onprem-and-cloud.md).
