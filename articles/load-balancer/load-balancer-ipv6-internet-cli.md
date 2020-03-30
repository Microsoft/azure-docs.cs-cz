---
title: Vytvoření veřejného vyvyřiču zatížení pomocí IPv6 – Azure CLI
titleSuffix: Azure Load Balancer
description: S tímto studijním programem můžete začít vytvářet veřejný vyrovnávání zatížení pomocí IPv6 pomocí Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, azure balancer, dual stack, public ip, nativní ipv6, mobilní, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: bff6a7ca6eb1a6859ec25d488f564c66946a780b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045407"
---
# <a name="create-a-public-load-balancer-with-ipv6-using-azure-cli"></a>Vytvoření veřejného vyvažovače zatížení pomocí IPv6 pomocí azure cli

>[!NOTE] 
>Tento článek popisuje úvodní funkci IPv6, která umožňuje základním vykladačům zatížení poskytovat připojení IPv4 i IPv6. Komplexní připojení IPv6 je teď k dispozici s [iPv6 pro virtuální sítě Azure,](../virtual-network/ipv6-overview.md) které integrují připojení IPv6 s virtuálními sítěmi a zahrnují klíčové funkce, jako jsou pravidla skupiny zabezpečení sítě IPv6, směrování definované uživatelem IPv6, vyrovnávání zatížení IPv6 basic a standardní vyrovnávání zatížení a další.  IPv6 pro virtuální užitky Azure je doporučený standard pro aplikace IPv6 v Azure. Viz [IPv6 pro nasazení Azure VNET Powershellu](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) 

Azure Load Balancer je nástroj pro vyrovnávání zatížení úrovně 4 (TCP, UDP). Nástroje pro vyrovnávání zatížení poskytují vysokou dostupnost tím, že distribuují příchozí provoz mezi instance služby v pořádku v cloudových službách nebo virtuálních počítačích v sadě nástroje pro vyrovnávání zatížení. Výčitek na zatížení může také prezentovat tyto služby na více portech nebo více IP adres nebo obojí.

## <a name="example-deployment-scenario"></a>Příklad scénáře nasazení

Následující diagram znázorňuje řešení vyrovnávání zatížení, které je nasazeno pomocí ukázkové šablony popsané v tomto článku.

