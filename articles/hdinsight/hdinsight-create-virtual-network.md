---
title: Vytváření virtuálních sítí pro clustery Azure HDInsight
description: Zjistěte, jak vytvořit virtuální síť Azure pro připojení HDInsightu k jiným cloudovým prostředkům nebo prostředkům ve vašem datovém centru.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 6fd23e3d41dda15b1ec439c1e8b02073722b8871
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272536"
---
# <a name="create-virtual-networks-for-azure-hdinsight-clusters"></a>Vytváření virtuálních sítí pro clustery Azure HDInsight

Tento článek obsahuje příklady a ukázky kódu pro vytváření a konfiguraci [virtuálních sítí Azure](../virtual-network/virtual-networks-overview.md) pro použití s clustery Azure HDInsight. Zobrazí se podrobné příklady vytváření skupin zabezpečení sítě (NSG) a konfigurace služby DNS. 

Základní informace o používání virtuálních sítí s Azure HDInsight najdete v [tématu Plánování virtuální sítě pro Azure HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="prerequisites-for-code-samples-and-examples"></a>Požadavky na ukázky kódu a příklady

Před spuštěním některého z ukázek kódu v tomto článku by měl mít ou znalosti o sítích TCP/IP. Pokud nejste obeznámeni se sítí TCP/IP, obraťte se na někoho, kdo provádí změny v produkčních sítích.

Další předpoklady pro ukázky v tomto článku zahrnují následující:

* Pokud používáte prostředí PowerShell, budete muset nainstalovat [modul AZ](https://docs.microsoft.com/powershell/azure/overview).
* Pokud chcete používat Azure CLI a ještě jste ho nenainstalovali, přečtěte si informace [o instalaci příkazového příkazového příkazu k azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

> [!IMPORTANT]  
> Pokud hledáte podrobné pokyny pro připojení HDInsightu k místní síti pomocí virtuální sítě Azure, [přečtěte si odkaz na připojení HDInsightu k místnímu síťovému](connect-on-premises-network.md) dokumentu.

## <a name="example-network-security-groups-with-hdinsight"></a><a id="hdinsight-nsg"></a>Příklad: skupiny zabezpečení sítě s HDInsight

Příklady v této části ukazují, jak vytvořit pravidla skupiny zabezpečení sítě, která umožňují HDInsight komunikovat se službami správy Azure. Před použitím příkladů upravte IP adresy tak, aby odpovídaly adresám pro oblast Azure, kterou používáte. Tyto informace naleznete v [IP adresách hdinsight pro správu](hdinsight-management-ip-addresses.md).

### <a name="azure-resource-management-template"></a>Šablona Azure Resource Management

Následující šablona správy prostředků vytvoří virtuální síť, která omezuje příchozí provoz, ale umožňuje přenosy z IP adres vyžadované hdinsight. Tato šablona také vytvoří cluster HDInsight ve virtuální síti.

* [Nasazení zabezpečené virtuální sítě Azure a clusteru HDInsight Hadoop](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

### <a name="azure-powershell"></a>Azure PowerShell

Pomocí následujícího skriptu prostředí PowerShell vytvořte virtuální síť, která omezuje příchozí provoz a umožňuje přenos y z IP adres pro oblast Severní Evropa.

> [!IMPORTANT]  
> Změňte IP adresy `hdirule1` `hdirule2` pro a v tomto příkladu tak, aby odpovídaly oblasti Azure, kterou používáte. Tyto informace naleznete na [IP adresách hdinsight pro správu](hdinsight-management-ip-addresses.md).

```powershell
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

Tento příklad ukazuje, jak přidat pravidla povolit příchozí provoz na požadovaných adresÁCH IP. Neobsahuje pravidlo pro omezení příchozího přístupu z jiných zdrojů. Následující kód ukazuje, jak povolit přístup SSH z Internetu:

```powershell
Get-AzNetworkSecurityGroup -Name hdisecure -ResourceGroupName RESOURCEGROUP |
Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
```

### <a name="azure-cli"></a>Azure CLI

Pomocí následujících kroků vytvořte virtuální síť, která omezuje příchozí provoz, ale umožňuje přenosy z IP adres vyžadované hdinsight.

1. Pomocí následujícího příkazu vytvořte novou `hdisecure`skupinu zabezpečení sítě s názvem . Nahraďte `RESOURCEGROUP` skupinou prostředků, která obsahuje virtuální síť Azure. Nahraďte `LOCATION` umístění (oblast), ve které byla skupina vytvořena.

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    Po vytvoření skupiny obdržíte informace o nové skupině.

2. Pomocí následujícího příkazu přidáte do nové skupiny zabezpečení sítě pravidla, která umožňují příchozí komunikaci na portu 443 ze služby Azure HDInsight pro stav a správu. Nahraďte `RESOURCEGROUP` název skupiny prostředků, která obsahuje virtuální síť Azure.

    > [!IMPORTANT]  
    > Změňte IP adresy `hdirule1` `hdirule2` pro a v tomto příkladu tak, aby odpovídaly oblasti Azure, kterou používáte. Tyto informace naleznete v [IP adresách hdinsight pro správu](hdinsight-management-ip-addresses.md).

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

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

4. Pomocí následujícího příkazu použijte skupinu zabezpečení sítě pro podsíť. Nahraďte hodnoty `GUID` a `RESOURCEGROUP` hodnotami vrácenými z předchozího kroku. Nahraďte `VNETNAME` a `SUBNETNAME` s názvem virtuální sítě a podsítě, které chcete vytvořit.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Po dokončení tohoto příkazu můžete hdinsight nainstalovat do virtuální sítě.


Tyto kroky pouze otevřený přístup ke službě HDInsight stavu a správy v cloudu Azure. Jakýkoli jiný přístup k clusteru HDInsight mimo virtuální síť je blokován. Chcete-li povolit přístup mimo virtuální síť, je nutné přidat další pravidla skupiny zabezpečení sítě.

Následující kód ukazuje, jak povolit přístup SSH z Internetu:

```azurecli
az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n ssh --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
```

## <a name="example-dns-configuration"></a><a id="example-dns"></a>Příklad: Konfigurace DNS

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Překlad názvů mezi virtuální sítí a připojenou místní sítí

Tento příklad umožňuje následující předpoklady:

* Máte virtuální síť Azure, která je připojena k místní síti pomocí brány VPN.

* Vlastní DNS server ve virtuální síti používá Linux nebo Unix jako operační systém.

* [Vazba](https://www.isc.org/downloads/bind/) je nainstalována na vlastním serveru DNS.

Na vlastním serveru DNS ve virtuální síti:

1. Pomocí Azure PowerShellu nebo Azure CLI vyhledejte příponu DNS virtuální sítě:

    Nahraďte `RESOURCEGROUP` název skupiny prostředků, která obsahuje virtuální síť, a zadejte příkaz:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Na vlastním serveru DNS pro virtuální síť použijte jako obsah `/etc/bind/named.conf.local` souboru následující text:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Nahraďte hodnotu `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` příponou DNS virtuální sítě.

    Tato konfigurace směruje všechny požadavky DNS pro příponu DNS virtuální sítě do rekurzivního překládání Azure.

2. Na vlastním serveru DNS pro virtuální síť použijte jako obsah `/etc/bind/named.conf.options` souboru následující text:

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
    
    * Nahraďte hodnotu `10.0.0.0/16` rozsahem IP adres vaší virtuální sítě. Tato položka umožňuje požadavky na překlad názvů adresy v tomto rozsahu.

    * Přidejte do `acl goodclients { ... }` oddílu rozsah IP adres místní sítě.  položka umožňuje požadavky na překlad názvů z prostředků v místní síti.
    
    * Nahraďte `192.168.0.1` hodnotu IP adresou místního serveru DNS. Tato položka směruje všechny ostatní požadavky DNS na místní server DNS.

3. Chcete-li použít konfiguraci, restartujte bind. Například, `sudo service bind9 restart`.

4. Přidejte podmíněný server pro předávání na místní server DNS. Nakonfigurujte podmíněný server pro předávání tak, aby odesílá požadavky na příponu DNS z kroku 1 na vlastní server DNS.

    > [!NOTE]  
    > Podrobnosti o tom, jak přidat podmíněný server pro předávání, naleznete v dokumentaci k softwaru DNS.

Po dokončení těchto kroků se můžete připojit k prostředkům v síti pomocí plně kvalifikovaných názvů domén (FQDN). Nyní můžete nainstalovat HDInsight do virtuální sítě.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Překlad názvů mezi dvěma připojenými virtuálními sítěmi

Tento příklad umožňuje následující předpoklady:

* Máte dvě virtuální sítě Azure, které jsou připojené pomocí brány VPN nebo partnerského vztahu.

* Vlastní DNS server v obou sítích používá Linux nebo Unix jako operační systém.

* [Vazba](https://www.isc.org/downloads/bind/) je nainstalována na vlastních serverech DNS.

1. Pomocí Azure PowerShellu nebo Azure CLI vyhledejte příponu DNS obou virtuálních sítí:

    Nahraďte `RESOURCEGROUP` název skupiny prostředků, která obsahuje virtuální síť, a zadejte příkaz:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Jako obsah `/etc/bind/named.config.local` souboru na vlastním serveru DNS použijte následující text. Proveďte tuto změnu na vlastním serveru DNS v obou virtuálních sítích.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Nahraďte hodnotu `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` příponou DNS __jiné__ virtuální sítě. Tato položka směruje požadavky na příponu DNS vzdálené sítě k vlastnímu dns v této síti.

3. Na vlastních serverech DNS v obou virtuálních sítích `/etc/bind/named.conf.options` použijte jako obsah souboru následující text:

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
    
   `10.0.0.0/16` Nahraďte `10.1.0.0/16` hodnoty a rozsahy IP adres virtuálních sítí. Tato položka umožňuje prostředkům v každé síti provádět požadavky na servery DNS.

    Všechny požadavky, které nejsou pro přípony DNS virtuálních sítí (například microsoft.com) je zpracována azure rekurzivní překládání.

4. Chcete-li použít konfiguraci, restartujte bind. Například `sudo service bind9 restart` na obou serverech DNS.

Po dokončení těchto kroků se můžete připojit k prostředkům ve virtuální síti pomocí plně kvalifikovaných názvů domén (FQDN). Nyní můžete nainstalovat HDInsight do virtuální sítě.

## <a name="next-steps"></a>Další kroky

* Příklad konfigurace HDInsightu pro připojení k místní síti najdete v tématu [Připojení HDInsightu k místní síti](./connect-on-premises-network.md).
* Informace o konfiguraci clusterů Apache HBase ve virtuálních sítích Azure najdete v tématu [Vytváření clusterů Apache HBase na HDInsightu ve virtuální síti Azure](hbase/apache-hbase-provision-vnet.md).
* Informace o konfiguraci geografické replikace Apache HBase najdete v tématu [Nastavení replikace clusteru Apache HBase ve virtuálních sítích Azure](hbase/apache-hbase-replication.md).
* Další informace o virtuálních sítích Azure najdete v přehledu [virtuální sítě Azure](../virtual-network/virtual-networks-overview.md).

* Další informace o skupinách zabezpečení sítě naleznete v [tématu Skupiny zabezpečení sítě](../virtual-network/security-overview.md).

* Další informace o uživatelem definovaných trasách naleznete v [tématu Uživatelem definované trasy a předávání IP](../virtual-network/virtual-networks-udr-overview.md)adres .
