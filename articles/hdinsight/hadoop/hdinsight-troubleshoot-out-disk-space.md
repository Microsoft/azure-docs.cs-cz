---
title: Uzel clusteru nemá volné místo na disku v Azure HDInsight.
description: Řešení potíží s Apache Hadoop problémy s místem na disku uzlu clusteru ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.openlocfilehash: f1a994ad07980c67e37d00bffb7e605ed610bb08
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289081"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Scénář: uzel clusteru má nedostatek místa na disku ve službě Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Úloha může selhat s chybovou zprávou podobnou následující: `/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Nebo se může zobrazit výstraha Apache Ambari podobná této: `local-dirs usable space is below configured utilization percentage` .

## <a name="cause"></a>Příčina

Mezipaměť aplikace Apache nitě mohla spotřebovat veškeré dostupné místo na disku. Aplikace Spark je nejspíš neefektivně spuštěna.

## <a name="resolution"></a>Řešení

1. Pomocí uživatelského rozhraní Ambari určete, který uzel má nedostatek místa na disku.

1. Určete, která složka v uzlu problematické přispívá k většině místa na disku. Nejprve na uzel SSH a potom spusťte příkaz pro `df` vypsání využití disku pro všechna připojení. Obvykle se jedná `/mnt` o dočasný disk používaný službou OSS. Můžete zadat do složky a potom `sudo du -hs` do složky zadat zobrazení souhrnných velikostí souborů. Pokud se zobrazí složka podobná `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007` , znamená to, že je aplikace stále spuštěná. To může být způsobeno RDD persistencí nebo mezilehlého náhodného souboru.

1. Chcete-li tento problém zmírnit, ukončete aplikaci, která bude vydávat místo na disku využité touto aplikací.

1. Pokud k problému dochází často na pracovních uzlech, můžete ladit nastavení místní mezipaměti PŘÍZ v clusteru.

    Otevřete uživatelské rozhraní Ambari, přejděte do části nitě--> config – > Upřesnit.  
    Přidejte následující 2 vlastnosti do oddílu Custom yarn-site.xml a uložte:

    ```
    yarn.nodemanager.localizer.cache.target-size-mb=2048
    yarn.nodemanager.localizer.cache.cleanup.interval-ms=300000
    ```

1. Pokud výše uvedené nejsou trvale vyřešeny, optimalizujte svoji aplikaci.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]