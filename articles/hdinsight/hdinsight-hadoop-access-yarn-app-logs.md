---
title: Protokolům aplikací Hadoop YARN přístup prostřednictvím kódu programu – Azure
description: Přístup k protokolům aplikací prostřednictvím kódu programu na clusteru Hadoop v HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: da105be19f7d546e530298f87974fe7f3f78989f
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53012211"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-windows-based-hdinsight"></a>Přístup k Apache Hadoop YARN protokolům aplikací na HDInsight se systémem Windows
Tento dokument popisuje, jak získat přístup k protokolům pro [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) aplikace, které dokončení na Apache Hadoop založené na Windows cluster v Azure HDInsight

> [!IMPORTANT]
> Informace v tomto dokumentu se vztahuje pouze na clustery HDInsight se systémem Windows. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Informace o přístupu k YARN protokoly na clusterech HDInsight založených na Linuxu, naleznete v tématu [protokolům aplikací přístup Apache Hadoop YARN v linuxu Apache Hadoop v HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
>


### <a name="prerequisites"></a>Požadavky
* Cluster HDInsight se systémem Windows.  Zobrazit [systémem Windows vytvořit Apache Hadoop clusterů v HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="yarn-timeline-server"></a>YARN Timeline Server
<a href="http://hadoop.apache.org/docs/r2.4.1/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Apache Hadoop YARN Timeline Server</a> poskytuje obecné informace o hotových aplikacích, informace o aplikaci stejně jako specifické pro architekturu prostřednictvím dvou různých rozhraní. Zejména:

* Ukládání a načítání informací o obecná aplikace na clusterech HDInsight bylo povolené verze 3.1.1.374 nebo vyšší.
* Součást informace specifické pro architekturu aplikace serveru časová osa není aktuálně k dispozici v clusterech HDInsight.

Obecné informace o aplikacích zahrnuje následující druhy dat:

* ID aplikace, jedinečný identifikátor aplikace
* Uživatel, který spouští aplikaci
* Informace o pokusů o dokončení aplikace
* Kontejnery používané pokusy dané aplikace

Tyto informace jsou uloženy v clusterech služby HDInsight pomocí Azure Resource Manageru. Informace se uloží do historie úložiště ve výchozím nastavení úložiště pro cluster. Tato obecná data na dokončené aplikace se dají získat pomocí rozhraní REST API:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>Protokoly aplikací YARN a
YARN podporuje několik programovacích modelů oddělením správy prostředků z plánování a sledování aplikací. YARN používá globální *ResourceManager* (SV), uzel za pracovního procesu *NodeManagers* (NMs) a každou aplikaci *ApplicationMasters* (AMs). Každou aplikaci AM vyjedná prostředků (procesoru, paměti, disku a sítě) pro používání aplikace s vzdálené správy služby Správce prostředků funguje s NMs pro udělení těchto prostředků, které jsou poskytovány jako *kontejnery*. AM zodpovídá pro sledování pokroku kontejnery přiřadit RM. Aplikace můžou vyžadovat mnoho kontejnerů v závislosti na povaze aplikace.

* Každá aplikace může skládat z více *pokusy aplikací o*. 
* Kontejnery jsou přidělena konkrétní pokus o aplikace. 
* Kontejner poskytuje kontext pro základní pracovní jednotkou. 
* Práce, která se provádí v rámci kontejneru se provádí na kontejneru byl přidělen do jednoho pracovního uzlu. 

Další informace najdete v tématu [Apache Hadoop YARN koncepty][YARN-concepts].

Protokoly aplikací (a protokoly přiřazeným kontejnerem) jsou velmi důležité při ladění aplikací Hadoop jiných problematické. YARN poskytuje dobré architekturu pro shromažďování, shromažďování a ukládání protokolů aplikace pomocí [protokolu agregace] [ log-aggregation] funkce. Funkce agregace protokolu je přístup k protokolům aplikací deterministického, protože protokoly agreguje přes všechny kontejnery na pracovním uzlu a uloží je jako jeden agregované souboru protokolu na pracovní uzel na výchozí systém souborů po dokončení aplikace. Vaše aplikace může používat stovek nebo tisíců kontejnerů, ale protokolů pro všechny kontejnery, které běží na uzlu jeden pracovního procesu se agregují do jednoho souboru, což v jednom souboru na pracovní uzel používá vaše aplikace. Ve výchozím nastavení v clusterech HDInsight je povolené protokolu agregace (verze 3.0 a vyšší), a agregované protokoly najdete ve výchozím kontejneru vašeho clusteru v následujícím umístění:

    wasb:///app-logs/<user>/logs/<applicationId>

V tomto umístění *uživatele* je jméno uživatele, který spustil aplikaci, a *applicationId* je jedinečný identifikátor aplikace přiřazené službou YARN RM.

Agregované protokoly nejsou přímo čitelné, jako jsou zapsány [TFile][T-file], [binární formát] [ binary-format] indexovat pomocí kontejnerů. YARN poskytuje rozhraní příkazového řádku nástrojů pro výpis tyto protokoly jako prostý text pro aplikace nebo kontejnery, které vás zajímají. Tyto protokoly můžete zobrazit jako prostý text spuštěním jednoho z následujících YARN příkazy přímo na uzlech clusteru (po připojení k němu přes RDP):

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>Uživatelské rozhraní správce prostředků YARN
Uživatelské rozhraní správce prostředků YARN běží na hlavního uzlu clusteru a přístupné prostřednictvím řídicího panelu Azure portal:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. V nabídce vlevo klikněte na tlačítko **Procházet**, klikněte na tlačítko **clustery HDInsight**, klikněte na cluster se systémem Windows, který chcete získat přístup k protokolům aplikace YARN.
3. V horní nabídce klikněte na tlačítko **řídicí panel**. Zobrazí se stránka otevře na nové prohlížeče kartu **konzoly pro dotazy HDInsight**.
4. Z **konzoly pro dotazy HDInsight**, klikněte na tlačítko **uživatelského rozhraní Yarn**.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
