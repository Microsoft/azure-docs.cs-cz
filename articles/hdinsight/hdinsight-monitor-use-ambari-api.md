---
title: Monitorování clusterů systému Hadoop v HDInsight pomocí rozhraní Ambari API – Azure
description: Pro tvorbu, správu a monitorování clusterů systému Hadoop pomocí rozhraní API Apache Ambari. Operátor intuitivní nástroje a rozhraní API překonávají složitost systému Hadoop.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/07/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: 23da477fc24eaea4a6f987f13ad055022d2ba032
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43108633"
---
# <a name="monitor-hadoop-clusters-in-hdinsight-using-the-ambari-api"></a>Sledování clusterů Hadoop ve službě HDInsight pomocí rozhraní API Ambari
Další informace o monitorování clusterů HDInsight pomocí rozhraní Ambari API.

> [!NOTE]
> Informace v tomto článku je primárně pro clustery HDInsight se systémem Windows, které poskytují verzi rozhraní Ambari REST API jen pro čtení. Pro clustery založené na Linuxu najdete v článku [spravovat Hadoop clusterů pomocí nástroje Ambari](hdinsight-hadoop-manage-ambari.md).
> 
> 

## <a name="what-is-ambari"></a>Co je Ambari?
[Apache Ambari] [ ambari-home] slouží k zřizování, správě a monitorování clusterů systému Apache Hadoop. Obsahuje intuitivní sadu nástrojů pro operátory a výkonnou sadu rozhraní API, které překonávají složitost systému Hadoop a zjednodušují operace s clustery. Další informace o rozhraních API najdete v tématu [Reference k rozhraní API Ambari][ambari-api-reference]. 

HDInsight aktuálně podporuje pouze sledování funkce Ambari. Ambari API 1.0 je podporováno v clusterech HDInsight verze 3.0 a 2.1. Tento článek se týká přístupu k rozhraní API Ambari v clusterech HDInsight verze 3.1 a 2.1. Klíčovým rozdílem mezi těmito dvěma je, že některé součásti se změnila s zavedení nových funkcí (jako je například Server historie úloh). 

**Požadavky**

Před zahájením tohoto kurzu musíte mít tyto položky:

* **Pracovní stanice s prostředím Azure PowerShell**.
* (Volitelné) [cURL][curl]. K jeho instalaci, naleznete v tématu [cURL vydané verze a soubory ke stažení][curl-download].
  
  > [!NOTE]
  > Při použití příkazu cURL ve Windows, použijte dvojité uvozovky místo jednoho uvozovky u hodnot možností.
  > 
  > 
* **Cluster Azure HDInsight**. Pokyny týkající se zřizování clusteru, naleznete v tématu [začněte používat HDInsight] [ hdinsight-get-started] nebo [clusterů HDInsight zřízení][hdinsight-provision]. Potřebujete absolvovat kurz následující údaje:
  
  | Vlastnost clusteru | Název proměnné Azure Powershellu | Hodnota | Popis |
  | --- | --- | --- | --- |
  |   Název clusteru HDInsight |$clusterName | |Název vašeho clusteru HDInsight. |
  |   Uživatelské jméno clusteru |$clusterUsername | |Uživatelské jméno clusteru zadán, kdy byla vytvořena clusteru. |
  |   Heslo clusteru |$clusterPassword | |Heslo uživatele clusteru. |

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


## <a name="jump-start"></a>Rychlé zprovoznění
Existuje několik způsobů monitorování clusterů HDInsight pomocí Ambari.

**Použití Azure Powershellu**

Následující skript prostředí Azure PowerShell získá informace o úlohách sledovacího modulu MapReduce *v clusteru služby HDInsight 3.5.*  Klíčovým rozdílem je, že jsme o přijetí změn tyto podrobnosti ze YARN služby (místo MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName/services/YARN/components/RESOURCEMANAGER"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

Následující skript Powershellu získá informace o sledování úloh MapReduce *v clusteru služby HDInsight 2.1*:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

Výstup bude:

![Jobtracker výstupu][img-jobtracker-output]

**Použití cURL**

Následující příklad získá informace o clusteru pomocí příkazu cURL:

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

Výstup bude:

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

**Verze 8/10/2014**:

Při použití koncového bodu Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}" *název_hostitele* pole Vrátí plně kvalifikovaný název domény (FQDN) uzlu místo názvu hostitele. Před vydáním 8/10/2014 v tomto příkladu vrátí jednoduše "**headnode0**". Po vydání 8/10/2014 získat plně kvalifikovaný název "**headnode0. { ClusterDNS} gt; .azurehdinsight .net**", jak je znázorněno v předchozím příkladu. Tato změna byla nutná k usnadnění scénářů, kde je možné nasadit více typů clusteru (jako je například HBase a Hadoop) v jedné virtuální síti (VNET). V takovém případě třeba při použití HBase jako back endovou platformu pro Hadoop.

## <a name="ambari-monitoring-apis"></a>Monitorování rozhraní API Ambari
V následující tabulce jsou uvedeny některé nejběžnější Ambari, monitorování volání rozhraní API. Další informace o rozhraní API najdete v tématu [Reference k rozhraní API Ambari][ambari-api-reference].

| Monitorování rozhraní API volání | URI | Popis |
| --- | --- | --- |
| Získat clustery |`/api/v1/clusters` | |
| Získání informací o clusteru. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net` |clustery, služby, hostitelé |
| Získejte služby |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services` |Mezi tyto služby patří: hdfs, mapreduce |
| Získání informací o služby. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>` | |
| Získat součásti služby |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components` |HDFS: namenode, datanodeMapReduce: jobtracker; tasktracker |
| Získání informací o součást. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>` |ServiceComponentInfo komponenty hostitele, metrik |
| Získání hostitelů |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts` |headnode0 workernode0 |
| Získání informací o hostiteli. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>` | |
| Získat komponentami |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components` |namenode, správce prostředků |
| Získejte informace o hostiteli. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>` |HostRoles, komponenty, hostitele, metrik |
| Získání konfigurace |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations` |Konfigurace typů: základního webu, hdfs-site, mapred web, server hive |
| Získejte informace o konfiguraci. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>` |Konfigurace typů: základního webu, hdfs-site, mapred web, server hive |

## <a name="next-steps"></a>Další kroky
Nyní jste se naučili, jak použít monitorování volání rozhraní API Ambari. Další informace naleznete v tématu:

* [Správa clusterů HDInsight pomocí webu Azure portal][hdinsight-admin-portal]
* [Správa clusterů HDInsight pomocí Azure Powershellu][hdinsight-admin-powershell]
* [Správa clusterů HDInsight pomocí rozhraní příkazového řádku][hdinsight-admin-cli]
* [Dokumentace ke službě HDInsight][hdinsight-documentation]
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
