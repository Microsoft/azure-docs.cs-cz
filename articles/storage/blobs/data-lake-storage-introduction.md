---
title: Azure Data Lake Storage Gen2 Úvod
description: Poskytuje přehled Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: d843e288297db656cca6e2a07f2e1f3322ebfa89
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299651"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Úvod do Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 je sada funkcí vyhrazených pro analýzy velkých objemů dat, která je založená na [službě Azure Blob Storage](storage-blobs-introduction.md). Data Lake Storage Gen2 je výsledkem sblížení schopností naší dvě existující služby úložiště, Azure Blob Storage a Azure Data Lake Storage Gen1. Funkce z [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), jako jsou sémantika systému souborů, adresář a zabezpečení na úrovni souborů a škálování, jsou kombinovány s nízkými náklady, vrstvenými úložištěm, vysokou dostupností a možnostmi zotavení po havárii z [Azure Blob Storage](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Navrženo pro podnikovou analýzu velkých objemů dat

Data Lake Storage Gen2 vytváří Azure Storage základ pro vytváření laků podnikových dat v Azure. Tato možnost je navržena od začátku až po obsluhu více petabajty informací a současně udržuje stovky gigabitových přenosů, Data Lake Storage Gen2 umožňuje snadno spravovat obrovské objemy dat.

Základní část Data Lake Storage Gen2 je přidání [hierarchického oboru názvů](data-lake-storage-namespace.md) do úložiště objektů BLOB. Hierarchický obor názvů uspořádává objekty/soubory do hierarchie adresářů pro efektivní přístup k datům. Společná konvence pojmenování objektů úložiště používá k napodobení hierarchické adresářové struktury lomítka v názvu. Tato struktura se zastará o Data Lake Storage Gen2. Operace, jako je přejmenování nebo odstranění adresáře, se stanou samostatnými operacemi atomických metadat v adresáři, nikoli vytvářením výčtu a zpracováním všech objektů, které sdílejí předponu názvu adresáře.

V minulosti se cloudové analýzy musely napadnout v oblasti výkonu, správy a zabezpečení. Data Lake Storage Gen2 řeší každou z těchto aspektů následujícími způsoby:

-   **Výkon** je optimalizován, protože není nutné kopírovat nebo transformovat data jako předpoklad pro analýzu. Hierarchický obor názvů významně vylepšuje výkon operací správy adresářů, což zvyšuje celkový výkon úloh.

-   **Správa** je jednodušší, protože můžete uspořádat soubory a manipulovat s nimi prostřednictvím adresářů a podadresářů.

-   **Zabezpečení** je vykonatelné, protože můžete definovat oprávnění POSIX pro adresáře nebo jednotlivé soubory.

-   **Nákladová efektivita** je možná, protože Data Lake Storage Gen2 je postavená na [Azure Blob Storage](storage-blobs-introduction.md)s nízkými náklady. Další funkce dále snižují celkové náklady na vlastnictví pro provozování analýz velkých objemů dat v Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Klíčové funkce Data Lake Storage Gen2

-   **Přístup**s platformou hadoop: Data Lake Storage Gen2 umožňuje správu a přístup k datům stejně, jako byste použili [systém souborů DFS (DISTRIBUTED File System) Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Nový [ovladač ABFS](data-lake-storage-abfs-driver.md) je k dispozici ve všech Apache Hadoop prostředích, včetně [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index) *,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index)a [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) pro přístup k datům uloženým v Data Lake Storage Gen2.

-   **Nadmnožina oprávnění POSIX**: model zabezpečení pro data Lake Gen2 podporuje oprávnění ACL a POSIX spolu s některými dalšími podrobnostmi, které jsou specifické pro data Lake Storage Gen2. Nastavení lze nakonfigurovat prostřednictvím Průzkumník služby Storage nebo prostřednictvím architektury jako podregistr a Spark.

-   **Cenově výhodné**: Data Lake Storage Gen2 nabízí cenovou kapacitu úložiště a transakce. Vzhledem k tomu, že se v průběhu svého kompletního životního cyklu data mění, účtují se náklady na minimum prostřednictvím integrovaných funkcí, jako je [životní cyklus služby Azure Blob Storage](storage-lifecycle-management-concepts.md).

-   **Optimalizovaný ovladač**: ovladač ABFS je [optimalizován speciálně](data-lake-storage-abfs-driver.md) pro analýzu velkých objemů dat. Odpovídající rozhraní REST API se procházejí prostřednictvím koncového bodu `dfs.core.windows.net`.

### <a name="scalability"></a>Škálovatelnost

