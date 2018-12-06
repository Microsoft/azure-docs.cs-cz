---
title: Data scénáře zahrnující Azure Data Lake Storage Gen2 | Dokumentace Microsoftu
description: Pochopit různé scénáře a nástroje, pomocí kterých můžete ingestuje, zpracování, stahování a vizualizovat v Data Lake Storage Gen2 (dříve označované jako Azure Data Lake Store)
services: storage
author: normesta
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.openlocfilehash: 978f86141d72cc7be43f24909f9780ab9570605d
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52974889"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Pro potřeby velkého objemu dat pomocí Azure Data Lake Storage Gen2

Existují čtyři fáze klíče ve zpracování velkého objemu dat:

* Ingestovat velké objemy dat do úložiště dat v reálném čase nebo v dávkách
* Zpracování dat
* Stahování dat
* Vizualizace dat

V tomto článku se podíváme na těchto fází s ohledem na Azure Data Lake Storage Gen2 vám pomohou pochopit možnosti a nástroje k vašim potřebám velkých objemů dat k dispozici.

## <a name="ingest-data-into-data-lake-storage-gen2"></a>Ingestovat data do Data Lake Storage Gen2
Tato část se zaměřuje různých zdrojích dat a různými způsoby, ve které je možné ingestovat data do účtu Azure Data Lake Storage Gen2.

