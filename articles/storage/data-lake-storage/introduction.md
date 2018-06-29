---
title: Azure Data Lake Storage Gen2 Preview Úvod
description: Poskytuje přehled Azure Data Lake Storage Gen2 Preview
services: storage
documentationcenter: ''
author: jamesbak
manager: jahogg
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 0631b1d0c8da925858f0b7fb1d778cb1161eb737
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061520"
---
# <a name="introduction-to-azure-data-lake-storage-gen2-preview"></a>Úvod do služby Azure Data Lake Storage Gen2 Preview

Azure Data Lake Storage Gen2 Preview je sada funkcí, které jsou vyhrazené pro analýzy velkých objemů dat, postavený na [úložiště objektů Azure Blob](../blobs/storage-blobs-introduction.md). Umožňuje rozhraní s daty pomocí obou vzorů souboru systému a objekt úložiště. Díky tomu službu pouze cloudového více modální úložiště umožňuje extrahovat analytics hodnotu ze všech svých dat Gen2 úložiště Data Lake.

Data Lake Storage Gen2 funkce všechny vlastnosti, které jsou požadovány pro celý životní analytická data. To vyplývá z slučování možnosti naše dvě existující služby úložiště. Funkce z [Azure Data Lake Storage Gen1](../../data-lake-store/index.md), jako je například sémantiku systému souborů, souboru úroveň zabezpečení a škálování spojují s nízkými náklady, vrstvené úložiště, možnosti obnovení vysoké dostupnosti nebo po havárii a velké sady SDK nebo nástrojů ekosystém z [úložiště objektů Azure Blob](../blobs/storage-blobs-introduction.md). V Data Lake Storage Gen2 zůstávají všechny vlastnosti objektu úložiště při přidávání výhody rozhraní systému souboru optimalizované pro analýzu úlohy.

## <a name="designed-for-enterprise-big-data-analytics"></a>Navržené pro organizace analýzy velkých objemů dat

Data Lake Storage Gen2 je služba základní úložiště pro vytváření enterprise datového jezera (EDL) v Azure. Umožňuje od spuštění služby několika petabajtů informace při udržování stovky gigabity propustnosti, Gen2 úložiště Data Lake poskytuje snadný způsob, jak spravovat masivní objemy dat.

Základní funkce Gen2 úložiště Data Lake je přidání [hierarchického oboru názvů](./namespace.md) ke službě úložiště objektů Blob, který umožňuje uspořádat objekty nebo soubory do hierarchie adresářů pro přístup k datům původce. Hierarchického oboru názvů, taky umožňuje Gen2 úložiště Data Lake pro podporu obou objekt úložiště a souboru systému vzorů ve stejnou dobu. Například běžné zásady vytváření názvů objekt úložiště používá lomítka v názvu tak, aby napodoboval hierarchickou strukturu složek. Tato struktura bude skutečné s Gen2 úložiště Data Lake. Operace, jako je například přejmenování nebo odstranění adresáře stát jeden atomic metadata operací na adresář místo vytváření výčtů a zpracování všechny objekty, které sdílejí předpona názvu adresáře.

V minulosti bylo cloudové analytics ohrožení v oblasti výkonu, správu a zabezpečení. Data Lake Storage Gen2 adresy každé z těchto aspektů následujícími způsoby:

- **Výkon** je optimalizovaná, protože není potřeba kopírovat nebo transformace dat v rámci předpokladů pro analýzu. Hierarchického oboru názvů výrazně zvyšuje výkon operací správy adresáře, který zlepšuje celkový výkon úlohy.

- **Správa** je jednodušší, protože můžete uspořádat a práce se soubory pomocí adresářů a podadresářů.

- **Efektivity nákladů** je možné jako Gen2 úložiště Data Lake je postavená na nízkými náklady [úložiště objektů Azure Blob](../blobs/storage-blobs-introduction.md). Další funkce další nižší celkové náklady na vlastnictví pro spuštění analýzy velkých objemů dat v Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Klíčové funkce Gen2 úložiště Data Lake

