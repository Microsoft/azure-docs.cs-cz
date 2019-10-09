---
title: Upgradujte řešení pro analýzu velkých objemů dat z Azure Data Lake Storage Gen1 na Azure Data Lake Storage Gen2
description: Upgradujte řešení, aby používalo Azure Data Lake Storage Gen2
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 02/07/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: rugopala
ms.openlocfilehash: aaff1d5e657b8acb28293f3450849b1446727680
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030789"
---
# <a name="upgrade-your-big-data-analytics-solutions-from-azure-data-lake-storage-gen1-to-azure-data-lake-storage-gen2"></a>Upgradujte řešení pro analýzu velkých objemů dat z Azure Data Lake Storage Gen1 na Azure Data Lake Storage Gen2

Pokud používáte Azure Data Lake Storage Gen1 ve vašich řešeních pro analýzu velkých objemů dat, tato příručka vám pomůže tato řešení upgradovat, aby používala Azure Data Lake Storage Gen2. Tento dokument můžete použít k vyhodnocení závislostí, které vaše řešení používá Data Lake Storage Gen1. V této příručce se také dozvíte, jak naplánovat a provést upgrade.

Pomůžeme vám následující úlohy:

: heavy_check_mark: posouzení připravenosti na upgrade

: heavy_check_mark: plánování upgradu

: heavy_check_mark: proveďte upgrade.

## <a name="assess-your-upgrade-readiness"></a>Posouzení připravenosti na upgrade

Naším cílem je, že všechny funkce, které jsou k dispozici v Data Lake Storage Gen1, budou zpřístupněny v Data Lake Storage Gen2. Způsob zveřejnění těchto schopností, například v sadě SDK, CLI atd., se může lišit mezi Data Lake Storage Gen1 a Data Lake Storage Gen2. Aplikace a služby, které pracují s Data Lake Storage Gen1, musí být schopné pracovat podobně s Data Lake Storage Gen2. A konečně, některé z možností nebudou k dispozici v Data Lake Storage Gen2 hned. Jakmile budou k dispozici, oznámíme je v tomto dokumentu.

Tyto další části vám pomohou při rozhodování, jak nejlépe upgradovat na Data Lake Storage Gen2 a kdy to může být vhodné.

### <a name="data-lake-storage-gen1-solution-components"></a>Data Lake Storage Gen1 součásti řešení

Pokud použijete Data Lake Storage Gen1 ve svých analytických řešeních nebo kanálech, máte k dispozici mnoho dalších technologií, které můžete využít k dosažení celé komplexní funkce. Tento [článek](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios) popisuje různé komponenty toku dat, které zahrnují ingestování, zpracování a využívání dat.

Kromě toho existují průřezové komponenty pro zřizování, správu a monitorování těchto komponent. Každá z komponent spolupracuje s Data Lake Storage Gen1 pomocí rozhraní, které pro ně nejlépe vyhovuje. Když plánujete upgradovat vaše řešení na Data Lake Storage Gen2, budete muset znát rozhraní, která se používají. Budete muset upgradovat rozhraní pro správu i datová rozhraní, protože každé rozhraní má rozdílné požadavky.

![Data Lake Storage součásti řešení](./media/data-lake-storage-upgrade/solution-components.png)

**Obrázek 1** výše zobrazuje komponenty funkcí, které se zobrazí ve většině analytických řešení.

**Obrázek 2** ukazuje příklad toho, jak budou tyto součásti implementovány pomocí konkrétních technologií.

Funkce ukládání v **Obrázek 1** je poskytována pomocí Data Lake Storage Gen1 (**Obrázek 2**). Všimněte si, jak různé komponenty v toku dat komunikují s Data Lake Storage Gen1 pomocí rozhraní REST API nebo Java SDK. Všimněte si také, jak komponenty funkcí pro křížové průřezy komunikují s Data Lake Storage Gen1. Součást zřizování používá šablony prostředků Azure, zatímco součást monitorování, která používá protokoly Azure Monitor, využívá provozní data, která pocházejí z Data Lake Storage Gen1.

Chcete-li upgradovat řešení z použití Data Lake Storage Gen1 na Data Lake Storage Gen2, bude nutné zkopírovat data a metadata a datové toky znovu připojit a pak všechny součásti budou muset být schopny pracovat s Data Lake Storage Gen2.

Níže uvedené části obsahují informace, které vám pomůžou lépe se rozhodovat:

: heavy_check_mark: možnosti platformy

: heavy_check_mark: programovací rozhraní

: heavy_check_mark: ekosystém Azure

: heavy_check_mark: partnerský ekosystém

: heavy_check_mark: provozní informace

