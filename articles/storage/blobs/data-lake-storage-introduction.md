---
title: Azure Data Lake Storage Gen2 Úvod
description: Poskytuje přehled Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 2f920e29fafdc55478e0e2c16d683bd1c3bc81d8
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942921"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Úvod do Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 je sada funkcí vyhrazených pro analýzy velkých objemů dat, která je založená na [službě Azure Blob Storage](storage-blobs-introduction.md). Data Lake Storage Gen2 je výsledkem slučuje možnosti našich dva existující služby storage, Azure Blob storage a Azure Data Lake Storage Gen1. Funkce z [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), jako jsou sémantika systému souborů, adresář a zabezpečení na úrovni souborů a škálování, jsou kombinovány s nízkými náklady, vrstvenými úložištěm, vysokou dostupností a možnostmi zotavení po havárii z [Azure Blob Storage](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Navržená pro analýzy velkých objemů dat organizace

Úložiště Azure data Lake Storage Gen2 díky základ pro vytváření datová jezera enterprise v Azure. Účelem je od samého začátku služby získejte tak několik petabajtů informace při udržování stovky gigabity propustnosti, Data Lake Storage Gen2 vám umožní snadno spravovat velké objemy dat.

Základní část Data Lake Storage Gen2 je přidání [hierarchického oboru názvů](data-lake-storage-namespace.md) do úložiště objektů BLOB. Hierarchického oboru názvů slouží k uspořádání objektů nebo souborů do hierarchie adresářů pro přístup k datům efektivní. Běžné zásady vytváření názvů úložiště objektu používá lomítek v názvu tak, aby napodoboval strukturu hierarchického adresáře. Tato struktura bude skutečné s Data Lake Storage Gen2. Operace, jako je přejmenování nebo odstranění adresáře se stanou metadat jednotného atomické operace na adresář místo vytváření výčtů a zpracování všech objektů, které sdílejí předpona názvu adresáře.

Data Lake Storage Gen2 se vytváří na BLOB Storage a vylepšuje výkon, správu a zabezpečení následujícími způsoby:

-   **Výkon** je optimalizován, protože není nutné kopírovat nebo transformovat data jako předpoklad pro analýzu. V porovnání s plochým oborem názvů v úložišti objektů BLOB by hierarchický obor názvů významně zlepšit výkon operací správy adresářů, což zvyšuje celkový výkon úloh.

-   **Správa** je jednodušší, protože můžete uspořádat soubory a manipulovat s nimi prostřednictvím adresářů a podadresářů.

-   **Zabezpečení** je vykonatelné, protože můžete definovat oprávnění POSIX pro adresáře nebo jednotlivé soubory.

Data Lake Storage Gen2 je taky velmi nákladově efektivní, protože je postavená na [Azure Blob Storage](storage-blobs-introduction.md)s nízkými náklady. Další funkce dále snížit celkové náklady na vlastnictví pro spuštění analýzy velkých objemů dat v Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Klíčové funkce služby Data Lake Storage Gen2

-   **Přístup**s platformou hadoop: Data Lake Storage Gen2 umožňuje správu a přístup k datům stejně, jako byste použili [systém souborů DFS (DISTRIBUTED File System) Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Nový [ovladač ABFS](data-lake-storage-abfs-driver.md) je k dispozici ve všech Apache Hadoop prostředích, včetně [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index) *,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index)a [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) pro přístup k datům uloženým v Data Lake Storage Gen2.

-   **Nadmnožina oprávnění POSIX**: model zabezpečení pro data Lake Gen2 podporuje oprávnění ACL a POSIX spolu s některými dalšími podrobnostmi, které jsou specifické pro data Lake Storage Gen2. Nastavení může být nakonfigurován pomocí Průzkumníka služby Storage nebo prostřednictvím architektury, jako je Hive a Spark.

-   **Cenově výhodné**: Data Lake Storage Gen2 nabízí cenovou kapacitu úložiště a transakce. Vzhledem k tomu, že se v průběhu svého kompletního životního cyklu data mění, účtují se náklady na minimum prostřednictvím integrovaných funkcí, jako je [životní cyklus služby Azure Blob Storage](storage-lifecycle-management-concepts.md).

