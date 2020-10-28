---
title: Vytvoření veřejného nástroje pro vyrovnávání zatížení s využitím IPv6 – Azure CLI
titleSuffix: Azure Load Balancer
description: Pomocí této cesty výukového programu Začněte vytvářet veřejný Nástroj pro vyrovnávání zatížení s protokolem IPv6 pomocí Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure Load Balancer, duální zásobník, veřejná IP adresa, nativní IPv6, mobilní zařízení, IoT
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: 97fdf55032e92585d723b54e21079098cdc19636
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735907"
---
# <a name="create-a-public-load-balancer-with-ipv6-using-azure-cli"></a>Vytvoření veřejného nástroje pro vyrovnávání zatížení s protokolem IPv6 pomocí Azure CLI

>[!NOTE] 
>Tento článek popisuje úvodní funkci IPv6, která umožňuje základním nástrojům pro vyrovnávání zatížení poskytovat připojení protokolem IPv4 i IPv6. K dispozici je teď komplexní připojení pomocí protokolu [IPv6 pro Azure virtuální sítě](../virtual-network/ipv6-overview.md) , které integruje připojení IPv6 k vašim virtuálním sítím a obsahuje klíčové funkce, jako jsou pravidla skupiny zabezpečení sítě IPv6, uživatelem definované směrování IPv6, protokol IPv6 Basic a standardní vyrovnávání zatížení a další.  Protokol IPv6 pro Azure virtuální sítě je doporučený standard pro aplikace IPv6 v Azure. Viz [protokol IPv6 pro nasazení PowerShellu pro virtuální síť Azure](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) 

Azure Load Balancer je nástroj pro vyrovnávání zatížení úrovně 4 (TCP, UDP). Nástroje pro vyrovnávání zatížení poskytují vysokou dostupnost distribucí příchozího provozu mezi funkční instance služby v cloudových službách nebo virtuálních počítačích v sadě nástrojů pro vyrovnávání zatížení. Nástroje pro vyrovnávání zatížení můžou tyto služby také prezentovat na několika portech nebo několika IP adresách nebo obojím.

## <a name="example-deployment-scenario"></a>Ukázkový scénář nasazení

Následující diagram znázorňuje řešení vyrovnávání zatížení, které je nasazené pomocí ukázkové šablony popsané v tomto článku.

