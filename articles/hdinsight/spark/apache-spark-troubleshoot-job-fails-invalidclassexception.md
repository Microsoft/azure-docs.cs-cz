---
title: InvalidClassExceptioná chyba z Apache Spark – Azure HDInsight
description: Úloha Apache Spark se nezdařila s InvalidClassException, neshoda verzí třídy v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 6220c328d05e7cd68460b7bfd0708a9d393a290f
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287886"
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

Tato chyba může být způsobena přidáním dalšího jar do `spark.yarn.jars` konfigurace, konkrétně do vybarveného jar, který obsahuje jinou verzi `commons-lang3` balíčku a zavádí neshodu třídy. Ve výchozím nastavení používá Spark 2.1/2/3 verzi 3,5 `commons-lang3` .

> [!TIP]
> K vystínování knihovny je umístění svého obsahu do vlastního jar, změna jeho balíčku. To se liší od balení knihovny, což znamená vložení knihovny do vlastního jar bez nutnosti opětovného balení.

## <a name="resolution"></a>Řešení

Buď odeberte jar, nebo znovu zkompilujte přizpůsobený jar (AzureLogAppender) a pomocí [Maven-destínového modulu plug-in](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) přemístěte třídy.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]