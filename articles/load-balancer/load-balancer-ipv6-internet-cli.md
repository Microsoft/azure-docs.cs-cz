---
title: Vytvořit nástroj pro vyrovnávání zatížení veřejnou s IPv6 – rozhraní příkazového řádku Azure | Microsoft Docs
description: Naučte se vytvářet Vyrovnávání zatížení veřejnou s IPv6 pomocí rozhraní příkazového řádku Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
tags: azure-resource-manager
keywords: protokol IPv6, nástroje pro vyrovnávání zatížení azure, duálním zásobníkem, veřejnou IP adresu, nativní protokol ipv6, mobilní, iot
ms.assetid: a1957c9c-9c1d-423e-9d5c-d71449bc1f37
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: kumud
ms.openlocfilehash: 10698c79b11a47a465604f90bf63e180615a5ed7
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112730"
---
# <a name="create-a-public-load-balancer-with-ipv6-using-azure-cli"></a>Vytvoření veřejné služba Vyrovnávání zatížení s IPv6 pomocí rozhraní příkazového řádku Azure


Azure Load Balancer je nástroj pro vyrovnávání zatížení úrovně 4 (TCP, UDP). Nástroje pro vyrovnávání zatížení zajištění vysoké dostupnosti distribucí příchozí komunikaci mezi instance pořádku služby ve cloudových službách nebo virtuálních počítačů v sadě nástroje pro vyrovnávání zatížení. Nástroje pro vyrovnávání zatížení můžete také k dispozici tyto služby na víc portů nebo více IP adres nebo obojí.

## <a name="example-deployment-scenario"></a>Příklad scénáře nasazení

Následující diagram znázorňuje řešení, které je nasazeno pomocí popsaných v tomto článku příklad šablony Vyrovnávání zatížení.

