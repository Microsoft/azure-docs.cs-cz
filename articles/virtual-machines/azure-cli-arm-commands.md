---
title: Příkazy rozhraní příkazového řádku Azure Classic
description: Příkazy rozhraní příkazového řádku (CLI) Azure pro správu prostředků.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/18/2017
ms.author: cynthn
ms.openlocfilehash: ad213cc02f707609a837ea66b79e51c6f2a617f8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79250878"
---
# <a name="azure-classic-cli-commands"></a>Příkazy rozhraní příkazového řádku Azure Classic 

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Toto téma popisuje postup instalace Azure Classic CLI. Rozhraní Classic CLI je zastaralé a je možné ho používat pouze s modele nasazení Classic. Pro všechna ostatní nasazení použijte rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/).

Tento článek poskytuje syntaxi a možnosti pro příkazy rozhraní příkazového řádku (CLI) Azure Classic, které byste běžně použili k vytváření a správě prostředků Azure. Nejedná se o úplný odkaz a verze rozhraní příkazového řádku (CLI) se může zobrazit trochu různých příkazů nebo parametrů. 

Začněte tím, že nejdřív [nainstalujete Azure Classic CLI](../cli-install-nodejs.md) a [připojíte se k předplatnému Azure](/cli/azure/authenticate-azure-cli).

Pro aktuální syntaxi příkazů a možnosti na příkazovém řádku v režimu Správce prostředků zadejte `azure help` nebo, abyste zobrazili nápovědu ke konkrétnímu příkazu `azure help [command]`. V dokumentaci k vytváření a správě konkrétních služeb Azure najdete taky příklady rozhraní příkazového řádku.

Volitelné parametry jsou uvedeny v hranatých závorkách (například `[parameter]`). Všechny ostatní parametry jsou povinné.

Kromě specifických volitelných parametrů, které jsou zde popsané, jsou k dispozici tři volitelné parametry, které lze použít k zobrazení podrobného výstupu, jako jsou možnosti žádosti a stavové kódy. Parametr `-v` poskytuje podrobný výstup a parametr `-vv` poskytuje ještě podrobnější podrobný výstup. Možnost `--json` výstup výsledek ve formátu RAW JSON.

## <a name="setting-the-resource-manager-mode"></a>Nastavení režimu Správce prostředků
Pomocí následujícího příkazu povolte příkazy Azure CLI Správce prostředků režimu.

    azure config mode arm

> [!NOTE]
> Režim Azure Resource Manager CLI a režim správy služby Azure se vzájemně vylučují. To znamená, že prostředky vytvořené v jednom režimu nelze spravovat z jiného režimu.
>


## <a name="account-information"></a>Informace o účtu
Informace o vašem předplatném Azure používá nástroj pro připojení k vašemu účtu.

**Výpis importovaných předplatných**

    account list [options]

**Zobrazit podrobnosti o předplatném**  

    account show [options] [subscriptionNameOrId]

**Nastavit aktuální předplatné**

    account set [options] <subscriptionNameOrId>

**Odeberte předplatné nebo prostředí nebo zrušte zaškrtnutí všech údajů o uloženém účtu a prostředí.**  

    account clear [options]

**Příkazy pro správu prostředí účtu**  

    account env list [options]
    account env show [options] [environment]
    account env add [options] [environment]
    account env set [options] [environment]
    account env delete [options] [environment]

## <a name="active-directory-objects"></a>Objekty služby Active Directory
**Příkazy pro zobrazení aplikací služby Active Directory**

    ad app create [options]
    ad app delete [options] <object-id>

**Příkazy pro zobrazení skupin služby Active Directory**

    ad group list [options]
    ad group show [options]

**Příkazy k poskytnutí informací o podskupině nebo členu služby Active Directory**

    ad group member list [options] [objectId]

**Příkazy pro zobrazení instančních objektů služby Active Directory**

    ad sp list [options]
    ad sp show [options]
    ad sp create [options] <application-id>
    ad sp delete [options] <object-id>

**Příkazy pro zobrazení uživatelů služby Active Directory**

    ad user list [options]
    ad user show [options]

## <a name="availability-sets"></a>Skupiny dostupnosti
**Vytvoří skupinu dostupnosti v rámci skupiny prostředků.**

    availset create [options] <resource-group> <name> <location> [tags]

**Seznam skupin dostupnosti v rámci skupiny prostředků**

    availset list [options] <resource-group>

**Načte jednu skupinu dostupnosti v rámci skupiny prostředků.**

    availset show [options] <resource-group> <name>

**Odstraní jednu skupinu dostupnosti v rámci skupiny prostředků.**

    availset delete [options] <resource-group> <name>

## <a name="local-settings"></a>Místní nastavení
**Vypsat nastavení konfigurace Azure CLI**

    config list [options]

**Odstraní nastavení konfigurace.**

    config delete [options] <name>

**Aktualizace konfiguračního nastavení**

    config set <name> <value>

**Nastaví pracovní režim Azure CLI buď na `arm`, nebo `asm`.**

    config mode [options] <modename>


## <a name="account-features"></a>Funkce účtu
**Zobrazit seznam všech funkcí dostupných pro vaše předplatné**

    feature list [options]

**Zobrazuje funkci**

    feature show [options] <providerName> <featureName>

**Zaregistruje předzobrazenou funkci poskytovatele prostředků.**

    feature register [options] <providerName> <featureName>

## <a name="resource-groups"></a>Skupiny prostředků
**Vytvoří skupinu prostředků.**

    group create [options] <name> <location>

**Nastavení značek na skupinu prostředků**

    group set [options] <name> <tags>

**Odstraní skupinu prostředků.**

    group delete [options] <name>

**Vypíše skupiny prostředků pro vaše předplatné.**

    group list [options]

**Zobrazuje skupinu prostředků pro vaše předplatné.**

    group show [options] <name>

**Příkazy pro správu protokolů skupin prostředků**

    group log show [options] [name]

**Příkazy pro správu nasazení ve skupině prostředků**

    group deployment create [options] [resource-group] [name]
    group deployment list [options] <resource-group> [state]
    group deployment show [options] <resource-group> [deployment-name]
    group deployment stop [options] <resource-group> [deployment-name]

**Příkazy pro správu místní šablony nebo šablony skupiny prostředků galerie**

    group template list [options]
    group template show [options] <name>
    group template download [options] [name] [file]
    group template validate [options] <resource-group>

## <a name="hdinsight-clusters"></a>Clustery HDInsight
**Příkazy pro vytvoření nebo přidání do konfiguračního souboru clusteru**

    hdinsight config create [options] <configFilePath> <overwrite>
    hdinsight config add-config-values [options] <configFilePath>
    hdinsight config add-script-action [options] <configFilePath>


Příklad: vytvořte konfigurační soubor, který obsahuje akci skriptu, která se spustí při vytváření clusteru.

    hdinsight config create "C:\myFiles\configFile.config"
    hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <scriptActionURI> --name myScriptAction --parameters "-param value"

**Příkaz pro vytvoření clusteru ve skupině prostředků**

    hdinsight cluster create [options] <clusterName>

Příklad: vytvoření zaplavení v clusteru se systémem Linux

    azure hdinsight cluster create -g myarmgroup -l westus -y Linux --clusterType Storm --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 myNewCluster01

    info:    Executing command hdinsight cluster create
    + Submitting the request to create cluster...
    info:    hdinsight cluster create command OK

Příklad: Vytvoření clusteru pomocí akce skriptu

    azure hdinsight cluster create -g myarmgroup -l westus -y Linux --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01

    info:    Executing command hdinsight cluster create
    + Submitting the request to create cluster...
    info:    hdinsight cluster create command OK

