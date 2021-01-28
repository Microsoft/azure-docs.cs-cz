---
title: 'Rychlý Start: vytvoření a Správa topologie Apache Storm – Azure HDInsight'
description: V rychlém startu se dozvíte, jak vytvořit a monitorovat topologii Apache Storm v Azure HDInsight.
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/14/2019
ms.custom: mvc
ms.openlocfilehash: fe7b1cdc96ae95ac361183342ea491432c3ab4b4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928848"
---
# <a name="quickstart-create-and-monitor-an-apache-storm-topology-in-azure-hdinsight"></a>Rychlý Start: vytvoření a monitorování topologie Apache Storm ve službě Azure HDInsight

Apache Storm je škálovatelný výpočetní systém v reálném čase odolný proti chybám, distribuovaný určený pro zpracování datových proudů. Pomocí Storm v Azure HDInsight můžete vytvořit cloudový cluster Storm, který bude provádět analýzy velkých objemů dat v reálném čase.

V tomto rychlém startu použijete příklad z projektu Apache [-Starter](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) k vytvoření a monitorování Apache Storm topologie do existujícího clusteru Apache Storm.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Storm v HDInsight. Přečtěte si téma [vytvoření Apache Hadoop clusterů pomocí Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) **a výběr funkce** pro **typ clusteru**.

* Klient SSH. Další informace najdete v tématu [Připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-topology"></a>Vytvoření topologie

1. Připojte se ke clusteru nevlastníce. Níže uvedený příkaz upravte nahrazením `CLUSTERNAME` názvem vašeho clusteru pro zaplavení a zadáním příkazu:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. **WORDCOUNT** příklad je součástí vašeho clusteru HDInsight na adrese `/usr/hdp/current/storm-client/contrib/storm-starter/` . Tato topologie vygeneruje náhodné věty a spočítá, kolikrát se slova vyskytují. K zahájení topologie **WORDCOUNT** v clusteru použijte následující příkaz:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount
    ```

## <a name="monitor-the-topology"></a>Monitorování topologie

Funkce poskytuje webové rozhraní pro práci s běžícími topologiemi a je součástí clusteru HDInsight.

Ke sledování topologie pomocí uživatelského rozhraní Storm použijte následující kroky:

1. Pokud chcete zobrazit uživatelské rozhraní Storm, otevřete ve webovém prohlížeči adresu `https://CLUSTERNAME.azurehdinsight.net/stormui`. Nahraďte `CLUSTERNAME` názvem svého clusteru.

2. V části **Souhrn topologie** vyberte položku **WORDCOUNT** ve sloupci **název** . Zobrazí se další informace o topologii.

    ![Řídicí panel Storm s informacemi o topologii Storm Starter WordCount.](./media/apache-storm-quickstart/hdi-topology-summary.png)

    Na nové stránce najdete následující informace:

    |Vlastnost | Popis |
    |---|---|
    |Statistiky topologie|Základní informace o výkonu topologie uspořádané do časových oken. Výběrem konkrétního časového okna změníte časové okno informací zobrazených v dalších částech stránky.|
    |Spoutů|Základní informace o spoutů, včetně poslední chyby vrácené jednotlivými Spout.|
    |Připevňovacích|Základní informace o šrouby.|
    |Konfigurace topologie|Podrobné informace o konfiguraci topologie|
    |Aktivovat|Obnoví zpracování deaktivované topologie.|
    |Deaktivovat|Pozastaví běžící topologii.|
    |Obnovení rovnováhy|Upravuje paralelismus topologie. Po změně počtu uzlů v clusteru musíte znovu vyvážit spuštěné topologie. Nové vyvážení upraví paralelismus, aby se vykompenzovalo zvýšení nebo snížení počtu uzlů v clusteru. Další informace najdete v tématu [Princip paralelismu Apache Storm topologie](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).|
    |Kill|Ukončí topologii nečinnosti po zadaném časovém limitu.|

3. Na této stránce vyberte položku z oddílu **Spouts** nebo **Bolts**. Zobrazí se informace o vybrané komponentě.

    ![Řídicí panel Storm s informacemi o vybraných součástech.](./media/apache-storm-quickstart/hdi-component-summary.png)

    Na nové stránce se zobrazí následující informace:

    |Vlastnost | Popis |
    |---|---|
    |Statistiky Spout/šroubů|Základní informace o výkonu komponenty uspořádané do časových oken. Výběrem konkrétního časového okna změníte časové okno informací zobrazených v dalších částech stránky.|
    |Vstupní statistiky (jenom šroub)|Informace o součástech, které vytváří data spotřebovaná šroubem.|
    |Statistiky výstupu|Informace o datech emitovaných tímto šroubem.|
    |Prováděcí moduly|Informace o instancích této součásti.|
    |Chyby|Chyby, které tato součást vytvořila.|

4. Chcete-li zobrazit podrobnosti pro konkrétní instanci komponenty, při zobrazení podrobností o funkcích spout nebo bolt vyberte položku ze sloupce **Port** v oddílu **Vykonavatelé**.

```output
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]
```

V tomto příkladu se slovo **seven** vyskytlo 1493957krát. Tolikrát bylo toto slovo zjištěno od spuštění této topologie.

## <a name="stop-the-topology"></a>Zastavení topologie

Vraťte se na stránku **Souhrn topologie**, kde naleznete topologii počtu slov a pak vyberte tlačítko **Zastavit** z oddílu **Topologie akce**. Po zobrazení výzvy zadejte hodnotu 10 jako počet sekund, po které se má počkat před zastavením topologie. Po uplynutí časového limitu se topologie už při návštěvě oddílu **Uživatelské rozhraní Storm** řídicího panelu nezobrazí.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu možná budete chtít cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Pokud chcete odstranit cluster, přečtěte si téma [odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového řádku Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili příklad z projektu Apache [-Starter](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) k vytvoření a monitorování Apache Storm topologie do existujícího clusteru Apache Storm. V dalším článku se naučíte základy správy a monitorování topologií Apache Storm.

> [!div class="nextstepaction"]
>[Nasazení a Správa topologií Apache Storm v Azure HDInsight](./apache-storm-deploy-monitor-topology-linux.md)