---
title: Scénáře dat obsahující Data Lake Store | Microsoft Docs
description: Pochopit různé scénáře a nástroje pro použití, která data můžete požity, zpracování, staženy a vizualizována v Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 37409a71-a563-4bb7-bc46-2cbd426a2ece
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: e6b89a4cb415c2d127244d55acf3e284425f03e0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="using-azure-data-lake-store-for-big-data-requirements"></a>Požadavky na velkých objemů dat pomocí Azure Data Lake Store
Existují čtyři fáze klíče v zpracování velkých objemů dat:

* Příjem velkých objemů dat do úložiště dat v reálném čase nebo v dávkách
* Zpracování dat
* Stahování dat
* Vizualizaci dat

V tomto článku se podíváme na tyto fáze s ohledem na Azure Data Lake Store pochopení nástroje dostupné pro podle svých potřeb velkých objemů dat a možnosti.

## <a name="ingest-data-into-data-lake-store"></a>Ingestovat data do Data Lake Store
V této části jsou zdůrazněné různých zdrojů dat a různé způsoby, ve kterém můžete konzumaci dat do účtu Data Lake Store.

![Ingestovat data do Data Lake Store](./media/data-lake-store-data-scenarios/ingest-data.png "Ingestovat data do Data Lake Store")

### <a name="ad-hoc-data"></a>Ad hoc dat
To představuje menší sady dat, které se používá pro vytváření prototypů velkých objemů dat aplikace. Příjem ad hoc dat v závislosti na zdroji dat různými způsoby.