Možnosti parametru:

    -h, --help                                                 output usage information
    -v, --verbose                                              use verbose output
    -vv                                                        more verbose with debug output
    --json                                                     use json output
    -g --resource-group <resource-group>                       The name of the resource group
    -c, --clusterName <clusterName>                            HDInsight cluster name
    -l, --location <location>                                  Data center location for the cluster
    -y, --osType <osType>                                      HDInsight cluster operating system
    'Windows' or 'Linux'
    --version <version>                                        HDInsight cluster version
    --clusterType <clusterType>                                HDInsight cluster type.
    Hadoop | HBase | Spark | Storm
    --defaultStorageAccountName <storageAccountName>           Storage account url to use for default HDInsight storage
    --defaultStorageAccountKey <storageAccountKey>             Key to the storage account to use for default HDInsight storage
    --defaultStorageContainer <storageContainer>               Container in the storage account to use for HDInsight default storage
    --headNodeSize <headNodeSize>                              (Optional) Head node size for the cluster
    --workerNodeCount <workerNodeCount>                        Number of worker nodes to use for the cluster
    --workerNodeSize <workerNodeSize>                          (Optional) Worker node size for the cluster)
    --zookeeperNodeSize <zookeeperNodeSize>                    (Optional) Zookeeper node size for the cluster
    --userName <userName>                                      Cluster username
    --password <password>                                      Cluster password
    --sshUserName <sshUserName>                                SSH username (only for Linux clusters)
    --sshPassword <sshPassword>                                SSH password (only for Linux clusters)
    --sshPublicKey <sshPublicKey>                              SSH public key (only for Linux clusters)
    --rdpUserName <rdpUserName>                                RDP username (only for Windows clusters)
    --rdpPassword <rdpPassword>                                RDP password (only for Windows clusters)
    --rdpAccessExpiry <rdpAccessExpiry>                        RDP access expiry.
    For example 12/12/2015 (only for Windows clusters)
    --virtualNetworkId <virtualNetworkId>                      (Optional) Virtual network ID for the cluster.
    Value is a GUID for Windows cluster and ARM resource ID for Linux cluster)
    --subnetName <subnetName>                                  (Optional) Subnet for the cluster
    --additionalStorageAccounts <additionalStorageAccounts>    (Optional) Additional storage accounts.
    Can be multiple.
    In the format of 'accountName#accountKey'.
    For example, --additionalStorageAccounts "acc1#key1;acc2#key2"
    --hiveMetastoreServerName <hiveMetastoreServerName>        (Optional) SQL Server name for the external metastore for Hive
    --hiveMetastoreDatabaseName <hiveMetastoreDatabaseName>    (Optional) Database name for the external metastore for Hive
    --hiveMetastoreUserName <hiveMetastoreUserName>            (Optional) Database username for the external metastore for Hive
    --hiveMetastorePassword <hiveMetastorePassword>            (Optional) Database password for the external metastore for Hive
    --oozieMetastoreServerName <oozieMetastoreServerName>      (Optional) SQL Server name for the external metastore for Oozie
    --oozieMetastoreDatabaseName <oozieMetastoreDatabaseName>  (Optional) Database name for the external metastore for Oozie
    --oozieMetastoreUserName <oozieMetastoreUserName>          (Optional) Database username for the external metastore for Oozie
    --oozieMetastorePassword <oozieMetastorePassword>          (Optional) Database password for the external metastore for Oozie
    --configurationPath <configurationPath>                    (Optional) HDInsight cluster configuration file path
    -s, --subscription <id>                                    The subscription id
    --tags <tags>                                              Tags to set to the cluster.
    Can be multiple.
    In the format of 'name=value'.
    Name is required and value is optional.
    For example, --tags tag1=value1;tag2


**Příkaz pro odstranění clusteru**

    hdinsight cluster delete [options] <clusterName>

**Příkaz pro zobrazení podrobností o clusteru**

    hdinsight cluster show [options] <clusterName>

**Příkaz pro výpis všech clusterů (v konkrétní skupině prostředků, pokud je k dispozici)**

    hdinsight cluster list [options]

**Příkaz pro změnu velikosti clusteru**

    hdinsight cluster resize [options] <clusterName> <targetInstanceCount>

**Příkaz pro povolení přístupu HTTP pro cluster**

    hdinsight cluster enable-http-access [options] <clusterName> <userName> <password>

**Příkaz pro zakázání přístupu HTTP pro cluster**

    hdinsight cluster disable-http-access [options] <clusterName>

**Příkaz pro povolení přístupu RDP pro cluster**

    hdinsight cluster enable-rdp-access [options] <clusterName> <rdpUserName> <rdpPassword> <rdpExpiryDate>

**Příkaz pro zakázání přístupu HTTP pro cluster**

    hdinsight cluster disable-rdp-access [options] <clusterName>

## <a name="insights-events-alert-rules-autoscale-settings-metrics"></a>Přehledy (události, pravidla výstrah, nastavení automatického škálování, metriky)
**Načtení protokolů operací pro předplatné, ID korelace, skupinu prostředků, prostředek nebo poskytovatele prostředků**

    insights logs list [options]

## <a name="locations"></a>Umístění 
**Vypsat dostupná umístění**

    location list [options]

## <a name="network-resources"></a>Síťové prostředky
**Příkazy pro správu virtuálních sítí**

    network vnet create [options] <resource-group> <name> <location>
Vytvoří virtuální síť. V následujícím příkladu vytvoříme virtuální síť s názvem newvnet pro skupinu prostředků myresourcegroup v oblasti Západní USA.

    azure network vnet create myresourcegroup newvnet "west us"
    info:    Executing command network vnet create
    + Looking up virtual network "newvnet"
    + Creating virtual network "newvnet"
     Loading virtual network state
    data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
    data:    Name:                 newvnet
    data:    Type:                 Microsoft.Network/virtualNetworks
    data:    Location:             westus
    data:    Tags:
    data:    Provisioning state:   Succeeded
    data:    Address prefixes:
    data:     10.0.0.0/8
    data:    DNS servers:
    data:    Subnets:
    data:
    info:    network vnet create command OK


Možnosti parametru:

     -h, --help                                 output usage information
     -v, --verbose                              use verbose output
    --json                                     use json output
     -g, --resource-group <resource-group>      the name of the resource group
     -n, --name <name>                          the name of the virtual network
     -l, --location <location>                  the location
     -a, --address-prefixes <address-prefixes>  the comma separated list of address prefixes for this virtual network
      For example -a 10.0.0.0/24,10.0.1.0/24.
      Default value is 10.0.0.0/8

    -d, --dns-servers <dns-servers>            the comma separated list of DNS servers IP addresses
     -t, --tags <tags>                          the tags set on this virtual network.
      Can be multiple. In the format of "name=value".
      Name is required and value is optional.
      For example, -t tag1=value1;tag2
     -s, --subscription <subscription>          the subscription identifier
<BR>

    network vnet set [options] <resource-group> <name>

Aktualizuje konfiguraci virtuální sítě v rámci skupiny prostředků.

    azure network vnet set myresourcegroup newvnet

    info:    Executing command network vnet set
    + Looking up virtual network "newvnet"
    + Updating virtual network "newvnet"
    + Loading virtual network state
    data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
    data:    Name:                 newvnet
    data:    Type:                 Microsoft.Network/virtualNetworks
    data:    Location:             westus
    data:    Tags:
    data:    Provisioning state:   Succeeded
    data:    Address prefixes:
    data:     10.0.0.0/8
    data:    DNS servers:
    data:    Subnets:
    data:
    info:    network vnet set command OK

Možnosti parametru:

       -h, --help                                 output usage information
       -v, --verbose                              use verbose output
       --json                                     use json output
       -g, --resource-group <resource-group>      the name of the resource group
       -n, --name <name>                          the name of the virtual network
       -a, --address-prefixes <address-prefixes>  the comma separated list of address prefixes for this virtual network.
        For example -a 10.0.0.0/24,10.0.1.0/24.
        This list will be appended to the current list of address prefixes.
        The address prefixes in this list should not overlap between them.
        The address prefixes in this list should not overlap with existing address prefixes in the vnet.

       -d, --dns-servers [dns-servers]            the comma separated list of DNS servers IP addresses.
        This list will be appended to the current list of DNS server IP addresses.

       -t, --tags <tags>                          the tags set on this virtual network.
        Can be multiple. In the format of "name=value".
        Name is required and value is optional. For example, -t tag1=value1;tag2.
        This list will be appended to the current list of tags

       --no-tags                                  remove all existing tags
       -s, --subscription <subscription>          the subscription identifier
<BR>

    network vnet list [options] <resource-group>

Příkaz vypíše všechny virtuální sítě ve skupině prostředků.

    C:\>azure network vnet list myresourcegroup

    info:    Executing command network vnet list
    + Listing virtual networks
        data:    ID
       Name      Location  Address prefixes  DNS servers
    data:    -------------------------------------------------------------------
    ------  --------  --------  ----------------  -----------
    data:    /subscriptions/###############################/resourceGroups/
    wvnet   newvnet   westus    10.0.0.0/8
    info:    network vnet list command OK

