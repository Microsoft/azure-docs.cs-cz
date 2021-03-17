---
title: Vytváření virtuálních sítí pro clustery Azure HDInsight
description: Naučte se, jak vytvořit Virtual Network Azure pro připojení HDInsight k ostatním cloudovým prostředkům nebo prostředkům ve vašem datovém centru.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/16/2020
ms.openlocfilehash: 43d57eac94cabb5c648183911e0c0bf72889946d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946065"
---
# <a name="create-virtual-networks-for-azure-hdinsight-clusters"></a>Vytváření virtuálních sítí pro clustery Azure HDInsight

Tento článek popisuje příklady a ukázky kódu pro vytváření a konfiguraci [virtuálních sítí Azure](../virtual-network/virtual-networks-overview.md). Pro použití s clustery Azure HDInsight. Zobrazí se podrobné příklady vytváření skupin zabezpečení sítě (skupin zabezpečení sítě) a konfigurace DNS.

Základní informace o používání virtuálních sítí s Azure HDInsight najdete v tématu [plánování virtuální sítě pro Azure HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="prerequisites-for-code-samples-and-examples"></a>Předpoklady pro ukázky kódu a příklady

Před spuštěním některé z ukázek kódu v tomto článku se porozumí síť TCP/IP. Pokud nejste obeznámeni s používáním sítě TCP/IP, obraťte se na někoho, než provedete úpravy v produkčních sítích.

Další požadavky pro ukázky v tomto článku zahrnují následující položky:

* Pokud používáte PowerShell, budete muset nainstalovat [modul AZ Module](/powershell/azure/).
* Pokud chcete použít rozhraní příkazového řádku Azure a ještě není nainstalované, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

> [!IMPORTANT]  
> Pokud hledáte podrobné pokyny k připojení HDInsight k místní síti pomocí Virtual Network Azure, přečtěte si téma [připojení HDInsight k místní síťové síti](connect-on-premises-network.md) .

## <a name="example-network-security-groups-with-hdinsight"></a><a id="hdinsight-nsg"></a>Příklad: skupiny zabezpečení sítě se službou HDInsight

Příklady v této části ukazují, jak vytvořit pravidla skupiny zabezpečení sítě. Pravidla umožňují službě HDInsight komunikovat se službami Azure Management Services. Než začnete používat příklady, upravte IP adresy tak, aby odpovídaly těm pro oblast Azure, kterou používáte. Tyto informace najdete v [IP adresách správy HDInsight](hdinsight-management-ip-addresses.md).

### <a name="azure-resource-management-template"></a>Šablona správy prostředků Azure

Následující šablona správy prostředků vytvoří virtuální síť, která omezuje příchozí provoz, ale umožňuje provoz z IP adres vyžadovaných službou HDInsight. Tato šablona také vytvoří cluster HDInsight ve virtuální síti.

* [Nasazení zabezpečeného Virtual Network Azure a clusteru HDInsight Hadoop](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

### <a name="azure-powershell"></a>Azure PowerShell

Pomocí následujícího skriptu PowerShellu vytvořte virtuální síť, která omezí příchozí provoz a povolí provoz z IP adres pro oblast Severní Evropa.

> [!IMPORTANT]  
> Změňte IP adresy pro `hdirule1` a `hdirule2` v tomto příkladu tak, aby odpovídaly oblasti Azure, kterou používáte. Tyto informace najdete v informacích o [IP adresách správy HDInsight](hdinsight-management-ip-addresses.md).

```azurepowershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"

# Get the Virtual Network object
$vnet = Get-AzVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName

# Get the region the Virtual network is in.
$location = $vnet.Location

# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName

# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `

# Set the changes to the security group
Set-AzNetworkSecurityGroup -NetworkSecurityGroup $nsg

# Apply the NSG to the subnet
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

Tento příklad ukazuje, jak přidat pravidla, která povolují příchozí přenosy na požadovaných IP adresách. Neobsahuje pravidlo pro omezení příchozího přístupu z jiných zdrojů. Následující kód ukazuje, jak povolit přístup přes SSH z Internetu:

```azurepowershell
Get-AzNetworkSecurityGroup -Name hdisecure -ResourceGroupName RESOURCEGROUP |
Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
```

### <a name="azure-cli"></a>Azure CLI

Pomocí následujících kroků můžete vytvořit virtuální síť, která omezuje příchozí provoz, ale umožňuje provoz z IP adres vyžadovaných službou HDInsight.

1. Pomocí následujícího příkazu vytvořte novou skupinu zabezpečení sítě s názvem `hdisecure` . Nahraďte `RESOURCEGROUP` skupinou prostředků, která obsahuje Virtual Network Azure. Nahraďte `LOCATION` umístěním (oblastí), ve kterém se skupina vytvořila.

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    Po vytvoření skupiny obdržíte informace o nové skupině.

2. Pomocí následujících kroků přidejte do nové skupiny zabezpečení sítě pravidla, která povolí příchozí komunikaci na portu 443 od služby Azure HDInsight Health and Management Service. Nahraďte `RESOURCEGROUP` názvem skupiny prostředků, která obsahuje Virtual Network Azure.

    > [!IMPORTANT]  
    > Změňte IP adresy pro `hdirule1` a `hdirule2` v tomto příkladu tak, aby odpovídaly oblasti Azure, kterou používáte. Tyto informace najdete v [IP adresách správy HDInsight](hdinsight-management-ip-addresses.md).

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Chcete-li načíst jedinečný identifikátor pro tuto skupinu zabezpečení sítě, použijte následující příkaz:

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query "id"
    ```

    Tento příkaz vrátí hodnotu podobnou následujícímu textu:

    ```output
    "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

4. Pomocí následujícího příkazu použijte skupinu zabezpečení sítě na podsíť. `GUID`Hodnoty a nahraďte `RESOURCEGROUP` těmi vrácenými z předchozího kroku. Nahraďte `VNETNAME` a názvem `SUBNETNAME` virtuální sítě a názvem podsítě, který chcete vytvořit.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Po dokončení tohoto příkazu můžete HDInsight nainstalovat do Virtual Network.

Tímto postupem otevřete jenom přístup ke službě stavu a správy HDInsight v cloudu Azure. Jakýkoli jiný přístup ke clusteru HDInsight z vnějšku Virtual Network je blokovaný. Pokud chcete povolit přístup mimo virtuální síť, musíte přidat další pravidla skupiny zabezpečení sítě.

Následující kód ukazuje, jak povolit přístup přes SSH z Internetu:

```azurecli
az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n ssh --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
```

## <a name="example-dns-configuration"></a><a id="example-dns"></a> Příklad: Konfigurace DNS

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Překlad názvů mezi virtuální sítí a připojenou místní sítí

Tento příklad provede následující předpoklady:

* Máte Virtual Network Azure, která je připojená k místní síti pomocí brány VPN.

* Vlastní server DNS ve virtuální síti používá jako operační systém Linux nebo UNIX.

* [Vazba](https://www.isc.org/downloads/bind/) je nainstalovaná na vlastním serveru DNS.

Na vlastním serveru DNS ve virtuální síti:

1. K vyhledání přípony DNS virtuální sítě použijte buď Azure PowerShell, nebo Azure CLI:

    Nahraďte `RESOURCEGROUP` názvem skupiny prostředků, která obsahuje virtuální síť, a pak zadejte příkaz:

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

1. Na vlastním serveru DNS pro virtuální síť použijte jako obsah souboru následující text `/etc/bind/named.conf.local` :

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Nahraďte `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` hodnotu příponou DNS vaší virtuální sítě.

    Tato konfigurace směruje všechny požadavky DNS na příponu DNS virtuální sítě do rekurzivního překladače Azure.

1. Na vlastním serveru DNS pro virtuální síť použijte jako obsah souboru následující text `/etc/bind/named.conf.options` :

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Hodnotu nahraďte `10.0.0.0/16` rozsahem IP adres vaší virtuální sítě. Tato položka umožňuje adresám požadavků na překlad názvů v rámci tohoto rozsahu.

    * Přidejte do oddílu rozsah IP adres místní sítě `acl goodclients { ... }` .  položka povoluje požadavky na překlad názvů z prostředků v místní síti.
    
    * Hodnotu nahraďte `192.168.0.1` IP adresou vašeho místního serveru DNS. Tato položka směruje všechny ostatní požadavky DNS na místní server DNS.

1. Chcete-li použít konfiguraci, restartujte službu BIND. Například `sudo service bind9 restart`.

1. Přidejte podmíněný Server pro směrování do místního serveru DNS. Nakonfigurujte podmíněný Server pro přeposílání tak, aby odesílal požadavky na příponu DNS z kroku 1 na vlastní server DNS.

    > [!NOTE]  
    > Konkrétní informace o tom, jak přidat podmíněný předávací server, najdete v dokumentaci k vašemu softwaru DNS.

Po dokončení těchto kroků se můžete připojit k prostředkům v libovolné síti pomocí plně kvalifikovaných názvů domény (FQDN). Nyní můžete HDInsight nainstalovat do virtuální sítě.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Překlad názvů mezi dvěma připojenými virtuálními sítěmi

Tento příklad provede následující předpoklady:

* Máte dvě virtuální sítě Azure, které jsou připojené pomocí brány VPN nebo partnerského vztahu.

* Vlastní server DNS v obou sítích používá jako operační systém Linux nebo UNIX.

* [Vazba](https://www.isc.org/downloads/bind/) je nainstalována na vlastní servery DNS.

1. Pro vyhledání přípony DNS obou virtuálních sítí použijte buď Azure PowerShell, nebo rozhraní příkazového řádku Azure.

    Nahraďte `RESOURCEGROUP` názvem skupiny prostředků, která obsahuje virtuální síť, a pak zadejte příkaz:

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Jako obsah `/etc/bind/named.config.local` souboru na vlastním serveru DNS použijte následující text. Tuto změnu udělejte na vlastním serveru DNS v obou virtuálních sítích.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Nahraďte `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` hodnotu příponou DNS __druhé__ virtuální sítě. Tato položka směruje požadavky na příponu DNS vzdálené sítě do vlastního DNS v dané síti.

3. Na vlastních serverech DNS v obou virtuálních sítích použijte jako obsah souboru následující text `/etc/bind/named.conf.options` :

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

   `10.0.0.0/16`Hodnoty a nahraďte `10.1.0.0/16` rozsahy IP adres virtuálních sítí. Tato položka umožňuje prostředkům v každé síti vytvářet požadavky na servery DNS.

    Všechny požadavky, které nejsou pro přípony DNS virtuálních sítí (například microsoft.com), se zpracovávají pomocí rekurzivního překladače Azure.

4. Chcete-li použít konfiguraci, restartujte službu BIND. Například `sudo service bind9 restart` na serverech DNS.

Po dokončení těchto kroků se můžete připojit k prostředkům ve virtuální síti pomocí plně kvalifikovaných názvů domény (FQDN). Nyní můžete HDInsight nainstalovat do virtuální sítě.

## <a name="next-steps"></a>Další kroky

* Úplný příklad konfigurace služby HDInsight pro připojení k místní síti najdete v tématu [připojení HDInsight k místní síti](./connect-on-premises-network.md).
* Informace o konfigurování clusterů Apache HBA v Azure Virtual Networks najdete v tématu [vytváření clusterů Apache HBA v HDInsight ve službě azure Virtual Network](hbase/apache-hbase-provision-vnet.md).
* Informace týkající se konfigurace geografické replikace Apache HBA najdete [v tématu Nastavení replikace clusteru Apache HBA v Azure Virtual Networks](hbase/apache-hbase-replication.md).
* Další informace o virtuálních sítích Azure najdete v tématu [Přehled azure Virtual Network](../virtual-network/virtual-networks-overview.md).

* Další informace o skupinách zabezpečení sítě najdete v tématu [skupiny zabezpečení sítě](../virtual-network/network-security-groups-overview.md).

* Další informace o trasách definovaných uživatelem najdete v tématu [trasy definované uživatelem a předávání IP](../virtual-network/virtual-networks-udr-overview.md).
