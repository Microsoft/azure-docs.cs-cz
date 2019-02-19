---
title: Data scénáře zahrnující Azure Data Lake Storage Gen2 | Dokumentace Microsoftu
description: Pochopit různé scénáře a nástroje, pomocí kterých můžete ingestuje, zpracování, stahování a vizualizovat v Data Lake Storage Gen2 (dříve označované jako Azure Data Lake Store)
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: normesta
ms.openlocfilehash: 66d7a2ce5833729d601ae896f0903a17a40fc8a3
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56339485"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Pro potřeby velkého objemu dat pomocí Azure Data Lake Storage Gen2

Existují čtyři fáze klíče ve zpracování velkého objemu dat:

> [!div class="checklist"]
> * Ingestovat velké objemy dat do úložiště dat v reálném čase nebo v dávkách
> * Zpracování dat
> * Stahování dat
> * Vizualizace dat

Začněte vytvořením účtu úložiště a systému souborů. Udělte přístup k datům. První několik částí tohoto článku můžete provádět tyto úlohy. Ve zbývající části poukážeme možností a nástrojů pro jednotlivé fáze zpracování.

## <a name="create-a-data-lake-storage-gen2-account"></a>Vytvoření účtu Data Lake Storage Gen2

Účet Data Lake Storage Gen2 je účet úložiště, který má hierarchického oboru názvů. 

