---
title: Monitorování clusterů systému Hadoop v HDInsight pomocí nástroje Ambari API - Azure | Microsoft Docs
description: Použití rozhraní Apache Ambari API pro vytvoření, správě a monitoringu clusterů systému Hadoop. Operátor intuitivní nástroje a rozhraní API překonávají složitost systému Hadoop.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
editor: cgronlun
manager: jhubbard
ms.assetid: 052135b3-d497-4acc-92ff-71cee49356ff
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 04/07/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 3c8b1af3ad151a7a901150352202ab0b85aa6ec8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="monitor-hadoop-clusters-in-hdinsight-using-the-ambari-api"></a>Sledování clusterů Hadoop ve službě HDInsight pomocí rozhraní API Ambari
Naučte se monitorovat clusterů HDInsight pomocí Ambari API.

> [!NOTE]
> Informace v tomto článku je určen pro clustery HDInsight se systémem Windows, které poskytují jen pro čtení verzi Ambari REST API. V clusterech se systémem Linux naleznete v části [clusterů systému Hadoop spravovat pomocí nástroje Ambari](hdinsight-hadoop-manage-ambari.md).
> 
> 

## <a name="what-is-ambari"></a>Co je Ambari?
[Apache Ambari] [ ambari-home] se používá pro zřizování, správě a monitoringu clusterů systému Apache Hadoop. Obsahuje intuitivní sadu nástrojů pro operátory a výkonnou sadu rozhraní API, které překonávají složitost systému Hadoop a zjednodušují operace s clustery. Další informace o rozhraních API najdete v tématu [referenční dokumentace rozhraní API Ambari][ambari-api-reference]. 

HDInsight aktuálně podporuje pouze monitorování funkce Ambari. Clustery HDInsight verze 3.0 a 2.1 podporuje Ambari API 1.0. Tento článek se týká přístupu k rozhraní API Ambari v clusterech HDInsight verze 3.1 nebo 2.1. Klíčovým rozdílem mezi těmito dvěma je, že některé součásti změnily se zavedením nové funkce (například Server historie úlohy). 

**Požadavky**

Před zahájením tohoto kurzu musíte mít tyto položky:

* **Pracovní stanice s prostředím Azure PowerShell**.
* (Volitelné) [cURL][curl]. Ho Pokud chcete nainstalovat, najdete v části [cURL verze a soubory ke stažení][curl-download].
  
  > [!NOTE]
  > Když v systému Windows, použijte dvojité uvozovky značky místo jednoduchých uvozovek pro hodnoty možnosti použít příkaz cURL.
  > 
  > 
* **Cluster Azure HDInsight**. Pokyny o zřizování clusteru najdete v tématu [začněte používat HDInsight] [ hdinsight-get-started] nebo [zřizování clusterů HDInsight][hdinsight-provision]. Je třeba projít tento kurz následující data:
  
  | Vlastnost clusteru | Název proměnné Azure PowerShell | Hodnota | Popis |
  | --- | --- | --- | --- |
  |   Název clusteru HDInsight |$clusterName | |Název clusteru HDInsight. |
  |   Uživatelské jméno clusteru |$clusterUsername | |Vytvoření clusteru zadané clusteru uživatelské jméno. |
  |   Heslo clusteru |$clusterPassword | |Heslo uživatele clusteru. |

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


## <a name="jump-start"></a>Rychle začít s prací
Existuje několik způsobů monitorování clusterů HDInsight pomocí Ambari.

**Použití Azure Powershellu**

Následující skript prostředí Azure PowerShell získá informace o sledování úlohy MapReduce *v clusteru služby HDInsight 3.5.*  Klíčovým rozdílem je, že jsme pro vyžádání obsahu tyto podrobnosti z YARN služby (nikoli MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName/services/YARN/components/RESOURCEMANAGER"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

Následující skript prostředí PowerShell získá informace o sledování úloh MapReduce *v clusteru služby HDInsight 2.1*:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

Výstup je:

![Výstup Jobtracker][img-jobtracker-output]

**Použití cURL**

Následující příklad získá informace o clusteru pomocí cURL:

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

Výstup je:

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**Pro tuto verzi 8/10/2014**:

Při použití Ambari koncového bodu, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}" *název_hostitele* pole Vrátí plně kvalifikovaný název domény (FQDN) uzlu místo názvu hostitele. Před vydáním 8/10/2014, v tomto příkladu vrátil jednoduše "**headnode0**". Po vydání 8/10/2014 získat plně kvalifikovaný název domény "**headnode0. { ClusterDNS} gt; .azurehdinsight .net**", jak je uvedeno v předchozím příkladu. Tato změna vyžaduje usnadnění scénáře, kde je možné nasadit více typů clusteru (například HBase a Hadoop) v jednu virtuální síť (VNET). K tomu dojde, například při použití HBase jako back-end platformy pro Hadoop.

## <a name="ambari-monitoring-apis"></a>Ambari monitorování rozhraní API
Následující tabulka uvádí některé z nejběžnějších Ambari monitorování volání rozhraní API. Další informace o rozhraní API najdete v tématu [referenční dokumentace rozhraní API Ambari][ambari-api-reference].

| Volání rozhraní API monitorování | URI | Popis |
| --- | --- | --- |
| Získat clustery |`/api/v1/clusters` | |
| Získání informací o clusteru. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net` |clustery, služby, hostitelé |
| Získat služby |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services` |Mezi služby patří: hdfs, mapreduce |
| Získání informací o služby. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>` | |
| Získat součásti služby |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components` |HDFS: namenode, datanodeMapReduce: jobtracker; tasktracker |
| Získání informací o součásti. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>` |ServiceComponentInfo, metriky, hostitele součásti |
| Získat hostitele |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts` |headnode0 workernode0 |
| Získání informací o hostiteli. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>` | |
| Získat komponenty hostitele |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components` |namenode, resourcemanager |
| Získáte informace o součást hostitele. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>` |HostRoles, součást, hostitele, metriky |
| Získání konfigurace |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations` |Konfigurace typů: základní site, hdfs-site, mapred-site, hive-site |
| Získáte informace o konfiguraci. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>` |Konfigurace typů: základní site, hdfs-site, mapred-site, hive-site |

## <a name="next-steps"></a>Další kroky
Nyní jste se naučili použití Ambari monitorování volání rozhraní API. Další informace naleznete v tématu:

* [Správa clusterů HDInsight pomocí portálu Azure][hdinsight-admin-portal]
* [Správa clusterů HDInsight pomocí prostředí Azure PowerShell][hdinsight-admin-powershell]
* [Správa clusterů HDInsight pomocí rozhraní příkazového řádku][hdinsight-admin-cli]
* [Dokumentace prostředí HDInsight][hdinsight-documentation]
* [Začínáme s HDInsight][hdinsight-get-started]

[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: https://docs.microsoft.com/azure/hdinsight/
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
