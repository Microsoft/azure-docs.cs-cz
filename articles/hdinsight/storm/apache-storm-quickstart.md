---
title: 'Rychlý start: Vytváření a monitorování topologií Apache Storm v Azure HDInsight'
description: V tomto rychlém startu zjistěte, jak vytvořit a monitorovat topologií Apache Storm v Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/14/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 12001aef970d3b465a7f5c8e0c7af072b8f4ec80
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428448"
---
# <a name="quickstart-create-and-monitor-an-apache-storm-topology-in-azure-hdinsight"></a>Rychlý start: Vytvořit a monitorovat topologií Apache Storm v Azure HDInsight

Apache Storm je škálovatelný výpočetní systém v reálném čase odolný proti chybám, distribuovaný určený pro zpracování datových proudů. Pomocí Storm v Azure HDInsight můžete vytvořit cloudový cluster Storm, který bude provádět analýzy velkých objemů dat v reálném čase.

V tomto rychlém startu použijete příkladem Apache [topologie storm starter](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) projekt můžete vytvořit a monitorovat Apache Storm topologie do existujícího clusteru Apache Storm.

## <a name="prerequisites"></a>Požadavky

* Clustery Apache Storm v HDInsight. Zobrazit [vytvořit Apache Hadoop clusterů pomocí webu Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md) a vyberte **Storm** pro **typ clusteru**.

* Klient SSH. Další informace najdete v tématu [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-topology"></a>Vytvoření topologie

1. Připojte ke clusteru Storm. Upravte následující příkaz tak, že nahradíte `CLUSTERNAME` s názvem Storm cluster a potom zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. **WordCount** příklad je zahrnuta v clusteru HDInsight na `/usr/hdp/current/storm-client/contrib/storm-starter/`. Topologie generuje náhodné věty a počítá kolikrát slova vyskytovat. Pomocí následujícího příkazu spusťte **wordcount** topologie v clusteru:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount
    ```

## <a name="monitor-the-topology"></a>Monitorování topologie

Storm poskytuje webové rozhraní pro práci se spuštěnými topologiemi a je zahrnuté ve vašem clusteru HDInsight.

Ke sledování topologie pomocí uživatelského rozhraní Storm použijte následující kroky:

1. Pokud chcete zobrazit uživatelské rozhraní Storm, otevřete ve webovém prohlížeči adresu `https://CLUSTERNAME.azurehdinsight.net/stormui`. Nahraďte `CLUSTERNAME` názvem svého clusteru.

2. V části **souhrn topologie**, vyberte **wordcount** položku **název** sloupec. Zobrazí se další informace o topologii.

    ![Řídicí panel Storm s informacemi o topologii Storm Starter WordCount.](./media/apache-storm-quickstart/topology-summary.png)

    Nová stránka obsahuje následující informace:

    |Vlastnost | Popis |
    |---|---|
    |Statistiky topologie|Základní informace o výkonu topologie uspořádané do časových oken. Výběrem konkrétního časového okna změníte časové okno informací zobrazených v dalších částech stránky.|
    |Spoutů|Základní informace o funkcích spouts, včetně poslední chyby vrácené každou funkcí spout.|
    |Bolty|Základní informace o funkcích bolts.|
    |Konfigurace topologie|Podrobné informace o konfiguraci topologie.|
    |Aktivovat|Obnoví zpracování deaktivované topologie.|
    |Deaktivace|Pozastaví spuštěné topologie.|
    |Obnovení rovnováhy|Upraví paralelismus topologii. Po změně počtu uzlů v clusteru musíte znovu vyvážit spuštěné topologie. Nové vyvážení upraví paralelismus, aby se vykompenzovalo zvýšení nebo snížení počtu uzlů v clusteru. Další informace najdete v tématu [pochopení paralelismu topologie Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).|
    |Kill|Ukončí topologii Storm po zadaný časový limit.|

3. Na této stránce vyberte položku z oddílu **Spouts** nebo **Bolts**. Zobrazí se informace o vybrané komponentě.

    ![Řídicí panel Storm s informacemi o vybraných součástech.](./media/apache-storm-quickstart/component-summary.png)

    Nová stránka zobrazí následující informace:

    |Vlastnost | Popis |
    |---|---|
    |Funkcí spout/Bolt statistiky|Základní informace o výkonu komponenty uspořádané do časových oken. Výběrem konkrétního časového okna změníte časové okno informací zobrazených v dalších částech stránky.|
    |Statistika vstupu (pouze bolt)|Informace o komponentech, které vytváří dat využívaná funkcí bolt.|
    |Statistika výstupu|Informace o datech vysílaných touto funkcí bolt.|
    |Prováděcí moduly|Informace o instancích této komponenty.|
    |Chyby|Chyby vytvořené touto komponentou.|

4. Chcete-li zobrazit podrobnosti pro konkrétní instanci komponenty, při zobrazení podrobností o funkcích spout nebo bolt vyberte položku ze sloupce **Port** v oddílu **Vykonavatelé**.

        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

    V tomto příkladu se slovo **seven** vyskytlo 1493957krát. Tolikrát bylo toto slovo zjištěno od spuštění této topologie.

## <a name="stop-the-topology"></a>Zastavení topologie

Vraťte se na stránku **Souhrn topologie**, kde naleznete topologii počtu slov a pak vyberte tlačítko **Zastavit** z oddílu **Topologie akce**. Po zobrazení výzvy zadejte hodnotu 10 jako počet sekund, po které se má počkat před zastavením topologie. Po uplynutí časového limitu se topologie už při návštěvě oddílu **Uživatelské rozhraní Storm** řídicího panelu nezobrazí.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto rychlého startu, můžete cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Odstranění clusteru, naleznete v tématu [odstranění clusteru HDInsight pomocí prohlížeče, Powershellu nebo rozhraní příkazového řádku Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste použili příkladem Apache [topologie storm starter](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) projekt můžete vytvořit a monitorovat Apache Storm topologie do existujícího clusteru Apache Storm. Přejděte k dalším článku se dozvíte základní informace o správě a monitorování topologií Apache Storm.

> [!div class="nextstepaction"]
>[Nasazení a správa topologií Apache Storm v Azure HDInsight](./apache-storm-deploy-monitor-topology-linux.md)