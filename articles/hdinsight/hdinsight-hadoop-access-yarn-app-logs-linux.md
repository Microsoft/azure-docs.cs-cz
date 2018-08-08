---
title: Protokolům aplikací Hadoop YARN přístupu na základě Linux HDInsight – Azure
description: Zjistěte, jak získat přístup k protokolům aplikace YARN na clusteru Linuxovým systémem HDInsight (Hadoop) pomocí příkazového řádku a webový prohlížeč.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: jasonh
ms.openlocfilehash: 0aa8d76ca97789844482d2b8ad7212c2f61a8ab8
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591799"
---
# <a name="access-yarn-application-logs-on-linux-based-hdinsight"></a>Protokoly aplikací YARN přístup na Linuxovým systémem HDInsight

Zjistěte, jak získat přístup k protokolům aplikací YARN (zatím jiné Resource Negotiator) v clusteru Hadoop v Azure HDInsight.

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují cluster HDInsight s Linuxem. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Správa verzí komponenty HDInsight](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="YARNTimelineServer"></a>YARN Timeline Server

[YARN Timeline Server](http://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) poskytuje obecné informace o hotových aplikacích a informace specifické pro architekturu aplikací prostřednictvím dvou různých rozhraní. Zejména:

* Ukládání a načítání informací o obecná aplikace na clusterech HDInsight bylo povolené verze 3.1.1.374 nebo vyšší.
* Součást informace specifické pro architekturu aplikace serveru časová osa není aktuálně k dispozici v clusterech HDInsight.

Obecné informace o aplikacích zahrnuje následující typ dat:

* ID aplikace, jedinečný identifikátor aplikace
* Uživatel, který spouští aplikaci
* Informace o pokusů o dokončení aplikace
* Kontejnery používané pokusy dané aplikace

## <a name="YARNAppsAndLogs"></a>Protokoly aplikací YARN a

YARN podporuje několik programovacích modelů (MapReduce právě jeden z nich) tím, že odpojí správy prostředků z plánování a sledování aplikací. YARN používá globální *ResourceManager* (SV), uzel za pracovního procesu *NodeManagers* (NMs) a každou aplikaci *ApplicationMasters* (AMs). Každou aplikaci AM vyjedná prostředků (procesoru, paměti, disku a sítě) pro používání aplikace s vzdálené správy služby Správce prostředků funguje s NMs pro udělení těchto prostředků, které jsou poskytovány jako *kontejnery*. AM zodpovídá pro sledování pokroku kontejnery přiřadit RM. Aplikace můžou vyžadovat mnoho kontejnerů v závislosti na povaze aplikace.

Každá aplikace může skládat z více *pokusy aplikací o*. Pokud se aplikaci nepodaří, může pokus o jako nový pokus o. Každý pokus o spuštění v kontejneru. V tom smyslu kontejner poskytuje kontext pro základní jednotku práce prováděné aplikací YARN. Veškerá práce, která se provádí v rámci kontejneru se provádí na jednu pracovní uzel, na kterém byl přidělen kontejneru. Zobrazit [YARN koncepty] [ YARN-concepts] pro odkaz na další.

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
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
