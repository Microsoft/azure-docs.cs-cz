---
title: Upgradovat řešení analýzy velkých objemů dat z Azure Data Lake Storage Gen1 na verzi Preview služby Azure Data Lake Storage Gen2
description: Upgradovat řešení pomocí Azure Data Lake Storage Gen2 ve verzi Preview
services: storage
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/19/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: dd8f33cd64728c1a66e5de05734b05ac35a9836b
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54200169"
---
# <a name="upgrade-your-big-data-analytics-solutions-from-azure-data-lake-storage-gen1-to-azure-data-lake-storage-gen2-preview"></a>Upgradovat řešení analýzy velkých objemů dat z Azure Data Lake Storage Gen1 na verzi Preview služby Azure Data Lake Storage Gen2

Pokud používáte Azure Data Lake Storage Gen1 ve vašich řešeních pro analýzy velkých objemů dat, tento průvodce vám pomůže při upgradu těchto řešení pomocí Azure Data Lake Storage Gen2 ve verzi Preview. Tento dokument slouží k vyhodnocení závislosti, které má vaše řešení v Data Lake Storage Gen1. Tento průvodce také se dozvíte, jak naplánovat a provést upgrade.

Pomůžeme vám prostřednictvím následujících úloh:

:heavy_check_mark: Vyhodnoťte svoji připravenost upgradu

:heavy_check_mark: Plánování upgradu

:heavy_check_mark: Proveďte upgrade

## <a name="assess-your-upgrade-readiness"></a>Vyhodnoťte svoji připravenost upgradu

Naším cílem je, že všechny možnosti, které se nacházejí v Data Lake Storage Gen1 bude k dispozici v Data Lake Storage Gen2. Jak se tyto možnosti zveřejňují třeba v sadě SDK, rozhraní příkazového řádku atd., se mohou lišit mezi Data Lake Storage Gen1 a Gen2 úložiště Data Lake. Aplikace a služby, které využívají službu Data Lake Storage Gen1 musí být schopen fungují podobně jako s Data Lake Storage Gen2. A konečně některé funkce nebudou k dispozici v Data Lake Storage Gen2 okamžitě. Jakmile budou k dispozici, zveřejníme je v tomto dokumentu.

Tyto další části vám pomohou při rozhodování, jak nejlépe upgradovat na Gen2 úložiště Data Lake a může být nejvhodnější sada Uděláte to tak.

### <a name="data-lake-storage-gen1-solution-components"></a>Součásti řešení data Lake Storage Gen1

Pravděpodobně při použití Data Lake Storage Gen1 v řešení pro analýzu nebo kanály, existuje mnoho dalších technologiích, které využívají k dosažení celkové začátku do konce funkce. To [článku](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios) popisuje různé součásti toku dat, včetně ingestování, zpracování a spotřebovává data.

Navíc existují komponenty vyskytující k zřizování, správě a monitorování těchto součástí. Všechny komponenty pracovat s Data Lake Storage Gen1 pomocí rozhraní pro ně nejvhodnější. Pokud plánujete upgrade vašeho řešení pro Data Lake Storage Gen2, budete muset mít na paměti, které se používají rozhraní. Budete muset upgradovat rozhraní pro správu i rozhraní dat od každého rozhraní má odlišné požadavky.

![Součásti řešení data Lake Storage](./media/data-lake-storage-upgrade/solution-components.png)

**Obrázek 1** výše znázorňuje funkce součásti, že se zobrazí ve většině analytická řešení.

**Obrázek 2** ukazuje příklad, jak tyto součásti bude možné implementovat pomocí konkrétní technologie.

Funkce ukládání v **obrázek 1** poskytuje Data Lake Storage Gen1 (**obrázek 2**). Všimněte si, jak různé součásti v toku dat interakci s Data Lake Storage Gen1 s využitím Java SDK nebo rozhraní REST API. Všimněte si také, jak komponenty funkce společné interagují s Data Lake Storage Gen1. Součástí zřizování používá šablony Azure Resource, zatímco využívá provozních dat, který přichází z Data Lake Storage Gen1 monitorovací komponenta, která používá Log Analytics.

Upgradovat řešení pomocí Data Lake Storage Gen1 na Gen2 úložiště Data Lake, bude nutné zkopírovat data a metadata, opětovné připojení toků dat, a potom všechny součásti bude muset být moct pracovat s Data Lake Storage Gen2.

Následující části poskytují informace, které vám pomůže zajistit lepší rozhodnutí:

:heavy_check_mark: Funkce platformy

:heavy_check_mark: Rozhraní pro programování

:heavy_check_mark: Ekosystému Azure

:heavy_check_mark: Ekosystém partnerů

:heavy_check_mark: Provozní informace

