---
title: Chyba InvalidClassException z Apache Spark - Azure HDInsight
description: Úloha Apache Spark se nezdaří s InvalidClassException, neshoda verzí třídy, v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: be50f8716835b0842f854842e5340b0bb8594136
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894367"
---
# <a name="apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>Úloha Apache Spark se nezdaří s InvalidClassException, neshoda verzí třídy, v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Pokusíte se vytvořit úlohu Apache Spark v clusteru Spark 2.x. Slápne s chybou podobnou následující:

```
18/09/18 09:32:26 WARN TaskSetManager: Lost task 0.0 in stage 1.0 (TID 1, wn7-dev-co.2zyfbddadfih0xdq0cdja4g.ax.internal.cloudapp.net, executor 4): java.io.InvalidClassException:
org.apache.commons.lang3.time.FastDateFormat; local class incompatible: stream classdesc serialVersionUID = 2, local class serialVersionUID = 1
        at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:699)
        at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1885)
        at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1751)
        at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2042)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1573)
```

## <a name="cause"></a>Příčina

Tato chyba může být způsobena přidáním další nádoby do `spark.yarn.jars` konfigurace, konkrétně stínované `commons-lang3` nádoby, která obsahuje jinou verzi balíčku a zavádí neshodu třídy. Ve výchozím nastavení spark 2.1/2/3 používá `commons-lang3`verzi 3.5 z .

> [!TIP]
> Chcete-li stín knihovny je dát svůj obsah do vlastní nádoby, změna jeho balíček. To se liší od balení knihovny, která je uvedení knihovny do vlastní nádoby bez přebalení.

## <a name="resolution"></a>Řešení

Odeberte nádobu nebo znovu zkompilujte vlastní nádobu (AzureLogAppender) a použijte [modul plug-in maven-shade-plugin](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) k přemístění tříd.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
