---
title: Vytváření clusterů HBase ve virtuální síti – Azure
description: Začínáme používat HBase v Azure HDInsight. Zjistěte, jak vytvářet clustery HDInsight HBase ve službě Azure Virtual Network.
services: hdinsight,virtual-network
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: 183f8fd47ea5239e31f03f3aecf420cfb5842098
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51009823"
---
# <a name="create-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Vytváření clusterů HBase v HDInsight ve virtuální síti Azure
Naučíte se vytvářet clustery Azure HDInsight HBase v [Azure Virtual Network][1].

Integrace virtuální sítě je možné nasadit clustery HBase používané ke stejné virtuální síti jako vaše aplikace tak, aby aplikace můžou přímo komunikovat s HBase. Mezi výhody patří:

* Přímé připojení k webové aplikace do uzlů clusteru HBase, který umožňuje komunikaci s použitím procedury vzdálený HBase Java volání rozhraní API (RPC).
* Vylepšený výkon tím, že provoz přejděte přes více bran a nástroje pro vyrovnávání zatížení.
* Možnost zpracování citlivých informací v lépe zabezpečeným způsobem bez vystavení veřejný koncový bod.

### <a name="prerequisites"></a>Požadavky
Před zahájením tohoto kurzu musíte mít tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Pracovní stanice s prostředím Azure PowerShell**. Zobrazit [instalace a použití Azure Powershellu](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## <a name="create-hbase-cluster-into-virtual-network"></a>Vytvoření clusteru HBase do virtuální sítě
V této části vytvoříte cluster HBase se systémem Linux s závislého účtu Azure Storage ve virtuální síti Azure pomocí [šablony Azure Resource Manageru](../../azure-resource-manager/resource-group-template-deploy.md). Další metody vytváření clusterů a Principy nastavení, najdete v tématu [clusterů HDInsight vytvořit](../hdinsight-hadoop-provision-linux-clusters.md). Další informace o vytváření clusterů Hadoop v HDInsight pomocí šablony najdete v tématu [vytváření clusterů Hadoop v HDInsight pomocí šablon Azure Resource Manageru](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]
> Některé vlastnosti jsou pevně zakódovaný do šablony. Příklad:
>
> * **Umístění**: USA – východ 2
> * **Verze clusteru**: 3.6
> * **Počet uzlů pracovního procesu clusteru**: 2
> * **Výchozí účet úložiště**: jedinečný řetězec
> * **Název virtuální sítě**: &lt;název clusteru >-vnet
> * **Adresní prostor virtuální sítě**: 10.0.0.0/16
> * **Název podsítě**: subnet1
> * **Rozsah adres podsítě**: 10.0.0.0/24
>
> &lt;Název clusteru > se nahradí názvem clusteru poskytují při použití šablony.
>
>

1. Kliknutím na následující obrázek otevřete šablonu na portálu Azure Portal. Tato šablona je umístěná na [šablony pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Z **vlastní nasazení** okně zadejte následující vlastnosti:

   * **Předplatné**: Vyberte předplatné Azure použité k vytvoření clusteru HDInsight a závislý účet úložiště, virtuální síť Azure.
   * **Skupina prostředků**: vyberte **vytvořit nový**a zadejte nový název skupiny prostředků.
   * **Umístění:** Vyberte umístění pro skupinu prostředků.
   * **Název clusteru**: Zadejte název pro vytvoření clusteru Hadoop.
   * **Přihlašovací jméno a heslo clusteru**: výchozí přihlašovací jméno je **admin**.
   * **Uživatelské jméno a heslo SSH**: výchozí uživatelské jméno **sshuser**.  Můžete ho změnit.
   * **Souhlasím s podmínkami a ujednáními uvedenými nahoře**: (Vybrat)
3. Klikněte na **Koupit**. Vytvoření clusteru trvá přibližně 20 minut. Jakmile je cluster vytvořen, můžete kliknout na portálu a otevře se okno clusteru.

Po dokončení tohoto kurzu můžete cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány. Postup odstranění clusteru naleznete v tématu [spravovat Hadoop clusterů v HDInsight pomocí webu Azure portal](../hdinsight-administer-use-management-portal.md#delete-clusters).

Pokud chcete začít pracovat s nového clusteru HBase pomocí postupů v [Začínáme používat HBase s Hadoop v HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-hbase-cluster-using-hbase-java-rpc-apis"></a>Připojte se ke clusteru HBase pomocí rozhraní API HBase Java RPC
1. Vytvoření infrastruktury jako služby (IaaS) virtuální počítač do stejné virtuální síti Azure a ve stejné podsíti. Pokyny k vytvoření nového virtuálního počítače IaaS, naleznete v tématu [vytvořte virtuálním počítači s Windows serverem](../../virtual-machines/windows/quick-create-portal.md). Podle kroků v tomto dokumentu, musíte použít následující hodnoty pro konfiguraci sítě:

   * **Virtuální síť**: &lt;název clusteru >-vnet
   * **Podsíť**: subnet1

   > [!IMPORTANT]
   > Nahraďte &lt;název clusteru > s názvem, který jste použili při vytváření clusteru HDInsight v předchozích krocích.
   >
   >

   Pomocí těchto hodnot, virtuální počítač je umístěn ve stejné virtuální síť a podsíť jako HDInsight cluster. Tato konfigurace umožňuje, aby mohla komunikovat přímo mezi sebou. Existuje způsob, jak vytvořit HDInsight cluster s prázdných hraničních uzlů. Na hraničním uzlu lze použít ke správě clusteru.  Další informace najdete v tématu [použití prázdných hraničních uzlů v HDInsight](../hdinsight-apps-use-edge-node.md).

2. Při použití aplikace v Javě pro vzdálené připojení k HBase, musíte použít plně kvalifikovaný název domény (FQDN). Určí, musíte získat příponu DNS specifickou pro připojení clusteru HBase. K tomuto účelu můžete použít jednu z následujících metod:

   * Používejte webový prohlížeč, aby volání rozhraní Ambari:

     Přejděte na https://&lt;Název_clusteru >.azurehdinsight.net/api/v1/clusters/&lt;Název_clusteru > / hostitelem? minimal_response = true. Ukazuje soubor JSON s přípony DNS.
   * Použijte Ambari web:

     1. Přejděte na https://&lt;Název_clusteru >. azurehdinsight.net.
     2. Klikněte na tlačítko **hostitele** z hlavní nabídky.
   * Použití Curl k volání REST:

    ```bash
        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest
    ```

     V datech JavaScript Object Notation (JSON) vrátila vyhledejte položku "název_hostitele". Obsahuje plně kvalifikovaný název domény pro uzly v clusteru. Příklad:

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     Část počáteční název domény s názvem clusteru je přípona DNS. Například mycluster.b1.cloudapp.net.
   * Použití Azure Powershell

     Pomocí následujícího skriptu prostředí Azure PowerShell k registraci **Get-ClusterDetail** funkce, které lze použít k vrácení přípona DNS:

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

     Po spuštění skriptu Azure Powershellu, použijte následující příkaz k vrácení příponu DNS s využitím **Get-ClusterDetail** funkce. Při použití tohoto příkazu zadejte název clusteru HDInsight HBase, jméno správce a heslo správce.

    ```powershell
        Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>
    ```

     Tento příkaz vrátí příponu DNS. Například **yourclustername.b4.internal.cloudapp.net**.


<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/PrimaryDNSSuffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

Pokud chcete ověřit, že virtuální počítač může komunikovat s clusterem HBase, použijte příkaz `ping headnode0.<dns suffix>` z virtuálního počítače. Například odešlete zprávu ping headnode0.mycluster.b1.cloudapp.net.

Pokud chcete použít tyto informace v aplikaci Java, provedením kroků v [použití Mavenu k vytváření aplikací v Javě, které používají HBase s HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) k vytvoření aplikace. Chcete-li mít aplikaci připojit ke vzdálenému serveru HBase, upravte **hbase-site.xml** soubor v tomto příkladu pro použití plně kvalifikovaný název domény pro Zookeeper. Příklad:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]
> Další informace o překlad názvů v Azure virtuální sítě, včetně použití vlastního serveru DNS, najdete v části [rozlišení DNS (Name)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
>
>

## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak vytvořit HBase cluster. Další informace naleznete v tématu:

* [Začínáme s HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Použití prázdných hraničních uzlů v HDInsight](../hdinsight-apps-use-edge-node.md)
* [Konfigurace replikace HBase v HDInsight](apache-hbase-replication.md)
* [Vytvoření clusterů Hadoop v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Začínáme používat HBase s Hadoopem ve službě HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Přehled služby Virtual Network](../../virtual-network/virtual-networks-overview.md)

[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

