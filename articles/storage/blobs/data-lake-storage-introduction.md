---
title: Azure Data Lake Storage Gen2 ve verzi Preview Úvod
description: Obsahuje základní informace o verzi Preview služby Azure Data Lake Storage Gen2
services: storage
author: jamesbak
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 0b3c4a4c21bc9b124a98b1f204f85e6e78ff233e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53099558"
---
# <a name="introduction-to-azure-data-lake-storage-gen2-preview"></a>Úvod do služby Azure Data Lake Storage Gen2 ve verzi Preview

Azure Data Lake Storage Gen2 ve verzi Preview je sada funkcí, které jsou vyhrazené pro analýzy velkých objemů dat, postavená na [úložiště objektů Blob v Azure](storage-blobs-introduction.md). Data Lake Storage Gen2 je výsledkem slučuje možnosti našich dva existující služby storage, Azure Blob storage a Azure Data Lake Storage Gen1. Funkce z [Azure Data Lake Storage Gen1](https://docs.microsoft.com/en-us/azure/data-lake-store/index), jako jsou sémantiku systému souborů, adresáře a zabezpečení na úrovni souborů a škálování se sloučí s s nízkými náklady, vrstveného úložiště, možnosti obnovení vysoká dostupnost/zotavení po havárii z [Azure Blob storage](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Navržená pro analýzy velkých objemů dat organizace

Úložiště Azure data Lake Storage Gen2 díky základ pro vytváření datová jezera enterprise v Azure. Účelem je od samého začátku služby získejte tak několik petabajtů informace při udržování stovky gigabity propustnosti, Data Lake Storage Gen2 vám umožní snadno spravovat velké objemy dat.

Základní součást Data Lake Storage Gen2 je přidání [hierarchického oboru názvů](data-lake-storage-namespace.md) do úložiště objektů Blob. Hierarchického oboru názvů slouží k uspořádání objektů nebo souborů do hierarchie adresářů pro přístup k datům efektivní. Běžné zásady vytváření názvů úložiště objektu používá lomítek v názvu tak, aby napodoboval strukturu hierarchického adresáře. Tato struktura bude skutečné s Data Lake Storage Gen2. Operace, jako je přejmenování nebo odstranění adresáře se stanou metadat jednotného atomické operace na adresář místo vytváření výčtů a zpracování všech objektů, které sdílejí předpona názvu adresáře.

V minulosti cloudové analýzy došlo k ohrožení v oblasti výkonu, správy a zabezpečení. Data Lake Storage Gen2 řeší všechny tyto aspekty následujícími způsoby:

-   **Výkon** je optimalizace, protože není potřeba kopírovat nebo transformace dat, což je nezbytná podmínka pro analýzu. Hierarchického oboru názvů výrazně zvyšuje výkon directory operace správy, který zlepšuje celkový výkon úlohy.

-   **Správa** je jednodušší, protože můžete uspořádat a pracovat se soubory adresářů a podadresářů.

-   **Zabezpečení** je vynutit, protože můžete definovat oprávnění POSIX na adresáře nebo jednotlivé soubory.

-   **Nákladovou efektivitu** je možné jako Data Lake Storage Gen2 je nástavbou s nízkými náklady [úložiště objektů Blob v Azure](storage-blobs-introduction.md). Další funkce dále snížit celkové náklady na vlastnictví pro spuštění analýzy velkých objemů dat v Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Klíčové funkce služby Data Lake Storage Gen2

-   **Hadoop kompatibilní přístup**: Data Lake Storage Gen2 umožňuje spravovat a přistupovat k datům, stejně jako při použití [souboru systému HDFS (Hadoop Distributed)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Nové [ABFS ovladač](data-lake-storage-abfs-driver.md) je k dispozici ve všech prostředích Apache Hadoop, včetně [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index)*,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index), a [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) přístup k datům uloženým v Data Lake Storage Gen2.

-   **Nadmnožina POSIX oprávnění**: model zabezpečení pro Data Lake Gen2 podporuje oprávnění řízení přístupu a POSIX spolu s nějaké další specifické pro Data Lake Storage Gen2 členitosti. Nastavení může být nakonfigurován pomocí Průzkumníka služby Storage nebo prostřednictvím architektury, jako je Hive a Spark.

-   **Nákladově efektivní**: nabízí kapacitu úložiště s nízkými náklady a transakce s Data Lake Storage Gen2. Jako přechody data prostřednictvím jejich kompletní životní cyklus, fakturační sazby změnit uchování nákladů na minimum prostřednictvím integrované funkce, jako [životní cyklus úložiště objektů Blob v Azure](storage-lifecycle-management-concepts.md).

-   **Optimalizované ovladač**: ovladač abfs [speciálně optimalizovaná](data-lake-storage-abfs-driver.md) pro analýzy velkých objemů dat. Zobrazují se prostřednictvím koncového bodu systému souborů dfs, odpovídající rozhraní REST API dfs.core.windows.net.

### <a name="scalability"></a>Škálovatelnost

Azure Storage je škálovatelné záměrné, ať už přistupujete prostřednictvím Data Lake Storage Gen2 nebo objekt Blob úložiště rozhraní. To bude možné ukládat a sloužit *mnoho exabajtů dat*. Tato velikost úložiště je k dispozici s propustnost měřenou v gigabitech za sekundu (Gbps) ve vysoké míře vstupních/výstupních operací za sekundu (IOPS). Nad rámec jenom trvalost provádí zpracování v blízkosti konstanta jednotlivých žádostí latenci, které se měří na úrovni služby, účet a soubor.

### <a name="cost-effectiveness"></a>Finanční efektivita

Jednou z mnoha výhod stavění úložiště objektů Blob v Azure Data Lake Storage Gen2 je nízké náklady sady kapacitou úložiště a transakce. Na rozdíl od jiných cloudových službách úložiště není nutné být přesunut nebo před provedením analýzy dat uložených v Data Lake Storage Gen2. Další informace o cenách najdete v tématu [ceny za Azure Storage]((https://azure.microsoft.com/pricing/details/storage)).

Kromě toho funkce, jako [hierarchického oboru názvů](data-lake-storage-namespace.md) výrazně zlepšit výkon mnoho úloh analytics. K tomuto zlepšení výkonu znamená, že vyžadují méně výpočetní výkon pro zpracování stejné množství dat, jejichž výsledkem je snížení celkových nákladů na vlastnictví (TCO) na začátku do konce analytics úlohy.

### <a name="one-service-multiple-concepts"></a>Jedna služba více koncepty

Data Lake Storage Gen2 je další schopností pro analýzy velkých objemů dat, postavené na Azure Blob storage. I když existují mnoho výhod v existující komponenty platformy přes Bloby až po vytvoření a provoz datová jezera pro analýzy využití, vést k několika koncepty popisující stejné, sdílené kroky.

Níže jsou ekvivalentní entity, jak je popsáno v různých koncepty. Pokud není určeno jinak, tyto entity jsou přímo shodné:

| Koncept                                | Nejvyšší úrovně organizace | Nižší úrovně organizace                                            | Datový kontejner |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Objekty BLOB – storage pro obecné účely objektu | Kontejner              | Virtuální adresář (sada SDK pouze – neposkytuje atomic manipulaci s) | Objekt blob           |
| ADLS Gen2 – analýzy úložiště          | systém souborů             | Adresář                                                           | File           |

## <a name="supported-open-source-platforms"></a>Podporované opensourcové platformy

Několik opensourcové platformy podporují Data Lake Storage Gen2. V následující tabulce se zobrazí tyto platformy.

> [!NOTE]
> Podporovány jsou pouze verze, které se zobrazí v této tabulce.

| Platforma |  Podporované verze | Další informace |
| --- | --- | --- |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | 3.6 + | [Co je Apache Hadoop komponenty a verze, které jsou k dispozici s HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning?toc=%2Fen-us%2Fazure%2Fhdinsight%2Fstorm%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
| [Hadoop](https://hadoop.apache.org/) | 2.7+ | [Apache Hadoop verze archivu](https://hadoop.apache.org/release.html) |
| [Cloudera](https://www.cloudera.com/) | 6.1 + | [Zpráva k vydání verze 6.x Cloudera Enterprise](https://www.cloudera.com/documentation/enterprise/6/release-notes/topics/rg_cdh_6_release_notes.html) |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) | 4.2 + | [Modul Databricks Runtime verze](https://docs.databricks.com/release-notes/runtime/databricks-runtime-ver.html) |
|[Hortonworks](https://hortonworks.com/)| 2.6 + | [Dokumentace ke službě Hortonworks](https://docs.hortonworks.com/) |

## <a name="next-steps"></a>Další postup

Následující články popisují některé hlavní koncepty nástroje Data Lake Storage Gen2 a podrobnosti k ukládání, přístup, správě a získávání informací z vašich dat:

-   [Hierarchického oboru názvů](data-lake-storage-namespace.md)
-   [Vytvoření účtu úložiště](data-lake-storage-quickstart-create-account.md)
-   [Vytvoření clusteru HDInsight s Data Lake Storage Gen2](data-lake-storage-quickstart-create-connect-hdi-cluster.md)
-   [Použití účtu Data Lake Storage Gen2 v Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md)