Možnosti parametru:

      -h, --help                             output usage information
      -v, --verbose                          use verbose output
      --json                                 use json output
      -g, --resource-group <resource-group>  the name of the resource group
      -s, --subscription <subscription>      the subscription identifier

<BR>

    network vnet show [options] <resource-group> <name>
Příkaz zobrazuje vlastnosti virtuální sítě ve skupině prostředků.

    azure network vnet show -g myresourcegroup -n newvnet

    info:    Executing command network vnet show
    + Looking up virtual network "newvnet"
    data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
    data:    Name:                 newvnet
    data:    Type:                 Microsoft.Network/virtualNetworks
    data:    Location:             westus
    data:    Tags:
    data:    Provisioning state:   Succeeded
    data:    Address prefixes:
    data:     10.0.0.0/8
    data:    DNS servers:
    data:    Subnets:
    data:
    info:    network vnet show command OK
<BR>

    network vnet delete [options] <resource-group> <name>
Příkaz odebere virtuální síť.

    azure network vnet delete myresourcegroup newvnetX

    info:    Executing command network vnet delete
    + Looking up virtual network "newvnetX"
    Delete virtual network newvnetX? [y/n] y
    + Deleting virtual network "newvnetX"
    info:    network vnet delete command OK

Možnosti parametru:

     -h, --help                             output usage information
     -v, --verbose                          use verbose output
     --json                                 use json output
     -g, --resource-group <resource-group>  the name of the resource group
     -n, --name <name>                      the name of the virtual network
     -q, --quiet                            quiet mode, do not ask for delete confirmation
     -s, --subscription <subscription>      the subscription identifier


**Příkazy pro správu podsítí virtuální sítě**

    network vnet subnet create [options] <resource-group> <vnet-name> <name>

Přidá další podsíť do existující virtuální sítě.

    azure network vnet subnet create -g myresourcegroup --vnet-name newvnet -n subnet --address-prefix 10.0.1.0/24

    info:    Executing command network vnet subnet create
    + Looking up the subnet "subnet"
    + Creating subnet "subnet"
    + Looking up the subnet "subnet"
    data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet
    data:    Name:                      subnet
    data:    Type:                      Microsoft.Network/virtualNetworks/subnets
    data:    Provisioning state:        Succeeded
    data:    Address prefix:            10.0.1.0/24
    info:    network vnet subnet create command OK

Možnosti parametru:

     -h, --help                                                       output usage information
     -v, --verbose                                                    use verbose output
         --json                                                           use json output
     -g, --resource-group <resource-group>                            the name of the resource group
     -e, --vnet-name <vnet-name>                                      the name of the virtual network
     -n, --name <name>                                                the name of the subnet
     -a, --address-prefix <address-prefix>                            the address prefix
     -w, --network-security-group-id <network-security-group-id>      the network security group identifier.
           e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkSecurityGroups/<nsg-name>
     -o, --network-security-group-name <network-security-group-name>  the network security group name
     -s, --subscription <subscription>                                the subscription identifier

<BR>

    network vnet subnet set [options] <resource-group> <vnet-name> <name>

Nastaví konkrétní podsíť virtuální sítě v rámci skupiny prostředků.

    C:\>azure network vnet subnet set -g myresourcegroup --vnet-name newvnet -n subnet1

    info:    Executing command network vnet subnet set
    + Looking up the subnet "subnet1"
    + Setting subnet "subnet1"
    + Looking up the subnet "subnet1"
    data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet1
    data:    Name:                      subnet1
    data:    Type:                      Microsoft.Network/virtualNetworks/subnets
    data:    Provisioning state:        Succeeded
    data:    Address prefix:            10.0.1.0/24
    info:    network vnet subnet set command OK
<BR>

    network vnet subnet list [options] <resource-group> <vnet-name>

Zobrazí seznam všech podsítí virtuální sítě pro konkrétní virtuální síť v rámci skupiny prostředků.

    azure network vnet subnet set -g myresourcegroup --vnet-name newvnet -n subnet1

    info:    Executing command network vnet subnet set
    + Looking up the subnet "subnet1"
    + Setting subnet "subnet1"
    + Looking up the subnet "subnet1"
    data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet1
    data:    Name:                      subnet1
    data:    Type:                      Microsoft.Network/virtualNetworks/subnets
    data:    Provisioning state:        Succeeded
    data:    Address prefix:            10.0.1.0/24
    info:    network vnet subnet set command OK
<BR>

    network vnet subnet show [options] <resource-group> <vnet-name> <name>
Zobrazí vlastnosti podsítě virtuální sítě.

    azure network vnet subnet show -g myresourcegroup --vnet-name newvnet -n subnet1

    info:    Executing command network vnet subnet show
    + Looking up the subnet "subnet1"
    data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft
    .Network/virtualNetworks/newvnet/subnets/subnet1
    data:    Name:                      subnet1
    data:    Type:                      Microsoft.Network/virtualNetworks/subnets
    data:    Provisioning state:        Succeeded
    data:    Address prefix:            10.0.1.0/24
    info:    network vnet subnet show command OK

Možnosti parametru:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -e, --vnet-name <vnet-name>            the name of the virtual network
    -n, --name <name>                      the name of the subnet
    -s, --subscription <subscription>      the subscription identifier
<BR>

    network vnet subnet delete [options] <resource-group> <vnet-name> <subnet-name>
Odebere podsíť ze stávající virtuální sítě.

    azure network vnet subnet delete -g myresourcegroup --vnet-name newvnet -n subnet1

    info:    Executing command network vnet subnet delete
    + Looking up the subnet "subnet1"
    Delete subnet "subnet1"? [y/n] y
    + Deleting subnet "subnet1"
    info:    network vnet subnet delete command OK

Možnosti parametru:

     -h, --help                             output usage information
     -v, --verbose                          use verbose output
     --json                                 use json output
     -g, --resource-group <resource-group>  the name of the resource group
     -e, --vnet-name <vnet-name>            the name of the virtual network
     -n, --name <name>                      the subnet name
     -s, --subscription <subscription>      the subscription identifier
     -q, --quiet                            quiet mode, do not ask for delete confirmation

**Příkazy pro správu nástrojů pro vyrovnávání zatížení**

    network lb create [options] <resource-group> <name> <location>
Vytvoří sadu nástroje pro vyrovnávání zatížení.

    azure network lb create -g myresourcegroup -n mylb -l westus

    info:    Executing command network lb create
    + Looking up the load balancer "mylb"
    + Creating load balancer "mylb"
    + Looking up the load balancer "mylb"
    data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
    data:    Name:                         mylb
    data:    Type:                         Microsoft.Network/loadBalancers
    data:    Location:                     westus
    data:    Provisioning state:           Succeeded
    info:    network lb create command OK

Možnosti parametru:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -n, --name <name>                      the name of the load balancer
    -l, --location <location>              the location
    -t, --tags <tags>                      the list of tags.
     Can be multiple. In the format of "name=value".
     Name is required and value is optional. For example, -t tag1=value1;tag2
    -s, --subscription <subscription>      the subscription identifier
<BR>

    network lb list [options] <resource-group>
Vypíše prostředky nástroje pro vyrovnávání zatížení v rámci skupiny prostředků.

    azure network lb list myresourcegroup

    info:    Executing command network lb list
    + Getting the load balancers
    data:    Name  Location
    data:    ----  --------
    data:    mylb  westus
    info:    network lb list command OK

Možnosti parametru:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -s, --subscription <subscription>      the subscription identifier
<BR>

    network lb show [options] <resource-group> <name>

Zobrazí informace o nástroji pro vyrovnávání zatížení konkrétního nástroje pro vyrovnávání zatížení v rámci skupiny prostředků.

    azure network lb show myresourcegroup mylb -v

    info:    Executing command network lb show
    verbose: Looking up the load balancer "mylb"
    data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
    data:    Name:                         mylb
    data:    Type:                         Microsoft.Network/loadBalancers
    data:    Location:                     westus
    data:    Provisioning state:           Succeeded
    info:    network lb show command OK

Možnosti parametru:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -n, --name <name>                      the name of the load balancer
    -s, --subscription <subscription>      the subscription identifier

<BR>

    network lb delete [options] <resource-group> <name>

