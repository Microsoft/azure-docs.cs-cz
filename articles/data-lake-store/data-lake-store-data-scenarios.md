---
title: Datové scénáře zahrnující úložiště datového jezera Gen1 | Dokumenty společnosti Microsoft
description: Seznamte se s různými scénáři a nástroji, pomocí kterých se data můžou ingestovat, zpracovávat, stahovat a vizualizovat v oblasti Data Lake Storage Gen1 (dříve známé jako Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: a8f9b66bf9a301888f2371fb1c58a4845c2232b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536152"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Použití Azure Data Lake Storage Gen1 pro požadavky na velké objemy dat

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Existuje čtyři klíčové fáze zpracování velkých objemů dat:

* Ingestování velkého množství dat do úložiště dat v reálném čase nebo v dávkách
* Zpracování údajů
* Stahování dat
* Vizualizace dat

V tomto článku se podíváme na tyto fáze s ohledem na Azure Data Lake Storage Gen1 pochopit možnosti a nástroje, které jsou k dispozici pro vaše potřeby velkých objemů dat.

## <a name="ingest-data-into-data-lake-storage-gen1"></a>Ingestování dat do úložiště datového jezera Gen1
Tato část upozorňuje na různé zdroje dat a různé způsoby, jakými mohou být tato data ingestována do účtu Data Lake Storage Gen1.

![Ingestování dat do úložiště datového jezera Gen1](./media/data-lake-store-data-scenarios/ingest-data.png "Ingestování dat do úložiště datového jezera Gen1")

### <a name="ad-hoc-data"></a>Ad hoc data
To představuje menší datové sady, které se používají pro vytváření prototypů aplikace velkých objemů dat. Existují různé způsoby ingestování dat ad hoc v závislosti na zdroji dat.

| Zdroj dat | Ingestovat pomocí |
| --- | --- |
| Místní počítač |<ul> <li>[Azure Portal](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure CLI](data-lake-store-get-started-cli-2.0.md)</li> <li>[Použití nástrojů datového jezera pro Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Objekt blob úložiště Azure |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[Nástroj AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp spuštěný v clusteru HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Streamovaná data
To představuje data, která mohou být generována z různých zdrojů, jako jsou aplikace, zařízení, senzory, atd. Tato data lze ingestovat do úložiště Datové ho jezera Gen1 pomocí různých nástrojů. Tyto nástroje obvykle zachycují a zpracovávají data na základě událostí v reálném čase a pak zapisují události v dávkách do data Lake Storage Gen1, aby mohly být dále zpracovány.

Níže jsou uvedeny nástroje, které můžete použít:

* [Azure Stream Analytics](../stream-analytics/stream-analytics-data-lake-output.md) – události přijaté do centra událostí se můžou zapisovat do Azure Data Lake Storage Gen1 pomocí výstupu Azure Data Lake Storage Gen1.
* [Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) – data můžete zapisovat přímo do data Lake Storage Gen1 z clusteru Storm.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) – můžete přijímat události z centra událostí a pak je zapsat do data lake storage gen1 pomocí [data lake storage gen1 .NET SDK](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Relační data
Můžete také zdrojovat data z relačních databází. V průběhu času relační databáze shromažďují obrovské množství dat, které mohou poskytnout klíčové poznatky, pokud jsou zpracovány prostřednictvím kanálu velkých objemů dat. Tato data můžete přesunout do úložiště datového jezera Gen1 pomocí následujících nástrojů.

* [Apačský sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Data protokolu webového serveru (nahrávání pomocí vlastních aplikací)
Tento typ datové sady je specificky volána, protože analýza dat protokolu webového serveru je běžný případ použití pro aplikace s velkými objemy dat a vyžaduje velké objemy souborů protokolu, které mají být odeslány do data Lake Storage Gen1. K nahrání těchto dat můžete použít některý z následujících nástrojů.

* [Azure CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

Pro nahrávání dat protokolu webového serveru a také pro nahrávání dalších druhů dat (např. data sociálních nálad) je to dobrý přístup k napsání vlastních skriptů / aplikací, protože vám dává flexibilitu zahrnout komponentu pro nahrávání dat jako součást vaší větší aplikace pro velké objemy dat. V některých případech může mít tento kód podobu skriptu nebo jednoduchého nástroje příkazového řádku. V ostatních případech může být kód použit k integraci zpracování velkých objemů dat do obchodní aplikace nebo řešení.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Data přidružená ke clusterům Azure HDInsight
Většina typů clusterů HDInsight (Hadoop, HBase, Storm) podporuje Data Lake Storage Gen1 jako úložiště dat. Clustery HDInsight přistupují k datům z objektů BLOB úložiště Azure (WASB). Pro lepší výkon můžete zkopírovat data z WASB do účtu Data Lake Storage Gen1 přidruženého ke clusteru. Ke kopírování dat můžete použít následující nástroje.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Služba AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Data uložená v místních clusterech nebo clusterech IaaS Hadoop
Velké množství dat může být uloženo v existujících clusterech Hadoop, místně na počítačích s hdfs. Clustery Hadoop mohou být v místním nasazení nebo mohou být v rámci clusteru IaaS v Azure. Mohou existovat požadavky na kopírování těchto dat do Azure Data Lake Storage Gen1 pro jednorázový přístup nebo opakovaným způsobem. Existují různé možnosti, které můžete použít k dosažení tohoto cíle. Níže je uveden seznam alternativ a souvisejících kompromisů.

| Přístup | Podrobnosti | Výhody | Požadavky |
| --- | --- | --- | --- |
| Použití Azure Data Factory (ADF) ke kopírování dat přímo z clusterů Hadoop do Azure Data Lake Storage Gen1 |[ADF podporuje HDFS jako zdroj dat](../data-factory/connector-hdfs.md) |ADF poskytuje ihned po vybalení podporu pro HDFS a prvotřídní komplexní správu a monitorování |Vyžaduje, aby byla brána pro správu dat nasazena místně nebo v clusteru IaaS. |
| Exportujte data z Hadoopu jako soubory. Pak zkopírujte soubory do Azure Data Lake Storage Gen1 pomocí vhodného mechanismu. |Soubory do Azure Data Lake Storage Gen1 můžete kopírovat pomocí: <ul><li>[Azure PowerShell pro operační systém s Windows](data-lake-store-get-started-powershell.md)</li><li>[Azure CLI](data-lake-store-get-started-cli-2.0.md)</li><li>Vlastní aplikace využívající libovolné úložiště datového jezera Gen1 SDK</li></ul> |Rychle začít. Umí přizpůsobené nahrávání. |Vícestupňový proces, který zahrnuje více technologií. Řízení a monitorování bude v průběhu času výzvou vzhledem k přizpůsobené povaze nástrojů |
| Pomocí distcp zkopírovat data z Hadoop do Azure Storage. Pak zkopírujte data z Azure Storage do Data Lake Storage Gen1 pomocí vhodného mechanismu. |Data z Azure Storage do Data Lake Storage Gen1 můžete kopírovat pomocí: <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[Nástroj AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Apache DistCp spuštěný v clusterech HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Můžete použít open-source nástroje. |Vícestupňový proces, který zahrnuje více technologií |

### <a name="really-large-datasets"></a>Opravdu velké datové sady
Pro nahrávání datových sad, které se pohybují v několika terabajtech, může být použití výše popsaných metod někdy pomalé a nákladné. V takových případech můžete použít níže uvedené možnosti.

* **Použití Azure ExpressRoute**. Azure ExpressRoute umožňuje vytvářet privátní připojení mezi datovými centry Azure a infrastrukturou ve vašem místním prostředí. To poskytuje spolehlivou možnost přenosu velkého množství dat. Další informace naleznete v [dokumentaci k Azure ExpressRoute](../expressroute/expressroute-introduction.md).
* **"Offline" nahrávání dat**. Pokud použití Azure ExpressRoute není možné z nějakého důvodu, můžete použít [Azure Import/Export služby](../storage/common/storage-import-export-service.md) k odeslání pevné disky s daty do datového centra Azure. Vaše data se nejprve nahrají do objektů Blob s úložištěm Azure. Potom můžete použít [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) nebo Nástroj [AdlCopy](data-lake-store-copy-data-azure-storage-blob.md) ke kopírování dat z objektů Blob služby Azure Storage do Data Lake Storage Gen1.

  > [!NOTE]
  > Při použití služby Import/Export by velikost souborů na discích, které dodáte do datového centra Azure, neměla být větší než 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-storage-gen1"></a>Zpracování dat uložených v úložišti datového jezera Gen1
Jakmile jsou data k dispozici v Data Lake Storage Gen1, můžete spustit analýzu na tato data pomocí podporovaných aplikací pro velké objemy dat. V současné době můžete pomocí Azure HDInsight a Azure Data Lake Analytics spouštět úlohy analýzy dat na datech uložených v gen1 úložiště datového jezera.

![Analýza dat v úložišti datových jezer Gen1](./media/data-lake-store-data-scenarios/analyze-data.png "Analýza dat v úložišti datových jezer Gen1")

Můžete se podívat na následující příklady.

* [Vytvoření clusteru HDInsight s úložištěm Data Lake Storage 1 jako úložištěm](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Použití Azure Data Lake Analytics s datelake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-storage-gen1"></a>Stahování dat z data Lake Storage Gen1
Můžete také stáhnout nebo přesunout data z Azure Data Lake Storage Gen1 pro scénáře, jako jsou:

* Přesuňte data do jiných úložišť do rozhraní s existujícími kanály pro zpracování dat. Můžete například chtít přesunout data z Data Lake Storage Gen1 do Azure SQL Database nebo místně SQL Server.
* Stáhněte data do místního počítače pro zpracování v prostředích IDE při vytváření prototypů aplikací.

![Odchozí data z úložiště datového jezera Gen1](./media/data-lake-store-data-scenarios/egress-data.png "Odchozí data z úložiště datového jezera Gen1")

V takových případech můžete použít některou z následujících možností:

* [Apačský sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Můžete také použít následující metody k napsání vlastního skriptu nebo aplikace ke stažení dat z Data Lake Storage Gen1.

* [Azure CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-storage-gen1"></a>Vizualizace dat v úložišti datových jezer Gen1
Pomocí kombinace služeb můžete vytvořit vizuální reprezentace dat uložených v datovém úložišti dat Gen1.

![Vizualizace dat v úložišti datových jezer Gen1](./media/data-lake-store-data-scenarios/visualize-data.png "Vizualizace dat v úložišti datových jezer Gen1")

* Můžete začít pomocí [Azure Data Factory k přesunutí dat z Data Lake Storage Gen1 do Azure SQL Data Warehouse](../data-factory/copy-activity-overview.md)
* Poté můžete [Power BI integrovat s Azure SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) a vytvořit tak vizuální reprezentaci dat.
