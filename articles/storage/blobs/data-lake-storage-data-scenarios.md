---
title: Scénáře dat zahrnující Azure Data Lake Storage Gen2 | Microsoft Docs
description: Seznamte se s různými scénáři a nástroji, pomocí kterých se data mohou ingestovat, zpracovávat, stahovat a vizuálně vyrozumět v Data Lake Storage Gen2 (dříve označované jako Azure Data Lake Store).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: eba0c6a8932a8c6d50bd98d94712c95516519274
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300328"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Použití Azure Data Lake Storage Gen2 pro požadavky na velké objemy dat

Existují čtyři klíčové fáze zpracování velkých objemů dat:

> [!div class="checklist"]
> * Ingestování velkých objemů dat do úložiště dat v reálném čase nebo v dávkách
> * Zpracování dat
> * Stahování dat
> * Vizualizace dat

Začněte tím, že vytvoříte účet úložiště a kontejner. Pak udělte přístup k datům. První část tohoto článku vám pomůžou tyto úlohy provést. Ve zbývajících částech zvýrazníme možnosti a nástroje pro každou fázi zpracování.

Úplný seznam služeb Azure, které můžete používat se službou Azure Data Lake Storage Gen2, najdete v tématu věnovaném [integraci Azure Data Lake Storage se službami Azure](data-lake-store-integrate-with-azure-services.md) .

## <a name="create-a-data-lake-storage-gen2-account"></a>Vytvoření účtu Data Lake Storage Gen2

Účet Data Lake Storage Gen2 je účet úložiště, který má hierarchický obor názvů. 