![Scénář nástroje pro vyrovnávání zatížení](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

V tomto scénáři vytvoříte následující prostředky Azure:

* Dva virtuální počítače (VM)
* Rozhraní virtuální sítě pro každý virtuální počítač s oba protokoly IPv4 a IPv6 adresy přiřazené
* Vyrovnávání zatížení veřejnou IPv4 a IPv6 veřejnou IP adresu
* Skupinu dostupnosti, která obsahuje dva virtuální počítače
* Dvě pravidla vyrovnávání mapovat veřejné virtuální privátní koncové body zatížení

## <a name="deploy-the-solution-by-using-azure-cli"></a>Nasazení řešení pomocí rozhraní příkazového řádku Azure

Následující kroky ukazují, jak vytvořit nástroj pro vyrovnávání zatížení veřejnou pomocí rozhraní příkazového řádku Azure. Pomocí rozhraní příkazového řádku, můžete vytvořit a nakonfigurovat každý objekt jednotlivě a pak je vložte dohromady a vytvoří prostředek.

Pokud chcete nasadit nástroj pro vyrovnávání zatížení, vytvořit a nakonfigurovat následující objekty:

* **Konfiguraci front-end IP adresy**: obsahuje veřejné IP adresy pro příchozí síťový provoz.
* **Fond back-end adres**: obsahuje síťová rozhraní (NIC) pro virtuální počítače přijímat síťový provoz z nástroje pro vyrovnávání zatížení.
* **Pravidla Vyrovnávání zatížení**: obsahuje pravidla, které mapují veřejný port ve vyrovnávání zátěže na port ve fondu adres back-end.
* **Příchozí pravidla NAT**: obsahuje síťové adresy překlad (NAT) pravidla, která mapování veřejný port ve vyrovnávání zátěže na port pro konkrétní virtuální počítač ve fondu adres back-end.
* **Sondy**: obsahuje sondy stavu, které slouží ke kontrole dostupnosti instancí virtuálního počítače ve fondu adres back-end.

## <a name="set-up-azure-cli"></a>Nastavení rozhraní příkazového řádku Azure

V tomto příkladu spusťte v příkazovém okně prostředí PowerShell nástrojů příkazového řádku Azure. Ke zlepšení čitelnosti a opakované použití, můžete použít možnosti skriptování Powershellu, není rutin prostředí Azure PowerShell.

1. [Instalace a konfigurace rozhraní příkazového řádku Azure]((https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)) podle následujících kroků v článku propojené a přihlaste se k účtu Azure.

2. Nastavení proměnných prostředí PowerShell pro použití s příkazy rozhraní příkazového řádku Azure:

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

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Vytvoření skupiny prostředků, nástroj pro vyrovnávání zatížení, virtuální sítě a podsítě

1. Vytvořte skupinu prostředků:

    ```azurecli
    az group create $rgName $location
    ```

2. Vytvořte nástroj pro vyrovnávání zatížení:

    ```azurecli
    $lb = az network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Vytvořte virtuální síť:

    ```azurecli
    $vnet = az network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. V této virtuální sítě vytvořte dvě podsítě:

    ```azurecli
    $subnet1 = az network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = az network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Vytvoření veřejné IP adresy pro front-end fond

1. Nastavení proměnných prostředí PowerShell:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Vytvoření veřejné IP adresy pro front-end fond IP adres:

    ```azurecli
    $publicipV4 = az network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --ip-version IPv4 --allocation-method Dynamic --domain-name-label $dnsLabel
    $publicipV6 = az network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --ip-version IPv6 --allocation-method Dynamic --domain-name-label $dnsLabel
    ```

    > [!IMPORTANT]
    > Nástroje pro vyrovnávání zatížení používá popisek domény veřejné IP adresy jako jeho plně kvalifikovaný název domény (FQDN). Tato změna z klasického nasazení, který používá cloudové služby název jako nástroj pro vyrovnávání zatížení plně kvalifikovaný název domény.
    >
    > V tomto příkladu je plně kvalifikovaný název domény *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Vytvořte front-end a back endové fondy

V této části můžete vytvořit následující fondy IP adres:
* Front-end IP fond, který přijme příchozí síťový provoz na nástroje pro vyrovnávání zatížení.
* Fond back-end IP, kde front-end fondu odešle síťový provoz s vyrovnáváním zatížení.

1. Nastavení proměnných prostředí PowerShell:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Vytvoření fondu IP front-endu a přidružte ji k veřejné IP adresy, kterou jste vytvořili v předchozím kroku a nástroje pro vyrovnávání zatížení.

    ```azurecli
    $frontendV4 = az network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-name $publicIpv4Name --lb-name $lbName
    $frontendV6 = az network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-name $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>Vytvoření testu, pravidla NAT a pravidla pro vyrovnávání zatížení

Tento příklad vytvoří následující položky:

* Pravidlo testu, zkontrolujte připojení k portům TCP 80 k.
* Pravidlo NAT přeložit všechny příchozí přenosy na portu 3389 k portu 3389 pro protokol RDP.\*
* Pravidlo NAT přeložit všechny příchozí přenosy na portu 3391 k portu 3389 pro protokolu vzdálené plochy (RDP).\*
* Pravidlo Vyrovnávání zatížení vyvážit všechny příchozí přenosy na portu 80 na portu 80 pro adresy ve fondu back-end.

\* Pravidla NAT jsou spojeny s konkrétní instanci virtuálního počítače za nástrojem pro vyrovnávání zatížení. Síťový provoz, který dorazí na portu 3389 posílá konkrétní virtuální počítač a port, který je spojen s je pravidlo překladu adres. Pro pravidlo překladu adres (NAT) je nutné zadat protokol (UDP nebo TCP). Oba protokoly nelze přiřadit stejný port.

1. Nastavení proměnných prostředí PowerShell:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Vytvořte test.

    Následující příklad vytvoří sondou TCP, který kontroluje pro připojení k back-end port TCP 80 každých 15 sekund. Po dvou po sobě jdoucích selhání označuje back-end prostředků jako nedostupné.

    ```azurecli
    $probeV4V6 = az network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --count 2 --lb-name $lbName
    ```

3. Vytvoření příchozích pravidel NAT, které umožňují připojení RDP k back endové prostředky:

    ```azurecli
    $inboundNatRuleRdp1 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Vytvořte pravidla nástroje pro vyrovnávání zatížení, které odesílá data na jiné porty back-end, v závislosti na front-end, který přijala požadavek.

    ```azurecli
    $lbruleIPv4 = az network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-address-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = az network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-address-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
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

Vytvoření síťové adaptéry a přiřadit je k pravidla NAT, pravidla nástroje pro vyrovnávání zatížení a sondy.

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

2. Vytvořit síťovou kartu pro každý back-end a konfigurace protokolu IPv6:

    ```azurecli
    $nic1 = az network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-version "IPv4" --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
    $nic1IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = az network nic create --name $nic2Name --resource-group $rgname --location $location --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule2V4Id
    $nic2IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Vytvořte prostředky virtuálních počítačů back-end a připojte každý síťový adaptér

Pokud chcete vytvořit virtuální počítače, musí mít účet úložiště. Pro vyrovnávání zatížení, musí být členy skupiny dostupnosti virtuálních počítačů. Další informace o vytváření virtuálních počítačů najdete v tématu [vytvoření virtuálního počítače Azure pomocí prostředí PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. Nastavení proměnných prostředí PowerShell:

    ```powershell
    $storageAccountName = "ps08092016v6sa0"
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $disk1Name = "WindowsVMosDisk1"
    $disk2Name = "WindowsVMosDisk2"
    $osDisk1Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk1Name.vhd"
    $osDisk2Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk2Name.vhd"
    $imageurn "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > Tento příklad používá uživatelské jméno a heslo pro virtuální počítače ve formě prostého textu. Při dostatečně opatrní použít tyto přihlašovací údaje ve formě prostého textu. Bezpečnější způsob zpracování pověření v prostředí PowerShell, najdete v článku [ `Get-Credential` ](https://technet.microsoft.com/library/hh849815.aspx) rutiny.

2. Vytvořte sadu dostupnosti a účet úložiště.

    Při vytváření virtuálních počítačů, můžete použít existující účet úložiště. Pomocí následujícího příkazu vytvoříte nový účet úložiště:

    ```azurecli
    $storageAcc = az storage account create $storageAccountName --resource-group $rgName --location $location --sku-name "LRS" --kind "Storage"
    ```

3. Vytvořte skupinu dostupnosti:

    ```azurecli
    $availabilitySet = az vm availability-set create --name $availabilitySetName --resource-group $rgName --location $location
    ```

4. Vytvoření virtuálních počítačů s přidružených síťových karet:

    ```azurecli
    $vm1 = az vm create --resource-group $rgname --location $location --availability-set $availabilitySet --name $vm1Name --nic-id $nic1Id --os-disk-vhd $osDisk1Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension

    $vm2 = azure vm create --resource-group $rgname --location $location --availability-set $availabilitySet --name $vm2Name --nic-id $nic2Id --os-disk-vhd $osDisk2Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension
    ```

## <a name="next-steps"></a>Další postup

[Začínáme s konfigurací interního nástroje pro vyrovnávání zatížení](load-balancer-get-started-ilb-arm-cli.md)  
[Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md)  
[Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)
