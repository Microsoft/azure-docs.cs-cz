---
title: Azure Data Lake Storage Gen2 Úvod
description: Přečtěte si Úvod k Azure Data Lake Storage Gen2. Přečtěte si klíčové funkce. Přečtěte si podporované funkce služby Blob Storage, integrace služeb Azure a platformy.
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: d85b0cd2f9fa7eb81f5c39bd5d163188e3cd7106
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87835761"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Úvod do Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 je sada funkcí vyhrazených pro analýzy velkých objemů dat, která je založená na [službě Azure Blob Storage](storage-blobs-introduction.md). Data Lake Storage Gen2 je výsledkem sblížení schopností naší dvě existující služby úložiště, Azure Blob Storage a Azure Data Lake Storage Gen1. Funkce z [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), jako jsou sémantika systému souborů, adresář a zabezpečení na úrovni souborů a škálování, jsou kombinovány s nízkými náklady, vrstvenými úložištěm, vysokou dostupností a možnostmi zotavení po havárii z [Azure Blob Storage](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Navrženo pro podnikovou analýzu velkých objemů dat

Data Lake Storage Gen2 vytváří Azure Storage základ pro vytváření laků podnikových dat v Azure. Tato možnost je navržena od začátku až po obsluhu více petabajty informací a současně udržuje stovky gigabitových přenosů, Data Lake Storage Gen2 umožňuje snadno spravovat obrovské objemy dat.

Základní část Data Lake Storage Gen2 je přidání [hierarchického oboru názvů](data-lake-storage-namespace.md) do úložiště objektů BLOB. Hierarchický obor názvů uspořádává objekty/soubory do hierarchie adresářů pro efektivní přístup k datům. Společná konvence pojmenování objektů úložiště používá k napodobení hierarchické adresářové struktury lomítka v názvu. Tato struktura se zastará o Data Lake Storage Gen2. Operace, jako je přejmenování nebo odstranění adresáře, se stanou samostatnými operacemi atomických metadat v adresáři, nikoli vytvářením výčtu a zpracováním všech objektů, které sdílejí předponu názvu adresáře.

Data Lake Storage Gen2 se vytváří na BLOB Storage a vylepšuje výkon, správu a zabezpečení následujícími způsoby:

-   **Výkon** je optimalizován, protože není nutné kopírovat nebo transformovat data jako předpoklad pro analýzu. V porovnání s plochým oborem názvů v úložišti objektů BLOB by hierarchický obor názvů významně zlepšit výkon operací správy adresářů, což zvyšuje celkový výkon úloh.

-   **Správa** je jednodušší, protože můžete uspořádat soubory a manipulovat s nimi prostřednictvím adresářů a podadresářů.

-   **Zabezpečení** je vykonatelné, protože můžete definovat oprávnění POSIX pro adresáře nebo jednotlivé soubory.

Data Lake Storage Gen2 je taky velmi nákladově efektivní, protože je postavená na [Azure Blob Storage](storage-blobs-introduction.md)s nízkými náklady. Další funkce dále snižují celkové náklady na vlastnictví pro provozování analýz velkých objemů dat v Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Klíčové funkce Data Lake Storage Gen2

-   **Přístup**s platformou hadoop: Data Lake Storage Gen2 umožňuje správu a přístup k datům stejně, jako byste použili [systém souborů DFS (DISTRIBUTED File System) Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Nový [ovladač ABFS](data-lake-storage-abfs-driver.md) je k dispozici ve všech Apache Hadoop prostředích, včetně [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index)*,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index)a [Azure synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics) pro přístup k datům uloženým v Data Lake Storage Gen2.

-   **Nadmnožina oprávnění POSIX**: model zabezpečení pro data Lake Gen2 podporuje oprávnění ACL a POSIX spolu s některými dalšími podrobnostmi, které jsou specifické pro data Lake Storage Gen2. Nastavení lze nakonfigurovat prostřednictvím Průzkumník služby Storage nebo prostřednictvím architektury jako podregistr a Spark.

