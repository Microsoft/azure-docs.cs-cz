---
title: Scénáře dat zahrnující Azure Data Lake Storage Gen2 | Microsoft Docs
description: Seznamte se s různými scénáři a nástroji, pomocí kterých se data mohou ingestovat, zpracovávat, stahovat a vizuálně vyrozumět v Data Lake Storage Gen2 (dříve označované jako Azure Data Lake Store).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: bffa7894f7603f95c4840019be5e5670797881df
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98873242"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Použití Azure Data Lake Storage Gen2 pro požadavky na velké objemy dat

Existují čtyři klíčové fáze zpracování velkých objemů dat:

> [!div class="checklist"]
> * Ingestování velkých objemů dat do úložiště dat v reálném čase nebo v dávkách
> * Zpracování dat
> * Stahování dat
> * Vizualizace dat

Tento článek popisuje možnosti a nástroje pro jednotlivé fáze zpracování.

Úplný seznam služeb Azure, které můžete používat se službou Azure Data Lake Storage Gen2, najdete v tématu věnovaném [integraci Azure Data Lake Storage se službami Azure](./data-lake-storage-supported-azure-services.md) .

## <a name="ingest-the-data-into-data-lake-storage-gen2"></a>Ingestování dat do Data Lake Storage Gen2

V této části se vysvětlují různé zdroje dat a různé způsoby, kterými je možné data do účtu Data Lake Storage Gen2 ingestovat.

