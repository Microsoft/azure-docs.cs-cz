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
ms.openlocfilehash: 1c4d04e25bf8f7d981c998baafb468f04b66eaf1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879894"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Úvod do Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 je sada funkcí vyhrazených pro analýzy velkých objemů dat, která je založená na [službě Azure Blob Storage](storage-blobs-introduction.md). 

Data Lake Storage Gen2 konverguje možnosti [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml) s úložištěm objektů BLOB v Azure. Data Lake Storage Gen2 například poskytuje sémantiku systému souborů, zabezpečení na úrovni souborů a škálování. Vzhledem k tomu, že tyto funkce jsou postavené na úložišti objektů blob, získáte také nízké náklady na vrstvené úložiště s vysokou dostupností a možností zotavení po havárii.

## <a name="designed-for-enterprise-big-data-analytics"></a>Navrženo pro podnikovou analýzu velkých objemů dat

Data Lake Storage Gen2 vytváří Azure Storage základ pro vytváření laků podnikových dat v Azure. Tato možnost je navržena od začátku až po obsluhu více petabajty informací a současně udržuje stovky gigabitových přenosů, Data Lake Storage Gen2 umožňuje snadno spravovat obrovské objemy dat.

Základní část Data Lake Storage Gen2 je přidání [hierarchického oboru názvů](data-lake-storage-namespace.md) do úložiště objektů BLOB. Hierarchický obor názvů uspořádává objekty/soubory do hierarchie adresářů pro efektivní přístup k datům. Společná konvence pojmenování objektů úložiště používá k napodobení hierarchické adresářové struktury lomítka v názvu. Tato struktura se zastará o Data Lake Storage Gen2. Operace jako přejmenování nebo odstranění adresáře se stanou samostatnými atomických operací s metadaty v adresáři. Není nutné vytvářet výčet a zpracovávat všechny objekty, které sdílejí předponu názvu adresáře.

Data Lake Storage Gen2 se vytváří na BLOB Storage a vylepšuje výkon, správu a zabezpečení následujícími způsoby:

-   **Výkon** je optimalizován, protože není nutné kopírovat nebo transformovat data jako předpoklad pro analýzu. V porovnání s plochým oborem názvů v úložišti objektů BLOB by hierarchický obor názvů významně zlepšit výkon operací správy adresářů, což zvyšuje celkový výkon úloh.

-   **Správa** je jednodušší, protože můžete uspořádat soubory a manipulovat s nimi prostřednictvím adresářů a podadresářů.

-   **Zabezpečení** je vykonatelné, protože můžete definovat oprávnění POSIX pro adresáře nebo jednotlivé soubory.

Data Lake Storage Gen2 je taky velmi nákladově efektivní, protože je postavená na [Azure Blob Storage](storage-blobs-introduction.md)s nízkými náklady. Další funkce dále snižují celkové náklady na vlastnictví pro provozování analýz velkých objemů dat v Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Klíčové funkce Data Lake Storage Gen2