Odstraňte prostředky nástroje pro vyrovnávání zatížení.

    azure network lb delete  myresourcegroup mylb

    info:    Executing command network lb delete
    + Looking up the load balancer "mylb"
    Delete load balancer "mylb"? [y/n] y
    + Deleting load balancer "mylb"
    info:    network lb delete command OK

Možnosti parametru:

     -h, --help                             output usage information
     -v, --verbose                          use verbose output
     --json                                 use json output
     -g, --resource-group <resource-group>  the name of the resource group
     -n, --name <name>                      the name of the load balancer
     -q, --quiet                            quiet mode, do not ask for delete confirmation
     -s, --subscription <subscription>      the subscription identifier

**Příkazy pro správu sond nástroje pro vyrovnávání zatížení**

    network lb probe create [options] <resource-group> <lb-name> <name>

Vytvořte konfiguraci sondy pro stav v nástroji pro vyrovnávání zatížení. Nezapomeňte spustit tento příkaz. váš nástroj pro vyrovnávání zatížení vyžaduje prostředek front-endu (Projděte si příkaz "síť Azure na front-endu") a přiřaďte IP adresu k nástroji pro vyrovnávání zatížení.

    azure network lb probe create -g myresourcegroup --lb-name mylb -n mylbprobe --protocol tcp --port 80 -i 300

    info:    Executing command network lb probe create
    + Looking up the load balancer "mylb"
    + Updating load balancer "mylb"
    info:    network lb probe create command OK

Možnosti parametru:

     -h, --help                             output usage information
     -v, --verbose                          use verbose output
     --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -n, --name <name>                      the name of the probe
    -p, --protocol <protocol>              the probe protocol
    -o, --port <port>                      the probe port
    -f, --path <path>                      the probe path
    -i, --interval <interval>              the probe interval in seconds
    -c, --count <count>                    the number of probes
    -s, --subscription <subscription>      the subscription identifier

<BR>

    network lb probe set [options] <resource-group> <lb-name> <name>

Aktualizuje existující test nástroje pro vyrovnávání zatížení o nové hodnoty.

    azure network lb probe set -g myresourcegroup -l mylb -n mylbprobe -p mylbprobe1 -p TCP -o 443 -i 300

    info:    Executing command network lb probe set
        + Looking up the load balancer "mylb"
    + Updating load balancer "mylb"
    info:    network lb probe set command OK

Možnosti parametrů

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -n, --name <name>                      the name of the probe
    -e, --new-probe-name <new-probe-name>  the new name of the probe
    -p, --protocol <protocol>              the new value for probe protocol
    -o, --port <port>                      the new value for probe port
    -f, --path <path>                      the new value for probe path
    -i, --interval <interval>              the new value for probe interval in seconds
    -c, --count <count>                    the new value for number of probes
    -s, --subscription <subscription>      the subscription identifier
<BR>

    network lb probe list [options] <resource-group> <lb-name>

Zobrazí seznam vlastností sondy pro sadu vyrovnávání zatížení.

    C:\>azure network lb probe list -g myresourcegroup -l mylb

    info:    Executing command network lb probe list
    + Looking up the load balancer "mylb"
    data:    Name       Protocol  Port  Path  Interval  Count
    data:    ---------  --------  ----  ----  --------  -----
    data:    mylbprobe  Tcp       443         300       2
    info:    network lb probe list command OK

Možnosti parametru:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -s, --subscription <subscription>      the subscription identifier


    network lb probe delete [options] <resource-group> <lb-name> <name>
Odebere test paměti vytvořený pro nástroj pro vyrovnávání zatížení.

    azure network lb probe delete -g myresourcegroup -l mylb -n mylbprobe

    info:    Executing command network lb probe delete
    + Looking up the load balancer "mylb"
    Delete a probe "mylbprobe?" [y/n] y
    + Updating load balancer "mylb"
    info:    network lb probe delete command OK

**Příkazy pro správu konfigurací IP adresy front-endu nástroje pro vyrovnávání zatížení**

    network lb frontend-ip create [options] <resource-group> <lb-name> <name>
Vytvoří konfiguraci IP adresy front-endu do existující sady nástroje pro vyrovnávání zatížení.

    azure network lb frontend-ip create -g myresourcegroup --lb-name mylb -n myfrontendip -o Dynamic -e subnet -m newvnet

    info:    Executing command network lb frontend-ip create
    + Looking up the load balancer "mylb"
    + Looking up the subnet "subnet"
    + Creating frontend IP configuration "myfrontendip"
    + Looking up the load balancer "mylb"
    data:    Id:                           /subscriptions/###############################/resourceGroups/Myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
    /frontendIPConfigurations/myfrontendip
    data:    Name:                         myfrontendip
    data:    Type:                         Microsoft.Network/loadBalancers/frontendIPConfigurations
    data:    Provisioning state:           Succeeded
    data:    Private IP allocation method: Dynamic
    data:    Private IP address:           10.0.1.4
    data:    Subnet:                       id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet
    data:    Public IP address:
    data:    Inbound NAT rules
    data:    Outbound NAT rules
    data:    Load balancing rules
    data:
    info:    network lb frontend-ip create command OK

<BR>

    network lb frontend-ip set [options] <resource-group> <lb-name> <name>

Aktualizuje existující konfiguraci IP adresy front-endu. Následující příkaz přidá veřejnou IP adresu s názvem mypubip5 do existující IP adresy front-endu pro vyrovnávání zatížení s názvem myfrontendip.

    azure network lb frontend-ip set -g myresourcegroup --lb-name mylb -n myfrontendip -i mypubip5

    info:    Executing command network lb frontend-ip set
    + Looking up the load balancer "mylb"
    + Looking up the public ip "mypubip5"
    + Updating load balancer "mylb"
    + Looking up the load balancer "mylb"
    data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
    data:    Name:                         myfrontendip
    data:    Type:                         Microsoft.Network/loadBalancers/frontendIPConfigurations
    data:    Provisioning state:           Succeeded
    data:    Private IP allocation method: Dynamic
    data:    Private IP address:
    data:    Subnet:
    data:    Public IP address:            id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypubip5
    data:    Inbound NAT rules
    data:    Outbound NAT rules
    data:    Load balancing rules
    data:
    info:    network lb frontend-ip set command OK

Možnosti parametru:

    -h, --help                                                         output usage information
    -v, --verbose                                                      use verbose output
    --json                                                             use json output
    -g, --resource-group <resource-group>                              the name of the resource group
    -l, --lb-name <lb-name>                                            the name of the load balancer
    -n, --name <name>                                                  the name of the frontend ip configuration
    -a, --private-ip-address <private-ip-address>                      the private ip address
    -o, --private-ip-allocation-method <private-ip-allocation-method>  the private ip allocation method [Static, Dynamic]
    -u, --public-ip-id <public-ip-id>                                  the public ip identifier.
    e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/publicIPAddresses/<public-ip-name>
    -i, --public-ip-name <public-ip-name>                              the public ip name.
    This public ip must exist in the same resource group as the lb.
    Please use public-ip-id if that is not the case.
    -b, --subnet-id <subnet-id>                                        the subnet id.
    e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/VirtualNetworks/<vnet-name>/subnets/<subnet-name>
    -e, --subnet-name <subnet-name>                                    the subnet name
    -m, --vnet-name <vnet-name>                                        the virtual network name.
    This virtual network must exist in the same resource group as the lb.
    Please use subnet-id if that is not the case.
    -s, --subscription <subscription>                                  the subscription identifier

<BR>

    network lb frontend-ip list [options] <resource-group> <lb-name>

Zobrazí seznam všech prostředků IP front-endu nakonfigurovaných pro nástroj pro vyrovnávání zatížení.

    azure network lb frontend-ip list -g myresourcegroup -l mylb

    info:    Executing command network lb frontend-ip list
    + Looking up the load balancer "mylb"
    data:    Name         Provisioning state  Private IP allocation method  Subnet
    data:    -----------  ------------------  ----------------------------  ------
    data:    myprivateip  Succeeded           Dynamic
    info:    network lb frontend-ip list command OK

Možnosti parametru:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -s, --subscription <subscription>      the subscription identifier
<BR>

    network lb frontend-ip delete [options] <resource-group> <lb-name> <name>
Odstraní objekt IP front-endu přidružený k nástroji pro vyrovnávání zatížení.

    network lb frontend-ip delete -g myresourcegroup -l mylb -n myfrontendip
    info:    Executing command network lb frontend-ip delete
    + Looking up the load balancer "mylb"
    Delete frontend ip configuration "myfrontendip"? [y/n] y
    + Updating load balancer "mylb"