V každé části budete schopní určit, "musí mezi" pro upgrade. Jakmile budete mít jistotu, že jsou k dispozici možnosti, nebo budete mít jistotu, že jsou splněné rozumné řešení, pokračujte [plánování upgradu](#planning-for-an-upgrade) části této příručky.

### <a name="platform-capabilities"></a>Funkce platformy

Tato část popisuje, jaké schopnosti platformy Gen1 úložiště Data Lake, které jsou aktuálně dostupné v Data Lake Storage Gen2.

| | Data Lake Storage Gen1 | Data Lake Storage Gen2 – cíl | Data Lake Storage Gen2 – stav dostupnosti  |
|-----------------------|-----------------|----------------------|----------------------------------|
| Data organizace| Podporuje data uložená jako souborů a složek | Podporuje daty uloženými jako objekty a objekty BLOB a složek a souborů – [odkaz](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | Podporuje data uložená jako složky a souboru – *nyní k dispozici*, podporuje daty uloženými jako objekty/objekty BLOB – *zatím není k dispozici* |
| Obor názvů| Hierarchické | Hierarchické |  *Teď k dispozici*  |
| Rozhraní API  | Rozhraní REST API přes protokol HTTPS | Rozhraní REST API přes HTTP/HTTPS| *Teď k dispozici* |
| Rozhraní API na straně serveru| [Kompatibilních s WebHDFS REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx) | Rozhraní REST API služby Azure Blob [REST API služby Data Lake Storage Gen2](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | Data Lake Storage Gen2 REST API – *nyní k dispozici*, Blob Service REST API služby Azure – *zatím není k dispozici*       |
| Klienta systému souborů Hadoop | Ano ([Azure Data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | Ano ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *Teď k dispozici*  |  | [Sdílený klíč](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key), [identit Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-authentication-scenarios), [sdílené přístupové podpisy (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) | *Teď k dispozici*  |
| Operace s daty – autorizace  | Soubory a složky na úrovni POSIX seznamů řízení přístupu (ACL) na základě Azure Active Directory identit  | Soubory a složky na úrovni POSIX seznamů řízení přístupu (ACL) na základě Azure Active Directory identit [klíč sdílení](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) pro úroveň autorizace účtu řízení přístupu na základě Role ([RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)) pro kontejnery přístupu | *Teď k dispozici* |
| Operace s daty – protokoly  | Ano | Jednorázové žádosti o protokoly pro konkrétní dobu pomocí lístku podpory Azure Monitoring integrace | Jednorázové lístek – podpory žádosti o protokoly pro konkrétní dobu používání *nyní k dispozici*, integrace Azure Monitoring – *zatím není k dispozici* |
| Šifrování neaktivních uložených dat | Transparentní, na straně serveru s použitím klíčů spravovaných služeb a pomocí klíčů spravovaných zákazníkem v Azure Key Vaultu | Transparentní, na straně serveru s použitím klíčů spravovaných služeb a pomocí klíčů zákazníka spravované klíče ve službě Azure Key Vaultu | Klíče spravované zákazníkem služby – *nyní k dispozici*, klíče spravované zákazníkem – *nyní k dispozici*  |
| Podpora služby Virtual Network (VNet)  | [Pomocí integrace služby Virtual Network (Preview)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Pomocí koncového bodu služby pro Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *Teď k dispozici* | Operace správy (například účet vytvořit) | [Řízení přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) poskytovaný platformou Azure pro správu účtu | [Řízení přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) poskytovaný platformou Azure pro správu účtu | *Teď k dispozici*| Sady SDK pro vývojáře | .NET, Java, Python, Node.js  | .NET, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS| *Zatím není k dispozici* | Optimalizaci výkonu pro paralelní analýzy úloh. Vysoká propustnost a vstupně-výstupních operací. | Optimalizaci výkonu pro paralelní analýzy úloh. Vysoká propustnost a vstupně-výstupních operací. | *Teď k dispozici* |
| Omezení velikosti | Žádná omezení velikosti účtů, velikosti souborů nebo počet souborů | Žádná omezení velikosti účtu nebo počet souborů. Soubor velikosti omezen na 5TB. | *Teď k dispozici*|
| Geografická redundance| Místně redundantní (LRS) | Místně redundantní (LRS) Zónově redundantní (ZRS) globálně redundantní (GRS) Read-access globálně redundantní (RA-GRS) najdete v tématu [tady](https://docs.microsoft.com/azure/storage/common/storage-redundancy) Další informace| *Teď k dispozici* |
| Regionální dostupnost | Zobrazit [zde](https://azure.microsoft.com/regions/) | Všechny [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *Teď k dispozici*                                                                                                                           |
| Cena                                       | Zobrazit [ceny](https://azure.microsoft.com/pricing/details/data-lake-store/)                                                                            | Zobrazit [ceny](https://azure.microsoft.com/pricing/details/storage/data-lake/)                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| SLA o dostupnosti                            | [Zobrazit smlouvu SLA](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [Zobrazit smlouvu SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *Zatím není k dispozici*                                                                                                                           |
| Správa dat                             | Vypršení platnosti souboru                                                                                                                                        | Zásady životního cyklu                                                                                                                                                                                                                                                                                                                                                                                                          | *Zatím není k dispozici*                                                                                                                       |

### <a name="programming-interfaces"></a>Rozhraní pro programování

Tato tabulka popisuje, které sady rozhraní API, které jsou dostupné pro vaše vlastní aplikace. Aby to bylo trochu jasnější, jsme jste oddělených tyto sady rozhraní API do 2 typy: rozhraní API pro správu a systému souborů rozhraní API.

API Management pomoct při správě účtů, zatímco systému souborů rozhraní API můžete použít pro soubory a složky.

|  Sada rozhraní API                           |  Data Lake Storage Gen1                                                                                                                                                                                                                                                                                                   | Dostupnost pro Data Lake Storage Gen2 - pomocí sdíleného klíče ověřování | Dostupnost pro Data Lake Storage Gen2 - pomocí ověřování OAuth                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sady .NET SDK - management                  | [Odkaz](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet)                                                                                                                                                                                                                 | *Nepodporuje se*                                                      | *Nyní je k dispozici –* [odkaz](https://docs.microsoft.com/rest/api/storageservices/operations-on-the-account--blob-service-)                                    |
| Sady .NET SDK – systému souborů                  | [Odkaz](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/client?view=azure-dotnet)                                                                                                                                                                                                                     | *Zatím není k dispozici*                                                | *Zatím není k dispozici*                                                                                                                                             |
| Java SDK – Správa                  | [Odkaz](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *Nepodporuje se*                                                      | *Nyní je k dispozici –* [odkaz](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob?view=azure-java-stable)                                     |
| Java SDK – systému souborů                  | [Odkaz](https://docs.microsoft.com/java/api/overview/azure/datalakestore/client)                                                                                                                                                                                                                                         | *Zatím není k dispozici*                                                | *Zatím není k dispozici*                                                                                                                                             |
| Node.js – Správa                   | [Odkaz](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | Nepodporuje se                                                      | *Nyní je k dispozici –* [odkaz](http://azure.github.io/azure-storage-node/)                                                                                            |
| Node.js – systému souborů                   | [Odkaz](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | *Zatím není k dispozici*                                                | *Zatím není k dispozici*                                                                                                                                             |
| Python – Správa                    | [Odkaz](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                 | *Nepodporuje se*                                                      | *Nyní je k dispozici –* [odkaz](https://docs.microsoft.com/python/api/overview/azure/storage/management?view=azure-python)                                       |
| Python – systému souborů                    | [Odkaz](http://azure-datalake-store.readthedocs.io/en/latest/)                                                                                                                                                                                                                                                                 | *Zatím není k dispozici*                                                | *Zatím není k dispozici*                                                                                                                                             |
| Rozhraní REST API – Správa                  | [Odkaz](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *Nepodporuje se*                                                      | *– Nyní k dispozici*                                                                                                                                               |
| Rozhraní REST API – systému souborů                  | [Odkaz](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis)                                                                                                                                                                                                                                       | *Teď k dispozici*                                                    | *Nyní je k dispozici –* [odkaz](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)                                                      |
| PowerShell – Správa a systému souborů | [Odkaz](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | Správa – nepodporuje systém souborů - *zatím není k dispozici*        | Správa – *– nyní k dispozici* [odkaz](https://docs.microsoft.com/powershell/module/az.storage) systém souborů - *zatím není k dispozici* |
| Rozhraní příkazového řádku – Správa                       | [Odkaz](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *Nepodporuje se*                                                      | *Nyní je k dispozici –* [odkaz](https://docs.microsoft.com/cli/azure/storage?view=azure-cli-latest)                                                              |
| Rozhraní příkazového řádku – systému souborů                       | [Odkaz](https://docs.microsoft.com/cli/azure/dls/fs?view=azure-cli-latest)                                                                                                                                                                                                                                               | *Zatím není k dispozici*                                                | *Zatím není k dispozici*                                                                                                                                             |
| Šablony Azure Resource Manageru – Správa             | [Template1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Template2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Template3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *Nepodporuje se*                                                      | *Nyní je k dispozici –* [odkaz](https://docs.microsoft.com/azure/templates/microsoft.storage/2018-07-01/storageaccounts)                                         |

### <a name="azure-ecosystem"></a>Ekosystému Azure

Při použití Data Lake Storage Gen1, můžete použít širokou škálu službami a produkty Microsoftu v kanály začátku do konce. Tyto služby a produkty práce s Data Lake Storage Gen1 přímo nebo nepřímo. Tato tabulka zobrazuje seznam služeb jsme upravili pro práci s Data Lake Storage Gen1 a ukazuje, které jsou momentálně kompatibilní s Data Lake Storage Gen2.

| **Oblast**             | **Dostupnost pro Data Lake Storage Gen1**                                                                                                                                    | **Dostupnost pro Data Lake Storage Gen2 – pomocí sdíleného klíče ověřování**                                                                                                           | **Dostupnost pro Data Lake Storage Gen2 – s OAuth**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Analytics framework  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *Teď k dispozici*                                                                                                                                                              | *Teď k dispozici*                                                                                                                                 |
|                      | [HDInsight ](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [HDInsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3.6 – *nyní k dispozici* HDInsight 4.0 – *zatím není k dispozici*      | ESP HDInsight 3.6 – *zatím není k dispozici* HDInsight ESP 4.0 – *zatím není k dispozici*                                                                 |
|                      | Modul Databricks Runtime verze 3.1 a vyšší                                                                                                                                               | [Modul Databricks Runtime 5.1 a novější](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) *– nyní k dispozici* | [Modul Databricks Runtime 5.1 a novější](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) – *nyní k dispozici.*                                                                                              |
|                      | [SQL Data Warehouse ](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *Nepodporuje se*                                                                                                                                                              | *Nyní je k dispozici* [odkaz](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) |
| Integrace dat     | [Data Factory ](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [Verze 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *nyní k dispozici* verze 1 – *není podporován*                               | [Verze 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *nyní k dispozici* verze 1 – *není podporován*  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *Nepodporuje se*                                                                                                                                                              | *Nepodporuje se*                                                                                                                                 |
|                      | [SQL Server Integration Services ](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *Zatím není k dispozici*                                                                                                                                                          | *Zatím není k dispozici*                                                                                                                             |
|                      | [Data Catalog](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *Zatím není k dispozici*                                                                                                                                                          | *Zatím není k dispozici*                                                                                                                             |
|                      | [Logic Apps ](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | *Zatím není k dispozici*                                                                                                                                                          | *Zatím není k dispozici*                                                                                                                             |
| IoT                  | [Event Hubs – zachytávání ](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | *Zatím není k dispozici*                                                                                                                                                          | *Zatím není k dispozici*                                                                                                                             |
|                      | [Stream Analytics ](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | *Zatím není k dispozici*                                                                                                                                                          | *Zatím není k dispozici*                                                                                                                             |
| Využití          | [Power BI Desktop  ](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | *Zatím není k dispozici*                                                                                                                                                          | *Zatím není k dispozici*                                                                                                                             |
|                      | [Excel ](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | *Zatím není k dispozici*                                                                                                                                                          | *Zatím není k dispozici*                                                                                                                             |
|                      | [Analysis Services ](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | *Zatím není k dispozici*                                                                                                                                                          | *Zatím není k dispozici*                                                                                                                             |
| Produktivita         | [Azure Portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *Nepodporuje se*                                                                                                                                                              | Správa účtů *– nyní k dispozici* operace s daty *–**zatím není k dispozici*                                                                   |
|                      | [Data Lake Tools pro Visual Studio ](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | *Zatím není k dispozici*                                                                                                                                                          | *Zatím není k dispozici*                                                                                                                             |
|                      | [Azure Storage Explorer ](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | *Teď k dispozici*                                                                                                                                                              | *Teď k dispozici*                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | *Zatím není k dispozici*                                                                                                                                                          | *Zatím není k dispozici*                                                                                                                             |

### <a name="partner-ecosystem"></a>Ekosystém partnerů

Tato tabulka zobrazuje seznam služeb třetích stran a produkty, které byly upraveny pro práci s Data Lake Storage Gen1. Profil také ukazuje, které jsou momentálně kompatibilní s Data Lake Storage Gen2.

| Oblast            | Partner  | Produkt nebo službu  | Dostupnost pro Data Lake Storage Gen1                                                                                                                                               | Dostupnost pro Data Lake Storage Gen2 – pomocí sdíleného klíče ověřování                                                   | Dostupnost pro Data Lake Storage Gen2 – s Oauth                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Analytics framework | Cloudera     | CDH –                  | [Odkaz](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *Zatím není k dispozici*                                                                                                  | *Zatím není k dispozici*                                                                                                  |
|                     | Cloudera     | Altus                | [Odkaz](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *Nepodporuje se*                                                                                                                  | *Zatím není k dispozici*                                                                                                  |
|                     | HortonWorks  | HDP 3.0              | [Odkaz](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                       | *Zatím není k dispozici*                                                                                                  | *Zatím není k dispozici*                                                                                                  |
|                     | Qubole       |                      | [Odkaz](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | *Zatím není k dispozici*                                                                                                  | *Zatím není k dispozici*                                                                                                  |
| ETL                 | StreamSets   |                      | [Odkaz](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | *Zatím není k dispozici*                                                                                                  | *Zatím není k dispozici*                                                                                                  |
|                     | Informatica  |                      | [Odkaz](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | *Zatím není k dispozici*                                                                                                  | *Zatím není k dispozici*                                                                                                  |
|                     | Řešení Attunity     |                      | [Odkaz](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | *Zatím není k dispozici*                                                                                                  | *Zatím není k dispozici*                                                                                                  |
|                     | Alteryx      |                      | [Odkaz](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | *Zatím není k dispozici*                                                                                                  | *Zatím není k dispozici*                                                                                                  |
|                     | ImanisData   |                      | [Odkaz](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | *Zatím není k dispozici*                                                                                                  | *Zatím není k dispozici*                                                                                                  |
|                     | WANdisco     |                      | [Odkaz](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [Odkaz](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [Odkaz](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>Provozní informace

Data Lake Storage Gen1 nabízených oznámení konkrétní informace a data do jiných služeb, které vám umožní zprovoznění kanálů. Tato tabulka zobrazuje dostupnost odpovídající podpory v Data Lake Storage Gen2.

| Typ dat                                                                                       | Dostupnost pro Data Lake Storage Gen1                                                                 | Dostupnost pro Data Lake Storage Gen2                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Fakturační údaje - měřiče, které se pošlou commerce týmu pro fakturaci a pak k dispozici zákazníkům  | *Teď k dispozici*                                                                                             | *Teď k dispozici*                                                                                                                           |
| Protokoly aktivit                                                                                          | [Odkaz](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | Jednorázové lístek – podpory žádosti o protokoly pro konkrétní dobu používání *nyní k dispozici* integrace Azure Monitoring - *zatím není k dispozici* |
| Diagnostické protokoly                                                                                        | [Odkaz](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | Jednorázové lístek – podpory žádosti o protokoly pro konkrétní dobu používání *nyní k dispozici* integrace Azure Monitoring - *zatím není k dispozici* |
| Metriky                                                                                                | *Nepodporuje se*                                                                                               | *Nyní je k dispozici –* [odkaz](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>Plánování upgradu

V této části se předpokládá, že jste si [vyhodnoťte svoji připravenost upgradu](#assess-your-upgrade-readiness) části této příručky a že jsou splněny všechny závislosti. Pokud je funkce, které stále nejsou k dispozici v Data Lake Storage Gen2, pokračujte pouze v případě, že znáte odpovídající řešení. Následující části obsahují pokyny na jak můžete plánovat pro upgradování vašich kanálů. Provádění skutečném upgradu najdete v [provádění upgradu](#performing-the-upgrade) části této příručky.

### <a name="upgrade-strategy"></a>Strategie upgradu

Nejdůležitější součást upgradu rozhoduje, strategie. Tato rozhodnutí určí dostupné možnosti.

Tato tabulka shrnuje některé známé strategií, které se používají k migraci databází, clustery Hadoop, atd. Vytvoříme přijmout podobné strategie v naše doprovodné materiály a přizpůsobit je náš kontext.

| Strategie                   | V oblasti IT                                                                                  | Nevýhody                                                           | Kdy použít?                                                                                                                                                                                             |
|--------------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Lift and shift                 | Nejjednodušší.                                                                                 | Nevyžaduje výpadek pro kopírování dat a přesun úloh a přesun příchozího přenosu dat a výchozí přenos dat                                             | Pro jednodušší řešení Pokud se více řešení nejsou stejné Gen1 přístup k účtu a proto daly přesunout najednou rychlé kontrolovaným způsobem.                                                  |
| Kopírování a jednou a kopírování přírůstkové | Snížit prostoje vytváří kopii na pozadí, zatímco zdrojový systém je stále aktivní. | Nevyžaduje výpadek pro přesun příchozí a odchozí.                   | Je velké množství dat, které se mají zkopírovat přes a výpadky související s životnost a shift je nepřijatelné. Testování může být vyžadováno s použitím významné provozních dat v cílovém systému před přechodu. |
| Paralelní přijetí              | Nejméně čas umožňuje výpadků aplikací a jejich migraci podle jejich vlastního uvážení.           | Nejsložitější od 2 způsob synchronizace je potřeba mezi těmito dvěma systémy. | Pro komplexní scénáře, kde aplikace založené na Data Lake Storage Gen1 nemůže být současně přímou migraci a musí být přesunut nad v přírůstkově.                                                      |

V následující tabulce jsou další podrobnosti o krocích potřebný k jednotlivým strategiím věnujeme. Kroky seznam, co byste udělali s součásti účastnící upgrade. To zahrnuje celého zdrojového systému, celkový cílového systému, zdroje příchozího přenosu dat pro zdrojový systém, výstup cíle pro zdrojový systém a úlohy spuštěné na zdrojovém systému.

Tyto kroky nejsou určeny jako doporučený. Účelem je nastavit framework o tom, jak jsme uvažujete o jednotlivých strategií. Poskytujeme případové studie pro každé strategie, jako je prováděna vidíme.

#### <a name="lift-and-shift"></a>Lift and shift

1. Pozastavte zdrojového systému – příchozí přenos dat zdrojů, úloh, výstup cíle.

2. Kopírování všech dat ze zdrojového systému do cílového systému.

3. Všechny zdroje příchozího přenosu dat, přejděte na cílovém systému. Přejděte na cíl výchozí přenos dat z cílového systému.

4. Přesunout, upravovat, spouštět všechny úlohy do cílového systému.

5. Vypněte zdrojový systém.

#### <a name="copy-once-and-copy-incremental"></a>Kopie-po a přírůstkového kopírování

1. Zkopírujte data ze zdrojového systému do cílového systému.

2. Zkopírovat přírůstkových dat ze zdrojového systému do cílového systému v pravidelných intervalech.

3. Přejděte na cíl výchozí přenos dat z cílového systému.

4. Přesunout, upravovat, spouštět všechny úlohy v cílovém systému.

5. Postupně bodu zdroje příchozího přenosu dat do cílového systému podle usnadnění práce.

6. Jakmile se všechny zdroje příchozího přenosu dat odkazují na cílovém systému.

    1. Vypněte přírůstkové kopírování.

    2. Vypněte zdrojový systém.

#### <a name="parallel-adoption"></a>Paralelní přijetí

1. Nastavení cílového systému.

2. Nastavte obousměrnou replikaci mezi systémem zdrojového a cílového systému.

3. Postupně bodu zdroje příchozího přenosu dat do cílového systému.

4. Přesunout, upravovat, spouštět úlohy postupně do cílového systému.

5. Přejděte na výstup cíle přírůstkově z cílového systému.

6. Po všechny původních zdrojů příchozího přenosu dat, úlohy a výchozí přenos dat cílové pracujete s cílovém systému, vypněte zdrojový systém.

### <a name="data-upgrade"></a>Upgrade dat

Celkové strategie používaný k provedení upgradu (popsané v [upgradovat strategie](#upgrade-strategy) této příručce), určí nástroje, které můžete použít pro data upgrade. Nástroje uvedené níže jsou založené na aktuálních informací a jsou návrhy. 

#### <a name="tools-guidance"></a>Doprovodné materiály nástroje

| Strategie                       | Nástroje                                                                                                             | V oblasti IT                                                                                                                             | Požadavky                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Lift and shift**                 | [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | Spravované cloudové služby                                                                                                                | Pouze kopie nad daty. V současné době nelze kopírovat, seznamy ACL.                                                                                                                                                                                                                                                                      |
|                                    | [Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | Známé nástroje poskytované Hadoop oprávnění například seznamy ACL je možné zkopírovat pomocí tohoto nástroje                                                   | Vyžaduje cluster, který se může připojit k Data Lake Storage Gen1 a Gen2 ve stejnou dobu.                                                                                                                                                                                   |
| **Kopírování a jednou a kopírování přírůstkové** | Azure Data Factory                                                                                                    | Spravované cloudové služby                                                                                                                | Pro podporu přírůstkové kopírování ve službě ADF, data musí být uspořádány v podobě časových řad. Nejkratší interval mezi přírůstkové kopie je [15 minut](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger)s. ADF kratších intervalech, nebude fungovat. V současné době nelze kopírovat, seznamy ACL. |
| **Paralelní přijetí**              | [WANdisco](http://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Replikaci konzistentní podpory, pokud čisté prostředí systému Hadoop pomocí připojení k Azure Data Lake Storage podporuje obousměrnou replikaci | Pokud nepoužíváte prostředí čistě Hadoop, může docházet k prodlevám v replikaci.                                                                                                                                                                                                                                                  |

Všimněte si, že jsou třetích stran, které dokáže zpracovat Data Lake Storage Gen1 k Data Lake Storage Gen2 upgradu bez zásahu výše uvedená data/meta-data nástroje pro kopírování (například: [Cloudera](http://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)). Jejich poskytovat "univerzálním", který provádí migraci dat, jakož i migraci pracovní zátěže. Budete muset provést upgrade out-of-band pro všechny nástroje, které jsou mimo jejich ekosystému.

#### <a name="considerations"></a>Požadavky

* Budete muset ručně, nejprve vytvořte účet Data Lake Storage Gen2 před zahájením libovolnou část upgrade, protože aktuální doporučené nezahrnuje automatický proces účet Gen2 podle informací o vašem Gen1 účtu. Ujistěte se, že porovnáte procesy vytváření účtů pro [Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) a [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account).

* Data Lake Storage Gen2 podporuje pouze soubory až 5 TB velikosti. Pokud chcete upgradovat na Gen2 úložiště Data Lake, potřebujete změnit velikost souborů v Data Lake Storage Gen1 tak, aby byly menší než velikost 5 TB.

* Pokud používáte nástroj, který nekopíruje seznamy ACL nebo nechcete zkopírovat seznamy ACL, pak budete muset nastavit seznamy ACL v cílovém ručně na odpovídající nejvyšší úrovni. Uděláte to pomocí Průzkumníka služby Storage. Ujistěte se, že tyto seznamy ACL jsou výchozí seznamy ACL, tak, aby soubory a složky, které můžete zkopírovat dědí je.

* V Data Lake Storage Gen1 nejvyšší úrovně můžete nastavit seznamy ACL je v kořenovém adresáři účtu. V Data Lake Storage Gen1 nejvyšší úrovně můžete nastavit seznamy ACL je však v kořenové složce v systému souborů, ne celý účet. Takže pokud chcete nastavit výchozí seznamy ACL na úrovni účtu, budete muset duplicitní těch všechny systémy souborů ve vašem účtu Data Lake Storage Gen2.

* Omezení pojmenování souboru se liší mezi těmito dvěma systémy úložiště. Tyto rozdíly jsou zejména při kopírování z Data Lake Storage Gen2 do Data Lake Storage Gen1 protože ten má více omezené omezení.

### <a name="application-upgrade"></a>Upgrade aplikací

Pokud potřebujete k vytváření aplikací v Data Lake Storage Gen1 a Gen2 úložiště Data Lake, budete muset nejprve zvolí vhodné programovací rozhraní. Při volání rozhraní API z rozhraní budete muset zadat odpovídající identifikátor URI a příslušné přihlašovací údaje. Reprezentace tyto tři prvky rozhraní API, identifikátor URI a jak jsou přihlašovací údaje k dispozici, jsou rozdíly mezi Data Lake Storage Gen1 a Data Lake Storage Gen2.So, jako součást upgradu aplikace, budete muset namapovat tyto tři konstrukcí odpovídajícím způsobem.

#### <a name="uri-changes"></a>Identifikátor URI změny

Hlavního úkolu je pro převod adl: / / identifikátor URI, který se používal existující úlohy do abfss: / / identifikátor URI.

Schéma identifikátoru URI pro Data Lake Storage Gen1 je uvedený [tady](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store) podrobností, ale zhruba řečeno, je *adl://mydatalakestore.azuredatalakestore.net/\<cesta_k_souboru\>.*

Schéma identifikátoru URI pro přístup k souborům Data Lake Storage Gen2 je vysvětleno [tady](https://docs.microsoft.com/azure/storage/data-lake-storage/use-hdi-cluster?branch=release-preview-abfs) podrobností, ale zhruba řečeno, je *abfss: / /\<FILE_SYSTEM_NAME\> \@ \< Název účtu\>.dfs.core.widows.net/\<cesta\>.*

Budete muset projít vašimi stávajícími aplikacemi a ujistěte se, že jste změnili identifikátory URI odpovídajícím způsobem tak, aby odkazoval na Gen2 úložiště Data Lake těch, které jsou. Kromě toho budete muset přidat příslušné přihlašovací údaje. A konečně jak vyřadit původní aplikace a nahraďte novou aplikaci muset nezarovnají úzce ke strategii zachování celkové upgradu.

#### <a name="custom-applications"></a>Vlastní aplikace

V závislosti na rozhraní, které vaše aplikace používá s Data Lake Storage Gen1 budete muset upravit tak, aby jej přizpůsobit Data Lake Storage Gen2.

##### <a name="rest-apis"></a>Rozhraní REST API

Pokud vaše aplikace používá REST API služby Data Lake Storage, bude nutné upravit svou aplikaci používat rozhraní REST API Data Lake Storage Gen2. Odkazy jsou uvedeny v *programovací rozhraní* oddílu.

##### <a name="sdks"></a>Sady SDK

Jako volané ve *vyhodnoťte svoji připravenost upgradu* části nejsou aktuálně dostupné sady SDK. Pokud chcete port u aplikací na Gen2 úložiště Data Lake, doporučujeme bude čekat podporovaných sad SDK k dispozici.

##### <a name="powershell"></a>PowerShell

Jak uvádějí se v posouzení připravenosti upgradu oddíl, není aktuálně k dispozici pro rovinu dat. podporu prostředí PowerShell.

Rutiny roviny správy můžete nahradit odpovídající značky v Data Lake Storage Gen2. Odkazy jsou uvedeny v *programovací rozhraní* oddílu.

##### <a name="cli"></a>Rozhraní příkazového řádku

Jako volané ve *vyhodnoťte svoji připravenost upgradu* části Podpora rozhraní příkazového řádku není aktuálně k dispozici pro rovinu dat.

Příkazy roviny správy můžete nahradit odpovídající značky v Data Lake Storage Gen2. Odkazy jsou uvedeny v *programovací rozhraní* oddílu.

### <a name="analytics-frameworks-upgrade"></a>Upgrade Analytics architektury

Pokud vaše aplikace vytvoří metadata o informace v úložišti, jako je například explicitní souboru a cesty ke složkám, bude nutné provést další akce po upgradu úložiště dat nebo metadat. To platí zejména rozhraní analytics jako je například Azure HDInsight atd. Databricks, které obvykle vytvoření katalogu dat pro úložiště data.

Analýza architektury pracovat s daty a meta-data uložená ve vzdálených úložištích, jako je Data Lake Storage Gen1 a Gen2. Takže teoreticky motory může být dočasné a aktivují jenom v případě, že se úlohy potřebují spouštět uložená data.

Ale za účelem optimalizace výkonu architektury analytics může vytvořit explicitní odkazy na soubory a složky uložené ve vzdáleném úložišti a vytvořte mezipaměť pro jejich uložení. Identifikátor URI vzdálených dat změnit, například cluster, který byl ukládání dat v Data Lake Storage Gen1 a teď se chce k uložení v Data Lake Storage Gen2 identifikátor URI pro stejný obsah zkopírovaný budou lišit. Ano po data a metadata aktualizace mezipaměti pro tyto moduly také muset aktualizovat ani znovu inicializovat

Jako součást procesu plánování budete potřebovat k identifikaci vaší aplikace a zjistěte, jak lze znovu inicializována tak, aby odkazoval na data, která jsou teď uložená v Data Lake Storage Gen2 informace metadat. Níže uvádíme pokyny pro běžně používaná analytics platformy, které vám pomůžou s jejich kroky upgradu.

#### <a name="azure-databricks"></a>Azure Databricks

V závislosti na upgrade strategie, kterou zvolíte se bude lišit kroky. Aktuální části se předpokládá, že jste se rozhodli strategie "Životnost and shift". Navíc se očekává existujícího pracovního prostoru Databricks, který používá pro přístup k datům v účtu Data Lake Storage Gen1 pro práci s daty, která se kopíruje do účtu Data Lake Storage Gen2.

Nejprve ověřte, že jste vytvořili účet Gen2 a pak zkopíruje data a metadata z Gen1 na Gen2 pomocí vhodného nástroje. Tyto nástroje jsou uvedeny v [upgradu dat](#data-upgrade) části této příručky.

Potom [upgradovat](https://docs.azuredatabricks.net/user-guide/clusters/index.html) existujícího clusteru Databricks chcete začít používat modul runtime Databricks 5.1 nebo novější, které by měly podporovat Gen2 úložiště Data Lake.

Kroky po tomto datu jsou založené na tom, jak existující pracovní prostor Databricks přistupuje k datům v účtu Data Lake Storage Gen1. To můžete udělat buď pomocí volání adl: / / identifikátory URI [přímo](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) z poznámkových bloků nebo prostřednictvím [přípojné body](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs).

Pokud se připojujete přímo z poznámkových bloků tím, že poskytuje úplné adl: / / identifikátory URI, musíte projít každý Poznámkový blok a měnit konfiguraci pro přístup k odpovídající URI Gen2 Data Lake Storage.

Do budoucna, musíte znovu nakonfigurovat ji tak, aby odkazoval na účet Data Lake Storage Gen2. Nejsou potřeba žádné další změny a poznámkových bloků by měl mít možnost pracovat stejně jako předtím.

Pokud používáte některou z upgradu strategií, můžete vytvořit varianta výše uvedené kroky, aby splňovaly vaše požadavky.

### <a name="azure-ecosystem-upgrade"></a>Upgrade ekosystému Azure

Všechny nástroje a služby v [ekosystému Azure](#azure-ecosystem) části tohoto průvodce muset být nakonfigurováno pro práci s Data Lake Storage Gen2.

Nejprve zkontrolujte, zda je k dispozici s Data Lake Storage Gen2 integrace.

Potom prvky vyznačeny výše (například: Identifikátor URI a přihlašovací údaje), bude nutné změnit. Můžete změnit existující instanci, která funguje s Data Lake Storage Gen1 nebo můžete vytvořit novou instanci, která bude fungovat s Data Lake Storage Gen2.

### <a name="partner-ecosystem-upgrade"></a>Upgrade ekosystému partnerů

Prosím práci s partnerem, komponenty a nástroje k zajištění, že můžete spolupracovat s Data Lake Storage Gen2. 

## <a name="performing-the-upgrade"></a>Provádění upgradu

### <a name="pre-upgrade"></a>Před upgradem

Jako součást tohoto postupu by jste prošli *vyhodnoťte svoji připravenost upgradu* oddílu a [plánování upgradu](#planning-for-an-upgrade) části této příručky, obdrželi jste všechny potřebné informace, a když jste Vytvořit plán, který by vašim potřebám. Testování úloh pravděpodobně budou mít v průběhu této fáze.

### <a name="in-upgrade"></a>V upgradu

V závislosti na strategii zvolíte a složitosti řešení této fázi může být krátký jeden nebo některý z rozšířené kdy existuje více úloh čekajících na přírůstkové přesunou na Gen2 úložiště Data Lake. Bude jím nejdůležitější část upgrade.

### <a name="post-upgrade"></a>Po upgradu

Jakmile budete hotovi s operací přechodu závěrečné kroky budou zahrnovat důkladné ověření. To by zahrnovat ale není omezena na ověřování dat byla zkopírována přes spolehlivě, ověřuje se seznamy ACL jsou nastavené správně, ověřuje e2e kanály správnou funkčnost atd. Po dokončení ověření můžete nyní vypnout staré kanály, odstranit vaše účty Data Lake Storage Gen1 zdroje a přejít na vaše řešení založená na Gen2 úložiště Data Lake plné rychlosti.

## <a name="conclusion"></a>Závěr

Pokyny v tomto dokumentu by měl pomohly při upgradu řešení používat Data Lake Storage Gen2. 

Pokud máte další otázky, nebo máte zpětnou vazbu, zadejte níže uvedené komentáře nebo poskytnout zpětnou vazbu v [fóru pro zpětnou vazbu Azure](https://feedback.azure.com/forums/327234-data-lake).