> [!NOTE]
> Během verzi public preview Gen2 úložiště Data Lake některé níže uvedené funkce se může lišit v jejich dostupnost. Nové funkce a oblastí, jsou vydané v programu preview, tyto informace se budou oznamovat.
> [Zaregistrujte si](https://aka.ms/adlsgen2signup) na veřejné verzi preview Gen2 úložiště Data Lake.  

- **Kompatibilní přístup Hadoop**: Gen2 úložiště Data Lake umožňuje spravovat a přistupovat k datům, stejně jako s [Hadoop Distributed File System (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Nové [ABFS ovladač](./abfs-driver.md) je k dispozici ve všech prostředích Apache Hadoop, včetně [Azure HDInsight](../../hdinsight/index.yml) a [Azure Databricks](../../azure-databricks/index.yml) přístup k datům uloženým v Data Lake Storage Gen2.

- **Přístup k datům s více protokol a více modální**: považuje za Gen2 úložiště Data Lake **více modální** služba úložiště, protože poskytuje objekt úložiště a soubor rozhraní systému ke stejným datům **ve stejné čas**. Toho dosáhnete tím, že poskytuje několik koncových bodů protokolu, která mají mít přístup ke stejným datům. 

    Na rozdíl od jiných řešení pro analýzu dat uložených v Data Lake Storage Gen2 nemusí přesunout nebo transformovat předtím, než můžete spustit celou řadu nástrojů pro analýzu. Můžete přístup k datům přes tradiční [rozhraní API úložiště objektů Blob](../blobs/storage-blobs-introduction.md) (například: ingestovat data prostřednictvím [zaznamenat centra událostí](../../event-hubs/event-hubs-capture-enable-through-portal.md)) a zpracování dat pomocí HDInsight nebo Azure Databricks ve stejnou dobu. 

- **Nákladově efektivní**: Gen2 úložiště Data Lake funkce kapacitu nízkonákladové úložiště a transakce. Jako data přechody prostřednictvím jeho dokončení životního cyklu, fakturační sazby změnit zachování nákladů na minimum pomocí integrovaných funkcí, jako [životního cyklu úložiště objektů Blob v Azure](../common/storage-lifecycle-managment-concepts.md).

- **Funguje s nástroji úložiště objektů Blob, rozhraní a aplikací**: Gen2 úložiště Data Lake i nadále pracovat s širokou škálu nástroje rozhraní a aplikací, které jsou dnes pro úložiště objektů Blob.

- **Optimalizované ovladač**: `abfs` ovladač [speciálně optimalizovaná](./abfs-driver.md) pro analýzy velkých objemů dat. Odpovídající rozhraní REST API jsou prezentované prostřednictvím `dfs` koncový bod, `dfs.core.windows.net`.

## <a name="scalability"></a>Škálovatelnost

Úložiště Azure je škálovatelná záměrné, zda přístup přes rozhraní úložiště Data Lake Storage Gen2 nebo objekt Blob. To bude možné ukládat a sloužit *mnoho exabajtů dat*. Toto množství úložiště je k dispozici s propustností měřená v gigabitech za sekundu (Gbps) s vysokou úrovní vstupně výstupních operací za sekundu (IOPS). Nad rámec jenom trvalost zpracování provedená latence téměř konstanta požadavků, které jsou měřeny na službu, účet a úrovně souboru.

## <a name="cost-effectiveness"></a>Finanční efektivita

Jednou z mnoha výhod vytváření Gen2 úložiště Data Lake nad úložiště objektů Blob v Azure je [nízkonákladové](https://azure.microsoft.com/pricing/details/storage) kapacity úložiště a transakce. Na rozdíl od jiných cloudových služeb pro úložiště Gen2 úložiště Data Lake snižuje náklady, protože není nutné přesunout nebo transformovat před provedením analýzy dat.

Kromě toho funkce, jako [hierarchického oboru názvů](./namespace.md) výrazně zvýšit celkový výkon mnoho úloh analytics. Toto vylepšení výkonu znamená, vyžadují menší výpočetní výkon při zpracování stejné množství dat, což vede k snížit náklady na vlastnictví (TCO) úlohy analytics začátku do konce.

## <a name="next-steps"></a>Další postup

V následujících článcích popisují některé hlavní koncepty Gen2 úložiště Data Lake a podrobnosti o ukládání, přístup, spravovat a získáte přehled o vašich datech:

* [Hierarchického oboru názvů](./namespace.md)
* [vytvořit účet úložiště](./quickstart-create-account.md)
* [Vytvoření clusteru HDInsight s Azure Data Lake Storage Gen2](./quickstart-create-connect-hdi-cluster.md)
* [Použít účet Azure Data Lake Storage Gen2 ve Azure Databricks](./quickstart-create-databricks-account.md) 