Možnosti parametru:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -n, --name <name>                      the name of the frontend ip configuration
    -q, --quiet                            quiet mode, do not ask for delete confirmation
    -s, --subscription <subscription>      the subscription identifier

**Příkazy pro správu fondů adres back-endu nástroje pro vyrovnávání zatížení**

    network lb address-pool create [options] <resource-group> <lb-name> <name>

Vytvořte fond adres back-end pro nástroj pro vyrovnávání zatížení.

    azure network lb address-pool create -g myresourcegroup --lb-name mylb -n myaddresspool

    info:    Executing command network lb address-pool create
    + Looking up the load balancer "mylb"
    + Updating load balancer "mylb"
    + Looking up the load balancer "mylb"
    data:    Id:                        /subscriptions/###############################/resourceGroups/myresourgroup/providers/Microso.Network/loadBalancers/mylb/backendAddressPools/myaddresspool
    data:    Name:                      myaddresspool
    data:    Type:                      Microsoft.Network/loadBalancers/backendAddressPools
    data:    Provisioning state:        Succeeded
    data:    Backend IP configurations:
    data:    Load balancing rules:
    data:
    info:    network lb address-pool create command OK

Možnosti parametru:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -n, --name <name>                      the name of the backend address pool
    -s, --subscription <subscription>      the subscription identifier

<BR>

    network lb address-pool list [options] <resource-group> <lb-name>

Vypsat rozsah fondu IP adres back-endu pro konkrétní skupinu prostředků

    azure network lb address-pool list -g myresourcegroup -l mylb

    info:    Executing command network lb address-pool list
    + Looking up the load balancer "mylb"
    data:    Name           Provisioning state
    data:    -------------  ------------------
    data:    mybackendpool  Succeeded
    info:    network lb address-pool list command OK

Možnosti parametru:

     -h, --help                             output usage information
     -v, --verbose                          use verbose output
     --json                                 use json output
     -g, --resource-group <resource-group>  the name of the resource group
     -l, --lb-name <lb-name>                the name of the load balancer
     -s, --subscription <subscription>      the subscription identifier

<BR>

    network lb address-pool delete [options] <resource-group> <lb-name> <name>

Odebere prostředek rozsahu IP adres back-endu z nástroje pro vyrovnávání zatížení.

    azure network lb address-pool delete -g myresourcegroup -l mylb -n mybackendpool

    info:    Executing command network lb address-pool delete
    + Looking up the load balancer "mylb"
    Delete backend address pool "mybackendpool"? [y/n] y
    + Updating load balancer "mylb"
    info:    network lb address-pool delete command OK

Možnosti parametru:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -n, --name <name>                      the name of the backend address pool
    -q, --quiet                            quiet mode, do not ask for delete confirmation
    -s, --subscription <subscription>      the subscription identifier

**Příkazy pro správu pravidel nástroje pro vyrovnávání zatížení**

    network lb rule create [options] <resource-group> <lb-name> <name>
Vytvořte pravidla nástroje pro vyrovnávání zatížení.

Můžete vytvořit pravidlo nástroje pro vyrovnávání zatížení, které konfiguruje koncový bod front-end pro nástroj pro vyrovnávání zatížení a rozsah fondu adres back-endu pro příjem příchozího síťového provozu. Nastavení zahrnuje taky porty pro koncový bod a porty front-endu a rozsahy fondu adres back-endu.

Následující příklad ukazuje, jak vytvořit pravidlo nástroje pro vyrovnávání zatížení, koncový bod front 80-Endu TCP a vyrovnávání zatížení sítě, které odesílá port 8080 pro rozsah fondu back-end adres.

    azure network lb rule create -g myresourcegroup -l mylb -n mylbrule -p tcp -f 80 -b 8080 -i 10


    info:    Executing command network lb rule create
    + Looking up the load balancer "mylb"
    + Updating load balancer "mylb"
    + Loading rule state
    data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/loadBalancingRules/mylbrule
    data:    Name:                      mylbrule
    data:    Type:                      Microsoft.Network/loadBalancers/loadBalancingRules
    data:    Provisioning state:        Succeeded
    data:    Frontend IP configuration: /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
    data:    Backend address pool:      id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
    data:    Protocol:                  Tcp
    data:    Frontend port:             80
    data:    Backend port:              8080
    data:    Enable floating IP:        false
    data:    Idle timeout in minutes:   10
    data:    Probes
    data:
    info:    network lb rule create command OK

<BR>

    network lb rule set [options] <resource-group> <lb-name> <name>

Aktualizuje existující sadu pravidel nástroje pro vyrovnávání zatížení v konkrétní skupině prostředků. V následujícím příkladu jsme změnili název pravidla z mylbrule na mynewlbrule.

    azure network lb rule set -g myresourcegroup -l mylb -n mylbrule -r mynewlbrule -p tcp -f 80 -b 8080 -i 10 -t myfrontendip -o mybackendpool

    info:    Executing command network lb rule set
    + Looking up the load balancer "mylb"
    + Updating load balancer "mylb"
    + Loading rule state
    data:    Id:                        /subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/loadBalancingRules/mynewlbrule
    data:    Name:                      mynewlbrule
    data:    Type:                      Microsoft.Network/loadBalancers/loadBalancingRules
    data:    Provisioning state:        Succeeded
    data:    Frontend IP configuration: /subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
    data:    Backend address pool:      id=/subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
    data:    Protocol:                  Tcp
    data:    Frontend port:             80
    data:    Backend port:              8080
    data:    Enable floating IP:        false
    data:    Idle timeout in minutes:   10
    data:    Probes
    data:
    info:    network lb rule set command OK

Možnosti parametru:

    -h, --help                                         output usage information
    -v, --verbose                                      use verbose output
    --json                                             use json output
    -g, --resource-group <resource-group>              the name of the resource group
    -l, --lb-name <lb-name>                            the name of the load balancer
    -n, --name <name>                                  the name of the rule
    -r, --new-rule-name <new-rule-name>                new rule name
    -p, --protocol <protocol>                          the rule protocol
    -f, --frontend-port <frontend-port>                the frontend port
    -b, --backend-port <backend-port>                  the backend port
    -e, --enable-floating-ip <enable-floating-ip>      enable floating point ip
    -i, --idle-timeout <idle-timeout>                  the idle timeout in minutes
    -a, --probe-name [probe-name]                      the name of the probe defined in the same load balancer
    -t, --frontend-ip-name <frontend-ip-name>          the name of the frontend ip configuration in the same load balancer
    -o, --backend-address-pool <backend-address-pool>  name of the backend address pool defined in the same load balancer
    -s, --subscription <subscription>                  the subscription identifier


    network lb rule list [options] <resource-group> <lb-name>

Vypíše všechna pravidla nástroje pro vyrovnávání zatížení nakonfigurovaná pro nástroj pro vyrovnávání zatížení v konkrétní skupině prostředků.

    azure network lb rule list -g myresourcegroup -l mylb

    info:    Executing command network lb rule list
    + Looking up the load balancer "mylb"
    data:    Name         Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes  Backend address pool  Probe data

    data:    mynewlbrule  Succeeded           Tcp       80             8080          false               10                       /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
    info:    network lb rule list command OK

Možnosti parametru:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -s, --subscription <subscription>      the subscription identifier

    network lb rule delete [options] <resource-group> <lb-name> <name>

Odstraní pravidlo nástroje pro vyrovnávání zatížení.

    azure network lb rule delete -g myresourcegroup -l mylb -n mynewlbrule

    info:    Executing command network lb rule delete
    + Looking up the load balancer "mylb"
    Delete load balancing rule mynewlbrule? [y/n] y
    + Updating load balancer "mylb"
    info:    network lb rule delete command OK

Možnosti parametru:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -n, --name <name>                      the name of the rule
    -q, --quiet                            quiet mode, do not ask for delete confirmation
    -s, --subscription <subscription>      the subscription identifier

**Příkazy pro správu příchozích pravidel NAT pro vyrovnávání zatížení**

    network lb inbound-nat-rule create [options] <resource-group> <lb-name> <name>
Vytvoří pravidlo příchozího překladu adres (NAT) pro nástroj pro vyrovnávání zatížení.

