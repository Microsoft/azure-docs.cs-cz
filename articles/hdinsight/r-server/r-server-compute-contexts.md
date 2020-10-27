---
title: Možnosti výpočetního kontextu pro služby ML ve službě HDInsight – Azure
description: Seznamte se s různými možnostmi výpočetního kontextu, které jsou dostupné pro uživatele se službami ML ve službě HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 21781015aa91c9c953d716b9b3399851f25be9b5
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92536330"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>Možnosti výpočetního kontextu pro služby ML v HDInsight

Služba ML Services ve službě Azure HDInsight řídí, jak se spouštějí volání, a to nastavením výpočetního kontextu. Tento článek popisuje možnosti, které jsou k dispozici pro určení, zda a jakým způsobem je provádění paralelní napříč jádry hraničního uzlu nebo clusteru HDInsight.

Hraniční uzel clusteru poskytuje vhodné místo pro připojení ke clusteru a spouštění skriptů jazyka R. Pomocí hraničního uzlu máte možnost spouštět paralelní distribuované funkce RevoScaleR napříč jádry serveru hraničního uzlu. Můžete je také spouštět v uzlech clusteru pomocí RevoScaleR mapy Hadoop pro redukci nebo Apache Spark výpočetních kontextů.

## <a name="ml-services-on-azure-hdinsight"></a>Služby ML ve službě Azure HDInsight

[Služba ml Services v Azure HDInsight](r-server-overview.md) poskytuje nejnovější funkce pro analýzy založené na jazyce R. Může použít data uložená v kontejneru Apache Hadoop HDFS v účtu služby [Azure Blob](../../storage/common/storage-introduction.md "Azure Blob Storage") Storage, v Data Lake Store nebo v místním systému souborů Linux. Vzhledem k tomu, že služba ML Services je postavená na Open Source R, aplikace založené na jazyce R můžou použít kterýkoli z balíčků verze 8000 + open-source. Můžou také používat rutiny v [RevoScaleR](/machine-learning-server/r-reference/revoscaler/revoscaler), což je balíček pro analýzy velkých objemů dat od Microsoftu, který je součástí ml služeb.  

## <a name="compute-contexts-for-an-edge-node"></a>Kontexty výpočtů pro hraniční uzel

Obecně platí, že skript R, který je spuštěn v clusteru služby ML na hraničním uzlu, je spuštěn v překladači R na tomto uzlu. Výjimkou jsou kroky, které volají funkci RevoScaleR. Volání RevoScaleR se spouštějí ve výpočetním prostředí, které je určeno nastavením výpočetního kontextu RevoScaleR.  Při spuštění skriptu R z hraničního uzlu jsou možné hodnoty kontextu Compute:

- místní sekvenční ( *místní* )
- místní paralelní ( *localpar* )
- Zmenšit mapování
- Spark

*Místní* a *localpar* možnosti se liší pouze v tom, jak se spouštějí volání **rxExec** . Oba mají paralelní volání funkce RX napříč všemi dostupnými jádry, pokud není uvedeno jinak pomocí možnosti RevoScaleR **numCoresToUse** , například `rxOptions(numCoresToUse=6)` . Možnosti paralelního provádění nabízejí optimální výkon.

Následující tabulka shrnuje různé možnosti výpočetního kontextu pro nastavení způsobu spuštění volání:

| Výpočetní kontext  | Jak nastavit                      | Kontext spuštění                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Místní sekvenční | rxSetComputeContext (místní)    | Paralelní provádění napříč jádry serveru hraničního uzlu, s výjimkou volání rxExec, která se spouštějí sériově |
| Místní paralelní   | rxSetComputeContext('localpar') | Paralelní provádění napříč jádry serveru hraničního uzlu |
| Spark            | Výpočetního rxspark ()                       | Paralelní distribuované provádění prostřednictvím Sparku napříč uzly clusteru HDI |
| Zmenšit mapování       | RxHadoopMR()                    | Paralelní distribuované provádění prostřednictvím mapy se zmenšuje v uzlech clusteru HDI. |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Pokyny pro rozhodování o výpočetním kontextu

Který ze tří možností, které si zvolíte, nabízí paralelní provádění, závisí na povaze vaší analýzy, velikosti a umístění vašich dat. Neexistuje žádný jednoduchý vzorec, který vám oznamuje, který výpočetní kontext se má použít. Existují však některé principy použití identifikátoru GUID, které vám pomohou s výběrem správné volby, nebo alespoň k zúžení výběru před spuštěním srovnávacího testu. Principy těchto identifikátorů GUID zahrnují:

- Místní systém souborů Linux je rychlejší než HDFS.
- Opakované analýzy jsou rychlejší, pokud jsou data místní a pokud jsou v XDF.
- Je vhodnější streamovat malé objemy dat z textového zdroje dat. Pokud je množství dat větší, převeďte je do XDF před analýzou.
- Režijní náklady na kopírování nebo streamování dat do hraničního uzlu pro účely analýzy se stávají nespravovatelnými pro velké objemy dat.
- ApacheSpark je rychlejší než omezení map pro analýzu v Hadoop.

V následujících částech jsou uvedené některé obecné pravidla pro výběr výpočetního kontextu.

### <a name="local"></a>Místní

- Pokud je objem dat k analýze malý a nevyžaduje opakovanou analýzu, pak ji Streamujte přímo do analytické rutiny pomocí *místních* nebo *localpar* .
- Pokud je množství dat k analýze malé nebo středně velké a vyžaduje opakovanou analýzu, zkopírujte ji do místního systému souborů, importujte ji do XDF a analyzujte ji prostřednictvím *místních* nebo *localpar* .

### <a name="apache-spark"></a>Apache Spark

- Pokud je objem dat k analýze velký, importujte ho do datového rámce Spark pomocí **RxHiveData** nebo **RXPARQUETDATA** nebo na XDF v HDFS (Pokud se nejedná o problém) a analyzujte ho pomocí výpočetního kontextu Spark.

### <a name="apache-hadoop-map-reduce"></a>Zmenšení Apache Hadoop mapy

- Použijte mapování omezit výpočetní kontext jenom v případě, že jste procházeli nepřekonatelným problémem s výpočetním kontextem Spark, protože je obecně pomalejší.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Vložená Pomocník pro rxSetComputeContext
Další informace a příklady RevoScaleR výpočetních kontextů najdete v tématu s vloženou nápovědu v R na metodě rxSetComputeContext, například:

```console
> ?rxSetComputeContext
```

Další informace najdete v tématu [Přehled distribuovaných výpočtů](/machine-learning-server/r/how-to-revoscaler-distributed-computing) v [dokumentaci Machine Learning Server](/machine-learning-server/).

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o možnostech, které jsou k dispozici pro určení toho, zda a jak je provádění paralelně v jádrech hraničního uzlu nebo clusteru HDInsight. Další informace o tom, jak používat služby ML s clustery HDInsight, najdete v následujících tématech:

- [Přehled služeb ML pro Apache Hadoop](r-server-overview.md)
- [Možnosti Azure Storage pro služby ML v HDInsight](r-server-storage.md)