---
title: Protokoly aplikací Hadoop YARN přístup prostřednictvím kódu programu - Azure | Microsoft Docs
description: Přístup k aplikaci prostřednictvím kódu programu přihlásí clusteru Hadoop v HDInsight.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0198d6c9-7767-4682-bd34-42838cf48fc5
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: aab7865548c034cb550874c31977b05936dc45b9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>Protokoly YARN aplikace přístup na HDInsight se systémem Windows
Tento dokument vysvětluje, jak získat přístup v souborech protokolů YARN aplikace, které dokončily na cluster Hadoop založených na systému Windows v Azure HDInsight

> [!IMPORTANT]
> Informace v tomto dokumentu se vztahují pouze na clustery HDInsight se systémem Windows. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Informace o přístupu k YARN přihlásí clustery HDInsight se systémem Linux, najdete v části [YARN přístupu aplikace přihlásí systémem Linux Hadoop v HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
>


### <a name="prerequisites"></a>Požadavky
* Cluster HDInsight se systémem Windows.  V tématu [založené na Windows vytvoření Hadoop clusterů v HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="yarn-timeline-server"></a>YARN časová osa serveru
<a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN časová osa serveru</a> poskytuje obecné informace o dokončené aplikace také jako framework informace specifické pro aplikaci prostřednictvím dvou různých rozhraní. Zejména:

* Ukládání a načítání informací o obecná aplikace v clusterech prostředí HDInsight byl povolený s verzí 3.1.1.374 nebo vyšší.
* Součást aplikace konkrétní rozhraní informace časová osa serveru není aktuálně k dispozici v clusterech prostředí HDInsight.

Obecné informace o aplikacích v zahrnuje následující typy dat:

* ID aplikace, jedinečný identifikátor aplikace
* Uživatel, který aplikaci
* Informace o dokončení aplikace provedených pokusů
* Kontejnery používané jakýkoliv pokus o dané aplikaci

V clusterech služby HDInsight tyto informace jsou uloženy pomocí Správce prostředků Azure. Informace se ukládají do historie úložiště ve výchozím nastavení úložiště pro cluster. Tato obecná data na dokončené aplikace se dají získat pomocí rozhraní REST API:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>Protokoly YARN aplikací a
YARN podporuje více programovacích modelů podle oddělení Správa prostředků z plánování/monitorování aplikací. YARN používá globální konfiguraci *ResourceManager* (RM) uzlu na pracovním *NodeManagers* (NMs) a každou aplikaci *ApplicationMasters* (AMs). Každou aplikaci AM vyjedná prostředků (procesoru, paměti, disku, sítě) pro spuštění aplikace s RM. Správce prostředků funguje s NMs udělení těchto prostředků, které jsou poskytovány jako *kontejnery*. AM zodpovídá za sledování postupu kontejnery přiřazen RM. Aplikace může vyžadovat mnoho kontejnerů v závislosti na povaze aplikace.

* Každá aplikace může obsahovat více *pokusy o aplikace*. 
* Kontejnery jsou udělena pro konkrétní pokus aplikace. 
* Kontejner poskytuje kontext pro základní pracovní jednotkou. 
* Práci, kterou se provádí v kontextu kontejneru se provádí na jednoho pracovního uzlu, který byl přidělen kontejneru. 

Další informace najdete v tématu [YARN koncepty][YARN-concepts].

Protokoly aplikací (a protokoly přidružené kontejneru) jsou kritické v ladění problematické aplikací Hadoop. YARN poskytuje dobrý rozhraní pro shromažďování, agregace a ukládání protokolů aplikace pomocí [protokolu agregace] [ log-aggregation] funkce. Funkci agregace protokolu díky přístupu k protokoly aplikací determinističtější, jak slučuje protokolů v rámci všech kontejnerů v pracovním uzlu a ukládá je jako jeden agregované soubor protokolu pro pracovního uzlu na výchozí systém souborů po dokončení aplikace. Vaše aplikace může používat stovkami nebo tisíci kontejnery, ale jsou protokoly pro všechny kontejnery spustit na jednom pracovního uzlu agregovány do jednoho souboru, což vede k jeden soubor pro pracovního uzlu používá vaše aplikace. Agregace protokolu je ve výchozím nastavení v clusterech HDInsight povolené (verze 3.0 a novější), a naleznete agregované protokoly ve výchozím kontejneru daného clusteru v následujícím umístění:

    wasb:///app-logs/<user>/logs/<applicationId>

V tomto umístění *uživatele* je jméno uživatele, který aplikaci, a *applicationId* je jedinečný identifikátor aplikace přiřazené službou YARN RM.

Agregované protokoly nejsou přímo čitelný, jako jsou zapsány [TFile][T-file], [binární formát] [ binary-format] indexovat pomocí kontejneru. YARN poskytuje nástrojů příkazového řádku pro výpis tyto protokoly jako prostý text pro aplikace nebo kontejnerů, které vás zajímají. Tyto protokoly můžete zobrazit jako prostý text spuštěním jednoho z následujících YARN příkazy přímo na uzlech clusteru (po k němu připojuje prostřednictvím protokolu RDP):

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager uživatelského rozhraní
Rozhraní YARN ResourceManager běží na clusteru headnode a je přístupný prostřednictvím řídicí panel portálu Azure:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. V nabídce vlevo klikněte na tlačítko **Procházet**, klikněte na tlačítko **clustery HDInsight**, klikněte na cluster systému Windows, který chcete získat přístup do aplikačních protokolů YARN.
3. V horní nabídce klikněte na tlačítko **řídicí panel**. Zobrazí stránka na nový prohlížeč otevřít kartu názvem **HDInsight dotazu konzoly**.
4. Z **HDInsight dotazu konzoly**, klikněte na tlačítko **uživatelském rozhraní Yarn**.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