V následujícím příkladu jsme vytvořili pravidlo překladu adres (NAT) z IP adresy front-endu (dříve definovaná pomocí příkazu "Azure Network Endu-IP") s příchozím portem naslouchání a odchozím portem, který nástroj pro vyrovnávání zatížení používá k odeslání síťového provozu.

    azure network lb inbound-nat-rule create -g myresourcegroup -l mylb -n myinboundnat -p tcp -f 80 -b 8080 -i myfrontendip

    info:    Executing command network lb inbound-nat-rule create
    + Looking up the load balancer "mylb"
    + Updating load balancer "mylb"
    + Looking up the load balancer "mylb"
    data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/inboundNatRules/myinboundnat
    data:    Name:                      myinboundnat
    data:    Type:                      Microsoft.Network/loadBalancers/inboundNatRules
    data:    Provisioning state:        Succeeded
    data:    Frontend IP Configuration: id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
    data:    Backend IP configuration
    data:    Protocol                   Tcp
    data:    Frontend port              80
    data:    Backend port               8080
    data:    Enable floating IP         false
    info:    network lb inbound-nat-rule create command OK

Možnosti parametru:

    -h, --help                                     output usage information
    -v, --verbose                                  use verbose output
    --json                                         use json output
    -g, --resource-group <resource-group>          the name of the resource group
    -l, --lb-name <lb-name>                        the name of the load balancer
    -n, --name <name>                              the name of the inbound NAT rule
    -p, --protocol <protocol>                      the rule protocol [tcp,udp]
    -f, --frontend-port <frontend-port>            the frontend port [0-65535]
    -b, --backend-port <backend-port>              the backend port [0-65535]
    -e, --enable-floating-ip <enable-floating-ip>  enable floating point ip [true,false]
    -i, --frontend-ip <frontend-ip>                the name of the frontend ip configuration
    -m, --vm-id <vm-id>                            the VM id.
    e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>
    -a, --vm-name <vm-name>                        the VM name.This VM must exist in the same resource group as the lb.
    Please use vm-id if that is not the case.
    this parameter will be ignored if --vm-id is specified
    -s, --subscription <subscription>              the subscription identifier
<BR>

    network lb inbound-nat-rule set [options] <resource-group> <lb-name> <name>
Aktualizuje stávající pravidlo příchozího překladu adres (NAT). V následujícím příkladu jsme změnili příchozí port naslouchání z 80 na 81.

    azure network lb inbound-nat-rule set -g group-1 -l mylb -n myinboundnat -p tcp -f 81 -b 8080 -i myfrontendip

    info:    Executing command network lb inbound-nat-rule set
    + Looking up the load balancer "mylb"
    + Updating load balancer "mylb"
    + Looking up the load balancer "mylb"
    data:    Id:                        /subscriptions/###############################/resourceGroups/group-1/providers/Microsoft.Network/loadBalancers/mylb/inboundNatRules/myinboundnat
    data:    Name:                      myinboundnat
    data:    Type:                      Microsoft.Network/loadBalancers/inboundNatRules
    data:    Provisioning state:        Succeeded
    data:    Frontend IP Configuration: id=/subscriptions/###############################/resourceGroups/group-1/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
    data:    Backend IP configuration
    data:    Protocol                   Tcp
    data:    Frontend port              81
    data:    Backend port               8080
    data:    Enable floating IP         false
    info:    network lb inbound-nat-rule set command OK

Možnosti parametru:

    -h, --help                                     output usage information
    -v, --verbose                                  use verbose output
    --json                                         use json output
    -g, --resource-group <resource-group>          the name of the resource group
    -l, --lb-name <lb-name>                        the name of the load balancer
    -n, --name <name>                              the name of the inbound NAT rule
    -p, --protocol <protocol>                      the rule protocol [tcp,udp]
    -f, --frontend-port <frontend-port>            the frontend port [0-65535]
    -b, --backend-port <backend-port>              the backend port [0-65535]
    -e, --enable-floating-ip <enable-floating-ip>  enable floating point ip [true,false]
    -i, --frontend-ip <frontend-ip>                the name of the frontend ip configuration
    -m, --vm-id [vm-id]                            the VM id.
    e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>
    -a, --vm-name <vm-name>                        the VM name.
    This virtual machine must exist in the same resource group as the lb.
    Please use vm-id if that is not the case
    -s, --subscription <subscription>              the subscription identifier
<BR>

    network lb inbound-nat-rule list [options] <resource-group> <lb-name>

Zobrazí všechna příchozí pravidla překladu adres (NAT) pro nástroj pro vyrovnávání zatížení.

    azure network lb inbound-nat-rule list -g myresourcegroup -l mylb

    info:    Executing command network lb inbound-nat-rule list
    + Looking up the load balancer "mylb"
    data:    Name          Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes  Backend IP configuration
    data:    ------------  ------------------  --------  -------------  ------------  ------------------  -----------------------  ---
    ---------------------
    data:    myinboundnat  Succeeded           Tcp       81             8080          false               4

    info:    network lb inbound-nat-rule list command OK

Možnosti parametru:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -s, --subscription <subscription>      the subscription identifier
<BR>

    network lb inbound-nat-rule delete [options] <resource-group> <lb-name> <name>

Odstraní pravidlo překladu adres (NAT) pro nástroj pro vyrovnávání zatížení v konkrétní skupině prostředků.

    azure network lb inbound-nat-rule delete -g myresourcegroup -l mylb -n myinboundnat

    info:    Executing command network lb inbound-nat-rule delete
    + Looking up the load balancer "mylb"
    Delete inbound NAT rule "myinboundnat?" [y/n] y
    + Updating load balancer "mylb"
    info:    network lb inbound-nat-rule delete command OK

Možnosti parametru:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -n, --name <name>                      the name of the inbound NAT rule
    -q, --quiet                            quiet mode, do not ask for delete confirmation
    -s, --subscription <subscription>      the subscription identifier

**Příkazy pro správu veřejných IP adres**

    network public-ip create [options] <resource-group> <name> <location>
Vytvoří prostředek veřejné IP adresy. Vytvoříte prostředek veřejné IP adresy a přidružíte ho k názvu domény.

    azure network public-ip create -g myresourcegroup -n mytestpublicip1 -l eastus -d azureclitest -a "Dynamic"
    info:    Executing command network public-ip create
    + Looking up the public ip "mytestpublicip1"
    + Creating public ip address "mytestpublicip1"
    + Looking up the public ip "mytestpublicip1"
    data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip1
    data:    Name:                 mytestpublicip1
    data:    Type:                 Microsoft.Network/publicIPAddresses
    data:    Location:             eastus
    data:    Provisioning state:   Succeeded
    data:    Allocation method:    Dynamic
    data:    Idle timeout:         4
    data:    Domain name label:    azureclitest
    data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
    info:    network public-ip create command OK


Možnosti parametru:

    -h, --help                                   output usage information
    -v, --verbose                                use verbose output
    --json                                       use json output
    -g, --resource-group <resource-group>        the name of the resource group
    -n, --name <name>                            the name of the public ip
    -l, --location <location>                    the location
    -d, --domain-name-label <domain-name-label>  the domain name label.
    This set DNS to <domain-name-label>.<location>.cloudapp.azure.com
    -a, --allocation-method <allocation-method>  the allocation method [Static][Dynamic]
    -i, --idletimeout <idletimeout>              the idle timeout in minutes
    -f, --reverse-fqdn <reverse-fqdn>            the reverse fqdn
    -t, --tags <tags>                            the list of tags.
    Can be multiple. In the format of "name=value".
    Name is required and value is optional.
    For example, -t tag1=value1;tag2
    -s, --subscription <subscription>            the subscription identifier
<br>

    network public-ip set [options] <resource-group> <name>
Aktualizuje vlastnosti existujícího prostředku veřejné IP adresy. V následujícím příkladu jsme změnili veřejnou IP adresu z dynamické na statickou.

    azure network public-ip set -g group-1 -n mytestpublicip1 -d azureclitest -a "Static"
    info:    Executing command network public-ip set
    + Looking up the public ip "mytestpublicip1"
    + Updating public ip address "mytestpublicip1"
    + Looking up the public ip "mytestpublicip1"
    data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip1
    data:    Name:                 mytestpublicip1
    data:    Type:                 Microsoft.Network/publicIPAddresses
    data:    Location:             eastus
    data:    Provisioning state:   Succeeded
    data:    Allocation method:    Static
    data:    Idle timeout:         4
    data:    IP Address:           (static IP address)
    data:    Domain name label:    azureclitest
    data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
    info:    network public-ip set command OK

