---
title: InvalidClassExceptioná chyba z Apache Spark – Azure HDInsight
description: Úloha Apache Spark se nezdařila s InvalidClassException, neshoda verzí třídy v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: ad9ec8e97827fb6158476165a610c9d69b12a528
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241170"
---
# <a name="apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>Úloha Apache Spark se nezdařila s InvalidClassException, neshoda verzí třídy v Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Pokusíte se vytvořit úlohu Apache Spark v clusteru Spark 2. x. Dojde k chybě, která bude vypadat přibližně takto:

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

Tato chyba může být způsobena přidáním dalšího jar do `spark.yarn.jars` config, což je "stínovaný" jar, který obsahuje jinou verzi balíčku `commons-lang3` a zavádí neshodu třídy. Ve výchozím nastavení používá Spark 2.1/2/3 `commons-lang3`verze 3,5.

## <a name="resolution"></a>Rozlišení

Buď odeberte jar, nebo znovu zkompilujte přizpůsobený jar (AzureLogAppender) a pomocí [Maven-destínového modulu plug-in](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) přemístěte třídy.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) – oficiální Microsoft Azure účet pro zlepšení prostředí pro zákazníky tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
