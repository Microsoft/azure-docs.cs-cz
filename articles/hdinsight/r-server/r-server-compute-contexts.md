---
title: Možnosti výpočetního kontextu pro služby ML na HDInsight – Azure
description: Seznamte se s různými výpočetními kontextu možnosti dostupné pro uživatele pomocí služby ML v HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 1e01a3db2c0ca1f9024afb3faecf677ac4e3131b
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52494474"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>Možnosti výpočetního kontextu pro služby ML v HDInsight

Ovládací prvky služby ML v Azure HDInsight, jak jsou spouštěny nastavení výpočetního kontextu volání. Tento článek popisuje možnosti, které jsou k dispozici k určení, zda a jak je provádění paralelizována přes jader hraničním uzlu nebo clusteru HDInsight.

Hraničním uzlu clusteru poskytuje praktické místo k připojení ke clusteru a ke spuštění skriptů jazyka R. Díky hraniční uzel máte možnost spuštění paralelizované distribuované funkce RevoScaleR mezi jádry hraničního uzlu serveru. Můžete také spustit je na uzlech clusteru s použitím jeho RevoScaleR Hadoop Map Reduce nebo kontexty služby compute Apache Spark.

## <a name="ml-services-on-azure-hdinsight"></a>ML služeb v Azure HDInsight
[ML služeb v Azure HDInsight](r-server-overview.md) přináší nejnovější schopnosti pro analýzy založené na jazyce R. Může používat data, která je uložená v kontejneru Apache Hadoop HDFS v vaše [objektů Blob v Azure](../../storage/common/storage-introduction.md "úložiště objektů Blob v Azure") účtu úložiště, Data Lake store nebo místního systému souborů systému Linux. Protože ML Services je založená na open-source jazyka R, můžete použít aplikace založené na jazyce R, které vytváříte, některý z balíčků opensourcového jazyka R 8000 +. Můžete také použít rutiny v [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), Microsoftu pro velké objemy dat analytics balíčku, která je součástí služby ML.  

## <a name="compute-contexts-for-an-edge-node"></a>Kontexty pro hraniční uzel služby COMPUTE
Obecně platí skript R, který běží v clusteru služby ML na hraničním uzlu běží v rámci interpret R na tomto uzlu. Výjimky jsou tyto kroky, které volají funkce RevoScaleR. Volání RevoScaleR spustit ve výpočetním prostředí, které se určuje podle nastavení RevoScaleR výpočetním kontextu.  Když spustíte svůj skript jazyka R z hraniční uzel, jsou možné hodnoty výpočetním kontextu:

- místní sekvenční (*místní*)
- místní paralelní (*localpar*)
- Map Reduce
- Spark

*Místní* a *localpar* možnosti se liší pouze v tom **rxExec** volání jsou provedeny. Oba provedení jiných volání funkcí rx paralelní způsobem ze všech dostupných jader Pokud není určeno jinak pomocí RevoScaleR **numCoresToUse** volby, například `rxOptions(numCoresToUse=6)`. Možnosti paralelního provádění nabízí optimální výkon.

Následující tabulka shrnuje různé možnosti výpočetního kontextu pro nastavení, jak provést volání:

| Výpočetní kontext  | Jak nastavit                      | Kontext spuštění                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Místní sekvenční | rxSetComputeContext('local')    | Paralelizovaná spuštění napříč jádra hraničního uzlu serveru, s výjimkou rxExec volání, které jsou prováděny sériově |
| Místní paralelní   | rxSetComputeContext('localpar') | Paralelizovaná spuštění napříč jádra hraničního uzlu serveru |
| Spark            | RxSpark()                       | Paralelizovaná distribuované spuštění prostřednictvím Spark na uzlech clusteru HDI |
| Map Reduce       | RxHadoopMR()                    | Paralelizovaná distribuované spuštění prostřednictvím Map Reduce na uzlech clusteru HDI |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Pokyny pro rozhodování o výpočetním kontextu

Z těchto tří možností je zvolit, které poskytují paralelizované spuštění závisí na povaze analytics práci, velikost a umístění dat. Neexistuje žádné jednoduchý vzorec, který říká, které výpočetní kontext má být použit. Existují však některé zásady, které můžete učinit správnou volbu, nebo aspoň, můžete zúžit vaše volby před spuštěním testu výkonnosti. Tyto zásady patří:

- Místní systém souborů Linux je rychlejší než HDFS.
- Opakované analýzy jsou rychlejší, pokud jsou data místní, a pokud se nachází v XDF.
- Je vhodnější pro streamování malé množství dat ze zdroje dat textu. Pokud je větší množství dat, ho převeďte na XDF před analýzou.
- Nezvladatelné pro opravdu velké objemy dat se stane režijní náklady na kopírování nebo streamovaná data k hraničnímu uzlu pro analýzu.
- ApacheSpark je rychlejší než Map Reduce pro analýzu v Hadoopu.

Zadaný tyto zásady, v dalších částech nabízejí některé obecné hrubé odhady pro výběr výpočetní kontext.

### <a name="local"></a>Místní
* Pokud objem dat a analyzovat je malý a nevyžaduje opakované analýzy, potom Streamovat ho přímo do rutiny analýzy pomocí *místní* nebo *localpar*.
* Pokud množství dat a analyzovat je malá nebo středně velký a vyžaduje opakované analýzy, potom zkopírujte ho do místního systému souborů, importovat ho do XDF a analyzovat ho přes *místní* nebo *localpar*.

### <a name="apache-spark"></a>Apache Spark
* Pokud je velký objem dat pro analýzu, pak ho importovat do Spark DataFrame pomocí **RxHiveData** nebo **RxParquetData**, nebo XDF v HDFS (Pokud úložiště není problém) a je analyzujte pomocí výpočtů Spark kontext.

### <a name="apache-hadoop-map-reduce"></a>Apache Hadoop Map Reduce
* Výpočetní kontext Map Reduce použijte pouze v případě, protože je většinou horší dojde nepřekonatelný problém s výpočetním kontextu Spark.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Vložená Nápověda na rxSetComputeContext
Další informace a příklady RevoScaleR výpočetní kontext naleznete v tématu nápovědy v jazyce R na metodu rxSetComputeContext, například vložený:

    > ?rxSetComputeContext

Můžete také odkazovat na [distribuované výpočetní přehled](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing) v [dokumentace ke službě Machine Learning Server](https://docs.microsoft.com/machine-learning-server/).

## <a name="next-steps"></a>Další postup
V tomto článku jste se dozvěděli o možnosti, které jsou k dispozici k určení, zda a jak je provádění paralelizována přes jader hraničním uzlu nebo clusteru HDInsight. Další informace o tom, jak používat služby ML s clustery HDInsight, naleznete v následujících tématech:

* [Přehled služby ML pro Apache Hadoop](r-server-overview.md)
* [Začínáme se službou ML pro Apache Hadoop](r-server-get-started.md)
* [Možnosti služby Azure Storage pro služby ML v HDInsight](r-server-storage.md)

