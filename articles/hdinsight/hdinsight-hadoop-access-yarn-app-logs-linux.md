---
title: Přístup k protokolům aplikace Apache Hadoop nitě v HDInsight založeném na systému Linux – Azure
description: Přečtěte si, jak získat přístup k protokolům aplikací PŘÍZ na clusteru HDInsight se systémem Linux (Apache Hadoop) pomocí příkazového řádku i webového prohlížeče.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/22/2018
ms.openlocfilehash: 6eb24e85d1d7ffa4f3377d4c2fe8b168303c15f0
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091507"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Přístup k protokolům aplikace Apache Hadoop nitě v HDInsight se systémem Linux

Naučte se, jak získat přístup k protokolům pro [Apache HADOOP přízi](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (ještě další aplikace pro vyjednávání prostředků) v clusteru [Apache Hadoop](https://hadoop.apache.org/) ve službě Azure HDInsight.

## <a name="YARNTimelineServer"></a>Server časové osy PŘÍZe

[Server časové osy Apache HADOOP příze](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) poskytuje obecné informace o dokončených aplikacích.

Server časové osy PŘÍZe obsahuje následující typy dat:

* ID aplikace, jedinečný identifikátor aplikace
* Uživatel, který spustil aplikaci
* Informace o pokusůch o dokončení aplikace
* Kontejnery používané jakýmkoli pokusy o aplikace

## <a name="YARNAppsAndLogs"></a>PŘÍZe aplikace a protokoly

PŘÍZe podporuje více programovacích modelů ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) jednu z nich) tím, že odpojuje správu prostředků od plánování a monitorování aplikací. K PŘÍZi se používá globální *Správce prostředků* (RM), *NodeManagers* (NMs) a per-Application *ApplicationMasters* (AMs). Jednotlivé aplikace vyjednávají prostředky (CPU, paměť, disk, síť) pro spuštění aplikace s nástrojem RM. RM spolupracuje s NMs pro udělení těchto prostředků, které se udělují jako *kontejnery*. Zodpovídá za sledování průběhu kontejnerů, které mu jsou přiřazeny pomocí služby RM. Aplikace může vyžadovat mnoho kontejnerů v závislosti na povaze aplikace.

Každá aplikace se může skládat z několika *pokusů o aplikace*. Pokud dojde k chybě aplikace, může se pokusit o nový pokus. Každý pokus se spustí v kontejneru. Ve smyslu kontejner poskytuje kontext pro základní pracovní jednotku, kterou provádí aplikace PŘÍZe. Veškerá práce, která je provedena v rámci kontextu kontejneru, je prováděna v jednom pracovním uzlu, na kterém byl kontejner přidělen. Další informace najdete v tématu [Apache Hadoop koncepce příze](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html) .

Protokoly aplikací (a přidružené protokoly kontejnerů) jsou důležité při ladění problematických aplikací Hadoop. PŘÍZe nabízí dobrý rámec pro shromažďování, agregaci a ukládání protokolů aplikací pomocí funkce [agregace protokolů](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) . Funkce agregace protokolu usnadňuje přístup k protokolům aplikací. Agreguje protokoly mezi všemi kontejnery v pracovním uzlu a ukládá je jako jeden agregovaný soubor protokolu pro každý pracovní uzel. Protokol je po dokončení aplikace uložen ve výchozím systému souborů. Vaše aplikace může používat stovky nebo tisíce kontejnerů, ale protokoly pro všechny kontejnery spuštěné v jednom pracovním uzlu jsou vždycky agregované do jednoho souboru. Proto je na jeden pracovní uzel, který používá vaše aplikace, jenom 1 protokol. Agregace protokolů je ve výchozím nastavení povolená v clusterech HDInsight verze 3,0 a vyšší. Agregované protokoly jsou umístěny ve výchozím úložišti clusteru. Následující cesta je cesta HDFS k protokolům:

    /app-logs/<user>/logs/<applicationId>

V cestě `user` je název uživatele, který aplikaci spustil. `applicationId` Je jedinečný identifikátor přiřazený k aplikaci pomocí příze RM.

Agregované protokoly nejsou přímo čitelné, protože jsou napsány v [TFile][T-file] [binárním formátu][binary-format] indexovaném kontejnerem. Použijte protokoly PŘÍZe ResourceManager nebo nástroje CLI k zobrazení těchto protokolů jako prostý text pro aplikace nebo kontejnery, které vás zajímají.

## <a name="yarn-cli-tools"></a>Nástroje rozhraní příkazového řádku PŘÍZ

Chcete-li použít nástroje rozhraní příkazového řádku PŘÍZ, musíte se nejprve připojit ke clusteru HDInsight pomocí protokolu SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Tyto protokoly můžete zobrazit jako prostý text spuštěním jednoho z následujících příkazů:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Zadejte > &lt; &lt;ApplicationId, User-Started-the-Application >, containerId > a &lt;Work-Node-Address > informace při spuštění těchto příkazů. &lt;

## <a name="yarn-resourcemanager-ui"></a>Uživatelské rozhraní Správce prostředků PŘÍZe

Uživatelské rozhraní Správce prostředků PŘÍZe běží na clusteru hlavnímu uzlu. Je k ní přistupované prostřednictvím webového uživatelského rozhraní Ambari. K zobrazení protokolů PŘÍZe použijte následující postup:

1. Ve webovém prohlížeči přejděte na https://CLUSTERNAME.azurehdinsight.net. Položku název_clusteru nahraďte názvem vašeho clusteru HDInsight.
2. V seznamu služeb vlevo vyberte možnost **příze**.

    ![Je vybraná služba Apache Ambari nitě](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. V rozevíracím seznamu **Rychlé odkazy** vyberte jeden z hlavních uzlů clusteru a pak vyberte **protokol ResourceManager**.

    ![Rychlé odkazy na Ambari nitě Apache](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    Zobrazí se seznam odkazů na záznamy PŘÍZe.

[YARN-timeline-server]:https://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
