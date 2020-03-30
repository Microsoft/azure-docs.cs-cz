---
title: Azure Data Lake Storage Gen2 Úvod
description: Obsahuje přehled azure data lake storage gen2
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 2f920e29fafdc55478e0e2c16d683bd1c3bc81d8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78942921"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Úvod do úložiště datových jezer Azure Gen2

Azure Data Lake Storage Gen2 je sada funkcí určených pro analýzu velkých objemů dat, která je postavená na [úložišti objektů Blob Azure](storage-blobs-introduction.md). Data Lake Storage Gen2 je výsledkem konvergování možností našich dvou stávajících služeb úložiště, úložiště objektů blob Azure a Azure Data Lake Storage Gen1. Funkce z [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), jako je sémantika systému souborů, zabezpečení a škálování na úrovni souborů, jsou kombinovány s levným vrstveným úložištěm, vysokou dostupností a možností zotavení po havárii z úložiště objektů [Blob Azure](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Navrženo pro podnikové analýzy velkých objemů dat

Data Lake Storage Gen2 dělá Azure Storage základem pro vytváření podnikových datových jezer v Azure. Aplikace Data Lake Storage Gen2, navržená od začátku, aby obsluhovala více petabajtů informací a zároveň udržovala stovky gigabitů propustností, umožňuje snadnospravovat obrovské množství dat.

Základní součástí Data Lake Storage Gen2 je přidání [hierarchického oboru názvů](data-lake-storage-namespace.md) do úložiště objektů Blob. Hierarchický obor názvů uspořádá objekty/soubory do hierarchie adresářů pro efektivní přístup k datům. Konvence pojmenování úložiště běžných objektů používá lomítka v názvu k napodobení hierarchické struktury adresářů. Tato struktura se stává reálnou s Datovým lakem Storage Gen2. Operace, jako je například přejmenování nebo odstranění adresáře, se stanou jednotlivými operacemi atomových metadat v adresáři, nikoli výčeta a zpracování všech objektů, které sdílejí předponu názvu adresáře.

Data Lake Storage Gen2 staví na úložišti objektů Blob a zvyšuje výkon, správu a zabezpečení následujícími způsoby:

-   **Výkon** je optimalizován, protože není nutné kopírovat nebo transformovat data jako předpoklad pro analýzu. Ve srovnání s plochým oborem názvů v úložišti objektů Blob hierarchický obor názvů výrazně zlepšuje výkon operací správy adresářů, což zlepšuje celkový výkon úlohy.

-   **Správa** je jednodušší, protože můžete organizovat a manipulovat se soubory prostřednictvím adresářů a podadresářů.

-   **Zabezpečení** je vynutitelné, protože můžete definovat oprávnění POSIX pro adresáře nebo jednotlivé soubory.

Také úložiště datových jezer Gen2 je velmi nákladově efektivní, protože je postavenna nad nízkonákladové [úložiště objektů blob Azure](storage-blobs-introduction.md). Další funkce dále snižují celkové náklady na vlastnictví pro spuštění analýzy velkých objemů dat v Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Klíčové vlastnosti úložiště dat Lake Gen2

-   **Přístup kompatibilní s Hadoopem**: Data Lake Storage Gen2 umožňuje spravovat a přistupovat k datům stejně jako u [hadoopského distribuovaného souborového systému (HDFS).](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) Nový [ovladač ABFS](data-lake-storage-abfs-driver.md) je k dispozici ve všech prostředích Apache Hadoop, včetně [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index)*,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index)a SQL Data [Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) pro přístup k datům uloženým v úložišti Data Lake Storage Gen2.

-   **Nadmnožina oprávnění POSIX**: Model zabezpečení pro Data Lake Gen2 podporuje oprávnění ACL a POSIX spolu s některými extra rozlišovací schopnost specifické pro Data Lake Storage Gen2. Nastavení může být nakonfigurováno prostřednictvím Průzkumníka úložiště nebo prostřednictvím architektur, jako je Hive a Spark.

