---
title: Vytváření clusterů HBA v Virtual Network – Azure
description: Začněte používat HBA ve službě Azure HDInsight. Naučte se vytvářet clustery HDInsight HBA na Azure Virtual Network.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: c8fc7c931f31e1ff58f41faa9a29f7e77e9655fd
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70917317"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Vytváření clusterů Apache HBA v HDInsight v Azure Virtual Network
Naučte se vytvářet clustery Azure HDInsight Apache HBA v [Virtual Network Azure][1].

Díky integraci virtuální sítě můžete clustery Apache HBA nasadit do stejné virtuální sítě jako své aplikace, aby aplikace mohly komunikovat s adaptéry HBA přímo. Mezi výhody patří:

* Přímá konektivita webové aplikace v uzlech clusteru HBA, který umožňuje komunikaci prostřednictvím rozhraní API vzdáleného volání procedur (RPC) Java.
* Vylepšený výkon tím, že není přenos přes několik bran a nástrojů pro vyrovnávání zatížení.
* Možnost zpracovávat citlivé informace zabezpečeným způsobem bez odhalení veřejného koncového bodu.

### <a name="prerequisites"></a>Požadavky
Než začnete tento článek, musíte mít následující položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Pracovní stanice s prostředím Azure PowerShell**. Viz [instalace a použití Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Vytvoření clusteru Apache HBA do virtuální sítě
V této části vytvoříte cluster Apache HBA založený na systému Linux s závislým Azure Storage účtem ve službě Azure Virtual Network pomocí [šablony Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md). Další metody vytváření clusterů a porozumění nastavením najdete v tématu [Vytvoření clusterů HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Další informace o použití šablony k vytvoření Apache Hadoop clusterů ve službě HDInsight najdete v tématu [vytvoření Apache Hadoop clusterů ve službě HDInsight pomocí šablon Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md) .

> [!NOTE]  
> Některé vlastnosti jsou pevně zakódované do šablony. Příklad:
>
> * **Umístění**: Východní USA 2
> * **Verze clusteru**: 3.6
> * **Počet uzlů pracovního procesu clusteru**: 2
> * **Výchozí účet úložiště**: jedinečný řetězec
> * **Název virtuální sítě**: &lt;Název clusteru > – VNet
> * **Adresní prostor virtuální sítě**: 10.0.0.0/16
> * **Název podsítě**: SUBNET1
> * **Rozsah adres podsítě**: 10.0.0.0/24
>
> &lt;Název clusteru > je nahrazen názvem clusteru, který zadáte při použití šablony.


1. Kliknutím na následující obrázek otevřete šablonu na portálu Azure Portal. Šablona se nachází v [šablonách rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure"></a>
2. V okně **vlastní nasazení** zadejte následující vlastnosti:

   * **Předplatné:** Vyberte předplatné Azure, které se používá k vytvoření clusteru HDInsight, závislého účtu úložiště a virtuální sítě Azure.
   * **Skupina prostředků**: Vyberte **vytvořit novou**a zadejte název nové skupiny prostředků.
   * **Umístění**: Vyberte umístění skupiny prostředků.
   * Název **clusteru**: Zadejte název clusteru Hadoop, který se má vytvořit.
   * **Přihlašovací jméno a heslo clusteru**: Výchozí přihlašovací jméno je **admin** (správce).
   * **Uživatelské jméno a heslo SSH**: Výchozí uživatelské jméno je **sshuser** (uživatelssh).  Můžete ho změnit.
   * **Souhlasím s podmínkami a podmínkami uvedenými nahoře**: Vybrali
3. Klikněte na **Koupit**. Vytvoření clusteru trvá přibližně 20 minut. Po vytvoření clusteru můžete kliknout na okno cluster na portálu a otevřít ho.

Po dokončení tohoto článku budete možná chtít cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány. Pokyny k odstranění clusteru najdete v tématu [správa Apache Hadoop clusterů ve službě HDInsight pomocí Azure Portal](../hdinsight-administer-use-portal-linux.md#delete-clusters).

Pokud chcete začít pracovat s novým clusterem adaptérů HBA, můžete použít postupy, které najdete v části [Začínáme s používáním prostředí Apache HBA s Apache Hadoop v HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Připojení ke clusteru Apache HBA pomocí rozhraní Apache HBA Java RPC API
1. Vytvořte virtuální počítač infrastruktury jako služby (IaaS) ve stejné podsíti Azure a stejné podsíti. Pokyny k vytvoření nového virtuálního počítače s IaaS najdete v tématu [Vytvoření virtuálního počítače s Windows serverem](../../virtual-machines/windows/quick-create-portal.md). Pokud budete postupovat podle kroků v tomto dokumentu, musíte pro konfiguraci sítě použít následující hodnoty:

   * **Virtuální síť**: &lt;Název clusteru > – VNet
   * **Podsíť**: SUBNET1

   > [!IMPORTANT]  
   > Nahraďte &lt;název clusteru > názvem, který jste použili při vytváření clusteru HDInsight v předchozích krocích.

   Pomocí těchto hodnot se virtuální počítač umístí do stejné virtuální sítě a podsítě jako cluster HDInsight. Tato konfigurace umožňuje, aby mezi sebou navzájem komunikovala přímo. Existuje způsob, jak vytvořit cluster HDInsight s prázdným hraničním uzlem. Hraniční uzel lze použít ke správě clusteru.  Další informace najdete v tématu [použití prázdných hraničních uzlů v HDInsight](../hdinsight-apps-use-edge-node.md).

2. Pokud se k připojení k adaptérům HBA vzdáleně připojujete pomocí aplikace Java, je nutné použít plně kvalifikovaný název domény (FQDN). Chcete-li to zjistit, je třeba získat příponu DNS pro adaptéry pro připojení clusteru HBA. K tomu můžete použít jednu z následujících metod:

   * Pomocí webového prohlížeče proveďte volání [Apache Ambari](https://ambari.apache.org/) :

     Přejděte do https://&lt;název_clusteru >. azurehdinsight. NET/API/v1/Clusters/&lt;název_clusteru >/Hosts? minimal_response = true. Zapíná soubor JSON s příponami DNS.
   * Použijte web Ambari:

     1. Přejděte do https://&lt;název_clusteru >. azurehdinsight. NET.
     2. V horní nabídce klikněte na **hostitelé** .
   * Pro volání REST použijte oblé:

     ```bash
        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest
     ```

     V vrácených datech JavaScript Object Notation (JSON) vyhledejte položku "HOST_NAME". Obsahuje plně kvalifikovaný název domény pro uzly v clusteru. Příklad:

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     Část názvu domény začínající názvem clusteru je přípona DNS. Například mycluster.b1.cloudapp.net.
   * Použití Azure Powershell

     Pomocí následujícího skriptu Azure PowerShell Zaregistrujte funkci **Get-ClusterDetail** , která se dá použít k vrácení přípony DNS:

     ```powershell
        function Get-ClusterDetail(
            [String]
            [Parameter( Position=0, Mandatory=$true )]
            $ClusterDnsName,
            [String]
            [Parameter( Position=1, Mandatory=$true )]
            $Username,
            [String]
            [Parameter( Position=2, Mandatory=$true )]
            $Password,
            [String]
            [Parameter( Position=3, Mandatory=$true )]
            $PropertyName
            )
        {
        <#
            .SYNOPSIS
            Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
            .Description
            This command shows the following 4 properties of an HDInsight cluster:
            1. ZookeeperQuorum (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.quorum".
            2. ZookeeperClientPort (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.property.clientPort".
            3. HBaseRestServers (supports only HBase type cluster)
                Shows a list of host FQDNs that run the HBase REST server.
            4. FQDNSuffix (supports all cluster types)
                Shows the FQDN suffix of hosts in the cluster.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
            This command shows the value of HBase property "hbase.zookeeper.quorum".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
            This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
            This command shows a list of host FQDNs that run the HBase REST server.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
            This command shows the FQDN suffix of hosts in the cluster.
        #>

            $DnsSuffix = ".azurehdinsight.net"

            $ClusterFQDN = $ClusterDnsName + $DnsSuffix
            $webclient = new-object System.Net.WebClient
            $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

            if($PropertyName -eq "ZookeeperQuorum")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
            }
            if($PropertyName -eq "ZookeeperClientPort")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
            }
            if($PropertyName -eq "HBaseRestServers")
            {
                $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                $Response1 = $webclient.DownloadString($Url1)
                $JsonObject1 = $Response1 | ConvertFrom-Json
                $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                $Response2 = $webclient.DownloadString($Url2)
                $JsonObject2 = $Response2 | ConvertFrom-Json
                foreach ($host_component in $JsonObject2.host_components)
                {
                    $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                    Write-host $ConnectionString
                }
            }
            if($PropertyName -eq "FQDNSuffix")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                $pos = $FQDN.IndexOf(".")
                $Suffix = $FQDN.Substring($pos + 1)
                Write-host $Suffix
            }
        }
     ```

     Po spuštění skriptu Azure PowerShell použijte následující příkaz a vraťte příponu DNS pomocí funkce **Get-ClusterDetail** . Při použití tohoto příkazu zadejte název clusteru HDInsight HBA, jméno správce a heslo správce.

     ```powershell
        Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>
     ```

     Tento příkaz vrátí příponu DNS. Například **yourclustername.B4.Internal.cloudapp.NET**.


<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/hdi-primary-dns-suffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

Pokud chcete ověřit, jestli virtuální počítač může komunikovat s clusterem HBA, použijte příkaz `ping headnode0.<dns suffix>` z virtuálního počítače. Například otestujete headnode0.mycluster.b1.cloudapp.net.

Pokud chcete tyto informace použít v aplikaci Java, můžete postupovat podle kroků v části [použití Apache Maven k vytváření aplikací v jazyce Java, které používají Apache HBA s HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) k vytvoření aplikace. Aby se aplikace mohla připojit ke vzdálenému serveru HBA, upravte v tomto příkladu soubor **HBase-site. XML** , aby používal plně kvalifikovaný název domény pro Zookeeper. Příklad:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]  
> Další informace o překladu názvů ve virtuálních sítích Azure, včetně toho, jak používat vlastní server DNS, najdete v tématu [překlad IP adres (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak vytvořit cluster Apache HBA. Další informace naleznete v tématu:

* [Začínáme se službou HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Použití prázdných hraničních uzlů v HDInsight](../hdinsight-apps-use-edge-node.md)
* [Konfigurace replikace Apache HBA v HDInsight](apache-hbase-replication.md)
* [Vytváření clusterů Apache Hadoop ve službě HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Začínáme používat Apache HBA s Apache Hadoop ve službě HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Přehled služby Virtual Network](../../virtual-network/virtual-networks-overview.md)

[1]: https://azure.microsoft.com/services/virtual-network/
[2]: https://technet.microsoft.com/library/ee176961.aspx
[3]: https://technet.microsoft.com/library/hh847889.aspx