![Ingestování dat do Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Ingestování dat do Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Ad hoc data

To představuje menší sady dat, které se používají pro vytváření prototypů aplikace s velkým objemem dat. Existují různé způsoby, jak ingestovat ad hoc data v závislosti na zdroji dat. 

Tady je seznam nástrojů, které můžete použít k ingestování ad hoc dat.

| Zdroj dat | Ingestování pomocí |
| --- | --- |
| Místní počítač |[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)<br><br>[Azure CLI](data-lake-storage-directory-file-acl-cli.md)<br><br>[Průzkumník služby Storage](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Nástroj AzCopy](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Nástroj AzCopy](../common/storage-use-azcopy-v10.md)<br><br>[DistCp běžící na clusteru HDInsight](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Streamovaná data

To představuje data, která je možné vygenerovat různými zdroji, jako jsou aplikace, zařízení, senzory atd. Tato data je možné ingestovat do Data Lake Storage Gen2 pomocí různých nástrojů. Tyto nástroje budou obvykle zaznamenávat a zpracovávat data na základě události v reálném čase a následně zapisovat události v dávkách do Data Lake Storage Gen2 tak, aby je bylo možné dále zpracovat.

Tady je seznam nástrojů, které můžete použít k ingestování datových proudů.

|Nástroj | Pokyny |
|---|--|
|Azure Stream Analytics|[Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](../../stream-analytics/stream-analytics-quick-create-portal.md) <br> [Odchozí přenos dat do Azure Data Lake Gen2](../../stream-analytics/stream-analytics-define-outputs.md)|
|Azure HDInsight – zaplavení | [Zápis do Apache Hadoop HDFS z Apache Storm ve službě HDInsight](../../hdinsight/storm/apache-storm-write-data-lake-store.md) |

### <a name="relational-data"></a>Relační data

Můžete také zdrojová data z relačních databází. V časovém intervalu relační databáze shromažďují velké objemy dat, které poskytují klíčové přehledy, pokud jsou zpracovávány prostřednictvím kanálu pro velké objemy dat. Pomocí následujících nástrojů můžete přesunout taková data do Data Lake Storage Gen2.

Tady je seznam nástrojů, které můžete použít k ingestování relačních dat.

|Nástroj | Pokyny |
|---|--|
|Azure Data Factory | [Aktivita kopírování ve službě Azure Data Factory](../../data-factory/copy-activity-overview.md) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Data protokolu webového serveru (nahrávání pomocí vlastních aplikací)

Tento typ datové sady je konkrétně vyvolán, protože analýza dat protokolu webového serveru je běžném případem použití pro aplikace s velkými objemy dat a vyžaduje nahrání velkých objemů souborů protokolu do Data Lake Storage Gen2. Pomocí některého z následujících nástrojů můžete napsat vlastní skripty nebo aplikace, které budou ukládat taková data.

Tady je seznam nástrojů, které můžete použít k ingestování dat protokolu webového serveru.

|Nástroj | Pokyny |
|---|--|
|Azure Data Factory | [Aktivita kopírování ve službě Azure Data Factory](../../data-factory/copy-activity-overview.md)  |
|Azure CLI|[Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|Azure PowerShell|[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)|

Pro nahrání dat protokolu webového serveru a také pro nahrávání dalších druhů dat (např. data ze sociálních sítí zabarvení) je dobrým přístupem k psaní vlastních skriptů nebo aplikací, protože nabízí flexibilitu, která umožňuje zahrnout komponentu pro nahrávání dat jako součást větší aplikace pro velké objemy dat. V některých případech může tento kód mít podobu skriptu nebo jednoduchého nástroje příkazového řádku. V jiných případech může být kód použit k integraci zpracování velkých objemů dat do obchodní aplikace nebo řešení.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Data přidružená k clusterům Azure HDInsight

Většina typů clusterů HDInsight (Hadoop, HBA, propamì) podporuje Data Lake Storage Gen2 jako úložiště dat. Clustery HDInsight přistupují k datům z objektů blob Azure Storage (WASB). Pro lepší výkon můžete zkopírovat data z WASB do účtu Data Lake Storage Gen2 přidruženého ke clusteru. K kopírování dat můžete použít následující nástroje.

Tady je seznam nástrojů, které můžete použít k ingestování dat přidružených ke clusterům HDInsight.

|Nástroj | Pokyny |
|---|--|
|Apache DistCp | [Použití DistCp ke kopírování dat mezi objekty blob Azure Storage a Azure Data Lake Storage Gen2](./data-lake-storage-use-distcp.md) |
|Nástroj AzCopy | [Přenos dat pomocí AzCopy](../common/storage-use-azcopy-v10.md) |
|Azure Data Factory | [Kopírování dat do nebo z Azure Data Lake Storage Gen2 pomocí Azure Data Factory](../../data-factory/load-azure-data-lake-storage-gen2.md) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Data uložená v místních nebo IaaS clusterech Hadoop

Velké objemy dat může být uloženo v existujících clusterech Hadoop místně na počítačích se systémem HDFS. Clustery Hadoop můžou být v místním nasazení nebo se můžou nacházet v rámci clusteru IaaS v Azure. Můžou nastat požadavky na kopírování takových dat do Azure Data Lake Storage Gen2 pro jednorázový přístup nebo pro opakovaný pokus. Existují různé možnosti, které můžete použít k dosažení tohoto. Níže je uveden seznam alternativ a Spojených kompromisů.

| Přístup | Podrobnosti | Výhody | Požadavky |
| --- | --- | --- | --- |
| Použití Azure Data Factory (ADF) ke kopírování dat přímo z clusterů Hadoop do Azure Data Lake Storage Gen2 |[ADF podporuje HDFS jako zdroj dat.](../../data-factory/connector-hdfs.md) |ADF poskytuje okamžitou podporu pro HDFS a první třídu a komplexní správu a monitorování. |Vyžaduje, aby byla Správa datá brána nasazená místně nebo v clusteru IaaS. |
| Pomocí Distcp zkopírujte data z Hadoop do Azure Storage. Pak zkopírujte data z Azure Storage pro Data Lake Storage Gen2 pomocí vhodného mechanismu. |Data z Azure Storage můžete zkopírovat do Data Lake Storage Gen2 pomocí: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[Nástroj AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[Apache DistCp běžící na clusterech HDInsight](data-lake-storage-use-distcp.md)</li></ul> |Můžete použít Open Source nástroje. |Proces s více kroky, který zahrnuje několik technologií |

### <a name="really-large-datasets"></a>Skutečně velké datové sady

Pro nahrání datových sad, které jsou v rozsahu několika terabajtů, můžou použití výše popsaných metod někdy být pomalé a nákladné. V takových případech můžete použít Azure ExpressRoute.  

Azure ExpressRoute vám umožňuje vytvářet privátní připojení mezi datovými centry Azure a infrastrukturou ve vašich prostorách. To poskytuje spolehlivé možnosti pro přenos velkých objemů dat. Další informace najdete v [dokumentaci ke službě Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-the-data"></a>Zpracování dat

Jakmile jsou data dostupná v Data Lake Storage Gen2 můžete pro tato data spustit analýzu pomocí podporovaných aplikací pro velké objemy dat. 

![Analyzovat data v Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "Analyzovat data v Data Lake Storage Gen2")

Tady je seznam nástrojů, pomocí kterých můžete spouštět úlohy analýzy dat pro data uložená v Data Lake Storage Gen2.

|Nástroj | Pokyny |
|---|--|
|Azure HDInsight | [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Rychlý Start: Analýza dat v Azure Data Lake Storage Gen2 pomocí Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Kurz: Extrakce, transformace a načítání dat pomocí Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Vizualizace dat

Pomocí konektoru Power BI můžete vytvářet vizuální reprezentace dat uložených v Data Lake Storage Gen2. Další informace najdete [v tématu Analýza dat v Azure Data Lake Storage Gen2 pomocí Power BI](/power-query/connectors/datalakestorage).

## <a name="download-the-data"></a>Stažení dat

Můžete také chtít stáhnout nebo přesunout data z Azure Data Lake Storage Gen2 ve scénářích, jako jsou například:

* Přesuňte data do jiných úložišť do rozhraní s vašimi stávajícími kanály zpracování dat. Například můžete chtít přesunout data z Data Lake Storage Gen2 do Azure SQL Database nebo instanci SQL Server.

* Při sestavování prototypů aplikací Stáhněte data do místního počítače pro zpracování v prostředích IDE.

![Výstupní data z Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Výstupní data z Data Lake Storage Gen2")

Tady je seznam nástrojů, pomocí kterých můžete stahovat data z Data Lake Storage Gen2.

|Nástroj | Pokyny |
|---|--|
|Azure Data Factory | [Aktivita kopírování ve službě Azure Data Factory](../../data-factory/copy-activity-overview.md) |
|Apache DistCp | [Použití DistCp ke kopírování dat mezi objekty blob Azure Storage a Azure Data Lake Storage Gen2](./data-lake-storage-use-distcp.md) |
|Průzkumník služby Azure Storage|[Správa adresářů, souborů a seznamů ACL ve službě Azure Data Lake Storage Gen2 pomocí Průzkumníka služby Azure Storage](data-lake-storage-explorer.md)|
|Nástroj AzCopy|[Přenos dat pomocí AzCopy a BLOB Storage](../common/storage-use-azcopy-v10.md#transfer-data)|
