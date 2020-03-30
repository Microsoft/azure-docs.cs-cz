---
title: Datové scénáře zahrnující Azure Data Lake Storage Gen2 | Dokumenty společnosti Microsoft
description: Seznamte se s různými scénáři a nástroji, pomocí kterých se data můžou ingestovat, zpracovávat, stahovat a vizualizovat v úložišti Datových jezer Gen2 (dříve známé jako Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b0ebe6cb505fa2a145dd3cbb94398912f2933a4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77369710"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Použití Azure Data Lake Storage Gen2 pro požadavky na velké objemy dat

Existuje čtyři klíčové fáze zpracování velkých objemů dat:

> [!div class="checklist"]
> * Ingestování velkého množství dat do úložiště dat v reálném čase nebo v dávkách
> * Zpracování údajů
> * Stahování dat
> * Vizualizace dat

Tento článek zvýrazní možnosti a nástroje pro každou fázi zpracování.

Úplný seznam služeb Azure, které můžete používat s Azure Data Lake Storage Gen2, najdete [v tématu Integrace Azure Data Lake Storage se službami Azure.](data-lake-storage-integrate-with-azure-services.md)

## <a name="ingest-the-data-into-data-lake-storage-gen2"></a>Ingestování dat do úložiště datového jezera Gen2

Tato část upozorňuje na různé zdroje dat a různé způsoby, ve kterých mohou být tato data ingestována do účtu Data Lake Storage Gen2.

![Ingestování dat do úložiště datového jezera Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Ingestování dat do úložiště datového jezera Gen2")

### <a name="ad-hoc-data"></a>Ad hoc data

To představuje menší datové sady, které se používají pro vytváření prototypů aplikace velkých objemů dat. Existují různé způsoby ingestování dat ad hoc v závislosti na zdroji dat. 

Tady je seznam nástrojů, které můžete použít k ingestování dat ad hoc.

| Zdroj dat | Ingestovat pomocí |
| --- | --- |
| Místní počítač |[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)<br><br>[Azure CLI](data-lake-storage-directory-file-acl-cli.md)<br><br>[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Nástroj AzCopy](../common/storage-use-azcopy-v10.md)|
| Objekt blob úložiště Azure |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Nástroj AzCopy](../common/storage-use-azcopy-v10.md)<br><br>[DistCp spuštěný v clusteru HDInsight](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Streamovaná data

To představuje data, která mohou být generována z různých zdrojů, jako jsou aplikace, zařízení, senzory, atd. Tato data lze ingestovat do úložiště Datové ho jezera Gen2 pomocí různých nástrojů. Tyto nástroje obvykle zachycují a zpracovávají data na základě událostí v reálném čase a pak zapisují události v dávkách do data Lake Storage Gen2, aby mohly být dále zpracovány.

Tady je seznam nástrojů, které můžete použít k ingestování streamovaných dat.

|Nástroj | Doprovodné materiály |
|---|--|
|Azure Stream Analytics|[Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Výstup do Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Bouře Azure HDInsight | [Napište Apache Hadoop HDFS z Apache Storm na HDInsight](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>Relační data

Můžete také zdrojovat data z relačních databází. V průběhu času relační databáze shromažďují obrovské množství dat, které mohou poskytnout klíčové poznatky, pokud jsou zpracovány prostřednictvím kanálu velkých objemů dat. Tato data můžete přesunout do úložiště datového jezera Gen2 pomocí následujících nástrojů.

Tady je seznam nástrojů, které můžete použít k ingestování relačních dat.

|Nástroj | Doprovodné materiály |
|---|--|
|Azure Data Factory | [Aktivita kopírování ve službě Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Data protokolu webového serveru (nahrávání pomocí vlastních aplikací)

Tento typ datové sady je specificky volána, protože analýza dat protokolu webového serveru je běžný případ použití pro aplikace s velkými objemy dat a vyžaduje velké objemy souborů protokolu, které mají být odeslány do data Lake Storage Gen2. K nahrání těchto dat můžete použít některý z následujících nástrojů.

Zde je seznam nástrojů, které můžete použít k ingestování dat protokolu webového serveru.

|Nástroj | Doprovodné materiály |
|---|--|
|Azure Data Factory | [Aktivita kopírování ve službě Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |
|Azure CLI|[Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|Azure PowerShell|[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)|

Pro nahrávání dat protokolu webového serveru a také pro nahrávání dalších druhů dat (např. data sociálních nálad) je to dobrý přístup k napsání vlastních skriptů / aplikací, protože vám dává flexibilitu zahrnout komponentu pro nahrávání dat jako součást vaší větší aplikace pro velké objemy dat. V některých případech může mít tento kód podobu skriptu nebo jednoduchého nástroje příkazového řádku. V ostatních případech může být kód použit k integraci zpracování velkých objemů dat do obchodní aplikace nebo řešení.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Data přidružená ke clusterům Azure HDInsight

Většina typů clusterů HDInsight (Hadoop, HBase, Storm) podporuje Data Lake Storage Gen2 jako úložiště dat. Clustery HDInsight přistupují k datům z objektů BLOB úložiště Azure (WASB). Pro lepší výkon můžete zkopírovat data z WASB do účtu Data Lake Storage Gen2 přidruženého ke clusteru. Ke kopírování dat můžete použít následující nástroje.

Tady je seznam nástrojů, které můžete použít k ingestování dat přidružených k clusterům HDInsight.

|Nástroj | Doprovodné materiály |
|---|--|
|Apache DistCp | [Kopírování dat mezi objekty BLOB úložiště Azure a Gen2 úložiště datových úložišť Azure](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|Nástroj AzCopy | [Přenos dat pomocí AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Kopírování dat do nebo z Azure Data Lake Storage Gen2 pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Data uložená v místních clusterech nebo clusterech IaaS Hadoop

Velké množství dat může být uloženo v existujících clusterech Hadoop, místně na počítačích s hdfs. Clustery Hadoop mohou být v místním nasazení nebo mohou být v rámci clusteru IaaS v Azure. Mohou existovat požadavky na kopírování těchto dat do Azure Data Lake Storage Gen2 pro jednorázový přístup nebo opakovaným způsobem. Existují různé možnosti, které můžete použít k dosažení tohoto cíle. Níže je uveden seznam alternativ a souvisejících kompromisů.

| Přístup | Podrobnosti | Výhody | Požadavky |
| --- | --- | --- | --- |
| Použití Azure Data Factory (ADF) ke kopírování dat přímo z clusterů Hadoop do Azure Data Lake Storage Gen2 |[ADF podporuje HDFS jako zdroj dat](../../data-factory/connector-hdfs.md) |ADF poskytuje ihned po vybalení podporu pro HDFS a prvotřídní komplexní správu a monitorování |Vyžaduje, aby byla brána pro správu dat nasazena místně nebo v clusteru IaaS. |
| Pomocí distcp zkopírovat data z Hadoop do Azure Storage. Pak zkopírujte data z Azure Storage do Data Lake Storage Gen2 pomocí vhodného mechanismu. |Data z Azure Storage do Data Lake Storage Gen2 můžete kopírovat pomocí: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[Nástroj AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[Apache DistCp spuštěný v clusterech HDInsight](data-lake-storage-use-distcp.md)</li></ul> |Můžete použít open-source nástroje. |Vícestupňový proces, který zahrnuje více technologií |

### <a name="really-large-datasets"></a>Opravdu velké datové sady

Pro nahrávání datových sad, které se pohybují v několika terabajtech, může být použití výše popsaných metod někdy pomalé a nákladné. V takových případech můžete použít Azure ExpressRoute.  

Azure ExpressRoute umožňuje vytvářet privátní připojení mezi datovými centry Azure a infrastrukturou ve vašem místním prostředí. To poskytuje spolehlivou možnost přenosu velkého množství dat. Další informace najdete v [dokumentaci k Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-the-data"></a>Zpracování dat

Jakmile jsou data k dispozici v Data Lake Storage Gen2, můžete spustit analýzu na tato data pomocí podporovaných aplikací pro velké objemy dat. 

![Analýza dat v úložišti datových jezer Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "Analýza dat v úložišti datových jezer Gen2")

Tady je seznam nástrojů, které můžete použít ke spuštění úloh analýzy dat na datech uložených v gen2 úložiště datového jezera.

|Nástroj | Doprovodné materiály |
|---|--|
|Azure HDInsight | [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Úvodní příručka: Analýza dat v Azure Data Lake Storage Gen2 pomocí Azure Databricks](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Kurz: Extrahujte, transformujte a načtěte data pomocí Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Vizualizace dat

Pomocí konektoru Power BI můžete vytvářet vizuální reprezentace dat uložených v zařízení Data Lake Storage Gen2. Viz [Analýza dat v Azure Data Lake Storage Gen2 pomocí Power BI](https://docs.microsoft.com/power-query/connectors/datalakestorage).

## <a name="download-the-data"></a>Stáhněte si data

Můžete také stáhnout nebo přesunout data z Azure Data Lake Storage Gen2 pro scénáře, jako jsou:

* Přesuňte data do jiných úložišť do rozhraní s existujícími kanály pro zpracování dat. Můžete například chtít přesunout data z Data Lake Storage Gen2 do Azure SQL Database nebo místně SQL Server.

* Stáhněte data do místního počítače pro zpracování v prostředích IDE při vytváření prototypů aplikací.

![Odchozí data z úložiště datového jezera Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Odchozí data z úložiště datového jezera Gen2")

Tady je seznam nástrojů, které můžete použít ke stažení dat z Data Lake Storage Gen2.

|Nástroj | Doprovodné materiály |
|---|--|
|Azure Data Factory | [Aktivita kopírování ve službě Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCp | [Kopírování dat mezi objekty BLOB úložiště Azure a Gen2 úložiště datových úložišť Azure](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|Azure Storage Explorer|[Správa adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2 pomocí Průzkumníka úložišť Azure](data-lake-storage-explorer.md)|
|Nástroj AzCopy|[Přenos dat pomocí úložiště AzCopy a Blob](../common/storage-use-azcopy-blobs.md)|