V každé části budete moci určit "musí být" pro váš upgrade. Až budete mít jistotu, že jsou dostupné možnosti, nebo máte jistotu, že jsou k dispozici přiměřená alternativní řešení, přejděte k části [plánování upgradu](#planning-for-an-upgrade) v této příručce.

### <a name="platform-capabilities"></a>Možnosti platformy

Tato část popisuje, které Data Lake Storage Gen1 možnosti platformy, které jsou aktuálně k dispozici v Data Lake Storage Gen2.

| | Data Lake Storage Gen1 | Data Lake Storage Gen2 – cíl | Data Lake Storage Gen2 – stav dostupnosti  |
|-|------------------------|-------------------------------|-----------------------------------------------|
| Organizace dat| Podporuje data uložená jako složky a soubory. | Podporuje data uložená jako objekty nebo objekty blob i složky a soubory – [odkaz](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | Podporuje data uložená jako složky a soubor – *nyní je k dispozici* . <br><br> Podporuje data uložená jako objekty nebo objekty blob – *zatím nejsou k dispozici* . |
| Obor názvů| Hierarchické | Hierarchické |  *Nyní k dispozici*  |
| rozhraní API  | REST API přes HTTPS | REST API přes HTTP/HTTPS| *Nyní k dispozici* |
| Rozhraní API na straně serveru| [REST API kompatibilní WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) | Služba Azure Blob Service REST API [Data Lake Storage Gen2 REST API](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | Data Lake Storage Gen2 REST API – *nyní k dispozici* <br><br> Služba Azure Blob Service REST API – *zatím není k dispozici*       |
| Klient systému souborů Hadoop | Ano ([Azure Data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | Ano ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *Nyní k dispozici*  |  
| Operace s daty – autorizace  | Na úrovni souborů a složek POSIX Access Control seznamy (ACL) na základě Azure Active Directory identit  | Na úrovni souborů a složek POSIX Access Control seznamy (ACL) založené na Azure Active Directory [klíč sdílení](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) identit na úrovni účtu Access Control ([RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)) pro přístup k kontejnerům | *Nyní k dispozici* |
| Operace s daty – protokoly  | Ano | Jednorázové požadavky na protokoly pro konkrétní dobu trvání s využitím podpory integrace monitorování Azure lístku | Jednorázové žádosti o protokoly pro konkrétní dobu trvání pomocí lístku podpory – *dostupné hned*<br><br> Integrace monitorování Azure – *zatím není k dispozici* |
| Data šifrování v klidovém umístění | Transparentní, strana serveru pomocí klíčů spravovaných službou a pomocí klíčů spravovaných zákazníkem ve službě Azure Key trezor | Transparentní, strana serveru pomocí klíčů spravovaných službou a se spravovanými klíči zákaznických klíčů ve službě Azure Key trezor | Klíče spravované službou – *nyní dostupné*<br><br> Klíče spravované zákazníkem – *dostupné hned*  |
| Operace správy (například vytvoření účtu) | [Řízení přístupu na základě role](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) poskytované Azure pro správu účtů | [Řízení přístupu na základě role](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) poskytované Azure pro správu účtů | *Nyní k dispozici*|
| Sady SDK pro vývojáře | .NET, Java, Python, Node. js  | .NET, Java, Python, Node. js, C++, Ruby, php, přejít, Android, iOS| *Ještě není k dispozici* |
| |Optimalizovaný výkon pro úlohy paralelní analýzy. Vysoká propustnost a IOPS | Optimalizovaný výkon pro úlohy paralelní analýzy. Vysoká propustnost a IOPS | *Nyní k dispozici* |
| Podpora Virtual Network (VNet)  | [Použití integrace Virtual Network](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Použití koncového bodu služby pro Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *Nyní k dispozici* |
| Omezení velikosti | Bez omezení velikosti účtů, velikostí souborů nebo počtu souborů | Neomezují velikosti účtů ani počet souborů. Velikost souboru je omezená na 5 TB. | *Nyní k dispozici*|
| Geografická redundance| Místně redundantní (LRS) | Místně redundantní (LRS) zóna redundantní (ZRS) geograficky redundantní (GRS) s přístupem pro čtení (RA-GRS). Další informace najdete [tady](https://docs.microsoft.com/azure/storage/common/storage-redundancy) .| *Nyní k dispozici* |
| Regionální dostupnost | Podívejte se [sem](https://azure.microsoft.com/regions/) | Všechny [oblasti Azure](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *Nyní k dispozici*                                                                                                                           |
| Cenové                                       | Zobrazit [ceny](https://azure.microsoft.com/pricing/details/data-lake-store/)                                                                            | Zobrazit [ceny](https://azure.microsoft.com/pricing/details/storage/data-lake/)                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| Smlouva SLA k dostupnosti                            | [Viz SLA](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [Viz SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *Nyní k dispozici*                                                                                                                           |
| Správa dat                             | Vypršení platnosti souboru                                                                                                                                        | Zásady životního cyklu                                                                                                                                                                                                                                                                                                                                                                                                          | *Ještě není k dispozici*                                                                                                                       |

### <a name="programming-interfaces"></a>Rozhraní pro programování

V této tabulce jsou popsány sady rozhraní API, které jsou k dispozici pro vaše vlastní aplikace. Aby bylo jasné, že se jedná o bit, oddělíme tyto sady rozhraní API na 2 typy: rozhraní API pro správu a rozhraní API systému souborů.

Rozhraní API pro správu vám pomůžou spravovat účty, zatímco rozhraní API systému souborů vám pomůžou pracovat se soubory a složkami.

|  Sada rozhraní API                           |  Data Lake Storage Gen1                                                                                                                                                                                                                                                                                                   | Dostupnost pro Data Lake Storage Gen2 s ověřováním pomocí sdíleného klíče | Dostupnost pro Data Lake Storage Gen2 s ověřováním OAuth                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| .NET SDK – Správa                  | [Propojit](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet)                                                                                                                                                                                                                 | *Nepodporováno*                                                      | *Nyní k dispozici –* [odkaz](https://docs.microsoft.com/rest/api/storageservices/operations-on-the-account--blob-service-)                                    |
| Sada .NET SDK – systém souborů                  | [Propojit](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/client?view=azure-dotnet)                                                                                                                                                                                                                     | *Ještě není k dispozici*                                                | *Ještě není k dispozici*                                                                                                                                             |
| Java SDK – Správa                  | [Propojit](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *Nepodporováno*                                                      | *Nyní k dispozici –* [odkaz](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob?view=azure-java-stable)                                     |
| Sada Java SDK – systém souborů                  | [Odkaz](https://docs.microsoft.com/java/api/overview/azure/datalake)                                                                                                                                                                                                                                         | *Ještě není k dispozici*                                                | *Ještě není k dispozici*                                                                                                                                             |
| Node. js – Správa                   | [Odkaz](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | Není podporováno                                                      | *Nyní k dispozici –* [odkaz](https://azure.github.io/azure-storage-node/)                                                                                            |
| Node. js – systém souborů                   | [Odkaz](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | *Ještě není k dispozici*                                                | *Ještě není k dispozici*                                                                                                                                             |
| Správa Pythonu                    | [Odkaz](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                 | *Nepodporováno*                                                      | *Nyní k dispozici –* [odkaz](https://docs.microsoft.com/python/api/overview/azure/storage/management?view=azure-python)                                       |
| Python – systém souborů                    | [Propojit](https://azure-datalake-store.readthedocs.io/en/latest/)                                                                                                                                                                                                                                                                 | *Ještě není k dispozici*                                                | *Ještě není k dispozici*                                                                                                                                             |
| REST API – Správa                  | [Propojit](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *Nepodporováno*                                                      | *Nyní k dispozici –*                                                                                                                                               |
| REST API – systém souborů                  | [Propojit](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis)                                                                                                                                                                                                                                       | *Nyní k dispozici*                                                    | *Nyní k dispozici –* [odkaz](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)                                                      |
| PowerShell – Správa a systém souborů | [Propojit](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | Správa – nepodporovaný systém souborů – *zatím není k dispozici*        | Správa – *nyní dostupný –* [odkaz](https://docs.microsoft.com/powershell/module/az.storage) <br><br>Systém souborů – *zatím není k dispozici* |
| CLI – Správa                       | [Propojit](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *Nepodporováno*                                                      | *Nyní k dispozici –* [odkaz](https://docs.microsoft.com/cli/azure/storage?view=azure-cli-latest)                                                              |
| CLI – systém souborů                       | [Propojit](https://docs.microsoft.com/cli/azure/dls/fs?view=azure-cli-latest)                                                                                                                                                                                                                                               | *Ještě není k dispozici*                                                | *Ještě není k dispozici*                                                                                                                                             |
| Azure Resource Manager – Správa šablon             | [Template1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Template2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Template3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *Nepodporováno*                                                      | *Nyní k dispozici –* [odkaz](https://docs.microsoft.com/azure/templates/microsoft.storage/2018-07-01/storageaccounts)                                         |

### <a name="azure-ecosystem"></a>Ekosystém Azure

Při použití Data Lake Storage Gen1 můžete v komplexních kanálech používat nejrůznější služby a produkty Microsoftu. Tyto služby a produkty pracují s Data Lake Storage Gen1, a to buď přímo, nebo nepřímo. Tato tabulka obsahuje seznam služeb, které jsme upravili pro práci s Data Lake Storage Gen1, a ukazuje, které z nich jsou aktuálně kompatibilní s Data Lake Storage Gen2.

| **Místo**             | **Dostupnost pro Data Lake Storage Gen1**                                                                                                                                    | **Dostupnost pro Data Lake Storage Gen2 – s ověřováním pomocí sdíleného klíče**                                                                                                           | **Dostupnost pro Data Lake Storage Gen2 – s OAuth**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Analytické rozhraní  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *Nyní k dispozici*                                                                                                                                                              | *Nyní k dispozici*                                                                                                                                 |
|                      | [HDInsight](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [Hdinsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3,6 – *k dispozici nyní* HDInsight 4,0 – *zatím není k dispozici*      | HDInsight 3,6 ESP – *nyní dostupné* <br><br>  HDInsight 4,0 ESP – *zatím není k dispozici*                                                                 |
|                      | Databricks Runtime 3,1 a vyšší                                                                                                                                               | [Databricks Runtime 5,1 a novější](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) *– nyní k dispozici* | [Databricks Runtime 5,1 a novější](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) – *nyní k dispozici*                                                                                              |
|                      | [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *Nepodporováno*                                                                                                                                                              | [Odkaz](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) *k dispozici nyní* |
| Integrace dat     | [Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [Verze 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – aktuálně *dostupné* verze 1 – *Nepodporováno*                               | [Verze 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *nyní k dispozici* <br><br> Verze 1 – *Nepodporováno*  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *Nepodporováno*                                                                                                                                                              | *Nepodporováno*                                                                                                                                 |
|                      | [služba SSIS (SQL Server Integration Services)](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *Ještě není k dispozici*                                                                                                                                                          | *Ještě není k dispozici*                                                                                                                             |
|                      | [Data Catalog](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *Ještě není k dispozici*                                                                                                                                                          | *Ještě není k dispozici*                                                                                                                             |
|                      | [Logic Apps](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | *Ještě není k dispozici*                                                                                                                                                          | *Ještě není k dispozici*                                                                                                                             |
| IoT                  | [Event Hubs – zachycení](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | *Ještě není k dispozici*                                                                                                                                                          | *Ještě není k dispozici*                                                                                                                             |
|                      | [Stream Analytics](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | *Ještě není k dispozici*                                                                                                                                                          | *Ještě není k dispozici*                                                                                                                             |
| Nároky          | [Power BI Desktop](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | *Ještě není k dispozici*                                                                                                                                                          | *Ještě není k dispozici*                                                                                                                             |
|                      | [Excel](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | *Ještě není k dispozici*                                                                                                                                                          | *Ještě není k dispozici*                                                                                                                             |
|                      | [Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | *Ještě není k dispozici*                                                                                                                                                          | *Ještě není k dispozici*                                                                                                                             |
| Produktivitu         | [Azure Portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *Nepodporováno*                                                                                                                                                              | Správa účtů *– nyní k dispozici* <br><br>Datové operace *–*  *zatím nejsou k dispozici*                                                                   |
|                      | [Data Lake nástroje pro Visual Studio](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | *Ještě není k dispozici*                                                                                                                                                          | *Ještě není k dispozici*                                                                                                                             |
|                      | [Průzkumník služby Azure Storage](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | *Nyní k dispozici*                                                                                                                                                              | *Nyní k dispozici*                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | *Ještě není k dispozici*                                                                                                                                                          | *Ještě není k dispozici*                                                                                                                             |

### <a name="partner-ecosystem"></a>Partnerský ekosystém

Tato tabulka obsahuje seznam služeb a produktů třetích stran, které byly upraveny pro práci s Data Lake Storage Gen1. Také ukazuje, které z nich jsou aktuálně kompatibilní s Data Lake Storage Gen2.

| Oblast            | Instituc  | Produkt/služba  | Dostupnost pro Data Lake Storage Gen1                                                                                                                                               | Dostupnost pro Data Lake Storage Gen2 – s ověřováním pomocí sdíleného klíče                                                   | Dostupnost pro Data Lake Storage Gen2 – s OAuth                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Analytické rozhraní | Cloudera     | CDH –                  | [Propojit](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *Ještě není k dispozici*                                                                                                  | [Propojit](https://www.cloudera.com/documentation/enterprise/latest/topics/admin_adls2_config.html)                                                                                                  |
|                     | Cloudera     | Altus                | [Propojit](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *Nepodporováno*                                                                                                                  | *Ještě není k dispozici*                                                                                                  |
|                     | HortonWorks  | HDP 3,0              | [Odkaz](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                       | *Ještě není k dispozici*                                                                                                  | *Ještě není k dispozici*                                                                                                  |
|                     | Qubole       |                      | [Propojit](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | *Ještě není k dispozici*                                                                                                  | *Ještě není k dispozici*                                                                                                  |
| ETL                 | StreamSets   |                      | [Propojit](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | *Ještě není k dispozici*                                                                                                  | *Ještě není k dispozici*                                                                                                  |
|                     | Informatica  |                      | [Propojit](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | *Ještě není k dispozici*                                                                                                  | *Ještě není k dispozici*                                                                                                  |
|                     | Attunity     |                      | [Propojit](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | *Ještě není k dispozici*                                                                                                  | *Ještě není k dispozici*                                                                                                  |
|                     | Alteryx      |                      | [Propojit](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | *Ještě není k dispozici*                                                                                                  | *Ještě není k dispozici*                                                                                                  |
|                     | ImanisData   |                      | [Propojit](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | *Ještě není k dispozici*                                                                                                  | *Ještě není k dispozici*                                                                                                  |
|                     | WANdisco     |                      | [Propojit](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [Propojit](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [Propojit](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>Provozní informace

Data Lake Storage Gen1 vloží konkrétní informace a data do jiných služeb, které vám pomůžou zprovoznění své kanály. Tato tabulka ukazuje dostupnost odpovídající podpory v Data Lake Storage Gen2.

| Typ dat                                                                                       | Dostupnost pro Data Lake Storage Gen1                                                                 | Dostupnost pro Data Lake Storage Gen2                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Fakturační data – měřiče, které se odesílají do Commerce Team pro účely fakturace a zpřístupněny zákazníkům  | *Nyní k dispozici*                                                                                             | *Nyní k dispozici*                                                                                                                           |
| Protokoly aktivit                                                                                          | [Propojit](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | Jednorázové požadavky na protokoly pro konkrétní dobu trvání pomocí lístku podpory – *k dispozici nyní je dostupná* integrace monitorování Azure – *zatím není k dispozici* . |
| Diagnostické protokoly                                                                                        | [Propojit](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | Jednorázové požadavky na protokoly pro konkrétní dobu trvání pomocí lístku podpory – *k dispozici nyní je dostupná* integrace monitorování Azure – *zatím není k dispozici* . |
| Metriky                                                                                                | *Nepodporováno*                                                                                               | *Nyní k dispozici –* [odkaz](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>Plánování upgradu

V této části se předpokládá, že jste si přezkoumali část [posouzení připravenosti na upgrade](#assess-your-upgrade-readiness) v tomto průvodci a že jsou splněné všechny závislosti. Pokud existují možnosti, které nejsou v Data Lake Storage Gen2 stále dostupné, pokračujte prosím pouze v případě, že znáte odpovídající alternativní řešení. V následujících částech najdete pokyny k plánování upgradu vašich kanálů. Vlastní upgrade bude popsán v části [provádění upgradu](#performing-the-upgrade) tohoto průvodce.

### <a name="upgrade-strategy"></a>Strategie upgradu

Nejdůležitější součástí upgradu je rozhodování o strategii. Toto rozhodnutí určuje možnosti, které vám budou mít k dispozici.

V této tabulce jsou uvedené některé známé strategie, které se použily k migraci databází, clusterů Hadoop atd. V našich pokynech přijmeme podobné strategie a přizpůsobíme je našemu kontextu.

| Strategie                   | IT                                                                                  | Cons                                                           | Kdy použít?                                                                                                                                                                                             |
|--------------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Přezvednutí a posunutí                 | Nejjednodušší.                                                                                 | Vyžaduje výpadek při kopírování dat, přesouvání úloh a přesunu příchozího a odchozího provozu.                                             | Pro jednodušší řešení, kde neexistují více řešení, která přistupují ke stejnému účtu Gen1, a proto je lze v rámci snadno kontrolovaného umístění přesouvat dohromady.                                                  |
| Přírůstkové kopírování a kopírování | Pokud je zdrojový systém stále aktivní, omezte prostoje při provádění kopírování na pozadí. | Při přesunu příchozího a odchozího přenosu dat se vyžaduje výpadek.                   | Objem dat, která se mají zkopírovat, je velký a výpadky spojené s nástrojem Life-and-Shift nejsou přijatelné. Před přechodem se může vyžadovat testování s významnými provozními daty v cílovém systému. |
| Paralelní přijetí              | Minimální výpadky umožňují, aby aplikace migrovali na vlastní uvážení.           | Nejvíce složitější, protože mezi těmito dvěma systémy je potřeba oboustranná synchronizace. | V případě složitých scénářů, kdy aplikace založené na Data Lake Storage Gen1 nemohou být přímou migraci všechny najednou a musí být přesunuty postupně.                                                      |

Níže najdete další podrobnosti o krocích, které se týkají jednotlivých strategií. Seznam kroků, co byste měli dělat se součástmi zapojenými do upgradu. To zahrnuje celkový zdrojový systém, Celkový cílový systém, zdroje příchozích dat pro zdrojový systém, cíle odchozího přenosu dat pro zdrojový systém a úlohy běžící na zdrojovém systému.

Tyto kroky nemusíte mít v tomto případě Doporučené. Jsou určeny k nastavení architektury o tom, jak uvažujete o každé strategii. Pro každou strategii zadáte případové studie, které jsou v implementaci.

#### <a name="lift-and-shift"></a>Přezvednutí a posunutí

1. Pozastavit zdrojový systém – příchozí zdroje dat, úlohy a cíle pro odchozí přenosy
2. Zkopírujte všechna data ze zdrojového systému do cílového systému.
3. Nasměrujte všechny zdroje příchozích dat do cílového systému. Najeďte na výstupní cíl z cílového systému.
4. Přesuňte, upravte a spusťte všechny úlohy do cílového systému.
5. Vypněte zdrojový systém.

#### <a name="copy-once-and-copy-incremental"></a>Kopírování jednou a kopírování – přírůstková

1. Zkopírujte data ze zdrojového systému do cílového systému.
2. Zkopírujte přes přírůstková data ze zdrojového systému do cílového systému v pravidelných intervalech.
3. Najeďte na výstupní cíl z cílového systému.
4. Přesuňte, upravte a spusťte všechny úlohy v cílovém systému.
5. Nasměrujte příchozí zdroje postupně do cílového systému podle vašich pohodlí.
6. Až všechny zdroje příchozích dat odkazují na cílový systém.
    1. Vypněte přírůstkové kopírování.
    2. Vypněte zdrojový systém.

#### <a name="parallel-adoption"></a>Paralelní přijetí

1. Nastavte cílový systém.
2. Nastavte obousměrnou replikaci mezi zdrojovým systémem a cílovým systémem.
3. Směrovat příchozí zdroje dat postupně do cílového systému.
4. Přesunutí, změna a spuštění úloh přírůstkově do cílového systému.
5. Odměřte se na odchozí cíle z cílového systému.
6. Až budou všechny původní zdroje příchozích dat, úlohy a cíle odchozího přenosu práce s cílovým systémem, vypněte zdrojový systém.

### <a name="data-upgrade"></a>Upgrade dat

V rámci celkové strategie, kterou použijete k provedení upgradu (popsané v části [strategie upgradu](#upgrade-strategy) této příručky), se určí nástroje, které můžete použít pro upgrade dat. Níže uvedené nástroje jsou založené na aktuálních informacích a návrhy. 

#### <a name="tools-guidance"></a>Doprovodné materiály k nástrojům

| Strategie                       | Nástroje                                                                                                             | IT                                                                                                                             | Odůvodněn                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Přezvednutí a posunutí**                 | [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | Spravovaná cloudová služba                                                                                                                | Data i seznamy ACL lze v současné době kopírovat.                                                                                                                                                                                                                                                                      |
|                                    | [Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | Dobře známá oprávnění nástroje poskytnuté systémem Hadoop, tj. seznamy ACL lze kopírovat pomocí tohoto nástroje                                                   | Vyžaduje cluster, který se může současně připojit Data Lake Storage Gen1 i Gen2.                                                                                                                                                                                   |
| **Přírůstkové kopírování a kopírování** | Azure Data Factory                                                                                                    | Spravovaná cloudová služba                                                                                                                | Data i seznamy ACL lze v současné době kopírovat. Aby bylo možné podporovat přírůstkové kopírování v ADF, je třeba uspořádat data v časové řadě. Nejkratší interval mezi přírůstkovým kopírováním je [15 minut](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). |
| **Paralelní přijetí**              | [WANdisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Podpora konzistentní replikace při použití čistě prostředí Hadoop připojeného k Azure Data Lake Storage podporuje obousměrnou replikaci | Pokud nepoužíváte prostředí čistě Hadoop, může být replikace prodleva.                                                                                                                                                                                                                                                  |

Všimněte si, že existují třetí strany, které mohou Data Lake Storage Gen1, aby Data Lake Storage Gen2 upgrade bez použití výše uvedených nástrojů pro kopírování dat/meta-dat (například: [Cloudera](https://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)). Poskytují prostředí pro jedno zastavení, které provádí migraci dat i migraci úloh. Pro všechny nástroje, které jsou mimo svůj ekosystém, možná budete muset provést upgrade mimo IP síť.

#### <a name="considerations"></a>Odůvodněn

* Nejdřív musíte ručně vytvořit Data Lake Storage Gen2 účet, než začnete jakoukoli část upgradu, protože aktuální pokyny nezahrnují žádný automatický proces Gen2 účtu na základě informací o účtu Gen1. Ujistěte se, že porovnáváte procesy vytváření účtů pro [Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) a [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account).

* Data Lake Storage Gen2 podporuje velikost jenom souborů o velikosti až 5 TB. Pokud chcete upgradovat na Data Lake Storage Gen2, možná budete muset změnit velikost souborů v Data Lake Storage Gen1 tak, aby byly menší než 5 TB.

* Pokud použijete nástroj, který nekopíruje seznamy řízení přístupu (ACL), nebo pokud nechcete kopírovat přes seznamy řízení přístupu (ACL), budete muset nastavit seznamy ACL na cílovém umístění ručně na příslušné nejvyšší úrovni. Můžete to udělat pomocí Průzkumník služby Storage. Zajistěte, aby tyto seznamy ACL byly výchozími seznamy ACL, aby bylo možné soubory a složky, které zkopírujete, dědit.

* V Data Lake Storage Gen1 je nejvyšší úroveň, kterou můžete nastavit, v kořenu účtu. V Data Lake Storage Gen2 ale nejvyšší úroveň, kterou můžete nastavit, je v kořenové složce kontejneru, ne u celého účtu. Pokud tedy chcete nastavit výchozí seznamy řízení přístupu (ACL) na úrovni účtu, musíte je duplikovat ve všech systémech souborů v Data Lake Storage Gen2m účtu.

* Mezi dvěma systémy úložiště se liší omezení pojmenovávání souborů. Tyto rozdíly se týkají zejména kopírování z Data Lake Storage Gen2 do Data Lake Storage Gen1, protože druhá má přísnější omezení.

### <a name="application-upgrade"></a>Upgrade aplikace

Pokud potřebujete sestavovat aplikace na Data Lake Storage Gen1 nebo Data Lake Storage Gen2, musíte nejprve vybrat vhodné programovací rozhraní. Při volání rozhraní API v tomto rozhraní budete muset zadat odpovídající identifikátor URI a příslušné přihlašovací údaje. Tyto tři prvky, rozhraní API, identifikátor URI a způsob, jakým jsou poskytnuty přihlašovací údaje, se liší od Data Lake Storage Gen1 a Data Lake Storage Gen2.So jako součást upgradu aplikace, budete muset tyto tři konstrukce vhodně namapovat.

#### <a name="uri-changes"></a>Změny identifikátoru URI

Hlavním úkolem je překládat identifikátor URI, který má předponu `adl://` do identifikátoru URI, který má předponu `abfss://`.

Schéma identifikátoru URI pro Data Lake Storage Gen1 je [uvedeno podrobněji](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store) , ale široce řečeno, je *ADL://mydatalakestore.azuredatalakestore.NET/\<file_path @ no__t-3.*

Schéma identifikátoru URI pro přístup k souborům Data Lake Storage Gen2 je [podrobně vysvětleno](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) , ale široce řečeno, je `abfss://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`.

Budete muset projít stávajícími aplikacemi a zajistit, aby se identifikátory URI vhodně změnily tak, aby odkazovaly na Data Lake Storage Gen2. Také budete muset přidat příslušné přihlašovací údaje. Nakonec, jak vyřadíte původní aplikace a nahradíte ji novou aplikací, bude nutné přesně zarovnávat celkovou strategii upgradu.

#### <a name="custom-applications"></a>Vlastní aplikace

V závislosti na rozhraní, které vaše aplikace Data Lake Storage Gen1 používá, je potřeba ji upravit, aby se přizpůsobila Data Lake Storage Gen2.

##### <a name="rest-apis"></a>Rozhraní REST API

Pokud vaše aplikace používá Data Lake Storage rozhraní REST API, budete muset aplikaci upravit tak, aby používala rozhraní Data Lake Storage Gen2 REST API. Odkazy jsou k dispozici v části *programovací rozhraní* .

##### <a name="sdks"></a>Sady SDK

Jak bylo vyvoláno v oddílu *posouzení připravenosti na upgrade, sady* SDK nejsou aktuálně k dispozici. Pokud chcete, aby se pro vaše aplikace Data Lake Storage Gen2 port, doporučujeme počkat, až budou dostupné podporované sady SDK.

##### <a name="powershell"></a>PowerShell

Jak bylo vyvoláno v oddílu posouzení připravenosti na upgrade, podpora prostředí PowerShell není aktuálně k dispozici pro rovinu dat.

Rutin roviny správy můžete nahradit odpovídajícími těmi v Data Lake Storage Gen2. Odkazy jsou k dispozici v části *programovací rozhraní* .

##### <a name="cli"></a>CLI

Jak bylo vyvoláno v části *posouzení připravenosti na upgrade* , podpora rozhraní příkazového řádku v současnosti není pro rovinu dat k dispozici.

Příkazy roviny správy můžete nahradit odpovídajícími těmi v Data Lake Storage Gen2. Odkazy jsou k dispozici v části *programovací rozhraní* .

### <a name="analytics-frameworks-upgrade"></a>Upgrade analytických architektur

Pokud vaše aplikace vytvoří meta data o informacích v úložišti, jako jsou například explicitní cesty k souborům a složkám, budete muset provést další akce po upgradu dat nebo meta-dat úložiště. To platí hlavně pro analytické architektury, jako je Azure HDInsight, datacihly atd., což obvykle vytváří Katalogová data v datech ze Storu.

Analytické architektury pracují s daty a metadaty uloženými ve vzdálených úložištích, jako jsou Data Lake Storage Gen1 a Gen2. Teoreticky proto mohou být moduly dočasné a jsou zavedeny pouze v případě, že úlohy musí běžet na uložená data.

Pro optimalizaci výkonu ale analytické architektury můžou vytvořit explicitní odkazy na soubory a složky uložené ve vzdáleném úložišti a pak vytvořit mezipaměť pro uložení. Má-li být identifikátor URI změny vzdálených dat například cluster, který ukládá data v Data Lake Storage Gen1 dříve a nyní chcete uložit v Data Lake Storage Gen2, identifikátor URI pro stejný zkopírovaný obsah bude jiný. Takže po upgradu dat a meta dat je také nutné aktualizovat nebo znovu inicializovat mezipaměť pro tyto moduly.

V rámci procesu plánování budete muset svou aplikaci identifikovat a zjistit, jak se dají znovu inicializovat informace o metadatech, aby odkazovaly na data, která jsou teď uložená v Data Lake Storage Gen2. Níže jsou uvedeny pokyny pro běžně přijímané analytické architektury, které vám pomůžou s kroky upgradu.

#### <a name="azure-databricks"></a>Azure Databricks

Postup se liší v závislosti na strategii upgradu, kterou zvolíte. Aktuální oddíl předpokládá, že jste zvolili strategii "životní a Shift". Očekává se taky, že stávající pracovní prostor datacihly, který se používá pro přístup k datům v účtu Data Lake Storage Gen1, bude pracovat s daty, která se zkopírují do Data Lake Storage Gen2 účtu.

Nejdřív se ujistěte, že jste vytvořili účet Gen2 a pak jste ho zkopírovali přes data a meta z Gen1 do Gen2 pomocí vhodného nástroje. Tyto nástroje jsou vyvolány v části [upgrade dat](#data-upgrade) tohoto průvodce.

Pak [upgradujte](https://docs.azuredatabricks.net/user-guide/clusters/index.html) existující cluster datacihly, abyste mohli začít používat datacihly runtime 5,1 nebo vyšší, které by měly podporovat Data Lake Storage Gen2.

Postup následně vychází z toho, jak existující pracovní prostor datacihly přistupuje k datům v Data Lake Storage Gen1m účtu. To lze provést buď voláním adl://identifikátorů URI [přímo](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) z poznámkových bloků, nebo prostřednictvím [mountpoints](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs).

Pokud k přímému přístupu z poznámkových bloků přistupujete pomocí úplných adl://identifikátorů URI, budete muset projít každý Poznámkový blok a změnit konfiguraci pro přístup k odpovídajícímu identifikátoru URI Data Lake Storage Gen2.

Až budete pokračovat, budete ho muset znovu nakonfigurovat tak, aby odkazoval na Data Lake Storage Gen2 účet. Žádné další změny nejsou potřeba a poznámkové bloky by měly být schopné pracovat jako dřív.

Pokud používáte některou z dalších strategií upgradu, můžete podle svých požadavků vytvořit variaci výše uvedených kroků.

### <a name="azure-ecosystem-upgrade"></a>Upgrade ekosystému Azure

Všechny nástroje a služby, které jsou vyvolány v části [ekosystém Azure](#azure-ecosystem) tohoto průvodce, bude nutné nakonfigurovat tak, aby fungovaly s Data Lake Storage Gen2.

Nejprve zajistěte, aby byla k dispozici integrace s Data Lake Storage Gen2.

Pak prvky, které jsou uvedené výše (například URI a přihlašovací údaje), bude nutné změnit. Můžete upravit existující instanci, která funguje s Data Lake Storage Gen1, nebo můžete vytvořit novou instanci, která bude fungovat s Data Lake Storage Gen2.

### <a name="partner-ecosystem-upgrade"></a>Upgrade partnerského ekosystému

Spolupracujte s partnerem, který poskytuje komponentu a nástroje, aby bylo zajištěno, že mohou pracovat s Data Lake Storage Gen2. 

## <a name="performing-the-upgrade"></a>Provádění upgradu

### <a name="pre-upgrade"></a>Před upgradem

V takovém případě jste prošli částkou *posouzení připravenosti na upgrade* a plánováním části s [upgradem](#planning-for-an-upgrade) této příručky, obdrželi jste všechny potřebné informace a vytvořili jste plán, který vyhovuje vašim potřebám. V průběhu této fáze pravděpodobně budete mít úkol testování.

### <a name="in-upgrade"></a>Upgrade

V závislosti na zvolené strategii a složitosti řešení by tato fáze mohla být krátká nebo rozšířená, pokud existuje více úloh, které čekají na přírůstkové přesuny do Data Lake Storage Gen2. To bude nejdůležitější součástí vašeho upgradu.

### <a name="post-upgrade"></a>Po upgradu

Po dokončení operace přechodu budou kompletní kroky zahrnovat důkladné ověření. To by zahrnovalo, ale nemělo by být omezené na ověření, že se data zkopírovala spolehlivě. ověření seznamů ACL se správně nastavilo a ověření koncových kanálů funguje správně atd. Po dokončení ověření teď můžete vypnout staré kanály, odstranit zdrojové Data Lake Storage Gen1 účty a využít veškerá urychlení vašich řešení založených na Data Lake Storage Gen2.

## <a name="conclusion"></a>Závěr

Pokyny uvedené v tomto dokumentu by vám měly pomoci při upgradu vašeho řešení na použití Data Lake Storage Gen2. 

Pokud máte další dotazy nebo máte zpětnou vazbu, poskytněte komentáře níže nebo poskytněte zpětnou vazbu na [fóru o názoru Azure](https://feedback.azure.com/forums/327234-data-lake).