Pokud ho chcete vytvořit, přečtěte si téma [rychlý Start: vytvoření účtu úložiště Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="create-a-container"></a>Vytvoření kontejneru

Tady je seznam nástrojů, pomocí kterých můžete vytvořit kontejner pro vaše soubory.

|Nástroj | Pokyny |
|---|--|
|Průzkumník služby Azure Storage | [Vytvoření kontejneru pomocí Průzkumník služby Storage](data-lake-storage-explorer.md#create-a-container) |
|AzCopy | [Vytvoření kontejneru objektů BLOB nebo sdílené složky pomocí AzCopyV10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#transfer-files)|
|Rozhraní příkazového řádku kontejneru Hadoop (HDFS) se službou HDInsight |[Vytvoření kontejneru pomocí HDFS v HDInsight](data-lake-storage-use-hdfs-data-lake-storage.md#create-a-container) |
|Kód v poznámkovém bloku Azure Databricks|[Vytvoření kontejneru účtu úložiště (Scala)](data-lake-storage-quickstart-create-databricks-account.md#create-storage-account-container) <br><br> [Vytvoření kontejneru a jeho připojení (Python)](data-lake-storage-use-databricks-spark.md#create-a-container-and-mount-it)|

Je nejjednodušší vytvořit systémy souborů pomocí Průzkumník služby Storage nebo AzCopy. Vytváření systémů souborů pomocí HDInsight a datacihly trvá trochu více práce. Pokud ale plánujete použít clustery HDInsight nebo datacihly ke zpracování vašich dat, můžete nejdřív vytvořit své clustery a použít rozhraní příkazového řádku HDFS pro vytváření systémů souborů.  

## <a name="grant-access-to-the-data"></a>Udělení přístupu k datům

Před zahájením ingestování dat nastavte pro svůj účet odpovídající přístupová oprávnění a data v účtu.

Existují tři způsoby, jak udělit přístup:

* Přiřaďte jednu z těchto rolí uživateli, skupině, uživatelsky spravované identitě nebo instančnímu objektu:

  [Čtečka dat objektů BLOB úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)

  [Přispěvatel dat objektu BLOB služby Storage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)

  [Vlastník dat objektu BLOB služby Storage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

* Použijte token sdíleného přístupového podpisu (SAS).

* Použijte klíč účtu úložiště.

Tato tabulka ukazuje, jak udělit přístup pro jednotlivé služby nebo nástroje Azure.

|Nástroj | Udělení přístupu | Pokyny |
|---|--|---|
|Průzkumník služby Storage| Přiřazení role uživatelům a skupinám | [Přiřazení rolí správců a uživatelů bez oprávnění správce k Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) |
|AzCopy| Přiřazení role uživatelům a skupinám <br>**ani**<br> Použít token SAS| [Přiřazení rolí správců a uživatelů bez oprávnění správce k Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)<br><br>[Snadné vytvoření SAS pro stažení souboru z Azure Storage – pomocí Průzkumník služby Azure Storage](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)|
|Apache DistCp | Přiřazení role k spravované identitě přiřazené uživatelem | [Vytvoření clusteru HDInsight pomocí Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Data Factory| Přiřazení role k uživatelsky přiřazené identitě spravované uživatelem<br>**ani**<br> Přiřazení role k instančnímu objektu<br>**ani**<br> Použití klíče účtu úložiště | [Vlastnosti propojené služby](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#linked-service-properties) |
|Azure HDInsight| Přiřazení role k spravované identitě přiřazené uživatelem | [Vytvoření clusteru HDInsight pomocí Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)|
|Azure Databricks| Přiřazení role k instančnímu objektu | [Postupy: použití portálu k vytvoření aplikace a instančního objektu služby Azure AD, který má přístup k prostředkům](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)|

Pokud chcete udělit přístup ke konkrétnímu souboru a složkám, přečtěte si tyto články.

* [Nastavení oprávnění na úrovni souborů a adresářů pomocí Průzkumník služby Azure Storage s Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

* [Seznamy řízení přístupu pro soubory a adresáře](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories)

Další informace o nastavení dalších aspektů zabezpečení najdete v tématu [Azure Data Lake Storage Gen2 Průvodce zabezpečením](https://docs.microsoft.com/azure/storage/common/storage-data-lake-storage-security-guide?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="ingest-the-data"></a>Ingestování dat

V této části se vysvětlují různé zdroje dat a různé způsoby, kterými je možné data do účtu Data Lake Storage Gen2 ingestovat.

Ingestování ![dat do data Lake Storage Gen2]ingestování(./media/data-lake-storage-data-scenarios/ingest-data.png "dat do data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Ad hoc data

To představuje menší sady dat, které se používají pro vytváření prototypů aplikace s velkým objemem dat. Existují různé způsoby, jak ingestovat ad hoc data v závislosti na zdroji dat. 

Tady je seznam nástrojů, které můžete použít k ingestování ad hoc dat.

| Zdroj dat | Ingestování pomocí |
| --- | --- |
| Místní počítač |[Průzkumník služby Storage](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Nástroj AzCopy](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Nástroj AzCopy](../common/storage-use-azcopy-v10.md)<br><br>[DistCp běžící na clusteru HDInsight](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Streamovaná data

To představuje data, která je možné vygenerovat různými zdroji, jako jsou aplikace, zařízení, senzory atd. Tato data je možné ingestovat do Data Lake Storage Gen2 pomocí různých nástrojů. Tyto nástroje budou obvykle zaznamenávat a zpracovávat data na základě události v reálném čase a následně zapisovat události v dávkách do Data Lake Storage Gen2 tak, aby je bylo možné dále zpracovat.

Tady je seznam nástrojů, které můžete použít k ingestování datových proudů.

|Nástroj | Pokyny |
|---|--|
|Azure HDInsight – zaplavení | [Zápis do Apache Hadoop HDFS z Apache Storm ve službě HDInsight](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>Relační data

Můžete také zdrojová data z relačních databází. V časovém intervalu relační databáze shromažďují velké objemy dat, které poskytují klíčové přehledy, pokud jsou zpracovávány prostřednictvím kanálu pro velké objemy dat. Pomocí následujících nástrojů můžete přesunout taková data do Data Lake Storage Gen2.

Tady je seznam nástrojů, které můžete použít k ingestování relačních dat.

|Nástroj | Pokyny |
|---|--|
|Azure Data Factory | [Aktivita kopírování ve službě Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Data protokolu webového serveru (nahrávání pomocí vlastních aplikací)

Tento typ datové sady je konkrétně vyvolán, protože analýza dat protokolu webového serveru je běžném případem použití pro aplikace s velkými objemy dat a vyžaduje nahrání velkých objemů souborů protokolu do Data Lake Storage Gen2. Pomocí některého z následujících nástrojů můžete napsat vlastní skripty nebo aplikace, které budou ukládat taková data.

Tady je seznam nástrojů, které můžete použít k ingestování dat protokolu webového serveru.

|Nástroj | Pokyny |
|---|--|
|Azure Data Factory | [Aktivita kopírování ve službě Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |

Pro nahrání dat protokolu webového serveru a také pro nahrávání dalších druhů dat (např. data ze sociálních sítí zabarvení) je dobrým přístupem k zápisu vlastních skriptů nebo aplikací, protože vám nabízí flexibilitu při zahrnutí komponenty pro nahrávání dat v rámci vaše větší aplikace pro velké objemy dat. V některých případech může tento kód mít podobu skriptu nebo jednoduchého nástroje příkazového řádku. V jiných případech může být kód použit k integraci zpracování velkých objemů dat do obchodní aplikace nebo řešení.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Data přidružená k clusterům Azure HDInsight

Většina typů clusterů HDInsight (Hadoop, HBA, propamì) podporuje Data Lake Storage Gen2 jako úložiště dat. Clustery HDInsight přistupují k datům z objektů blob Azure Storage (WASB). Pro lepší výkon můžete zkopírovat data z WASB do účtu Data Lake Storage Gen2 přidruženého ke clusteru. K kopírování dat můžete použít následující nástroje.

Tady je seznam nástrojů, které můžete použít k ingestování dat přidružených ke clusterům HDInsight.

|Nástroj | Pokyny |
|---|--|
|Apache DistCp | [Použití DistCp ke kopírování dat mezi objekty blob Azure Storage a Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|Nástroj AzCopy | [Přenos dat pomocí AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Kopírování dat do nebo z Azure Data Lake Storage Gen2 pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

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

![Analyzovat data v Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "analyzovat data v Data Lake Storage Gen2")

Tady je seznam nástrojů, pomocí kterých můžete spouštět úlohy analýzy dat pro data uložená v Data Lake Storage Gen2.

|Nástroj | Pokyny |
|---|--|
|Azure HDInsight | [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Rychlý Start: Analýza dat v Azure Data Lake Storage Gen2 pomocí Azure Databricks](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Kurz: extrakce, transformace a načtení dat pomocí Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Vizualizace dat

Pomocí kombinace služeb můžete vytvářet vizuální reprezentace dat uložených v Data Lake Storage Gen2.

![Vizualizovat data v Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/visualize-data.png "vizualizovat data v Data Lake Storage Gen2")

* [K přesunu dat z Data Lake Storage Gen2 na Azure SQL Data Warehouse](../../data-factory/copy-activity-overview.md) můžete začít pomocí Azure Data Factory.
* Potom můžete [integrovat Power BI s Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) a vytvořit tak vizuální reprezentaci dat.

## <a name="download-the-data"></a>Stažení dat

Můžete také chtít stáhnout nebo přesunout data z Azure Data Lake Storage Gen2 ve scénářích, jako jsou například:

* Přesuňte data do jiných úložišť do rozhraní s vašimi stávajícími kanály zpracování dat. Například můžete chtít přesunout data z Data Lake Storage Gen2 do Azure SQL Database nebo místních SQL Server.

* Při sestavování prototypů aplikací Stáhněte data do místního počítače pro zpracování v prostředích IDE.

![Výstupní data z Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "výstupní data z Data Lake Storage Gen2")

Tady je seznam nástrojů, pomocí kterých můžete stahovat data z Data Lake Storage Gen2.

|Nástroj | Pokyny |
|---|--|
|Azure Data Factory | [Aktivita kopírování ve službě Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCp | [Použití DistCp ke kopírování dat mezi objekty blob Azure Storage a Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