![Scénář nástroje pro vyrovnávání zatížení](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

V tomto scénáři vytvoříte následující prostředky Azure:

* Dva virtuální počítače (VM)
* Virtuální síťové rozhraní pro každý virtuální počítač s přiřazenými adresami IPv4 i IPv6
* Veřejný správce zatížení s iPv4 a veřejnou IP adresou IPv6
* Skupina dostupnosti, která obsahuje dva virtuální servery
* Dvě pravidla vyrovnávání zatížení pro mapování veřejných VIP na soukromé koncové body

## <a name="deploy-the-solution-by-using-azure-cli"></a>Nasazení řešení pomocí azure cli

Následující kroky ukazují, jak vytvořit veřejný vyrovnávání zatížení pomocí Azure CLI. Pomocí rozhraní příkazového příkazového příkazu můžete vytvořit a nakonfigurovat každý objekt jednotlivě a pak je sestavit dohromady a vytvořit tak prostředek.

Chcete-li nasadit vyvyčažitetele zatížení, vytvořte a nakonfigurujte následující objekty:

* **Konfigurace IP front-endu**: Obsahuje veřejné IP adresy pro příchozí síťový provoz.
* **Fond back-endových adres**: Obsahuje síťová rozhraní (NIC), pro virtuální počítače pro příjem síťového provozu z nástroje pro vyrovnávání zatížení.
* **Pravidla vyrovnávání zatížení**: Obsahuje pravidla, která mapují veřejný port na vyrovnávání zatížení na port ve fondu adres back-end.
* **Příchozí pravidla PŘEKLADU ADRES**: Obsahuje pravidla překladu síťových adres (NAT), která mapují veřejný port v nástrojpro vyrovnávání zatížení na port pro konkrétní virtuální počítač ve fondu adres back-end.
* **Sondy**: Obsahuje sondy stavu, které se používají ke kontrole dostupnosti instancí virtuálních strojů ve fondu adres back-end.

## <a name="set-up-azure-cli"></a>Nastavení Azure CLI

V tomto příkladu spustíte nástroje Příkazového příkazu Azure v příkazovém okně prostředí PowerShell. Chcete-li zlepšit čitelnost a opakované použití, použijte možnosti skriptování prostředí PowerShell, nikoli rutiny Prostředí Azure PowerShell.

1. [Nainstalujte a nakonfigurujte rozhraní příkazového příkazového příkazu Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) podle kroků v propojeném článku a přihlaste se ke svému účtu Azure.

2. Nastavení proměnných prostředí PowerShell pro použití s příkazy Azure CLI:

    ```powershell
    $subscriptionid = "########-####-####-####-############"  # enter subscription id
    $location = "southcentralus"
    $rgName = "pscontosorg1southctrlus09152016"
    $vnetName = "contosoIPv4Vnet"
    $vnetPrefix = "10.0.0.0/16"
    $subnet1Name = "clicontosoIPv4Subnet1"
    $subnet1Prefix = "10.0.0.0/24"
    $subnet2Name = "clicontosoIPv4Subnet2"
    $subnet2Prefix = "10.0.1.0/24"
    $dnsLabel = "contoso09152016"
    $lbName = "myIPv4IPv6Lb"
    ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Vytvoření skupiny prostředků, nástroje pro vyrovnávání zatížení, virtuální sítě a podsítí

1. Vytvořte skupinu prostředků:

    ```azurecli
    az group create --name $rgName --location $location
    ```

2. Vytvoření zátěžového a reliédovního:

    ```azurecli
    $lb = az network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Vytvořte virtuální síť:

    ```azurecli
    $vnet = az network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. V této virtuální síti vytvořte dvě podsítě:

    ```azurecli
    $subnet1 = az network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = az network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Vytvoření veřejných IP adres pro front-endový fond

1. Nastavení proměnných prostředí PowerShell:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Vytvoření veřejné IP adresy pro front-endový fond IP adres:

    ```azurecli
    $publicipV4 = az network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --version IPv4 --allocation-method Dynamic --dns-name $dnsLabel
    $publicipV6 = az network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --version IPv6 --allocation-method Dynamic --dns-name $dnsLabel
    ```

    > [!IMPORTANT]
    > Vykladač zatížení používá popisek domény veřejné IP jako plně kvalifikovaný název domény (Plně kvalifikovaný název domény). Jedná se o změnu od klasického nasazení, které používá název cloudové služby jako fQDN pro vyrovnávání zatížení.
    >
    > V tomto příkladu je *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Vytvoření fondů front-endu a back-endu

V této části vytvoříte následující fondy IP adres:
* Front-end ový fond IP, který přijímá příchozí síťový provoz v systému vyrovnávání zatížení.
* Back-end ový fond IP, kde front-endový fond odesílá síťový provoz s vyrovnáváním zatížení.

1. Nastavení proměnných prostředí PowerShell:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Vytvořte front-endový fond IP a přidružte ho k veřejné IP adrese, kterou jste vytvořili v předchozím kroku, a k vykladači zatížení.

    ```azurecli
    $frontendV4 = az network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-address $publicIpv4Name --lb-name $lbName
    $frontendV6 = az network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-address $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>Vytvoření sondy, pravidel NAT a pravidel vyrovnávání zatížení

Tento příklad vytvoří následující položky:

* Pravidlo sondy pro kontrolu připojení k portu TCP 80.
* Pravidlo NAT přeložit všechny příchozí přenosy na portu 3389 na port 3389 pro RDP.\*
* Pravidlo NAT přeložit všechny příchozí přenosy na portu 3391 na port 3389 pro protokol vzdálené plochy (RDP).\*
* Pravidlo pro vyrovnávání zatížení pro vyrovnání všech příchozích přenosů na portu 80 na port80 na adresách v back-endovém fondu.

\*Pravidla NAT jsou přidružena k konkrétní instanci virtuálního počítače za nástrojem pro vyrovnávání zatížení. Síťový provoz, který přichází na port 3389, je odeslán do konkrétního virtuálního počítače a portu, který je přidružen k pravidlu NAT. Pro pravidlo překladu adres (NAT) je nutné zadat protokol (UDP nebo TCP). Oba protokoly nelze přiřadit ke stejnému portu.

1. Nastavení proměnných prostředí PowerShell:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Vytvořte sondu.

    Následující příklad vytvoří sondu TCP, která kontroluje připojení k portu TCP back-end u 80 každých 15 sekund. Po dvou po sobě jdoucích selhání, označí prostředek back-end jako nedostupný.

    ```azurecli
    $probeV4V6 = az network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --threshold 2 --lb-name $lbName
    ```

3. Vytvořte příchozí pravidla NAT, která umožňují připojení RDP k back-endovým prostředkům:

    ```azurecli
    $inboundNatRuleRdp1 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Vytvořte pravidla pro vyrovnávání zatížení, která odesílají provoz na různé back-endové porty, v závislosti na front-endu, který obdržel požadavek.

    ```azurecli
    $lbruleIPv4 = az network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = az network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Zkontrolujte nastavení:

    ```azurecli
    az network lb show --resource-group $rgName --name $lbName
    ```

    Očekávaný výstup:

        info:    Executing command network lb show
        info:    Looking up the load balancer "myIPv4IPv6Lb"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
        data:    Name                            : myIPv4IPv6Lb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : southcentralus
        data:    Provisioning state              : Succeeded
        data:
        data:    Frontend IP configurations:
        data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
        data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
        data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
        data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
        data:
        data:    Probes:
        data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
        data:    -------------------  ------------------  --------  ----  ----  --------  -----
        data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
        data:
        data:    Backend Address Pools:
        data:    Name             Provisioning state
        data:    ---------------  ------------------
        data:    BackendPoolIPv4  Succeeded
        data:    BackendPoolIPv6  Succeeded
        data:
        data:    Load Balancing Rules:
        data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
        data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
        data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
        data:
        data:    Inbound NAT Rules:
        data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
        data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
        data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
        info:    network lb show

## <a name="create-nics"></a>Vytvoření síťových rozhraní

Vytvořte síťové karty a přidružte je k pravidlům NAT, pravidlům vyrovnávání zatížení a sondám.

1. Nastavení proměnných prostředí PowerShell:

    ```powershell
    $nic1Name = "myIPv4IPv6Nic1"
    $nic2Name = "myIPv4IPv6Nic2"
    $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
    $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
    $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
    $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
    $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
    $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"
    ```

2. Vytvořte nic pro každý back-end a přidejte konfiguraci IPv6:

    ```azurecli
    $nic1 = az network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet1Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule1V4Id
    $nic1IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = az network nic create --name $nic2Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet2Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule2V4Id
    $nic2IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Vytvoření prostředků back-endového virtuálního soudu a připojení jednotlivých nic

Chcete-li vytvořit virtuální chod, musíte mít účet úložiště. Pro vyrovnávání zatížení musí být virtuální virtuální společnosti členy skupiny dostupnosti. Další informace o vytváření virtuálních počítačů najdete [v tématu Vytvoření virtuálního počítače Azure pomocí PowerShellu](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. Nastavení proměnných prostředí PowerShell:

    ```powershell
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $imageurn = "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > Tento příklad používá uživatelské jméno a heslo pro virtuální počítače ve prostém textu. Při použití těchto přihlašovacích údajů ve mprostém textu postupujte odpovídajícím způsobem. Bezpečnější způsob zpracování přihlašovacích údajů v prostředí [`Get-Credential`](https://technet.microsoft.com/library/hh849815.aspx) PowerShell najdete v tématu rutina.

2. Vytvořte sadu dostupnosti:

    ```azurecli
    $availabilitySet = az vm availability-set create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. Vytvořte virtuální počítače s přidruženými síťové karty:

    ```azurecli
    az vm create --resource-group $rgname --name $vm1Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic1Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 

    az vm create --resource-group $rgname --name $vm2Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic2Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 
    ```