-   **Optimalizovaný ovladač**: ovladač ABFS je [optimalizován speciálně](data-lake-storage-abfs-driver.md) pro analýzu velkých objemů dat. Odpovídající rozhraní REST API se docházejí z `dfs.core.windows.net`koncového bodu.

### <a name="scalability"></a>Škálovatelnost

Azure Storage je škálovatelné záměrné, ať už přistupujete prostřednictvím Data Lake Storage Gen2 nebo objekt Blob úložiště rozhraní. Může ukládat a obsluhovat *spoustu datových Exabyte*. Tato velikost úložiště je k dispozici s propustnost měřenou v gigabitech za sekundu (Gbps) ve vysoké míře vstupních/výstupních operací za sekundu (IOPS). Nad rámec jenom trvalost provádí zpracování v blízkosti konstanta jednotlivých žádostí latenci, které se měří na úrovni služby, účet a soubor.

### <a name="cost-effectiveness"></a>Finanční efektivita

Jednou z mnoha výhod stavění úložiště objektů Blob v Azure Data Lake Storage Gen2 je nízké náklady sady kapacitou úložiště a transakce. Na rozdíl od jiných cloudových službách úložiště není nutné být přesunut nebo před provedením analýzy dat uložených v Data Lake Storage Gen2. Další informace o cenách najdete v tématu [Azure Storage ceny](https://azure.microsoft.com/pricing/details/storage).

Kromě toho funkce, jako je [hierarchický obor názvů](data-lake-storage-namespace.md) , významně zlepšují celkový výkon řady analytických úloh. K tomuto zlepšení výkonu znamená, že vyžadují méně výpočetní výkon pro zpracování stejné množství dat, jejichž výsledkem je snížení celkových nákladů na vlastnictví (TCO) na začátku do konce analytics úlohy.

### <a name="one-service-multiple-concepts"></a>Jedna služba více koncepty

Data Lake Storage Gen2 je další schopností pro analýzy velkých objemů dat, postavené na Azure Blob storage. I když existují mnoho výhod v existující komponenty platformy přes Bloby až po vytvoření a provoz datová jezera pro analýzy využití, vést k několika koncepty popisující stejné, sdílené kroky.

Níže jsou ekvivalentní entity, jak je popsáno v různých koncepty. Pokud není určeno jinak, tyto entity jsou přímo shodné:

| Koncept                                | Nejvyšší úrovně organizace | Nižší úrovně organizace                                            | Datový kontejner |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Objekty BLOB – storage pro obecné účely objektu | Kontejner              | Virtuální adresář (sada SDK pouze – neposkytuje atomic manipulaci s) | Objekt blob           |
| Azure Data Lake Storage Gen2 – analytické úložiště          | Kontejner            | Adresář                                                           | File           |

## <a name="supported-blob-storage-features"></a>Podporované funkce služby Blob Storage

Funkce služby Blob Storage, jako je [protokolování diagnostiky](../common/storage-analytics-logging.md), [úrovně přístupu](storage-blob-storage-tiers.md)a [BLOB Storage zásady správy životního cyklu](storage-lifecycle-management-concepts.md) , teď fungují s účty, které mají hierarchický obor názvů. Proto můžete v účtech BLOB Storage povolit hierarchické obory názvů bez ztráty přístupu k těmto funkcím. 

Seznam podporovaných funkcí služby Blob Storage najdete [v tématu BLOB Storage funkcích dostupných v Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Podporované integrace služeb Azure

Data Lake Storage Gen2 podporuje několik služeb Azure, které můžete použít k ingestování dat, provádění analýz a vytváření vizuální reprezentace. Seznam podporovaných služeb Azure najdete v tématu [služby Azure, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Podporované opensourcové platformy

Několik opensourcové platformy podporují Data Lake Storage Gen2. Úplný seznam najdete v tématu [Open Source Platforms, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Viz také

- [Známé problémy s Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Přístup k více protokolům na Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)