![Scénář nástroje pro vyrovnávání zatížení](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

V tomto scénáři vytvoříte následující prostředky Azure:

* Dva virtuální počítače
* Virtuální síťové rozhraní pro každý virtuální počítač s přiřazenými adresami IPv4 i IPv6
* Veřejný Nástroj pro vyrovnávání zatížení s IPv4 a veřejnou IP adresou IPv6
* Skupina dostupnosti, která obsahuje dva virtuální počítače
* Dvě pravidla vyrovnávání zatížení pro mapování veřejných virtuálních IP adres na privátní koncové body

## <a name="deploy-the-solution-by-using-azure-cli"></a>Nasazení řešení pomocí rozhraní příkazového řádku Azure

Následující kroky ukazují, jak vytvořit veřejný Nástroj pro vyrovnávání zatížení pomocí rozhraní příkazového řádku Azure CLI. Pomocí rozhraní příkazového řádku můžete vytvořit a nakonfigurovat jednotlivé objekty jednotlivě a pak je umístit dohromady a vytvořit prostředek.

Pokud chcete nasadit nástroj pro vyrovnávání zatížení, vytvořte a nakonfigurujte následující objekty:

* **Konfigurace front-ENDOVÉ IP** adresy: obsahuje veřejné IP adresy pro příchozí síťový provoz.
* **Fond back-endové adresy** : obsahuje síťová rozhraní (nic) pro virtuální počítače pro příjem síťového provozu z nástroje pro vyrovnávání zatížení.
* **Pravidla vyrovnávání zatížení** : obsahuje pravidla, která mapují veřejný port v nástroji pro vyrovnávání zatížení na port ve fondu back-end adres.
* **Pravidla příchozího překladu adres** (NAT): obsahuje pravidla překladu síťových adres (NAT), která mapují veřejný port v nástroji pro vyrovnávání zatížení na port pro konkrétní virtuální počítač v rámci fondu back-end adres.
* **Sondy** : obsahuje sondy stavu, které slouží ke kontrole dostupnosti instancí virtuálních počítačů ve fondu back-end adres.

## <a name="set-up-azure-cli"></a>Nastavení Azure CLI

V tomto příkladu spustíte nástroje rozhraní příkazového řádku Azure CLI v příkazovém okně PowerShellu. Pro zlepšení čitelnosti a opakovaného použití využívají skriptovací možnosti prostředí PowerShell, nikoli rutiny Azure PowerShell.

1. [Nainstalujte a nakonfigurujte Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) podle kroků v propojeném článku a přihlaste se ke svému účtu Azure.

2. Nastavení proměnných PowerShellu pro použití s příkazy Azure CLI:

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

2. Vytvořte Nástroj pro vyrovnávání zatížení:

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

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Vytvořit veřejné IP adresy pro front-end fond

1. Nastavte proměnné PowerShellu:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Vytvořte veřejnou IP adresu pro front-end fond IP adres:

    ```azurecli
    $publicipV4 = az network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --version IPv4 --allocation-method Dynamic --dns-name $dnsLabel
    $publicipV6 = az network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --version IPv6 --allocation-method Dynamic --dns-name $dnsLabel
    ```

    > [!IMPORTANT]
    > Nástroj pro vyrovnávání zatížení používá označení domény veřejné IP adresy jako plně kvalifikovaného názvu domény (FQDN). Tím se změní nastavení klasického nasazení, které jako plně kvalifikovaný název domény nástroje pro vyrovnávání zatížení používá název cloudové služby.
    >
    > V tomto příkladu je plně kvalifikovaný název domény *contoso09152016.southcentralus.cloudapp.Azure.com* .

## <a name="create-front-end-and-back-end-pools"></a>Vytvoření front-endové a back-endové fondy

V této části vytvoříte následující fondy IP adres:
* Front-end fond IP adres, který přijímá příchozí síťový provoz v nástroji pro vyrovnávání zatížení.
* Fond back-end IP adres, kde front-end fond odesílá síťový provoz s vyrovnáváním zatížení.

1. Nastavte proměnné PowerShellu:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Vytvořte front-end fond IP adres a přidružte ho k veřejné IP adrese, kterou jste vytvořili v předchozím kroku a nástroji pro vyrovnávání zatížení.

    ```azurecli
    $frontendV4 = az network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-address $publicIpv4Name --lb-name $lbName
    $frontendV6 = az network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-address $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>Vytvoření sondy, pravidel překladu adres (NAT) a pravidel nástroje pro vyrovnávání zatížení

Tento příklad vytvoří následující položky:

* Pravidlo testu pro kontrolu připojení k portu TCP 80.
* Pravidlo překladu adres (NAT) pro překlad veškerého příchozího provozu na portu 3389 na port 3389 pro protokol RDP.\*
* Pravidlo překladu adres (NAT) pro překlad veškerého příchozího provozu na portu 3391 na port 3389 pro protokol RDP (Remote Desktop Protocol).\*
* Pravidlo nástroje pro vyrovnávání zatížení, které vyrovnává veškerý příchozí provoz na portu 80 na port 80 u adres ve fondu back-end.

\* Pravidla překladu adres (NAT) jsou přidružená ke konkrétní instanci virtuálního počítače za nástrojem pro vyrovnávání zatížení. Síťový provoz, který se dorazí na port 3389, se pošle na konkrétní virtuální počítač a port, který je přidružený k pravidlu NAT. Pro pravidlo překladu adres (NAT) je nutné zadat protokol (UDP nebo TCP). Ke stejnému portu nemůžete přiřadit oba protokoly.

1. Nastavte proměnné PowerShellu:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Vytvořte sondu.

    Následující příklad vytvoří test TCP, který po 15 sekundách kontroluje připojení k back-endovému portu TCP 80. Po dvou po sobě jdoucích selhání označí prostředek back-end jako nedostupný.

    ```azurecli
    $probeV4V6 = az network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --threshold 2 --lb-name $lbName
    ```

3. Vytvořte pravidla příchozího překladu adres (NAT), která povolí připojení RDP k prostředkům back-endu:

    ```azurecli
    $inboundNatRuleRdp1 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Vytvořte pravidla nástroje pro vyrovnávání zatížení, která odesílají provoz na různé porty back-endu v závislosti na front-endu, který požadavek přijal.

    ```azurecli
    $lbruleIPv4 = az network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = az network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Ověřte nastavení:

    ```azurecli
    az network lb show --resource-group $rgName --name $lbName
    ```

    Očekávaný výstup:

    ```output
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
    ```

## <a name="create-nics"></a>Vytvoření síťových rozhraní

Vytvořte nic a přidružte je k pravidlům NAT, pravidlům nástroje pro vyrovnávání zatížení a testům.

1. Nastavte proměnné PowerShellu:

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

2. Vytvořte síťovou kartu pro každý back-end a přidejte konfiguraci IPv6:

    ```azurecli
    $nic1 = az network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet1Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule1V4Id
    $nic1IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = az network nic create --name $nic2Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet2Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule2V4Id
    $nic2IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Vytvořte prostředky záložního virtuálního počítače a připojte jednotlivé síťové karty.

Pokud chcete vytvořit virtuální počítače, musíte mít účet úložiště. Pro vyrovnávání zatížení musí být virtuální počítače členy skupiny dostupnosti. Další informace o vytváření virtuálních počítačů najdete v tématu [Vytvoření virtuálního počítače Azure pomocí PowerShellu](../virtual-machines/windows/quick-create-powershell.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. Nastavte proměnné PowerShellu:

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
    > V tomto příkladu se pro virtuální počítače používá uživatelské jméno a heslo v nešifrovaném textu. Pokud tyto přihlašovací údaje použijete v nešifrovaném textu, postupujte opatrně. Bezpečnější metoda zpracování přihlašovacích údajů v PowerShellu najdete v tématu [`Get-Credential`](https://technet.microsoft.com/library/hh849815.aspx) rutina.

2. Vytvořte skupinu dostupnosti:

    ```azurecli
    $availabilitySet = az vm availability-set create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. Vytvořte virtuální počítače s přidruženými síťovými kartami:

    ```azurecli
    az vm create --resource-group $rgname --name $vm1Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic1Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 

    az vm create --resource-group $rgname --name $vm2Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic2Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 
    ```


