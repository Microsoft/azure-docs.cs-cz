---
title: Azure Data Lake Storage Gen2 ve verzi Preview Úvod
description: Obsahuje základní informace o verzi Preview služby Azure Data Lake Storage Gen2
services: storage
author: jamesbak
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 072573b16fbeebac1ec942b0be508cf901b5cd27
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "42055021"
---
# <a name="introduction-to-azure-data-lake-storage-gen2-preview"></a>Úvod do služby Azure Data Lake Storage Gen2 ve verzi Preview

Azure Data Lake Storage Gen2 ve verzi Preview je sada funkcí, které jsou vyhrazené pro analýzy velkých objemů dat, postavený na [úložiště objektů Blob v Azure](../blobs/storage-blobs-introduction.md). Umožňuje uživateli rozhraní s vašimi daty použitím obou paradigmat souboru systému a objektu úložiště. Službu pouze založené na cloudu více modální úložiště, abyste mohli extrahovat hodnotu analytics ze všech vašich dat díky Data Lake Storage Gen2.

Gen2 úložiště data Lake obsahuje všechny vlastnosti, které jsou požadovány pro celý životní cyklus analytická data. To vyplývá z slučuje možnosti naše dva existující služby úložiště. Funkce z [Azure Data Lake Storage Gen1](../../data-lake-store/index.md), jako je například sémantiku systému souborů, souboru úroveň zabezpečení a škálování se sloučí s vrstveného úložiště s nízkými náklady, funkcím pro obnovení vysoká dostupnost/zotavení po havárii a velké sady SDK a nástroje ekosystém z [úložiště objektů Blob v Azure](../blobs/storage-blobs-introduction.md). V Data Lake Storage Gen2 zůstanou všechny vlastnosti objektu úložiště při přidávání výhody rozhraní systému souborů optimalizované pro analýzy úloh.

## <a name="designed-for-enterprise-big-data-analytics"></a>Navržená pro analýzy velkých objemů dat organizace

Data Lake Storage Gen2 je základní úložiště služba pro sestavování podnikových datová jezera (EDL) v Azure. Účelem je od samého začátku služby získejte tak několik petabajtů informace při udržování stovky gigabity propustnosti, Data Lake Storage Gen2 poskytuje snadný způsob, jak spravovat velké objemy dat.

Základní funkcí služby Data Lake Storage Gen2 je přidání [hierarchického oboru názvů](./namespace.md) do služby Blob storage, který slouží k uspořádání objektů nebo souborů do hierarchie adresářů pro přístup k datům výkonné. Data Lake Storage Gen2 podporovat i úložiště objektů a zaslat paradigmat systému ve stejnou dobu, taky umožňuje hierarchického oboru názvů. Běžné zásady vytváření názvů úložiště objektů pro instanci používá lomítek v názvu tak, aby napodoboval hierarchickou strukturu složek. Tato struktura bude skutečné s Data Lake Storage Gen2. Operace, jako je přejmenování nebo odstranění adresáře se stanou metadat jednotného atomické operace na adresář místo vytváření výčtů a zpracování všech objektů, které sdílejí předpona názvu adresáře.

V minulosti cloudové analýzy došlo k ohrožení v oblasti výkonu, správy a zabezpečení. Data Lake Storage Gen2 řeší všechny tyto aspekty následujícími způsoby:

- **Výkon** je optimalizace, protože není potřeba kopírovat nebo transformace dat, což je nezbytná podmínka pro analýzu. Hierarchického oboru názvů výrazně zlepšuje výkon operace správy adresáře, který zlepšuje celkový výkon úlohy.

- **Správa** je jednodušší, protože můžete uspořádat a pracovat se soubory adresářů a podadresářů.

- **Zabezpečení** je vynutit, protože můžete definovat POSIX oprávnění u složky nebo jednotlivce, souborů.

- **Nákladovou efektivitu** je možné jako Data Lake Storage Gen2 je nástavbou s nízkými náklady [úložiště objektů Blob v Azure](../blobs/storage-blobs-introduction.md). Další funkce dále snížit celkové náklady na vlastnictví pro spuštění analýzy velkých objemů dat v Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Klíčové funkce služby Data Lake Storage Gen2

