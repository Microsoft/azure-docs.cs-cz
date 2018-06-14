---
title: Výpočetní kontextu možnosti pro R Server v HDInsight - Azure | Microsoft Docs
description: Další informace o různých výpočetních kontextu možnosti dostupné uživatelům s R Server v HDInsight
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 0deb0b1c-4094-459b-94fc-ec9b774c1f8a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: nitinme
ms.openlocfilehash: 2aa10e1eab6cabe058062519ecc023b88361d742
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31409065"
---
# <a name="compute-context-options-for-r-server-on-hdinsight"></a>Výpočetní kontextu možnosti pro R Server v HDInsight

Microsoft R serverem v Azure HDInsight řídí, jak jsou spouštěny nastavení kontext výpočetní volání. Tento článek popisuje možnosti, které jsou k dispozici k určení, zda a jak je paralelizovaná provádění málo mezi jader hraniční uzel nebo clusteru HDInsight.

Hraničního uzlu clusteru poskytuje vhodné místo pro připojení ke clusteru a spustit skripty R. S hraniční uzel máte možnost spuštění parallelized distribuované funkce RevoScaleR mezi jader hraničního uzlu serveru. Můžete také spustit je mezi uzly clusteru pomocí RevoScaleR na Hadoop mapy snížit nebo výpočetní kontexty Spark.

## <a name="microsoft-r-server-on-azure-hdinsight"></a>Microsoft R serverem v Azure HDInsight
[Microsoft R serverem v Azure HDInsight](r-server-overview.md) přináší nejnovější schopnosti pro R na základě analýzy. Může používat data, která je uložená v kontejneru HDFS ve vaší [objektů Blob v Azure](../../storage/common/storage-introduction.md "úložiště objektů Azure Blob") účet úložiště, Data Lake store nebo místního souboru systému Linux. Vzhledem k tomu, že R Server je založený na R s otevřeným zdrojem, můžete použít na základě R aplikací, které vytvoříte, žádný z balíčků R s otevřeným zdrojem 8000 +. Může také používat rutiny v [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), balíček analýzy velkých objemů dat společnosti Microsoft, který je součástí R Server.  

## <a name="compute-contexts-for-an-edge-node"></a>Výpočetní kontexty pro hraniční uzel
Obecně platí R skript, který běží v R Server na uzlu edge běží v rámci překladač R v tomto uzlu. Výjimky jsou tyto kroky, které volají funkce RevoScaleR. Volání RevoScaleR spustit ve výpočetním prostředí, který je určen jak nastavit kontext výpočetní RevoScaleR.  Když spustíte R skript z hraniční uzel, kontextu výpočetní hodnoty jsou:

- místní sekvenční (*místní*)
- místní paralelní (*localpar*)
- Snižte mapy
- Spark

*Místní* a *localpar* možnosti se liší pouze v tom **rxExec** provedení volání. Obě provést jiná volání funkce rx paralelní způsobem mezi všechny dostupné jader není uvedeno jinak prostřednictvím použití RevoScaleR **numCoresToUse** volby, například `rxOptions(numCoresToUse=6)`. Paralelní provádění možnosti nabízejí optimální výkon.

Následující tabulka shrnuje různé možnosti kontextu výpočetní nastavit, jak jsou vykonány volání:

| Výpočetní kontext  | Postup nastavení                      | Kontext spuštění                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Místní sekvenčních | rxSetComputeContext('local')    | Provádění paralelizovaná málo napříč jádrech hraničního uzlu serveru, s výjimkou rxExec volání, které jsou prováděny sériově |
| Místní paralelní   | rxSetComputeContext('localpar') | Provádění paralelizovaná málo napříč jádrech hraničního uzlu serveru |
| Spark            | RxSpark()                       | Paralelizovaná málo distribuované zpracování prostřednictvím Spark mezi uzly clusteru HDI |
| Snižte mapy       | RxHadoopMR()                    | Paralelizovaná málo distribuované zpracování prostřednictvím snížit mapy mezi uzly clusteru HDI |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Pokyny pro rozhodování v kontextu výpočetní

Která z těchto tří možností zvolíte, které poskytují paralelizovaná málo provádění závisí na povaze práci analýzy, velikost a umístění vašich dat. Neexistuje žádný jednoduchý vzorec, který zjistíte, které výpočetní kontext, který má použít. Existují však některé základní zásady, které můžete vybrat správně, nebo aspoň, můžete zúžit vaše volby před spuštěním testu výkonnosti. Tyto zásady patří:

- Místní systém souborů Linux je rychlejší než HDFS.
- Pokud se místní data, a pokud se nachází v XDF se rychlejší opakované analýzy.
- Je vhodnější k vysílání datového proudu malé množství dat ze zdroje dat text. Pokud větší množství dat, se ji převeďte XDF před analýzou.
- Nezvladatelné pro velmi velké objemy dat se stane režií při kopírování nebo streamování dat k uzlu edge pro analýzu.
- Spark je rychlejší než mapy snížit pro analýzu v Hadoop.

Zadané tyto zásady, následující části nabízí některé zásady pro výběr výpočetní kontextu.

### <a name="local"></a>Místní
* Pokud množství dat k analýze je malá a nevyžaduje opakovanou analýzu, pak Streamovat ho přímo do rutiny analysis pomocí *místní* nebo *localpar*.
* Pokud množství dat k analýze je malá nebo středně velký a vyžaduje opakovanou analýzu, pak zkopírujte ho do místního systému souborů, importujte je do XDF a analyzujte ji prostřednictvím *místní* nebo *localpar*.

### <a name="hadoop-spark"></a>Hadoop, Spark
* Pokud je velké množství dat k analýze, pak ho importovat do Spark DataFrame pomocí **RxHiveData** nebo **RxParquetData**, nebo XDF v HDFS (Pokud úložiště je problém) a analyzujte ji pomocí Spark výpočetní kontext.

### <a name="hadoop-map-reduce"></a>Snižte Hadoop mapy
* Použijte kontext mapy snížit výpočetní pouze v případě, že narazíte na problém s nepřekonatelnou s kontextem výpočtů Spark vzhledem k tomu, že je obecně pomalejší.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Vložené nápovědu k rxSetComputeContext
Další informace a příklady RevoScaleR výpočetní kontexty najdete v tématu vložený nápovědy v R na metodu rxSetComputeContext, například:

    > ?rxSetComputeContext

Můžete se také podívat na [distribuovat výpočetní přehled](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing) v [dokumentaci Machine Learning serveru](https://docs.microsoft.com/machine-learning-server/).

## <a name="next-steps"></a>Další postup
V tomto článku jste se dozvěděli o možnostech, které jsou k dispozici k určení, zda a jak je paralelizovaná provádění málo mezi jader hraniční uzel nebo clusteru HDInsight. Další informace o tom, jak používat R Server s clustery HDInsight, naleznete v následujících tématech:

* [Přehled R Server pro Hadoop](r-server-overview.md)
* [Začínáme s R Server pro Hadoop](r-server-get-started.md)
* [Možnosti služby Azure Storage pro R Server ve službě HDInsight](r-server-storage.md)

