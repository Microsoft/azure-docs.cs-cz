---
title: Přesunutí dat pomocí aktivity kopírování
description: 'Další informace o přesunu dat v kanálech Data Factory: migrace dat mezi cloudovými obchody a mezi místním úložištěm a cloudovým úložištěm. Použijte aktivitu kopírování.'
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
ms.openlocfilehash: fbaa8c3544b35978786404619879f59ab91a6979
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281883"
---
# <a name="move-data-by-using-copy-activity"></a>Přesunutí dat pomocí aktivity kopírování
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-data-movement-activities.md)
> * [Verze 2 (aktuální verze)](../copy-activity-overview.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Kopírování aktivity ve Verzi 2](../copy-activity-overview.md).

## <a name="overview"></a>Přehled
V Azure Data Factory můžete použít aktivitu kopírování ke kopírování dat mezi místními a cloudovými úložišti dat. Po zkopírování mohou být data dále transformována a analyzována. Aktivita kopírování můžete také použít k publikování výsledků transformace a analýzy pro business intelligence (BI) a spotřebu aplikací.

![Role aktivity kopírování](media/data-factory-data-movement-activities/copy-activity.png)

Aktivita kopírování je poháněna zabezpečenou, spolehlivou, škálovatelnou a [globálně dostupnou službou](#global). Tento článek obsahuje podrobnosti o přesunu dat v datové továrně a aktivitě kopírování.

Nejprve se podívejme, jak dochází k migraci dat mezi dvěma úložišti cloudových dat a mezi místním úložištěm dat a cloudovým úložištěm dat.

> [!NOTE]
> Informace o aktivitách obecně najdete v [tématu Principy kanálů a aktivit](data-factory-create-pipelines.md).
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Kopírování dat mezi dvěma úložišti cloudových dat
Když úložiště dat zdroje a jímky jsou v cloudu, aktivita kopírování prochází následující fáze zkopírovat data ze zdroje do jímky. Služba, která pohání aktivitu kopírování:

1. Čte data ze zdrojového úložiště dat.
2. Provádí serializaci/deserializaci, kompresi/dekompresi, mapování sloupců a převod typů. Provádí tyto operace na základě konfigurace vstupní datové sady, výstupní datové sady a aktivity kopírování.
3. Zapíše data do cílového úložiště dat.

Služba automaticky zvolí optimální oblast pro provedení přesunu dat. Tato oblast je obvykle jeden nejblíže úložiště dat jímky.

![Kopírování z cloudu na cloud](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopírování dat mezi místním úložištěm dat a cloudovým úložištěm dat
Pokud chcete bezpečně přesouvat data mezi místním úložištěm dat a cloudovým úložištěm dat, nainstalujte bránu pro správu dat do místního počítače. Brána pro správu dat je agent, který umožňuje hybridní přesun a zpracování dat. Můžete jej nainstalovat na stejném počítači jako samotné úložiště dat nebo na samostatném počítači, který má přístup k úložišti dat.

V tomto scénáři brána pro správu dat provádí serializaci/deserializaci, kompresi/dekompresi, mapování sloupců a převod typu. Data neprotékají službou Azure Data Factory. Místo toho brána pro správu dat přímo zapíše data do cílového úložiště.

![Místní kopírování do cloudu](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Viz [Přesun dat mezi místními a cloudovými úložišti dat](data-factory-move-data-between-onprem-and-cloud.md) pro úvod a návod. Podrobné informace o tomto [agentovi najdete v tématu Brána pro správu dat.](data-factory-data-management-gateway.md)

Data můžete také přesunout z/do podporovaných datových úložišť, které jsou hostované na virtuálních počítačích Azure IaaS (VMs) pomocí brány pro správu dat. V takovém případě můžete nainstalovat bránu pro správu dat na stejný virtuální počítač jako samotné úložiště dat nebo na samostatný virtuální počítač, který má přístup k úložišti dat.

## <a name="supported-data-stores-and-formats"></a>Podporovaná úložiště a formáty dat
Aktivita kopírování ve službě Data Factory kopíruje data ze zdrojového úložiště dat do úložiště dat jímky. Data Factory podporuje následující typy úložišť dat. Data z libovolného zdroje lze zapsat do libovolné jímky. Kliknutím na úložiště dat se dozvíte, jak kopírovat data z a do daného úložiště.

> [!NOTE] 
> Pokud potřebujete přesunout data do nebo z úložiště dat, které aktivita kopírování nepodporuje, použijte ke kopírování nebo přesunu **vlastní aktivitu** ve službě Data Factory s vaší vlastní logikou. Podrobnosti o vytvoření a používání vlastní aktivity najdete v tématu [Použití vlastních aktivit v kanálu služby Azure Data Factory](data-factory-use-custom-activities.md).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Úložiště dat s * mohou být místní nebo v Azure IaaS a vyžadují nainstalování [Brány správy dat](data-factory-data-management-gateway.md) na místním počítači nebo na počítači Azure IaaS.

### <a name="supported-file-formats"></a>Podporované formáty souborů
Kopírovat aktivitu můžete použít ke **kopírování souborů tak, jak jsou** mezi dvěma úložišti dat založených na souborech, můžete přeskočit oddíl [formátu](data-factory-create-datasets.md) v definicích vstupníi i výstupní datové sady. Data jsou zkopírována efektivně bez serializace/deserializace.

Aktivita kopírování také čte a zapisuje do souborů v určených formátech: **Text, JSON, Avro, ORC a Parkety**a kompresní kodek **GZip, Deflate, BZip2 a ZipDeflate** jsou podporovány. Viz [Podporované formáty souborů a komprese](data-factory-supported-file-and-compression-formats.md) s podrobnostmi.

Můžete například provést následující aktivity kopírování:

* Zkopírujte data v místním SQL Serveru a zapište do Azure Data Lake Store ve formátu ORC.
* Zkopírujte soubory v textovém formátu (CSV) z místního systému souborů a zapište do objektu Blob Azure ve formátu Avro.
* Zkopírujte soubory z webu z místního systému souborů a dekomprimujte a pak přiložte do úložiště Azure Data Lake Store.
* Zkopírujte data ve formátu gzip komprimovaného textu (CSV) z azure blob a zapište do Azure SQL Database.

## <a name="globally-available-data-movement"></a><a name="global"></a>Globálně dostupný přesun dat
Azure Data Factory je k dispozici jenom v oblastech Západní USA, Východní USA a Severní Evropa. Služba, která pohání kopírovat aktivity je však k dispozici globálně v následujících oblastech a zeměpisných oblastech. Globálně dostupná topologie zajišťuje efektivní přesun dat, který se obvykle vyhýbá směrování mezi oblastmi. Dostupnost data factory a přesun dat v oblasti najdete v tématu [Služby podle oblastí.](https://azure.microsoft.com/regions/#services)

### <a name="copy-data-between-cloud-data-stores"></a>Kopírování dat mezi úložišti cloudových dat
Když jsou úložiště dat zdroje i jímky v cloudu, Data Factory používá nasazení služby v oblasti, která je nejblíže k jímce ve stejné zeměpisné oblasti k přesunutí dat. Informace o mapování najdete v této tabulce:

| Zeměpisná poloha cílových úložišť dat | Oblast cílového úložiště dat | Oblast používaná pro přesun dat |
|:--- |:--- |:--- |
| Spojené státy | USA – východ | USA – východ |
| &nbsp; | USA – východ 2 | USA – východ 2 |
| &nbsp; | USA – střed | USA – střed |
| &nbsp; | USA – středosever | USA – středosever |
| &nbsp; | USA – středojih | USA – středojih |
| &nbsp; | USA – středozápad | USA – středozápad |
| &nbsp; | USA – západ | USA – západ |
| &nbsp; | USA – západ 2 | USA – západ 2 |
| Kanada | Kanada – východ | Střední Kanada |
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
| Indie | Indie – střed | Indie – střed |
| &nbsp; | Indie – západ | Indie – střed |
| &nbsp; | Indie – jih | Indie – střed |
| Japonsko | Japonsko – východ | Japonsko – východ |
| &nbsp; | Japonsko – západ | Japonsko – východ |
| Jižní Korea | Jižní Korea – střed | Jižní Korea – střed |
| &nbsp; | Jižní Korea – jih | Jižní Korea – střed |

Případně můžete explicitně označit oblast služby Data Factory, která má `executionLocation` být použita `typeProperties`k provedení kopie zadáním vlastnosti v části Aktivita kopírování . Podporované hodnoty pro tuto vlastnost jsou uvedeny ve výše uvedené **oblasti používané pro přesun dat** sloupec. Všimněte si, že vaše data procházejí tuto oblast přes drát během kopírování. Chcete-li například kopírovat mezi obchody Azure `"executionLocation": "Japan East"` v Koreji, můžete zadat trasu přes oblast Japonska (viz [ukázka JSON](#by-using-json-scripts) jako odkaz).

> [!NOTE]
> Pokud oblast cílového úložiště dat není v předchozím seznamu nebo nezjistitelné, ve výchozím nastavení aktivita kopírování se nezdaří namísto prochází alternativní oblasti, pokud `executionLocation` není zadán. Seznam podporovaných oblastí bude v průběhu času rozbalen.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopírování dat mezi místním úložištěm dat a cloudovým úložištěm dat
Když se data kopírují mezi místními (nebo virtuálními počítači Azure/IaaS) a cloudovými obchody, [brána pro správu dat](data-factory-data-management-gateway.md) provádí přesun dat v místním počítači nebo virtuálním počítači. Data neprotékají službou v cloudu, pokud nepoužíváte funkci [fázované kopie.](data-factory-copy-activity-performance.md#staged-copy) V takovém případě data toky prostřednictvím pracovní úložiště objektů blob Azure před zapsándo úložiště dat jímky.

## <a name="create-a-pipeline-with-copy-activity"></a>Vytvoření kanálu s aktivitou kopírování
Kanál s aktivitou kopírování můžete vytvořit několika způsoby:

### <a name="by-using-the-copy-wizard"></a>Pomocí Průvodce kopírováním
Průvodce kopírováním do datové továrny vám pomůže vytvořit kanál s aktivitou kopírování. Tento kanál umožňuje kopírovat data z podporovaných zdrojů do cílů bez psaní definic *JSON* pro propojené služby, datové sady a kanály. Podrobnosti o průvodci naleznete v [Průvodci kopírováním do datové továrny.](data-factory-copy-wizard.md)  

### <a name="by-using-json-scripts"></a>Pomocí skriptů JSON
Editor factory v sadě Visual Studio nebo Azure PowerShell můžete vytvořit definici JSON pro kanál (pomocí aktivity kopírování). Potom můžete nasadit k vytvoření kanálu v datové továrně. [Viz kurz: Použití aktivity kopírování v kanálu Azure Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pro kurz s podrobnými pokyny.    

Vlastnosti JSON (například název, popis, vstupní a výstupní tabulky a zásady) jsou k dispozici pro všechny typy aktivit. Vlastnosti, které `typeProperties` jsou k dispozici v části aktivity se liší podle každého typu aktivity.

U aktivity `typeProperties` kopírování se oddíl liší v závislosti na typech zdrojů a propadů. Kliknutím na zdroj nebo jímku v části [Podporované zdroje a jímky](#supported-data-stores-and-formats) se dozvíte o vlastnostech typu, které podporují aktivita kopírování pro toto úložiště dat.

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
Plán, který je definován ve výstupní datové sadě, určuje, kdy se aktivita spustí (například: **denní**, frekvence jako **den**a interval jako **1**). Aktivita zkopíruje data ze vstupní datové sady (**zdroj**) do výstupní datové sady (**jímky**).

Můžete zadat více než jednu vstupní datovou sadu pro kopírovat aktivitu. Používají se k ověření závislostí před spuštěním aktivity. Do cílové datové sady se však zkopírují pouze data z první datové sady. Další informace naleznete v [tématu Plánování a provádění](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Výkon a ladění
Podívejte se na [průvodce výkonem a laděním aktivity kopírování](data-factory-copy-activity-performance.md), který popisuje klíčové faktory, které ovlivňují výkon přesunu dat (aktivita kopírování) v Azure Data Factory. Také uvádí pozorovaný výkon během interního testování a popisuje různé způsoby optimalizace výkonu aktivity kopírování.

## <a name="fault-tolerance"></a>Odolnost proti chybám
Ve výchozím nastavení aktivita kopírování zastaví kopírování dat a vrátí selhání při výskytu nekompatibilních dat mezi zdrojem a jímkou; zatímco můžete explicitně nakonfigurovat přeskočení a protokolování nekompatibilních řádků a zkopírovat pouze tato kompatibilní data, aby byla kopie úspěšná. Další podrobnosti naleznete v [tématu Tolerance poruchy aktivity kopírování.](data-factory-copy-activity-fault-tolerance.md)

## <a name="security-considerations"></a>Důležité informace o zabezpečení
Podívejte se na [důležité informace o zabezpečení](data-factory-data-movement-security-considerations.md), které popisují infrastrukturu zabezpečení, kterou služby přesunu dat v Azure Data Factory používají k zabezpečení vašich dat.

## <a name="scheduling-and-sequential-copy"></a>Plánování a sekvenční kopírování
Podrobné informace o tom, jak plánování a provádění v Datové továrně dat funguje, najdete v tématu [Plánování a provádění.](data-factory-scheduling-and-execution.md) Je možné spustit více operací kopírování jeden po druhém sekvenčním/seřazeným způsobem. Viz část [Kopírovat postupně.](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)

## <a name="type-conversions"></a>Převody typů
Různá úložiště dat mají různé systémy nativního typu. Aktivita kopírování provádí automatické převody typů z typů zdrojů na typy jímek s následujícím dvoustupňovým přístupem:

1. Převod z nativních typů zdrojů na typ .NET.
2. Převést z typu .NET na nativní typ jímky.

Mapování ze systému nativního typu na typ .NET pro úložiště dat je v příslušném článku úložiště dat. (Klikněte na konkrétní odkaz v tabulce Podporovaná úložiště dat). Tato mapování můžete použít k určení vhodných typů při vytváření tabulek, takže aktivita kopírování provádí správné převody.

## <a name="next-steps"></a>Další kroky
* Další informace o aktivitě kopírování najdete v [tématu Kopírování dat z úložiště objektů Blob Azure do Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Informace o přesunu dat z místního úložiště dat do cloudového úložiště dat najdete [v tématu Přesun dat z místních do cloudových úložišť dat](data-factory-move-data-between-onprem-and-cloud.md).