> [!NOTE]
> Ve verzi public preview služby Data Lake Storage Gen2 některé funkce uvedené níže mohou lišit v jejich dostupnost. Nové funkce a oblasti vydané během do programu preview, tyto informace se předávají.
> [Zaregistrujte](https://aka.ms/adlsgen2signup) verze public Preview služby Data Lake Storage Gen2.  

- **Hadoop kompatibilní přístup**: Data Lake Storage Gen2 umožňuje spravovat a přistupovat k datům, stejně jako při použití [souboru systému HDFS (Hadoop Distributed)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Nové [ABFS ovladač](./abfs-driver.md) je k dispozici ve všech prostředích Apache Hadoop, včetně [Azure HDInsight](../../hdinsight/index.yml) a [Azure Databricks](../../azure-databricks/index.yml) přístup k datům uloženým v Data Lake Storage Gen2.

- **Nadmnožina POSIX oprávnění**: model zabezpečení pro Data Lake Gen2 plně podporuje oprávnění řízení přístupu a POSIX spolu s nějaké další specifické pro Data Lake Storage Gen2 členitosti. Nastavení může být nakonfigurován prostřednictvím správce nástroje nebo architektury, jako je Hive a Spark.

- **Nákladově efektivní**: Gen2 úložiště Data Lake nabízí kapacitu úložiště s nízkými náklady a transakce. Jako přechody data prostřednictvím jejich kompletní životní cyklus, fakturační sazby změnit uchování nákladů na minimum prostřednictvím integrované funkce, jako [životní cyklus úložiště objektů Blob v Azure](../common/storage-lifecycle-managment-concepts.md).

- **Funguje s Blob storage nástroje, platformy a aplikace**: Data Lake Storage Gen2 nadále využívat širokou škálu nástroje, platformy a aplikace, které existují ještě dnes pro úložiště objektů Blob.

- **Optimalizované ovladač**: `abfs` ovladač [speciálně optimalizovaná](./abfs-driver.md) pro analýzy velkých objemů dat. Zobrazují se prostřednictvím odpovídající rozhraní REST API `dfs` koncový bod, `dfs.core.windows.net`.

## <a name="scalability"></a>Škálovatelnost

Azure Storage je škálovatelné záměrné, ať už přistupujete prostřednictvím Data Lake Storage Gen2 nebo objekt Blob úložiště rozhraní. To bude možné ukládat a sloužit *mnoho exabajtů dat*. Tato velikost úložiště je k dispozici s propustnost měřenou v gigabitech za sekundu (Gbps) ve vysoké míře vstupních/výstupních operací za sekundu (IOPS). Nad rámec jenom trvalost provádí zpracování v blízkosti konstanta jednotlivých žádostí latenci, které se měří na úrovni služby, účet a soubor.

## <a name="cost-effectiveness"></a>Finanční efektivita

Jednou z mnoha výhod stavění úložiště objektů Blob v Azure Data Lake Storage Gen2 je [s nízkými náklady](https://azure.microsoft.com/pricing/details/storage) sady kapacitou úložiště a transakce. Na rozdíl od jiných cloudových službách úložiště Data Lake Storage Gen2 snižuje náklady, protože data není povinen být přesunut nebo před provedením analýzy.

Kromě toho funkce, jako [hierarchického oboru názvů](./namespace.md) výrazně zlepšit výkon mnoho úloh analytics. K tomuto zlepšení výkonu znamená, že vyžadují méně výpočetní výkon pro zpracování stejné množství dat, jejichž výsledkem je snížení celkových nákladů na vlastnictví (TCO) na začátku do konce analytics úlohy.

## <a name="next-steps"></a>Další postup

Následující články popisují některé hlavní koncepty nástroje Data Lake Storage Gen2 a podrobnosti k ukládání, přístup, správě a získávání informací z vašich dat:

* [Hierarchického oboru názvů](./namespace.md)
* [vytvořit účet úložiště](./quickstart-create-account.md)
* [Vytvoření clusteru HDInsight s Azure Data Lake Storage Gen2](./quickstart-create-connect-hdi-cluster.md)
* [Použít účet Azure Data Lake Storage Gen2 v Azure Databricks](./quickstart-create-databricks-account.md) 