Možnosti parametru:

    -h, --help                                   output usage information
    -v, --verbose                                use verbose output
    --json                                       use json output
    -g, --resource-group <resource-group>        the name of the resource group
    -n, --name <name>                            the name of the public ip
    -d, --domain-name-label [domain-name-label]  the domain name label.
    This set DNS to <domain-name-label>.<location>.cloudapp.azure.com
    -a, --allocation-method <allocation-method>  the allocation method [Static][Dynamic]
    -i, --idletimeout <idletimeout>              the idle timeout in minutes
    -f, --reverse-fqdn [reverse-fqdn]            the reverse fqdn
    -t, --tags <tags>                            the list of tags.
    Can be multiple. In the format of "name=value".
    Name is required and value is optional.
    For example, -t tag1=value1;tag2
    --no-tags                                    remove all existing tags
    -s, --subscription <subscription>            the subscription identifier

<br>

    network public-ip list [options] <resource-group>
Zobrazí seznam všech prostředků veřejné IP adresy v rámci skupiny prostředků.

    azure network public-ip list -g myresourcegroup

    info:    Executing command network public-ip list
    + Getting the public ip addresses
    data:    Name             Location  Allocation  IP Address    Idle timeout  DNS Name
    data:    ---------------  --------  ----------  ------------  ------------  -------------------------------------------
    data:    mypubip5         westus    Dynamic                   4             "domain name".westus.cloudapp.azure.com
    data:    myPublicIP       eastus    Dynamic                   4             "domain name".eastus.cloudapp.azure.com
    data:    mytestpublicip   eastus    Dynamic                   4             "domain name".eastus.cloudapp.azure.com
    data:    mytestpublicip1  eastus   Static (Static IP address) 4             azureclitest.eastus.cloudapp.azure.com

Možnosti parametru:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -s, --subscription <subscription>      the subscription identifier

<BR>

    network public-ip show [options] <resource-group> <name>

Zobrazí vlastnosti veřejné IP adresy pro prostředek veřejné IP adresy v rámci skupiny prostředků.

    azure network public-ip show -g myresourcegroup -n mytestpublicip

    info:    Executing command network public-ip show
    + Looking up the public ip "mytestpublicip1"
    data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip
    data:    Name:                 mytestpublicip
    data:    Type:                 Microsoft.Network/publicIPAddresses
    data:    Location:             eastus
    data:    Provisioning state:   Succeeded
    data:    Allocation method:    Static
    data:    Idle timeout:         4
    data:    IP Address:           (static IP address)
    data:    Domain name label:    azureclitest
    data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
    info:    network public-ip show command OK

Možnosti parametru:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -n, --name <name>                      the name of the public IP
    -s, --subscription <subscription>      the subscription identifier


    network public-ip delete [options] <resource-group> <name>

Odstraní prostředek veřejné IP adresy.

    azure network public-ip delete -g group-1 -n mypublicipname
    info:    Executing command network public-ip delete
    + Looking up the public ip "mypublicipname"
    Delete public ip address "mypublicipname"? [y/n] y
    + Deleting public ip address "mypublicipname"
    info:    network public-ip delete command OK

Možnosti parametru:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -n, --name <name>                      the name of the public IP
    -q, --quiet                            quiet mode, do not ask for delete confirmation
    -s, --subscription <subscription>      the subscription identifier


**Příkazy pro správu síťových rozhraní**

    network nic create [options] <resource-group> <name> <location>
Vytvoří prostředek nazvaný síťové rozhraní (NIC), který se dá použít pro nástroje pro vyrovnávání zatížení nebo k virtuálnímu počítači.

    azure network nic create -g myresourcegroup -l eastus -n testnic1 --subnet-name subnet-1 --subnet-vnet-name myvnet

    info:    Executing command network nic create
    + Looking up the network interface "testnic1"
    + Looking up the subnet "subnet-1"
    + Creating network interface "testnic1"
    + Looking up the network interface "testnic1"
    data:    Id:                     /subscriptions/c4a17ddf-aa84-491c-b6f9-b90d882299f7/resourceGroups/group-1/providers/Microsoft.Network/networkInterfaces/testnic1
    data:    Name:                   testnic1
    data:    Type:                   Microsoft.Network/networkInterfaces
    data:    Location:               eastus
    data:    Provisioning state:     Succeeded
    data:    IP configurations:
    data:       Name:                         NIC-config
    data:       Provisioning state:           Succeeded
    data:       Private IP address:           10.0.0.5
    data:       Private IP Allocation Method: Dynamic
    data:       Subnet:                       /subscriptions/c4a17ddf-aa84-491c-b6f9-b90d882299f7/resourceGroups/group-1/providers/Microsoft.Network/virtualNetworks/myVNET/subnets/Subnet-1

Možnosti parametru:

    -h, --help                                                       output usage information
    -v, --verbose                                                    use verbose output
    --json                                                           use json output
    -g, --resource-group <resource-group>                            the name of the resource group
    -n, --name <name>                                                the name of the network interface
    -l, --location <location>                                        the location
    -w, --network-security-group-id <network-security-group-id>      the network security group identifier.
    e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkSecurityGroups/<nsg-name>
    -o, --network-security-group-name <network-security-group-name>  the network security group name.
    This network security group must exist in the same resource group as the nic.
    Please use network-security-group-id if that is not the case.
    -i, --public-ip-id <public-ip-id>                                the public IP identifier.
    e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/publicIPAddresses/<public-ip-name>
    -p, --public-ip-name <public-ip-name>                            the public IP name.
    This public ip must exist in the same resource group as the nic.
    Please use public-ip-id if that is not the case.
    -a, --private-ip-address <private-ip-address>                    the private IP address
    -u, --subnet-id <subnet-id>                                      the subnet identifier.
    e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>/subnets/<subnet-name>
    --subnet-name <subnet-name>                                  the subnet name
    -m, --subnet-vnet-name <subnet-vnet-name>                        the vnet name under which subnet-name exists
    -t, --tags <tags>                                                the comma separated list of tags.
    Can be multiple. In the format of "name=value".
    Name is required and value is optional.
    For example, -t tag1=value1;tag2
    -s, --subscription <subscription>                                the subscription identifier
    data:
    info:    network nic create command OK

<BR>

    network nic set [options] <resource-group> <name>

    network nic list [options] <resource-group>
    network nic show [options] <resource-group> <name>
    network nic delete [options] <resource-group> <name>

**Příkazy pro správu skupin zabezpečení sítě**

    network nsg create [options] <resource-group> <name> <location>
    network nsg set [options] <resource-group> <name>
    network nsg list [options] <resource-group>
    network nsg show [options] <resource-group> <name>
    network nsg delete [options] <resource-group> <name>

**Příkazy pro správu pravidel skupin zabezpečení sítě**

    network nsg rule create [options] <resource-group> <nsg-name> <name>
    network nsg rule set [options] <resource-group> <nsg-name> <name>
    network nsg rule list [options] <resource-group> <nsg-name>
    network nsg rule show [options] <resource-group> <nsg-name> <name>
    network nsg rule delete [options] <resource-group> <nsg-name> <name>

**Příkazy pro správu profilu Traffic Manageru**

    network traffic-manager profile create [options] <resource-group> <name>
    network traffic-manager profile set [options] <resource-group> <name>
    network traffic-manager profile list [options] <resource-group>
    network traffic-manager profile show [options] <resource-group> <name>
    network traffic-manager profile delete [options] <resource-group> <name>
    network traffic-manager profile is-dns-available [options] <resource-group> <relative-dns-name>

**Příkazy pro správu koncových bodů Traffic Manageru**

    network traffic-manager profile endpoint create [options] <resource-group> <profile-name> <name> <endpoint-location>
    network traffic-manager profile endpoint set [options] <resource-group> <profile-name> <name>
    network traffic-manager profile endpoint delete [options] <resource-group> <profile-name> <name>

**Příkazy pro správu bran virtuální sítě**

    network gateway list [options] <resource-group>

## <a name="resource-provider-registrations"></a>Registrace poskytovatele prostředků
**Seznam aktuálně registrovaných zprostředkovatelů v Správce prostředků**

    provider list [options]

**Zobrazit podrobnosti o požadovaném oboru názvů poskytovatele**

    provider show [options] <namespace>

**Registrovat poskytovatele u předplatného**

    provider register [options] <namespace>