-   **Cenově výhodné**: Data Lake Storage Gen2 nabízí cenovou kapacitu úložiště a transakce. Vzhledem k tomu, že se v průběhu svého kompletního životního cyklu data mění, účtují se náklady na minimum prostřednictvím integrovaných funkcí, jako je [životní cyklus služby Azure Blob Storage](storage-lifecycle-management-concepts.md).

-   **Optimalizovaný ovladač**: ovladač ABFS je [optimalizován speciálně](data-lake-storage-abfs-driver.md) pro analýzu velkých objemů dat. Odpovídající rozhraní REST API se procházejí prostřednictvím koncového bodu `dfs.core.windows.net` .

### <a name="scalability"></a>Škálovatelnost

Azure Storage jsou škálovatelné podle návrhu bez ohledu na to, jestli máte přístup přes rozhraní Data Lake Storage Gen2 nebo BLOB Storage. Může ukládat a obsluhovat *spoustu datových Exabyte*. Tato velikost úložiště je dostupná s propustností měřenou v gigabitech za sekundu (GB/s) na vysoké úrovni vstupních/výstupních operací za sekundu (IOPS). Mimo rámec pouhého trvalosti se zpracování provádí při téměř konstantních latencích požadavků, které se měří na úrovni služby, účtu a souboru.

### <a name="cost-effectiveness"></a>Efektivita nákladů

Jednou z mnoha výhod sestavování Data Lake Storage Gen2 na Azure Blob Storage je nízké náklady na kapacitu úložiště a transakce. Na rozdíl od jiných služeb cloudového úložiště není nutné před provedením analýzy přesunout nebo transformovat data uložená v Data Lake Storage Gen2. Další informace o cenách najdete v tématu [Azure Storage ceny](https://azure.microsoft.com/pricing/details/storage).

Kromě toho funkce, jako je [hierarchický obor názvů](data-lake-storage-namespace.md) , významně zlepšují celkový výkon řady analytických úloh. Toto zlepšení výkonu znamená, že potřebujete méně výpočetní výkon pro zpracování stejného množství dat, což vede ke snížení celkových nákladů na vlastnictví pro ucelenou úlohu analýzy.

### <a name="one-service-multiple-concepts"></a>Jedna služba, více konceptů

Data Lake Storage Gen2 je další možností pro analýzy velkých objemů dat, která je postavená na službě Azure Blob Storage. I když využívá stávající součásti platformy objektů BLOB k vytváření a provozování datových laků pro analýzy spoustu výhod, může to vést k několika konceptům, které popisují stejné sdílené věci.

Níže jsou uvedené ekvivalentní entity, jak je popsáno v různých konceptech. Pokud není uvedeno jinak, jsou tyto entity přímo synonymné:

| Koncepce                                | Organizace nejvyšší úrovně | Organizace nižší úrovně                                            | Datový kontejner |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Objekty blob – úložiště objektů pro obecné účely | Kontejner              | Virtuální adresář (pouze sada SDK) – neposkytuje atomickou manipulaci | Blob           |
| Azure Data Lake Storage Gen2 – analytické úložiště          | Kontejner            | Adresář                                                           | Soubor           |

## <a name="supported-blob-storage-features"></a>Podporované funkce Blob Storage

Funkce služby Blob Storage, jako je [protokolování diagnostiky](../common/storage-analytics-logging.md), [úrovně přístupu](storage-blob-storage-tiers.md)a [BLOB Storage zásady správy životního cyklu](storage-lifecycle-management-concepts.md) , teď fungují s účty, které mají hierarchický obor názvů. Proto můžete v účtech BLOB Storage povolit hierarchické obory názvů bez ztráty přístupu k těmto funkcím. 

Seznam podporovaných funkcí služby Blob Storage najdete [v tématu BLOB Storage funkcích dostupných v Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Podporované integrace služeb Azure

Data Lake Storage Gen2 podporuje několik služeb Azure, které můžete použít k ingestování dat, provádění analýz a vytváření vizuální reprezentace. Seznam podporovaných služeb Azure najdete v tématu [služby Azure, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Podporované opensourcové platformy

Data Lake Storage Gen2 podporuje několik Open Source platforem. Úplný seznam najdete v tématu [Open Source Platforms, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Viz také

- [Známé problémy s Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Přístup k více protokolům na Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)