-   **Přístup** s platformou hadoop: Data Lake Storage Gen2 umožňuje správu a přístup k datům stejně, jako byste použili [systém souborů DFS (DISTRIBUTED File System) Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Nový [ovladač ABFS](data-lake-storage-abfs-driver.md) (používaný pro přístup k datům) je k dispozici ve všech Apache Hadoop prostředích. Mezi tato prostředí patří [Azure HDInsight](../../hdinsight/index.yml)*,* [Azure Databricks](/azure/databricks/)a [Azure synapse Analytics](../../synapse-analytics/index.yml).

-   **Nadmnožina oprávnění POSIX**: model zabezpečení pro data Lake Gen2 podporuje oprávnění ACL a POSIX spolu s některými dalšími podrobnostmi, které jsou specifické pro data Lake Storage Gen2. Nastavení lze nakonfigurovat prostřednictvím Průzkumník služby Storage nebo prostřednictvím architektury jako podregistr a Spark.

-   **Cenově výhodné**: Data Lake Storage Gen2 nabízí cenovou kapacitu úložiště a transakce. Funkce, jako je [životní cyklus služby Azure Blob Storage](storage-lifecycle-management-concepts.md) , optimalizují náklady jako přechody dat během jejího životního cyklu.

-   **Optimalizovaný ovladač**: ovladač ABFS je [optimalizován speciálně](data-lake-storage-abfs-driver.md) pro analýzu velkých objemů dat. Odpovídající rozhraní REST API se procházejí prostřednictvím koncového bodu `dfs.core.windows.net` .

### <a name="scalability"></a>Škálovatelnost

Azure Storage jsou škálovatelné podle návrhu bez ohledu na to, jestli máte přístup přes rozhraní Data Lake Storage Gen2 nebo BLOB Storage. Může ukládat a obsluhovat *spoustu datových Exabyte*. Tato velikost úložiště je dostupná s propustností měřenou v gigabitech za sekundu (GB/s) na vysoké úrovni vstupních/výstupních operací za sekundu (IOPS). Zpracování se provádí při téměř konstantních latencích požadavků, které se měří na úrovni služby, účtu a souboru.

### <a name="cost-effectiveness"></a>Efektivita nákladů

Vzhledem k tomu, že Data Lake Storage Gen2 je postavená na Azure Blob Storage, kapacita úložiště a náklady na transakce jsou nižší. Na rozdíl od jiných služeb cloudového úložiště nemusíte data přesouvat nebo transformovat, než je budete moct analyzovat. Další informace o cenách najdete v tématu [Azure Storage ceny](https://azure.microsoft.com/pricing/details/storage).

Kromě toho funkce, jako je [hierarchický obor názvů](data-lake-storage-namespace.md) , významně zlepšují celkový výkon řady analytických úloh. Toto zlepšení výkonu znamená, že potřebujete méně výpočetní výkon pro zpracování stejného množství dat, což vede ke snížení celkových nákladů na vlastnictví pro ucelenou úlohu analýzy.

### <a name="one-service-multiple-concepts"></a>Jedna služba, více konceptů

Vzhledem k tomu, že Data Lake Storage Gen2 je postavená na Azure Blob Storage, více konceptů může popsat stejné, sdílené věci.

Níže jsou uvedené ekvivalentní entity, jak je popsáno v různých konceptech. Pokud není uvedeno jinak, jsou tyto entity přímo synonymné:

| Koncepce                                | Organizace nejvyšší úrovně | Organizace nižší úrovně                                            | Datový kontejner |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Objekty blob – úložiště objektů pro obecné účely | Kontejner              | Virtuální adresář (pouze sada SDK) – neposkytuje atomickou manipulaci | Objekt blob           |
| Azure Data Lake Storage Gen2 – analytické úložiště          | Kontejner            | Adresář                                                           | Soubor           |

## <a name="supported-blob-storage-features"></a>Podporované funkce Blob Storage

K vašemu účtu jsou k dispozici funkce služby Blob Storage, jako je [protokolování diagnostiky](../common/storage-analytics-logging.md), [úrovně přístupu](storage-blob-storage-tiers.md)a [zásady správy životního cyklu BLOB Storage](storage-lifecycle-management-concepts.md) . 

Seznam podporovaných funkcí služby Blob Storage najdete [v tématu BLOB Storage funkcích dostupných v Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Podporované integrace služeb Azure

Data Lake Storage Gen2 podporuje několik služeb Azure. Můžete je použít k ingestování dat, provádění analýz a vytváření vizuální reprezentace. Seznam podporovaných služeb Azure najdete v tématu [služby Azure, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Podporované opensourcové platformy

Data Lake Storage Gen2 podporuje několik Open Source platforem. Úplný seznam najdete v tématu [Open Source Platforms, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Viz také

- [Známé problémy s Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Přístup k více protokolům na Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)