Pokud chcete jeden vytvořit, přečtěte si téma [rychlý start: Vytvoření účtu úložiště Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="create-a-file-system"></a>Vytvořit systém souborů

A *systém souborů* je kontejner pro soubory a složky. Budete potřebovat alespoň jeden z nich zahájíte ingestovat data ve vašem účtu úložiště.  Tady je seznam nástrojů, které můžete použít k jejich vytvoření.

|Nástroj | Doprovodné materiály |
|---|--|
|Azure Storage Explorer | [Vytvořit systém souborů pomocí Průzkumníka služby Storage](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-explorer#create-a-filesystem) |
|AzCopy | [Vytvoření kontejneru objektů Blob nebo sdílené složky pomocí AzCopyV10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-blob-container-or-file-share)|
|Soubor Hadoop System (HDFS) rozhraní příkazového řádku (CLI) s HDInsight |[Vytvořit systém souborů pomocí HDFS s HDInsight](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-hdfs-data-lake-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system) |
|Kód v Azure poznámkového bloku Databricks|[Vytvořit systém souborů (Scala) účet úložiště](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-storage-account-file-system) <br><br> [Vytvořit systém souborů a připojit ji (Python)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-databricks-spark?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system-and-mount-it)|

To je nejjednodušší vytvořit systémy souborů pomocí Průzkumníka služby Storage a AzCopy. Trvá o něco více práce vytvořit systémy souborů pomocí HDInsight a Databricks. Nicméně pokud plánujete používat HDInsight nebo clustery Databricks zpracování vašich dat i přesto, potom můžete nejprve vytvořte své clustery a používat rozhraní příkazového řádku HDFS systémů vytvořit soubor.  

## <a name="grant-access-to-the-data"></a>Udělení přístupu k datům

Než začnete ingestovat data, nastavte odpovídající přístupová oprávnění k vašemu účtu a data ve vašem účtu.

Není k dispozici tři způsoby, jak udělit přístup:

* Přiřadíte jednu z těchto rolí uživatele, skupiny, identita spravované uživatele nebo instanční objekt:

  [Čtenář dat objektu Blob služby Storage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview)

  [Storage Blob Data Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview)

  [Vlastník dat úložiště objektů Blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner-preview)

* Pomocí tokenu sdíleného přístupového podpisu (SAS).

* Použijte klíče účtu úložiště.

Tato tabulka ukazuje, jak udělit přístup pro každý nástroj nebo služba Azure.

|Nástroj | K udělení přístupu | Doprovodné materiály |
|---|--|---|
|Storage Explorer| Přiřazení role pro uživatele a skupiny | [Přiřazení rolí správce a bez oprávnění správce uživatelům v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) |
|AzCopy| Přiřazení role pro uživatele a skupiny <br>**nebo**<br> Pomocí tokenu SAS| [Přiřazení rolí správce a bez oprávnění správce uživatelům v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)<br><br>[Snadné vytvoření SAS ke stažení souboru z Azure Storage – pomocí Průzkumníka služby Azure Storage](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)|
|Apache DistCp | Přiřazení role uživatel přiřazenou spravované identity | [Vytvoření clusteru služby HDInsight s Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2#creating-an-hdinsight-cluster-with-data-lake-storage-gen2) |
|Azure Data Factory| Přiřazení role na identitu uživatele přiřazeny – spravované<br>**nebo**<br> Přiřadit roli instančnímu objektu služby<br>**nebo**<br> Použít klíče účtu úložiště | [Vlastnosti propojené služby](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#linked-service-properties) |
|Azure HDInsight| Přiřazení role uživatel přiřazenou spravované identity | [Vytvoření clusteru služby HDInsight s Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2#creating-an-hdinsight-cluster-with-data-lake-storage-gen2)|
|Azure Databricks| Přiřadit roli instančnímu objektu služby | [Postup: Použití portálu k vytvoření aplikace Azure AD a instanční objekt, který má přístup k prostředkům](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)|

Pokud chcete udělit přístup pro konkrétní soubor a složky, najdete v těchto článcích.

* [Nastavení souborů a adresářů úrovně oprávnění pomocí Průzkumníka služby Azure Storage s Azure Data Lake Storage Gen2](https://review.docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

* [Seznamy řízení přístupu na soubory a adresáře](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories)

Další informace o nastavení dalších aspektů zabezpečení najdete v tématu [Průvodci zabezpečením Azure Data Lake Storage Gen2](https://review.docs.microsoft.com/en-us/azure/storage/common/storage-data-lake-storage-security-guide?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="ingest-the-data"></a>Zpracování příjmu dat

Tato část se zaměřuje různých zdrojích dat a různými způsoby, ve které je možné ingestovat data do účtu Azure Data Lake Storage Gen2.

![Ingestovat data do Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Ingestovat data do Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Dat ad hoc

To představuje menší datové sady, které se používá pro vytváření prototypů velké objemy dat aplikace. Ingestuje dat ad hoc v závislosti na zdroji dat různými způsoby. 

Tady je seznam nástrojů, které slouží k ingestování dat ad hoc.

| Zdroj dat | Ingestování pomocí |
| --- | --- |
| Místní počítač |[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Nástroj AzCopy](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Nástroj AzCopy](../common/storage-use-azcopy-v10.md)<br><br>[DistCp běžící v clusteru HDInsight](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Datové proudy

Reprezentuje data, která mohou být generovány různých zdrojů, jako jsou aplikace, zařízení, snímačů atd. Tato data je možné ingestovat v Data Lake Sorage Gen2 pomocí různých nástrojů. Tyto nástroje se obvykle zachycení a zpracování dat na základě událostí událostí v reálném čase a napište událostí v dávkách do Data Lake Storage Gen2 tak, aby se mohou být dále zpracovány.

Tady je seznam nástrojů, které vám umožní ingestovat streamovaná data.

|Nástroj | Doprovodné materiály |
|---|--|
|Azure HDInsight Storm | [Zápis do Apache Hadoop HDFS z Apache Storm v HDInsight](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>Relační data

Můžete také zdroje dat z relačních databází. Po určitou dobu shromažďovat relačních databází obrovské objemy dat, které můžou poskytovat hlavní zjištěné poznatky, pokud se zpracovává prostřednictvím kanálu velké objemy dat. Tyto nástroje můžete použít k přesunutí těchto dat do Data Lake Storage Gen2.

Tady je seznam nástrojů, které vám umožní ingestovat relační data.

|Nástroj | Doprovodné materiály |
|---|--|
|Azure Data Factory | [Aktivita kopírování ve službě Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Data protokolu webového serveru (nahrávání pomocí vlastních aplikací)

Tento typ datové sady je konkrétně volat, protože analýzy dat protokolů webového serveru je běžným případem použití pro velké objemy dat aplikace a vyžaduje velké objemy soubory protokolů k nahrání do Data Lake Storage Gen2. K zápisu vlastních skriptů nebo aplikací k odeslání těchto dat můžete použít některý z následujících nástrojů.

Tady je seznam nástrojů, které vám umožní ingestovat data webového serveru.

|Nástroj | Doprovodné materiály |
|---|--|
|Azure Data Factory | [Aktivita kopírování ve službě Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |

Pro nahrávání dat protokolů webového serveru a také pro nahrávání jiné druhy dat (např. sociální zabarvení dat) je dobrý přístup k zapisovat vlastní vlastních skriptů nebo aplikací, protože poskytuje flexibilní zahrnout data nahrávání komponenty jako součást větší velké objemy dat aplikace. V některých případech může být tento kód ve tvaru skriptu nebo nástroj příkazového řádku jednoduché. V ostatních případech kódu lze integrovat zpracování velkého objemu dat do podnikových aplikací nebo řešení.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Data související s využitím clusterů Azure HDInsight

Většina typy clusterů HDInsight (Hadoop, HBase, Storm) podporují jako úložiště dat úložiště Data Lake Storage Gen2. Clustery HDInsight přistupovat k datům z objektů BLOB Azure Storage (WASB). Pro zajištění lepšího výkonu můžete zkopírovat data z WASB do účtu Data Lake Storage Gen2 přidružené ke clusteru. Ke zkopírování dat můžete použít následující nástroje.

Tady je seznam nástrojů, které vám umožní ingestovat data související s clustery HDInsight.

|Nástroj | Doprovodné materiály |
|---|--|
|Apache DistCp | [Použití DistCp ke kopírování dat mezi objekty BLOB Azure Storage a Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|Nástroj AzCopy | [Přenos dat pomocí AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Kopírování dat do nebo z Azure Data Lake Storage Gen1 pomocí služby Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Data uložená v místním nebo IaaS Hadoop clusterů

Velké objemy dat mohou být uložena ve stávajících clusterů Hadoop, místně na počítači, které používají HDFS. Clusterů systému Hadoop, může být v místním nasazení nebo může být v rámci clusteru služby IaaS v Azure. Je možné požadavky ke zkopírování těchto dat do Azure Data Lake Storage Gen2 přístup, jednorázové nebo opakované způsobem. Existují různé možnosti, které vám umožní dosáhnout. Níže je seznam alternativních a přidružené kompromisy.

| Přístup | Podrobnosti | Výhody | Požadavky |
| --- | --- | --- | --- |
| Použití Azure Data Factory (ADF) ke kopírování dat do Azure Data Lake Storage Gen2 přímo z clusterů Hadoop |[ADF podporuje HDFS jako zdroj dat](../../data-factory/connector-hdfs.md) |ADF poskytuje podporu out-of-the-box HDFS a prvotřídní end-to-end správy a monitorování |Vyžaduje bránu správy dat nasazená místně, nebo infrastruktury jako clusterů |
| Použití Distcp ke kopírování dat z Hadoopu do služby Azure Storage. Zkopírujte data ze služby Azure Storage k Data Lake Storage Gen2 pomocí vhodný mechanismus. |Pomocí Data Lake Storage Gen2 můžete kopírování dat ze služby Azure Storage: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[Nástroj AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[Apache DistCp spouštění v clusterech HDInsight](data-lake-storage-use-distcp.md)</li></ul> |Můžete použít open source nástroje. |Vícefázový proces, který zahrnuje více technologií |

### <a name="really-large-datasets"></a>Velmi rozsáhlé datové sady

Pro nahrání datových sad, které v rozsahu v několika terabajtů, pomocí metod popsaných výše někdy může být pomalé a nákladná. V takovém případě můžete použít Azure ExpressRoute.  

Azure ExpressRoute umožňuje vytvářet privátní připojení mezi datacentry Azure a infrastrukturou ve vlastních prostorách. To poskytuje spolehlivé variantou při přenosech velkých objemů dat. Další informace najdete v tématu [dokumentace ke službě Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-the-data"></a>Zpracování dat

Jakmile jsou data dostupná v Data Lake Storage Gen2 je na těchto datech pomocí aplikací podporovaných velké objemy dat spustit analýzu. 

![Analýza dat v Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "analýzy dat v Data Lake Storage Gen2")

Tady je seznam nástrojů, které můžete použít ke spuštění úlohy analýzy dat na data, která je uložená v Data Lake Storage Gen2.

|Nástroj | Doprovodné materiály |
|---|--|
|Azure HDInsight | [Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Rychlé zprovoznění: Analýza dat v Azure Data Lake Storage Gen2 pomocí Azure Databricks](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Kurz: Extrakce, transformace a načítání dat pomocí Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Vizualizace dat

Chcete-li vytvořit vizuální reprezentace dat uložených v Data Lake Storage Gen2 můžete použít kombinaci služeb.

![Vizualizace dat v Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/visualize-data.png "vizualizace dat v Data Lake Storage Gen2")

* Můžete spustit pomocí [Azure Data Factory k přesunu dat z Data Lake Storage Gen2 do služby Azure SQL Data Warehouse](../../data-factory/copy-activity-overview.md)
* Potom můžete [integrace Power BI s využitím Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) vizuální znázornění dat vytvořit.

## <a name="download-the-data"></a>Stáhnout data

Můžete také chtít stáhnout nebo přesun dat z Azure Data Lake Storage Gen2 pro scénáře, jako například:

* Přesun dat do jiných úložišť rozhraní s vaší stávající kanálů zpracování dat. Můžete například chtít přesunout data z Data Lake Storage Gen2 do Azure SQL Database nebo místní SQL Server.

* Stahování dat do místního počítače pro zpracování v prostředí (IDE) při vytváření prototypů aplikací.

![Výchozí přenos dat data z Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "výchozí přenos dat data z Data Lake Storage Gen2")

Tady je seznam nástrojů, které vám umožní stáhnout data z Data Lake Storage Gen2.

|Nástroj | Doprovodné materiály |
|---|--|
|Azure Data Factory | [Aktivita kopírování ve službě Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCop | [Použití DistCp ke kopírování dat mezi objekty BLOB Azure Storage a Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