-   **Nákladově efektivní**: Data Lake Storage Gen2 nabízí nízkonákladovou úložnou kapacitu a transakce. S přechody dat prostřednictvím celého životního cyklu se sazby za fakturaci mění a udržují náklady na minimum prostřednictvím integrovaných funkcí, jako je [životní cyklus úložiště objektů Blob Azure](storage-lifecycle-management-concepts.md).

-   **Optimalizovaný ovladač**: Ovladač ABFS je [optimalizován speciálně](data-lake-storage-abfs-driver.md) pro analýzu velkých objemů dat. Odpovídající REST API jsou konsurfaced `dfs.core.windows.net`přes koncový bod .

### <a name="scalability"></a>Škálovatelnost

Azure Storage je škálovatelné podle návrhu, ať už přistupujete přes rozhraní úložiště Data Lake Storage Gen2 nebo Blob. Je schopen ukládat a obsluhovat *mnoho exabajtů dat*. Toto množství úložiště je k dispozici s propustností měřenou v gigabitech za sekundu (Gbps) při vysokých úrovních vstupních a výstupních operací za sekundu (IOPS). Kromě pouze trvalost, zpracování se provádí na téměř konstantní latence na požadavek, které se měří na úrovni služby, účtu a souboru.

### <a name="cost-effectiveness"></a>Efektivita nákladů

Jednou z mnoha výhod vytváření úložiště datového jezera Gen2 nad úložiště objektů blob Azure je nízké náklady na kapacitu úložiště a transakce. Na rozdíl od jiných služeb cloudového úložiště se před provedením analýzy data uložená v úložišti Data Lake Storage Gen2 nemusí přesouvat ani transformovat. Další informace o cenách najdete v tématu [Ceny azure storage](https://azure.microsoft.com/pricing/details/storage).

Kromě toho funkce, jako je [hierarchický obor názvů](data-lake-storage-namespace.md) výrazně zlepšit celkový výkon mnoha úloh analýzy. Toto zlepšení výkonu znamená, že ke zpracování stejného množství dat vyžadujete méně výpočetního výkonu, což vede k nižším celkovým nákladům na vlastnictví (TCO) pro úlohu analýzy mezi koncovými náklady.

### <a name="one-service-multiple-concepts"></a>Jedna služba, více konceptů

Data Lake Storage Gen2 je další funkce pro analýzu velkých objemů dat, která je postavená na úložišti objektů Blob Azure. I když existuje mnoho výhod při využívání stávajících komponent platformy objektů BLOB k vytváření a provozu datových jezer pro analýzy, vede to k několika konceptům popisujícím stejné sdílené věci.

Následují ekvivalentní entity, jak je popsáno v různých konceptech. Není-li uvedeno jinak, jsou tyto entity přímo synonymní:

| Koncept                                | Organizace nejvyšší úrovně | Organizace nižší úrovně                                            | Kontejner dat |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Objekty BLOB – úložiště objektů pro obecné účely | Kontejner              | Virtuální adresář (pouze sada SDK – neposkytuje atomovou manipulaci) | Objekt blob           |
| Azure Data Lake Storage Gen2 – Analytics Storage          | Kontejner            | Adresář                                                           | File           |

## <a name="supported-blob-storage-features"></a>Podporované funkce úložiště objektů Blob

Funkce úložiště objektů blob, jako je [diagnostické protokolování](../common/storage-analytics-logging.md), [vrstvy přístupu](storage-blob-storage-tiers.md)a [zásady správy životního cyklu úložiště blob,](storage-lifecycle-management-concepts.md) teď fungují s účty, které mají hierarchický obor názvů. Proto můžete povolit hierarchické obory názvů na účtech úložiště objektů Blob bez ztráty přístupu k těmto funkcím. 

Seznam podporovaných funkcí úložiště objektů Blob najdete [v tématu Funkce úložiště objektů blob dostupné v Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Podporované integrace služeb Azure

Storage data lake 2 podporuje několik služeb Azure, které můžete použít k ingestování dat, provádění analýz a vytváření vizuálních reprezentací. Seznam podporovaných služeb Azure najdete v [tématu služby Azure, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Podporované open source platformy

Několik open source platforem podporuje Data Lake Storage Gen2. Úplný seznam najdete [v tématu Open source platformy, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Viz také

- [Známé problémy s Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Přístup k více protokolům v úložišti Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)


