---
title: Protokoly aplikací Hadoop YARN přístup na HDInsight se systémem Linux - Azure | Microsoft Docs
description: Zjistěte, jak pro přístup k protokoly YARN aplikací na clusteru HDInsight se systémem Linux (Hadoop) pomocí příkazového řádku a webový prohlížeč.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: 3ec08d20-4f19-4a8e-ac86-639c04d2f12e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: larryfr
ms.openlocfilehash: 67732b3a4c63408d8fc6ab5ed75b40a0eb043167
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31392234"
---
# <a name="access-yarn-application-logs-on-linux-based-hdinsight"></a>Protokoly YARN aplikace přístup na HDInsight se systémem Linux

Zjistěte, jak získat přístup v protokolech aplikací YARN (ještě jiný prostředek Vyjednavač) v clusteru Hadoop v prostředí Azure HDInsight.

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují clusteru služby HDInsight, který používá Linux. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Správa verzí komponenty HDInsight](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="YARNTimelineServer"></a>YARN časová osa serveru

[YARN časová osa serveru](http://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) poskytuje obecné informace o dokončené aplikace a informace o konkrétní rozhraní aplikaci prostřednictvím dvou různých rozhraní. Zejména:

* Ukládání a načítání informací o obecná aplikace v clusterech prostředí HDInsight byl povolený s verzí 3.1.1.374 nebo vyšší.
* Součást aplikace konkrétní rozhraní informace časová osa serveru není aktuálně k dispozici v clusterech prostředí HDInsight.

Obecné informace o aplikacích v patří následující typy dat:

* ID aplikace, jedinečný identifikátor aplikace
* Uživatel, který aplikaci
* Informace o dokončení aplikace provedených pokusů
* Kontejnery používané jakýkoliv pokus o dané aplikaci

## <a name="YARNAppsAndLogs"></a>Protokoly YARN aplikací a

YARN podporuje více programovacích modelů (MapReduce právě jeden z nich) tím, že odpojí Správa prostředků z plánování/monitorování aplikací. YARN používá globální konfiguraci *ResourceManager* (RM) uzlu na pracovním *NodeManagers* (NMs) a každou aplikaci *ApplicationMasters* (AMs). Každou aplikaci AM vyjedná prostředků (procesoru, paměti, disku, sítě) pro spuštění aplikace s RM. Správce prostředků funguje s NMs udělení těchto prostředků, které jsou poskytovány jako *kontejnery*. AM zodpovídá za sledování postupu kontejnery přiřazen RM. Aplikace může vyžadovat mnoho kontejnerů v závislosti na povaze aplikace.

Každá aplikace může obsahovat více *pokusy o aplikace*. Pokud aplikace selže, může pokus jako nový pokus o. Každý pokus o spuštění v kontejneru. V tom smyslu kontejner poskytuje kontext pro základní jednotkou úlohy prováděné YARN aplikace. Všechny práci, kterou se provádí v kontextu kontejneru se provádí na jednoho pracovního uzlu, na kterém byl přidělen kontejneru. V tématu [YARN koncepty] [ YARN-concepts] pro odkaz na další.

Protokoly aplikací (a protokoly přidružené kontejneru) jsou kritické v ladění problematické aplikací Hadoop. YARN poskytuje dobrý rozhraní pro shromažďování, agregace a ukládání protokolů aplikace pomocí [protokolu agregace] [ log-aggregation] funkce. Díky funkci agregace protokolu přístupem protokoly aplikací více deterministický. Ho slučuje protokolů v rámci všech kontejnerů v pracovním uzlu a ukládá je jako jeden soubor protokolu agregované pro pracovního uzlu. V protokolu jsou uloženy na výchozí systém souborů po dokončení aplikace. Vaše aplikace může používat stovkami nebo tisíci kontejnery, ale protokoly pro všechny kontejnery spustit na jednom pracovního uzlu agregovány vždy pro jeden soubor. Proto není pouze 1 protokolu za pracovního uzlu používá vaše aplikace. Agregace protokolu je povoleno ve výchozím nastavení v clusterech HDInsight verze 3.0 nebo novějším. Agregované protokoly jsou umístěny ve výchozím nastavení úložiště pro cluster. Následující cesta je cesta HDFS do protokolů:

    /app-logs/<user>/logs/<applicationId>

V cestě `user` je jméno uživatele, který aplikaci. `applicationId` Je jedinečný identifikátor přiřadila YARN RM. k aplikaci

Agregované protokoly nejsou přímo čitelný, jako jsou zapsány [TFile][T-file], [binární formát] [ binary-format] indexovat pomocí kontejneru. Chcete-li zobrazit tyto protokoly jako prostý text pro aplikace nebo kontejnerů, které vás zajímají použijte protokoly YARN ResourceManager nebo nástrojů příkazového řádku.

## <a name="yarn-cli-tools"></a>YARN rozhraní příkazového řádku nástroje

Při použití nástroje pro YARN rozhraní příkazového řádku, je nutné se nejprve připojit ke clusteru HDInsight pomocí protokolu SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Tyto protokoly můžete zobrazit jako prostý text spuštěním jednoho z následujících příkazů:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Zadejte &lt;applicationId >, &lt;uživatel kdo spuštění--application >, &lt;identifikátor containerId >, a &lt;adresa pracovní node > informace při spuštění těchto příkazů.

## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager uživatelského rozhraní

Rozhraní YARN ResourceManager běží na clusteru headnode. Je přístupný prostřednictvím webového uživatelského rozhraní Ambari. Pokud chcete zobrazit protokoly YARN použijte následující kroky:

1. Ve webovém prohlížeči, přejděte na https://CLUSTERNAME.azurehdinsight.net. Nahraďte název clusteru s názvem clusteru HDInsight.
2. V seznamu služeb na levé straně vyberte **YARN**.

    ![Yarn služby vybrané](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)
3. Z **rychlé odkazy** rozevíracího seznamu, vyberte jeden z hlavních uzlech clusteru a pak vyberte **ResourceManager protokolu**.

    ![Yarn rychlé odkazy](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)

    Zobrazí se seznam odkazů na protokoly YARN.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