| Zdroj dat | Ingestování pomocí |
| --- | --- |
| Místní počítač |<ul> <li>[Azure Portal](/data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure CLI a platformy 2.0](data-lake-store-get-started-cli-2.0.md)</li> <li>[Pomocí nástrojů Data Lake pro Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Objekt Blob úložiště Azure |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[Nástroj AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp běžící v clusteru HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Datové proudy
Reprezentuje data, která může být generována různých zdrojů, jako je například aplikace, zařízení, senzorů, atd. Tato data můžete konzumaci do Data Lake Store pomocí různých nástrojů. Tyto nástroje se obvykle zachycení a zpracování dat na základě událostí událostí v reálném čase a zapište si událostí v dávkách do Data Lake Store tak, že můžete mít další zpracovány.

Toto jsou nástroje, které můžete použít:

* [Azure Stream Analytics](../stream-analytics/stream-analytics-data-lake-output.md) -události požity do centra událostí je možné zapsat do Azure Data Lake pomocí Azure Data Lake Store výstup.
* [Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) -zápisu dat do Data Lake Store přímo z clusteru Storm.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) – může přijímat události ze služby Event Hubs a pak zapsat pomocí Data Lake Store [Data Lake Store .NET SDK](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Relační data
Zdrojem může také data z relační databáze. Období čas relačních databází shromažďovat obrovské objemy dat, které můžou poskytovat klíče statistiky, pokud je zpracování velkých objemů dat kanálu. Tyto nástroje můžete přesunout taková data do Data Lake Store.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Data protokolu webového serveru (nahrávání pomocí vlastních aplikací)
Tento typ datové sady je konkrétně označuje, protože analýzy dat protokolu webového serveru se běžně používá pro velké objemy dat aplikace a vyžaduje velké svazky protokolových souborů k odeslání do Data Lake Store. Zápis vlastní skripty nebo aplikace pro nahrávání těchto dat můžete použít některý z následujících nástrojů.

* [Azure CLI a platformy 2.0](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [.NET SDK služby Azure Data Lake Store](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

Pro nahrávání dat protokolu webového serveru a také pro odesílání dalších typů dat (např. sociálních chráněny data) je dobré přístup pro zápis vlastní vlastních skriptů nebo aplikací, protože poskytuje vám flexibilitu zahrnout data odesílání součásti jako součást větší velkých objemů dat aplikace. V některých případech může tento kód mít formu skript nebo nástroj příkazového řádku jednoduché. V ostatních případech kód lze integrovat zpracování velkých objemů dat do obchodní aplikace nebo řešení.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Data přidružená k Azure HDInsight clustery
Většina typů clusteru HDInsight (Hadoop, HBase, Storm) podporují jako úložiště dat úložiště Data Lake Store. Clustery HDInsight přístup k datům z Azure úložiště objektů BLOB (WASB). Pro lepší výkon můžete zkopírovat data z WASB do účtu Data Lake Store související s clusterem. Ke zkopírování dat můžete použít následující nástroje.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy služby](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Data uložená v místní nebo IaaS Hadoop clusterů
Velké objemy dat mohou být uloženy ve stávajících clusterů Hadoop, místně do počítače, které používají HDFS. Může být v místním nasazení clusterů systému Hadoop, nebo může být v rámci clusteru služby IaaS v Azure. Může se požadavky na přístup, jednorázové nebo opakované způsobem zkopírovat taková data do Azure Data Lake Store. Existují různé možnosti, které můžete použít k dosažení tohoto cíle. Níže je seznam alternativních a přidružené kompromisy.

| Přístup | Podrobnosti | Výhody | Požadavky |
| --- | --- | --- | --- |
| Kopírování dat přímo z clusterů systému Hadoop do Azure Data Lake Store pomocí Azure Data Factory (ADF) |[ADF podporuje HDFS jako zdroj dat](../data-factory/connector-hdfs.md) |ADF poskytuje podporu se na pole pro HDFS a první klient server správy a monitorování |Vyžaduje Brána pro správu dat nasadit místně nebo v IaaS clusteru |
| Exportujte data z Hadoop jako soubory. Poté zkopírujte soubory do Azure Data Lake Store pomocí vhodný mechanismus. |Pomocí Azure Data Lake Store můžete zkopírovat soubory: <ul><li>[Prostředí Azure PowerShell pro systém Windows operačního systému](data-lake-store-get-started-powershell.md)</li><li>[Azure CLI a platformy 2.0 pro Windows OS](data-lake-store-get-started-cli-2.0.md)</li><li>Vlastní aplikaci pomocí jakékoli Data Lake Store SDK</li></ul> |Rychle začít pracovat. Můžete provést vlastní nahrávání |Vícekrokový proces, který zahrnuje několik technologie. Správa a sledování se zvýší být výzvu nad doba zadaná přizpůsobené povaha nástroje |
| Použití Distcp ke zkopírování dat z Hadoopu do úložiště Azure. Potom zkopírujte data z úložiště Azure Data Lake Store pomocí vhodný mechanismus. |Může kopírovat data z úložiště Azure Data Lake Store pomocí: <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[Nástroj AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Apache DistCp systémem clustery HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Můžete použít nástroje open source. |Vícekrokový proces, který zahrnuje několik technologie |

### <a name="really-large-datasets"></a>Opravdu rozsáhlých datových sad
Nahrát datové sady, které rozsahu v několika terabajtů, pomocí metod popsaných výše může být někdy pomalé a nákladná. V takových případech můžete pomocí následujících možností.

* **Pomocí služby Azure ExpressRoute**. Azure ExpressRoute vám umožňuje vytvářet privátní připojení mezi datovými centry Azure a infrastruktury místně. To poskytuje spolehlivé možnost pro přenos velkých objemů dat. Další informace najdete v tématu [dokumentace Azure ExpressRoute](../expressroute/expressroute-introduction.md).
* **"Do režimu offline" nahrávání dat**. Pokud pomocí Azure ExpressRoute není možné je z jakéhokoli důvodu, můžete použít [služba Azure Import/Export](../storage/common/storage-import-export-service.md) pro odeslání jednotky pevného disku s daty pro datové centrum Azure. Vaše data, je nejprve nahrán do úložiště objektů BLOB služby Azure. Pak můžete použít [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) nebo [AdlCopy nástroj](data-lake-store-copy-data-azure-storage-blob.md) ke zkopírování dat z úložiště objektů BLOB Azure do Data Lake Store.

  > [!NOTE]
  > Když použijete službu Import/Export, velikosti souborů na discích, které jsou k datovému centru Azure by neměla být větší než 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-store"></a>Zpracování dat uložených v Data Lake Store
Jakmile jsou data k dispozici v Data Lake Store můžete spustit analýzu na tato data pomocí aplikací podporovaných velkých objemů dat. V současné době můžete použít Azure HDInsight a Azure Data Lake Analytics ke spuštění úloh analýzy dat na data uložená v Data Lake Store.

![Analýza dat v Data Lake Store](./media/data-lake-store-data-scenarios/analyze-data.png "analýza dat v Data Lake Store")

Můžete si prohlédnout následující příklady.

* [Vytvoření clusteru HDInsight s Data Lake Store jako úložiště](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Použití Azure Data Lake Analytics se službou Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-store"></a>Stahování dat z Data Lake Store
Můžete také chtít stáhnout nebo přesun dat z Azure Data Lake Store pro scénáře, jako například:

* Přesun dat do jiných úložišť na rozhraní s vaší stávající zpracování dat kanály. Například můžete chtít přesun dat z Data Lake Store do Azure SQL Database nebo místní SQL Server.
* Stahování dat do místního počítače pro zpracování v prostředí IDE při vytváření prototypů aplikace.

![Výstupních dat z Data Lake Store](./media/data-lake-store-data-scenarios/egress-data.png "výstupních dat z Data Lake Store")

V takových případech můžete použít některý z následujících možností:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Následující metody můžete také psát vlastní skript nebo aplikace ke stahování dat z Data Lake Store.

* [Azure CLI a platformy 2.0](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [.NET SDK služby Azure Data Lake Store](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-store"></a>Vizualizace dat v Data Lake Store
Směs služby slouží k vytvoření vizuální znázornění dat uložených v Data Lake Store.

![Vizualizace dat v Data Lake Store](./media/data-lake-store-data-scenarios/visualize-data.png "vizualizaci dat v Data Lake Store")

* Můžete spustit pomocí [Azure Data Factory pro přesun dat z Data Lake Store k Azure SQL Data Warehouse](../data-factory/copy-activity-overview.md)
* Potom můžete [Power BI integrovat Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) vizuální znázornění dat vytvořit.