Azure Storage jsou škálovatelné podle návrhu bez ohledu na to, jestli máte přístup přes rozhraní Data Lake Storage Gen2 nebo BLOB Storage. Může ukládat a obsluhovat *spoustu datových Exabyte*. Tato velikost úložiště je dostupná s propustností měřenou v gigabitech za sekundu (GB/s) na vysoké úrovni vstupních/výstupních operací za sekundu (IOPS). Mimo rámec pouhého trvalosti se zpracování provádí při téměř konstantních latencích požadavků, které se měří na úrovni služby, účtu a souboru.

### <a name="cost-effectiveness"></a>Efektivita nákladů

Jednou z mnoha výhod sestavování Data Lake Storage Gen2 na Azure Blob Storage je nízké náklady na kapacitu úložiště a transakce. Na rozdíl od jiných služeb cloudového úložiště není nutné před provedením analýzy přesunout nebo transformovat data uložená v Data Lake Storage Gen2. Další informace o cenách najdete v tématu [Azure Storage ceny](https://azure.microsoft.com/pricing/details/storage).

Kromě toho funkce, jako je [hierarchický obor názvů](data-lake-storage-namespace.md) , významně zlepšují celkový výkon řady analytických úloh. Toto zlepšení výkonu znamená, že potřebujete méně výpočetní výkon pro zpracování stejného množství dat, což vede ke snížení celkových nákladů na vlastnictví pro ucelenou úlohu analýzy.

### <a name="one-service-multiple-concepts"></a>Jedna služba, více konceptů

Data Lake Storage Gen2 je další možností pro analýzy velkých objemů dat, která je postavená na službě Azure Blob Storage. I když využívá stávající součásti platformy objektů BLOB k vytváření a provozování datových laků pro analýzy spoustu výhod, může to vést k několika konceptům, které popisují stejné sdílené věci.

Níže jsou uvedené ekvivalentní entity, jak je popsáno v různých konceptech. Pokud není uvedeno jinak, jsou tyto entity přímo synonymné:

| Koncept                                | Organizace nejvyšší úrovně | Organizace nižší úrovně                                            | Datový kontejner |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Objekty blob – úložiště objektů pro obecné účely | Kontejner              | Virtuální adresář (pouze sada SDK) – neposkytuje atomickou manipulaci | Objekt blob           |
| Azure Data Lake Storage Gen2 – analytické úložiště          | Kontejner            | Adresář                                                           | Soubor           |

## <a name="supported-open-source-platforms"></a>Podporované Open Source platformy

Data Lake Storage Gen2 podporuje několik Open Source platforem. Tyto platformy se zobrazí v následující tabulce.

> [!NOTE]
> Podporovány jsou pouze verze, které jsou uvedeny v této tabulce.

| Platforma |  Podporované verze | Další informace |
| --- | --- | --- |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | 3.6 + | [Jaké jsou součásti Apache Hadoop a verze, které jsou k dispozici v HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning?toc=%2Fen-us%2Fazure%2Fhdinsight%2Fstorm%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
| [Hadoop](https://hadoop.apache.org/) | 3.2 + | [Archiv Apache Hadoopch verzí](https://hadoop.apache.org/release.html) |
| [Cloudera](https://www.cloudera.com/) | 6.1 + | [Poznámky k verzi Cloudera Enterprise 6. x](https://www.cloudera.com/documentation/enterprise/6/release-notes/topics/rg_cdh_6_release_notes.html) |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) | 5.1 + | [Verze Databricks Runtime](https://docs.databricks.com/release-notes/runtime/databricks-runtime-ver.html) |
|[Hortonworks](https://hortonworks.com/)| 3.1. x + + | [Konfigurace přístupu k datům cloudu](https://docs.hortonworks.com/HDPDocuments/Cloudbreak/Cloudbreak-2.9.0/cloud-data-access/content/cb_configuring-access-to-adls2.html) |

## <a name="supported-azure-services"></a>Podporované služby Azure

Data Lake Storage Gen2 podporuje několik služeb Azure, které můžete použít k ingestování dat, provádění analýz a vytváření vizuální reprezentace. Seznam podporovaných služeb Azure najdete v tématu věnovaném [integraci Azure Data Lake Storage se službami Azure](data-lake-store-integrate-with-azure-services.md).

## <a name="next-steps"></a>Další kroky

Následující články popisují některé z hlavních konceptů Data Lake Storage Gen2 a podrobné informace o tom, jak ukládat, přistupovat, spravovat a získávat poznatky z vašich dat:

- [Hierarchický obor názvů](data-lake-storage-namespace.md)
- [vytvořit účet úložiště](data-lake-storage-quickstart-create-account.md)
- [Přístup k více protokolům na Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)
- [Integrace Azure Data Lake Storage se službami Azure](data-lake-store-integrate-with-azure-services.md);
