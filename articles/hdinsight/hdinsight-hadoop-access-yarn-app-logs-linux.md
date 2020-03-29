---
title: Přístup k protokolům aplikací Apache Hadoop YARN - Azure HDInsight
description: Zjistěte, jak přistupovat k protokolům aplikací YARN v clusteru HDInsight (Apache Hadoop) založeném na Linuxu pomocí příkazového řádku i webového prohlížeče.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/23/2020
ms.openlocfilehash: 2a7d71c6d751d4a48ec93f020e657a4d43114cfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76764384"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Přístup k protokolům aplikací Apache Hadoop YARN na Linuxu založeném na HDInsightu

Zjistěte, jak získat přístup k protokolům aplikací [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (Yet Another Resource Negotiator) v clusteru [Apache Hadoop](https://hadoop.apache.org/) v Azure HDInsight.

## <a name="what-is-apache-yarn"></a>Co je Apache YARN?

YARN podporuje více programovacích modelů[(Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) je jedním z nich) oddělením správy prostředků od plánování/monitorování aplikací. YARN používá globální *ResourceManager* (RM), *nodeManagers* pro pracující uzel (NMs) a pro každý aplikační *server* (AMs). AM pro aplikaci vyjedná prostředky (PROCESOR, paměť, disk, síť) pro spuštění aplikace s RM. Rm spolupracuje s NMs udělit tyto prostředky, které jsou poskytovány jako *kontejnery*. AM je zodpovědný za sledování průběhu kontejnerů, které jsou k němu přiřazeny RM. Aplikace může vyžadovat mnoho kontejnerů v závislosti na povaze aplikace.

Každá aplikace se může skládat z více *pokusů o aplikaci*. Pokud se aplikace nezdaří, může být opakována jako nový pokus. Každý pokus běží v kontejneru. V jistém smyslu kontejner poskytuje kontext pro základní jednotky práce prováděné aplikací YARN. Všechny práce, které se provádí v rámci kontejneru se provádí na jeden pracovní uzel, na kterém byl přidělen kontejneru. Viz [Hadoop: Psaní YARN aplikace](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html), nebo [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) pro další referenci.

Chcete-li změnit velikost clusteru tak, aby podporoval větší propustnost zpracování, můžete [clustery](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters) [přizpůsobit](hdinsight-autoscale-clusters.md) pomocí několika různých jazyků .

## <a name="yarn-timeline-server"></a>Server časové osy yarn

[Apache Hadoop YARN Timeline Server](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) poskytuje obecné informace o dokončených aplikacích

YARN Timeline Server obsahuje následující typ dat:

* ID aplikace, jedinečný identifikátor aplikace
* Uživatel, který aplikaci spustil
* Informace o pokusech o vyplnění žádosti
* Kontejnery používané daným pokusem o aplikaci

## <a name="yarn-applications-and-logs"></a>Aplikace a protokoly YARN

YARN podporuje více programovacích modelů[(Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) je jedním z nich) oddělením správy prostředků od plánování/monitorování aplikací. YARN používá globální *ResourceManager* (RM), *nodeManagers* pro pracující uzel (NMs) a pro každý aplikační *server* (AMs). AM pro aplikaci vyjedná prostředky (PROCESOR, paměť, disk, síť) pro spuštění aplikace s RM. Rm spolupracuje s NMs udělit tyto prostředky, které jsou poskytovány jako *kontejnery*. AM je zodpovědný za sledování průběhu kontejnerů, které jsou k němu přiřazeny RM. Aplikace může vyžadovat mnoho kontejnerů v závislosti na povaze aplikace.

Každá aplikace se může skládat z více *pokusů o aplikaci*. Pokud se aplikace nezdaří, může být opakována jako nový pokus. Každý pokus běží v kontejneru. V jistém smyslu kontejner poskytuje kontext pro základní jednotky práce prováděné aplikací YARN. Všechny práce, které se provádí v rámci kontejneru se provádí na jeden pracovní uzel, na kterém byl přidělen kontejneru. Viz [Apache Hadoop YARN Koncepty](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html) pro další reference.

Protokoly aplikací (a přidružené protokoly kontejnerů) jsou důležité při ladění problematických aplikací Hadoop. YARN poskytuje pěkný rámec pro shromažďování, agregaci a ukládání protokolů aplikací s funkcí [agregace protokolů.](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) Funkce Agregace protokolu umožňuje přístup k protokolům aplikací více deterministický. Agreguje protokoly napříč všemi kontejnery na pracovní uzel a ukládá je jako jeden agregovaný soubor protokolu na pracovní uzel. Protokol je po dokončení aplikace uložen ve výchozím systému souborů. Vaše aplikace může používat stovky nebo tisíce kontejnerů, ale protokoly pro všechny kontejnery spuštěné na jednom pracovním uzlu jsou vždy agregovány do jednoho souboru. Takže je tu pouze 1 protokol na pracovní uzel používá vaše aplikace. Agregace protokolů je ve výchozím nastavení povolena v clusterech HDInsight verze 3.0 a vyšší. Agregované protokoly jsou umístěny ve výchozím úložišti pro cluster. Následující cesta je cesta HDFS k protokolům:

```
/app-logs/<user>/logs/<applicationId>
```

V cestě `user` je jméno uživatele, který spustil aplikaci. Jedná `applicationId` se o jedinečný identifikátor přiřazený aplikaci službou YARN RM.

Agregované protokoly nejsou přímo čitelné, protože jsou zapsány v [TFile](https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf), [binární formát](https://issues.apache.org/jira/browse/HADOOP-3315) indexován kontejnerem. Pomocí protokolů YARN ResourceManager nebo nástrojů cli zobrazit tyto protokoly jako prostý text pro aplikace nebo kontejnery zájmu.

## <a name="yarn-logs-in-an-esp-cluster"></a>Protokoly příze v clusteru ESP

Dvě konfigurace musí být přidány `mapred-site` do vlastní v Ambari.

1. Z webového prohlížeče `https://CLUSTERNAME.azurehdinsight.net`přejděte `CLUSTERNAME` na , kde je název clusteru.

1. Z ui Ambari přejděte na **MapReduce2** > **Configs** > **Advanced** > Custom**mapred-site**.

1. Přidejte *jednu* z následujících sad vlastností:

    **Sada 1**

    ```
    mapred.acls.enabled=true
    mapreduce.job.acl-view-job=*
    ```

    **Sada 2**

    ```
    mapreduce.job.acl-view-job=<user1>,<user2>,<user3>
    ```

1. Uložte změny a restartujte všechny služby obsahující tuto chybu.

## <a name="yarn-cli-tools"></a>Nástroje CLI příze

1. Pomocí [příkazu ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke clusteru. Upravte níže uvedený příkaz nahrazením názvu clusteru názvem clusteru a zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Seznam všech ID aplikace aktuálně spuštěných aplikací příze s následujícím příkazem:

    ```bash
    yarn top
    ```

    Všimněte si ID `APPLICATIONID` aplikace ze sloupce, jehož protokoly mají být staženy.

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

    Zadejte &lt;applicationId &lt;>, uživatel,kdo začal-aplikace &lt;>, containerId &lt;> a worker-node-address> informace při spuštění těchto příkazů.

### <a name="other-sample-commands"></a>Další ukázkové příkazy

1. Stáhněte si protokoly kontejnerů příze pro všechny předlohy aplikací pomocí níže uvedeného příkazu. Tím vytvoříte soubor `amlogs.txt` protokolu pojmenovaný v textovém formátu.

    ```bash
    yarn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

1. Stáhněte si protokoly kontejneru příze pouze pro nejnovější hlavní aplikační server s následujícím příkazem:

    ```bash
    yarn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

1. Stáhněte si protokoly kontejnerů YARN pro první dva hlavní aplikace s následujícím příkazem:

    ```bash
    yarn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

1. Stáhněte si všechny protokoly kontejneru příze pomocí následujícího příkazu:

    ```bash
    yarn logs -applicationId <application_id> > logs.txt
    ```

1. Stáhněte si protokol kontejneru příze pro konkrétní kontejner s následujícím příkazem:

    ```bash
    yarn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

## <a name="yarn-resourcemanager-ui"></a>UI Správce prostředků yARN

Ui YARN ResourceManager běží na hlavovém uzlu clusteru. Je přístupný prostřednictvím webového uživatelského uživatelského nastavení Ambari. Pomocí následujících kroků zobrazte protokoly YARN:

1. Ve webovém prohlížeči `https://CLUSTERNAME.azurehdinsight.net`přejděte na . Nahraďte CLUSTERNAME názvem clusteru HDInsight.

2. Ze seznamu služeb vlevo vyberte **YARN**.

    ![Apache Ambari Příze služba vybrána](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. V rozevíracím seznamu **Rychlé odkazy** vyberte jeden z hlavních uzlů clusteru a pak vyberte **Protokol ResourceManager**.

    ![Apache Ambari Příze rychlé odkazy](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    Zobrazí se seznam odkazů na protokoly YARN.
