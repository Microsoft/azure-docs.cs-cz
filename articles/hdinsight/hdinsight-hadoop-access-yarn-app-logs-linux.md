---
title: Přístup aplikací Apache Hadoop YARN přihlášení založené na Linuxu HDInsight – Azure
description: Zjistěte, jak získat přístup k protokolům aplikace YARN na cluster HDInsight založených na Linuxu (Apache Hadoop) pomocí příkazového řádku a webový prohlížeč.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.openlocfilehash: e6f778016f4f465cd438b74dff95cb1b37c42d79
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015666"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Přístup aplikací Apache Hadoop YARN přihlášení založené na Linuxu HDInsight

Zjistěte, jak získat přístup k protokolům pro [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (zatím jiné Resource Negotiator) žádosti o [Apache Hadoop](https://hadoop.apache.org/) clusteru v Azure HDInsight.

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují cluster HDInsight s Linuxem. Linux je pouze operační systém používaný v HDInsight verze 3.6 nebo novější. Další informace najdete v tématu [Správa verzí komponenty HDInsight](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="YARNTimelineServer"></a>YARN Timeline Server

[Apache Hadoop YARN Timeline Server](http://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) poskytuje obecné informace o hotových aplikacích

YARN Timeline Server zahrnuje následující typ dat:

* ID aplikace, jedinečný identifikátor aplikace
* Uživatel, který spouští aplikaci
* Informace o pokusů o dokončení aplikace
* Kontejnery používané pokusy dané aplikace

## <a name="YARNAppsAndLogs"></a>Protokoly aplikací YARN a

YARN podporuje několik programovacích modelů ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) právě jeden z nich) tím, že odpojí správy prostředků z plánování a sledování aplikací. YARN používá globální *ResourceManager* (SV), uzel za pracovního procesu *NodeManagers* (NMs) a každou aplikaci *ApplicationMasters* (AMs). Každou aplikaci AM vyjedná prostředků (procesoru, paměti, disku a sítě) pro používání aplikace s vzdálené správy služby Správce prostředků funguje s NMs pro udělení těchto prostředků, které jsou poskytovány jako *kontejnery*. AM zodpovídá pro sledování pokroku kontejnery přiřadit RM. Aplikace můžou vyžadovat mnoho kontejnerů v závislosti na povaze aplikace.

Každá aplikace může skládat z více *pokusy aplikací o*. Pokud se aplikaci nepodaří, může pokus o jako nový pokus o. Každý pokus o spuštění v kontejneru. V tom smyslu kontejner poskytuje kontext pro základní jednotku práce prováděné aplikací YARN. Veškerá práce, která se provádí v rámci kontejneru se provádí na jednu pracovní uzel, na kterém byl přidělen kontejneru. Zobrazit [Apache Hadoop YARN koncepty] [ YARN-concepts] pro odkaz na další.

Protokoly aplikací (a protokoly přiřazeným kontejnerem) jsou velmi důležité při ladění aplikací Hadoop jiných problematické. YARN poskytuje dobré architekturu pro shromažďování, shromažďování a ukládání protokolů aplikace pomocí [protokolu agregace] [ log-aggregation] funkce. Díky funkci agregace protokolu přístup k protokolům aplikací deterministického. Protokoly agreguje přes všechny kontejnery na pracovním uzlu a uloží je jako jeden soubor protokolu agregované podle počtu uzlů pracovního procesu. V protokolu je uložen na výchozí systém souborů po dokončení aplikace. Vaše aplikace může používat stovek nebo tisíců kontejnerů, ale protokolů pro všechny kontejnery, které běží na uzlu jeden pracovního procesu se vždy agregují do jednoho souboru. Je proto pouze 1 protokolu na pracovní uzel používaný vaší aplikací. Agregace protokolu je povolené ve výchozím nastavení verze clustery HDInsight 3.0 a vyšší. Agregované protokoly jsou umístěny ve výchozím nastavení úložiště pro cluster. Následující cesta je cesta HDFS do protokolů:

    /app-logs/<user>/logs/<applicationId>

V cestě `user` je jméno uživatele, který spustil aplikaci. `applicationId` Je jedinečný identifikátor přidružených k aplikaci pomocí YARN RM.

Agregované protokoly nejsou přímo čitelné, jako jsou zapsány [TFile][T-file], [binární formát] [ binary-format] indexovat pomocí kontejnerů. Chcete-li zobrazit tyto protokoly jako prostý text pro aplikace nebo kontejnery, které vás zajímají použijte protokoly YARN ResourceManager nebo nástroje rozhraní příkazového řádku.

## <a name="yarn-cli-tools"></a>Nástroje rozhraní příkazového řádku YARN

Chcete-li využívat nástroje příkazového řádku YARN, se musí se poprvé připojíte ke clusteru HDInsight pomocí SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Tyto protokoly můžete zobrazit jako prostý text spuštěním jednoho z následujících příkazů:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Zadejte &lt;applicationId >, &lt;uživatele kdo spuštění--aplikace >, &lt;containerId >, a &lt;adresa uzlu pracovního procesu > informace při spuštění těchto příkazů.

## <a name="yarn-resourcemanager-ui"></a>Uživatelské rozhraní správce prostředků YARN

Uživatelské rozhraní správce prostředků YARN běží na hlavního uzlu clusteru. Je přístupný prostřednictvím webového uživatelského rozhraní Ambari. Chcete-li zobrazit protokoly YARN, postupujte následovně:

1. Ve webovém prohlížeči přejděte na https://CLUSTERNAME.azurehdinsight.net. CLUSTERNAME nahraďte názvem vašeho clusteru HDInsight.
2. V seznamu služeb na levé straně vyberte **YARN**.

    ![Vybraná služba yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)
3. Z **rychlé odkazy** rozevíracím seznamu vyberte jednu z hlavní uzly clusteru a pak vyberte **ResourceManager protokolu**.

    ![Rychlé odkazy yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)

    Zobrazí se seznam odkazů na protokoly YARN.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