![Ingestovat data do Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Ingestovat data do Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Dat ad hoc
To představuje menší datové sady, které se používá pro vytváření prototypů velké objemy dat aplikace. Ingestuje dat ad hoc v závislosti na zdroji dat různými způsoby.

| Zdroj dat | Ingestování pomocí |
| --- | --- |
| Místní počítač |<ul> <li>[Průzkumník služby Storage](https://azure.microsoft.com/features/storage-explorer/)</ul> |
| Azure Storage Blob |<ul> <li>[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)</li> <li>[Nástroj AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[DistCp běžící v clusteru HDInsight](data-lake-storage-use-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Datové proudy
Reprezentuje data, která mohou být generovány různých zdrojů, jako jsou aplikace, zařízení, snímačů atd. Tato data je možné ingestovat v Data Lake Storage Gen2 pomocí různých nástrojů. Tyto nástroje se obvykle zachycení a zpracování dat na základě událostí událostí v reálném čase a napište událostí v dávkách do Data Lake Storage Gen2 tak, aby se mohou být dále zpracovány.

Toto jsou nástroje, které můžete použít:

* [Azure HDInsight Storm](../../hdinsight/storm/apache-storm-write-data-lake-store.md) – umožňuje zápis dat přímo do Data Lake Storage Gen2 z clusteru Storm.

### <a name="relational-data"></a>Relační data
Můžete také zdroje dat z relačních databází. Po určitou dobu shromažďovat relačních databází obrovské objemy dat, které můžou poskytovat hlavní zjištěné poznatky, pokud se zpracovává prostřednictvím kanálu velké objemy dat. Tyto nástroje můžete použít k přesunutí těchto dat do Data Lake Storage Gen2.

* [Azure Data Factory](../../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Data protokolu webového serveru (nahrávání pomocí vlastních aplikací)
Tento typ datové sady je konkrétně volat, protože analýzy dat protokolů webového serveru je běžným případem použití pro velké objemy dat aplikace a vyžaduje velké objemy soubory protokolů k nahrání do Data Lake Storage Gen2. K zápisu vlastních skriptů nebo aplikací k odeslání těchto dat můžete použít některý z následujících nástrojů.

* [Azure Data Factory](../../data-factory/copy-activity-overview.md)

Pro nahrávání dat protokolů webového serveru a také pro nahrávání jiné druhy dat (např. sociální zabarvení dat) je dobrý přístup k zapisovat vlastní vlastních skriptů nebo aplikací, protože poskytuje flexibilní zahrnout data nahrávání komponenty jako součást větší velké objemy dat aplikace. V některých případech může být tento kód ve tvaru skriptu nebo nástroj příkazového řádku jednoduché. V ostatních případech kódu lze integrovat zpracování velkého objemu dat do podnikových aplikací nebo řešení.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Data související s využitím clusterů Azure HDInsight
Většina typy clusterů HDInsight (Hadoop, HBase, Storm) podporují jako úložiště dat úložiště Data Lake Storage Gen2. Clustery HDInsight přistupovat k datům z objektů BLOB Azure Storage (WASB). Pro zajištění lepšího výkonu můžete zkopírovat data z WASB do účtu Data Lake Storage Gen2 přidružené ke clusteru. Ke zkopírování dat můžete použít následující nástroje.

* [Apache DistCp](data-lake-storage-use-distcp.md)
* [Nástroj AzCopy](../common/storage-use-azcopy-v10.md)
* [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Data uložená v místním nebo IaaS Hadoop clusterů
Velké objemy dat mohou být uložena ve stávajících clusterů Hadoop, místně na počítači, které používají HDFS. Clusterů systému Hadoop, může být v místním nasazení nebo může být v rámci clusteru služby IaaS v Azure. Je možné požadavky ke zkopírování těchto dat do Azure Data Lake Storage Gen2 přístup, jednorázové nebo opakované způsobem. Existují různé možnosti, které vám umožní dosáhnout. Níže je seznam alternativních a přidružené kompromisy.

| Přístup | Podrobnosti | Výhody | Požadavky |
| --- | --- | --- | --- |
| Použití Azure Data Factory (ADF) ke kopírování dat do Azure Data Lake Storage Gen2 přímo z clusterů Hadoop |[ADF podporuje HDFS jako zdroj dat](../../data-factory/connector-hdfs.md) |ADF poskytuje podporu out-of-the-box HDFS a prvotřídní end-to-end správy a monitorování |Vyžaduje bránu správy dat nasazená místně, nebo infrastruktury jako clusterů |
| Použití Distcp ke kopírování dat z Hadoopu do služby Azure Storage. Zkopírujte data ze služby Azure Storage k Data Lake Storage Gen2 pomocí vhodný mechanismus. |Pomocí Data Lake Storage Gen2 můžete kopírování dat ze služby Azure Storage: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[Nástroj AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[Apache DistCp spouštění v clusterech HDInsight](data-lake-storage-use-distcp.md)</li></ul> |Můžete použít open source nástroje. |Vícefázový proces, který zahrnuje více technologií |

### <a name="really-large-datasets"></a>Velmi rozsáhlé datové sady
Pro nahrání datových sad, které v rozsahu v několika terabajtů, pomocí metod popsaných výše někdy může být pomalé a nákladná. V takovém případě můžete pomocí následujících možností.

* **Pomocí Azure ExpressRoute**. Azure ExpressRoute umožňuje vytvářet privátní připojení mezi datacentry Azure a infrastrukturou ve vlastních prostorách. To poskytuje spolehlivé variantou při přenosech velkých objemů dat. Další informace najdete v tématu [dokumentace ke službě Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-data-stored-in-data-lake-storage-gen2"></a>Zpracování dat uložených v Data Lake Storage Gen2
Jakmile jsou data dostupná v Data Lake Storage Gen2 je na těchto datech pomocí aplikací podporovaných velké objemy dat spustit analýzu. V současné době můžete použít Azure HDInsight a Azure Databricks můžete spouštět úlohy analýzy dat na data uložená v Data Lake Storage Gen2.

![Analýza dat v Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "analýzy dat v Data Lake Storage Gen2")


## <a name="download-data-from-data-lake-storage-gen2"></a>Stáhnout data z Data Lake Storage Gen2
Můžete také chtít stáhnout nebo přesun dat z Azure Data Lake Storage Gen2 pro scénáře, jako například:

* Přesun dat do jiných úložišť rozhraní s vaší stávající kanálů zpracování dat. Můžete například chtít přesunout data z Data Lake Storage Gen2 do Azure SQL Database nebo místní SQL Server.
* Stahování dat do místního počítače pro zpracování v prostředí (IDE) při vytváření prototypů aplikací.

![Výchozí přenos dat data z Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "výchozí přenos dat data z Data Lake Storage Gen2")

V takovém případě můžete použít některý z následujících možností:

* [Azure Data Factory](../../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-storage-use-distcp.md)

## <a name="visualize-data-in-data-lake-storage-gen2"></a>Vizualizace dat v Data Lake Storage Gen2
Chcete-li vytvořit vizuální reprezentace dat uložených v Data Lake Storage Gen2 můžete použít kombinaci služeb.

![Vizualizace dat v Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/visualize-data.png "vizualizace dat v Data Lake Storage Gen2")

* Můžete spustit pomocí [Azure Data Factory k přesunu dat z Data Lake Storage Gen2 do služby Azure SQL Data Warehouse](../../data-factory/copy-activity-overview.md)
* Potom můžete [integrace Power BI s využitím Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) vizuální znázornění dat vytvořit.
