---
title: Možnosti výpočetního kontextu pro služby ML na HDInsight – Azure
description: Informace o různých možnostech výpočetního kontextu dostupných uživatelům se službami ML na HDInsightu
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: b67bd5b6310e1f8ce35dc14690757209ef62c9d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75660252"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>Možnosti výpočetního kontextu pro služby ML na hdinsightu

Ml Služby na Azure HDInsight řídí, jak se volání provádí nastavením výpočetního kontextu. Tento článek popisuje možnosti, které jsou k dispozici určit, zda a jak je paralelní provádění mezi jádry hraničního uzlu nebo clusteru HDInsight.

Hraniční uzel clusteru poskytuje vhodné místo pro připojení ke clusteru a spuštění skriptů R. S hraničním uzlem máte možnost spustit paralelizované distribuované funkce RevoScaleR přes jádra serveru hraničního uzlu. Můžete je také spustit přes uzly clusteru pomocí revoscaler je Hadoop mapa snížit nebo Apache Spark výpočetní kontexty.

## <a name="ml-services-on-azure-hdinsight"></a>Ml služby na Azure HDInsight

[Ml Services na Azure HDInsight](r-server-overview.md) poskytuje nejnovější funkce pro analýzy založené na R. Můžete použít data uložená v kontejneru Apache Hadoop HDFS ve vašem účtu úložiště [objektů Blob Azure,](../../storage/common/storage-introduction.md "Azure Blob Storage") úložiště Data Lake Store nebo v místním souborovém systému Linux. Vzhledem k tomu, že ml služby je postaven na open source R, aplikace založené na R, které vytvoříte můžete použít některý z 8000 + open source R balíčky. Mohou také používat rutiny v [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), Microsoft big data analytics balíček, který je součástí ML Services.  

## <a name="compute-contexts-for-an-edge-node"></a>Výpočetní kontexty pro hraniční uzel

Obecně platí, že skript R, který je spuštěn v clusteru SLUŽBY ML na hraničním uzlu, běží v rámci překladače R v tomto uzlu. Výjimky jsou tyto kroky, které volají funkci RevoScaleR. Volání RevoScaleR spustit ve výpočetním prostředí, které je určeno tím, jak nastavit kontext výpočetnírevoměřítkr.  Při spuštění skriptu R z hraničního uzlu jsou možné hodnoty výpočetního kontextu:

- lokální sekvenční (*místní*)
- lokální paralelní (*localpar*)
- Omezit mapování
- Spark

*Místní* a *localpar* možnosti se liší pouze v tom, jak **rxExec** volání jsou prováděny. Oba provádějí další volání funkce rx paralelním způsobem ve všech dostupných jádrech, pokud není uvedeno jinak pomocí `rxOptions(numCoresToUse=6)`možnosti RevoScaleR **numCoresToUse,** například . Možnosti paralelního spuštění nabízejí optimální výkon.

Následující tabulka shrnuje různé možnosti výpočetního kontextu a nastavuje způsob spouštění volání:

| Výpočetní kontext  | Jak nastavit                      | Kontext spuštění                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Místní sekvenční | rxSetComputeContext('místní')    | Paralelní provádění mezi jádry serveru hraničního uzlu, s výjimkou volání rxExec, která jsou prováděna sériově |
| Místní paralelní   | rxSetComputeContext('localpar') | Paralelizované provádění mezi jádry serveru hraničního uzlu |
| Spark            | RxSpark()                       | Paralelizované distribuované spuštění přes Spark přes uzly clusteru HDI |
| Omezit mapování       | RxHadoopMR()                    | Paralelizované distribuované spuštění pomocí funkce Omezit mapování napříč uzly clusteru HDI |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Pokyny pro rozhodování o výpočetním kontextu

Která ze tří možností, které zvolíte a které poskytují paralelní spuštění, závisí na povaze vaší analytické práce, velikosti a umístění dat. Neexistuje žádný jednoduchý vzorec, který vám řekne, který výpočetní kontext použít. Existují však některé hlavní zásady, které vám mohou pomoci učinit správnou volbu, nebo alespoň pomoci zúžit vaše volby před spuštěním benchmarku. Mezi tyto hlavní zásady patří:

- Místní souborový systém Linux je rychlejší než HDFS.
- Opakované analýzy jsou rychlejší, pokud jsou data místní a pokud jsou v XDF.
- Je vhodnější streamovat malá množství dat ze zdroje textových dat. Pokud je množství dat větší, převeďte je před analýzou na XDF.
- Režie kopírování nebo streamování dat do hraničního uzlu pro analýzu se stane nespravovatelné pro velmi velké množství dat.
- ApacheSpark je rychlejší než Map Reduce pro analýzu v Hadoopu.

Vzhledem k těmto zásadám následující části nabízejí některá obecná pravidla pro výběr výpočetního kontextu.

### <a name="local"></a>Local

- Pokud je množství dat k analýze malé a nevyžaduje opakovanou analýzu, pak je streamujte přímo do rutiny analýzy pomocí *místního* nebo *localparu*.
- Pokud je množství dat k analýze malé nebo střední a vyžaduje opakovanou analýzu, zkopírujte je do místního systému souborů, importujte je do XDF a analyzujte je pomocí *místního* nebo *localparu*.

### <a name="apache-spark"></a>Apache Spark

- Pokud je množství dat k analýze velké, importujte je do datového rámce Spark pomocí **RxHiveData** nebo **RxParquetData**nebo do XDF v HDFS (pokud úložiště není problém) a analyzujte je pomocí výpočetního kontextu Spark.

### <a name="apache-hadoop-map-reduce"></a>Apache Hadoop mapa snížit

- Kontext výpočetního prostředí Omezit mapu použijte pouze v případě, že narazíte na nepřekonatelný problém s výpočetním kontextem Spark, protože je obecně pomalejší.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Inline nápověda k rxSetComputeContext
Další informace a příklady výpočetních kontextů RevoScaleR naleznete v nápovědě v r na metodě rxSetComputeContext, například:

    > ?rxSetComputeContext

Můžete také odkazovat na [přehled distribuovaného výpočetního prostředí](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing) v [dokumentaci k serveru Machine Learning Server](https://docs.microsoft.com/machine-learning-server/).

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o možnostech, které jsou k dispozici určit, zda a jak je provádění paralelizováno mezi jádry hraničního uzlu nebo clusteru HDInsight. Další informace o používání služeb ML v clusterech HDInsight naleznete v následujících tématech:

- [Přehled ml služeb pro Apache Hadoop](r-server-overview.md)
- [Možnosti úložiště Azure pro služby ML na WEBU HDInsight](r-server-storage.md)
