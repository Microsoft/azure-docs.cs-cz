---
title: Přístup k protokolům aplikace Apache Hadoop nitě – Azure HDInsight
description: Přečtěte si, jak získat přístup k protokolům aplikací PŘÍZ na clusteru HDInsight se systémem Linux (Apache Hadoop) pomocí příkazového řádku i webového prohlížeče.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 882384b5e57db27cff981f80e790dfd41b624c93
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980690"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Přístup k protokolům aplikace Apache Hadoop nitě v HDInsight se systémem Linux

Naučte se, jak získat přístup k protokolům pro [Apache HADOOP přízi](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (ještě další aplikace pro vyjednávání prostředků) v clusteru Apache Hadoop ve službě Azure HDInsight.

## <a name="what-is-apache-yarn"></a>Co je Apache PŘÍZe?

PŘÍZe podporuje více programovacích modelů (Apache Hadoop MapReduce jednu z nich) tím, že odpojuje správu prostředků od plánování a monitorování aplikací. PŘÍZe používá globální *`ResourceManager`* (RM), per-Work-node *NodeManagers* (NMs) a *ApplicationMasters* pro jednotlivé aplikace (AMs). Jednotlivé aplikace vyjednávají prostředky (CPU, paměť, disk, síť) pro spuštění aplikace s nástrojem RM. RM spolupracuje s NMs pro udělení těchto prostředků, které se udělují jako *kontejnery*. Zodpovídá za sledování průběhu kontejnerů, které mu jsou přiřazeny pomocí služby RM. Aplikace může vyžadovat mnoho kontejnerů v závislosti na povaze aplikace.

Každá aplikace se může skládat z několika *pokusů o aplikace*. Pokud dojde k chybě aplikace, může se pokusit o nový pokus. Každý pokus se spustí v kontejneru. Ve smyslu kontejner poskytuje kontext pro základní pracovní jednotku, kterou provádí aplikace PŘÍZe. Veškerá práce prováděná v rámci kontextu kontejneru se provádí v jednom pracovním uzlu, na kterém byl kontejner uveden. Další informace najdete v tématu [Hadoop: zápis přízch aplikací](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html)nebo [Apache Hadoop příze](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) .

Pokud chcete škálovat cluster tak, aby podporoval větší propustnost zpracování, můžete použít automatické [škálování](hdinsight-autoscale-clusters.md) nebo [škálování clusterů ručně pomocí několika různých jazyků](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters).

## <a name="yarn-timeline-server"></a>Server časové osy PŘÍZe

[Server časové osy Apache HADOOP příze](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) poskytuje obecné informace o dokončených aplikacích.

Server časové osy PŘÍZe obsahuje následující typy dat:

* ID aplikace, jedinečný identifikátor aplikace
* Uživatel, který spustil aplikaci
* Informace o pokusůch o dokončení aplikace
* Kontejnery používané jakýmkoli pokusy o aplikace

## <a name="yarn-applications-and-logs"></a>PŘÍZe aplikace a protokoly

Protokoly aplikací (a přidružené protokoly kontejnerů) jsou důležité při ladění problematických aplikací Hadoop. PŘÍZe nabízí dobrý rámec pro shromažďování, agregaci a ukládání protokolů aplikací pomocí agregace protokolů.

Funkce agregace protokolu usnadňuje přístup k protokolům aplikací. Agreguje protokoly mezi všemi kontejnery v pracovním uzlu a ukládá je jako jeden agregovaný soubor protokolu pro každý pracovní uzel. Protokol je po dokončení aplikace uložen ve výchozím systému souborů. Vaše aplikace může používat stovky nebo tisíce kontejnerů, ale protokoly pro všechny kontejnery spuštěné v jednom pracovním uzlu jsou vždycky agregované do jednoho souboru. Proto existuje pouze 1 protokol na jeden pracovní uzel používaný vaší aplikací. Agregace protokolů je ve výchozím nastavení povolená v clusterech HDInsight verze 3,0 a vyšší. Agregované protokoly jsou umístěny ve výchozím úložišti clusteru. Následující cesta je cesta HDFS k protokolům:

```
/app-logs/<user>/logs/<applicationId>
```

V cestě `user` je název uživatele, který aplikaci spustil. `applicationId`Je jedinečný identifikátor přiřazený k aplikaci pomocí příze RM.

Agregované protokoly nejsou přímo čitelné, protože jsou napsány v TFile binárním formátu indexovaném kontejnerem. Pomocí `ResourceManager` nástrojů pro zápisy příze nebo rozhraní příkazového řádku můžete tyto protokoly zobrazit jako prostý text pro aplikace nebo kontejnery, které vás zajímají.

## <a name="yarn-logs-in-an-esp-cluster"></a>Záznamy příze v clusteru ESP

Do vlastní v Ambari musí být přidány dvě konfigurace `mapred-site` .

1. Z webového prohlížeče přejděte do `https://CLUSTERNAME.azurehdinsight.net` umístění, kde `CLUSTERNAME` je název vašeho clusteru.

1. V uživatelském rozhraní Ambari přejděte do **MapReduce2**  >  **config**  >  **Advanced**  >  **Custom mapred-site**.

1. Přidejte *jednu* z následujících sad vlastností:

    **Nastavit 1**

    ```
    mapred.acls.enabled=true
    mapreduce.job.acl-view-job=*
    ```

    **Nastavení 2**

    ```
    mapreduce.job.acl-view-job=<user1>,<user2>,<user3>
    ```

1. Uložte změny a restartujte všechny ovlivněné služby.

## <a name="yarn-cli-tools"></a>Nástroje rozhraní příkazového řádku PŘÍZ

1. Připojte se ke clusteru pomocí [příkazu SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) . Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Vypíše všechna ID aplikací aktuálně spuštěných Přízových aplikací pomocí následujícího příkazu:

    ```bash
    yarn top
    ```

    Poznamenejte si ID aplikace ze `APPLICATIONID` sloupce, jehož protokoly se mají stáhnout.

    ```output
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved
    
                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. Tyto protokoly můžete zobrazit jako prostý text spuštěním jednoho z následujících příkazů:

    ```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    ```

    Zadejte &lt;> ApplicationId, &lt; User-Started-the-Application>, &lt; containerId> a &lt; Work-Node-Address> informace při spuštění těchto příkazů.

### <a name="other-sample-commands"></a>Další ukázkové příkazy

1. Pomocí níže uvedeného příkazu Stáhněte protokoly kontejnerů příz pro všechny hlavní servery aplikací. Tento krok vytvoří soubor protokolu s názvem `amlogs.txt` v textovém formátu.

    ```bash
    yarn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

1. Stáhněte si protokoly kontejneru příze jenom pro nejnovější hlavní aplikace pomocí následujícího příkazu:

    ```bash
    yarn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

1. Stáhněte si protokoly kontejneru PŘÍZe pro první dva hlavní aplikační servery pomocí následujícího příkazu:

    ```bash
    yarn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

1. Stáhněte všechny protokoly kontejneru příze pomocí následujícího příkazu:

    ```bash
    yarn logs -applicationId <application_id> > logs.txt
    ```

1. Stáhněte si protokol kontejneru příze pro konkrétní kontejner pomocí následujícího příkazu:

    ```bash
    yarn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

## <a name="yarn-resourcemanager-ui"></a>rozhraní PŘÍZe `ResourceManager`

`ResourceManager`Uživatelské rozhraní příze běží na clusteru hlavnímu uzlu. Je k ní přistupované prostřednictvím webového uživatelského rozhraní Ambari. K zobrazení protokolů PŘÍZe použijte následující postup:

1. Ve webovém prohlížeči přejděte na `https://CLUSTERNAME.azurehdinsight.net` . Nahraďte CLUSTERNAME názvem clusteru HDInsight.

2. V seznamu služeb vlevo vyberte možnost **příze**.

    ![Je vybraná služba Apache Ambari nitě](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. V rozevíracím seznamu **Rychlé odkazy** vyberte jeden z hlavních uzlů clusteru a pak vyberte **`ResourceManager Log`** .

    ![Rychlé odkazy na Ambari nitě Apache](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    Zobrazí se seznam odkazů na záznamy PŘÍZe.

## <a name="next-steps"></a>Další kroky

* [Architektura Apache Hadoop v HDInsightu](hdinsight-hadoop-architecture.md)
* [Řešení potíží s Apache Hadoop YARN pomocí služby Azure HDInsight](hdinsight-troubleshoot-yarn.md)