**Zrušení registrace poskytovatele k předplatnému**

    provider unregister [options] <namespace>

## <a name="resources"></a>Prostředky
**Vytvoří prostředek ve skupině prostředků.**

    resource create [options] <resource-group> <name> <resource-type> <location> <api-version>

**Aktualizuje prostředek ve skupině prostředků bez jakýchkoli šablon nebo parametrů.**

    resource set [options] <resource-group> <name> <resource-type> <properties> <api-version>

**Zobrazí seznam prostředků.**

    resource list [options] [resource-group]

**Získá jeden prostředek v rámci skupiny prostředků nebo předplatného.**

    resource show [options] <resource-group> <name> <resource-type> <api-version>

**Odstraní prostředek ve skupině prostředků.**

    resource delete [options] <resource-group> <name> <resource-type> <api-version>

## <a name="azure-roles"></a>Role Azure
**Získat všechny dostupné definice rolí**

    role list [options]

**Získat dostupnou definici role**

    role show [options] [name]

**Příkazy pro správu přiřazení role**

    role assignment create [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
    role assignment list [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
    role assignment delete [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]

## <a name="storage-objects"></a>Objekty úložiště
**Příkazy pro správu účtů úložiště**

    storage account list [options]
    storage account show [options] <name>
    storage account create [options] <name>
    storage account set [options] <name>
    storage account delete [options] <name>

**Příkazy pro správu klíčů účtu úložiště**

    storage account keys list [options] <name>
    storage account keys renew [options] <name>

**Příkazy pro zobrazení připojovacího řetězce úložiště**

    storage account connectionstring show [options] <name>

**Příkazy pro správu kontejnerů úložiště**

    storage container list [options] [prefix]
    storage container show [options] [container]
    storage container create [options] [container]
    storage container delete [options] [container]
    storage container set [options] [container]

**Příkazy pro správu sdílených přístupových podpisů kontejneru úložiště**

    storage container sas create [options] [container] [permissions] [expiry]

**Příkazy pro správu uložených zásad přístupu kontejneru úložiště**

    storage container policy create [options] [container] [name]
    storage container policy show [options] [container] [name]
    storage container policy list [options] [container]
    storage container policy set [options] [container] [name]
    storage container policy delete [options] [container] [name]

**Příkazy pro správu objektů BLOB úložiště**

    storage blob list [options] [container] [prefix]
    storage blob show [options] [container] [blob]
    storage blob delete [options] [container] [blob]
    storage blob upload [options] [file] [container] [blob]
    storage blob download [options] [container] [blob] [destination]

**Příkazy pro správu operací kopírování objektů BLOB**

    storage blob copy start [options] [sourceUri] [destContainer]
    storage blob copy show [options] [container] [blob]
    storage blob copy stop [options] [container] [blob] [copyid]

**Příkazy pro správu sdíleného přístupového podpisu objektu BLOB úložiště**

    storage blob sas create [options] [container] [blob] [permissions] [expiry]

**Příkazy pro správu sdílených složek úložiště**

    storage share create [options] [share]
    storage share show [options] [share]
    storage share delete [options] [share]
    storage share list [options] [prefix]

**Příkazy pro správu souborů úložiště**

    storage file list [options] [share] [path]
    storage file delete [options] [share] [path]
    storage file upload [options] [source] [share] [path]
    storage file download [options] [share] [path] [destination]

**Příkazy pro správu adresáře souborů úložiště**

    storage directory create [options] [share] [path]
    storage directory delete [options] [share] [path]

**Příkazy pro správu front úložiště**

    storage queue create [options] [queue]
    storage queue list [options] [prefix]
    storage queue show [options] [queue]
    storage queue delete [options] [queue]

**Příkazy pro správu sdílených přístupových podpisů vaší fronty úložiště**

    storage queue sas create [options] [queue] [permissions] [expiry]

**Příkazy pro správu uložených zásad přístupu ve vaší frontě úložiště**

    storage queue policy create [options] [queue] [name]
    storage queue policy show [options] [queue] [name]
    storage queue policy list [options] [queue]
    storage queue policy set [options] [queue] [name]
    storage queue policy delete [options] [queue] [name]

**Příkazy pro správu vlastností protokolování úložiště**

    storage logging show [options]
    storage logging set [options]

**Příkazy pro správu vlastností metrik úložiště**

    storage metrics show [options]
    storage metrics set [options]

**Příkazy pro správu tabulek úložiště**

    storage table create [options] [table]
    storage table list [options] [prefix]
    storage table show [options] [table]
    storage table delete [options] [table]

**Příkazy pro správu sdílených přístupových podpisů tabulky úložiště**

    storage table sas create [options] [table] [permissions] [expiry]

**Příkazy pro správu uložených zásad přístupu pro tabulku úložiště**

    storage table policy create [options] [table] [name]
    storage table policy show [options] [table] [name]
    storage table policy list [options] [table]
    storage table policy set [options] [table] [name]
    storage table policy delete [options] [table] [name]

## <a name="tags"></a>Značky
**Přidat značku**

    tag create [options] <name> <value>

**Odebrání celé značky nebo hodnoty značky**

    tag delete [options] <name> <value>

**Zobrazí seznam informací o značce.**

    tag list [options]

**Získat značku**

    tag show [options] [name]

## <a name="virtual-machines"></a>Virtuální počítače
**Vytvoření virtuálního počítače**

    vm create [options] <resource-group> <name> <location> <os-type>

**Vytvoření virtuálního počítače s výchozími prostředky**

    vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password

> [!TIP]
> Počínaje rozhraním CLI verze 0,10 můžete zadat krátký alias, jako je například "UbuntuLTS" nebo "Win2012R2Datacenter", jako `image-urn` u některých oblíbených imagí na webu Marketplace. Pro možnosti spusťte `azure help vm quick-create`. Kromě toho počínaje verzí 0,10 používá `azure vm quick-create` standardně Storage úrovně Premium, pokud je k dispozici ve vybrané oblasti.
> 
> 

**Výpis virtuálních počítačů v rámci účtu**

    vm list [options]

**Získání jednoho virtuálního počítače v rámci skupiny prostředků**

    vm show [options] <resource-group> <name>

**Odstranění jednoho virtuálního počítače v rámci skupiny prostředků**

    vm delete [options] <resource-group> <name>

**Vypnutí jednoho virtuálního počítače v rámci skupiny prostředků**

    vm stop [options] <resource-group> <name>

**Restartování jednoho virtuálního počítače v rámci skupiny prostředků**

    vm restart [options] <resource-group> <name>

**Spuštění jednoho virtuálního počítače v rámci skupiny prostředků**

    vm start [options] <resource-group> <name>

**Vypnutí jednoho virtuálního počítače v rámci skupiny prostředků a uvolnění výpočetních prostředků**

    vm deallocate [options] <resource-group> <name>

**Zobrazit seznam dostupných velikostí virtuálních počítačů**

    vm sizes [options]

**Zachycení virtuálního počítače jako image operačního systému nebo image virtuálního počítače**

    vm capture [options] <resource-group> <name> <vhd-name-prefix>

**Nastavení stavu virtuálního počítače na zobecněný**

    vm generalize [options] <resource-group> <name>

**Získat zobrazení instance virtuálního počítače**

    vm get-instance-view [options] <resource-group> <name>

**Umožňuje resetovat přístup ke vzdálené ploše nebo nastavení SSH na virtuálním počítači a resetovat heslo pro účet, který má oprávnění správce nebo sudo.**

    vm reset-access [options] <resource-group> <name>

**Aktualizace virtuálního počítače s novými daty**

    vm set [options] <resource-group> <name>

**Příkazy pro správu datových disků virtuálního počítače**

    vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]
    vm disk detach [options] <resource-group> <vm-name> <lun>
    vm disk attach [options] <resource-group> <vm-name> [vhd-url]

**Příkazy pro správu rozšíření prostředků virtuálních počítačů**

    vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>
    vm extension get [options] <resource-group> <vm-name>

**Příkazy pro správu virtuálního počítače Docker**

    vm docker create [options] <resource-group> <name> <location> <os-type>

**Příkazy pro správu imagí virtuálních počítačů**

    vm image list-publishers [options] <location>
    vm image list-offers [options] <location> <publisher>
    vm image list-skus [options] <location> <publisher> <offer>
    vm image list [options] <location> <publisher> [offer] [sku]
