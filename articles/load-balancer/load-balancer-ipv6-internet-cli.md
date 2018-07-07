---
title: Vytvořte veřejný load balancer s IPv6 – rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Informace o vytvoření veřejného load balanceru s protokolem IPv6 pomocí rozhraní příkazového řádku Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
tags: azure-resource-manager
keywords: protokol IPv6, nástroje pro vyrovnávání zatížení azure, duálním zásobníkem, veřejné IP adresy, nativní protokol ipv6, mobilní zařízení, iot
ms.assetid: a1957c9c-9c1d-423e-9d5c-d71449bc1f37
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: kumud
ms.openlocfilehash: 3172736edf4e38f53858620ebac95b711857010b
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901261"
---
# <a name="create-a-public-load-balancer-with-ipv6-using-azure-cli"></a>Vytvoření veřejného load balanceru s protokolem IPv6 pomocí rozhraní příkazového řádku Azure


Azure Load Balancer je nástroj pro vyrovnávání zatížení úrovně 4 (TCP, UDP). Nástroje pro vyrovnávání zatížení poskytuje vysokou dostupnost díky distribuci příchozích přenosů mezi funkční instance cloud services nebo virtual machines v sadě nástroje pro vyrovnávání zatížení. Nástroje pro vyrovnávání zatížení ale může také představovat tyto služby ve více portech, více IP adres nebo obojí.

## <a name="example-deployment-scenario"></a>Příklad scénáře nasazení

Následující diagram znázorňuje řešení, která se nasazuje s použitím Ukázková šablona popsaných v tomto článku Vyrovnávání zatížení.

![Scénář nástroje pro vyrovnávání zatížení](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

V tomto scénáři vytvoříte následující prostředky Azure:

* Dva virtuální počítače (VM)
* Rozhraní virtuální sítě pro každý virtuální počítač s protokoly IPv4 a IPv6 adresy přiřazené
* Nástroj pro vyrovnávání zatížení veřejnou adresu IPv4 a IPv6 veřejnou IP adresu
* Skupinu dostupnosti, která obsahuje dva virtuální počítače
* Dvě pravidla vyrovnávání přiřadit privátní koncových bodů veřejných virtuálních IP adres zatížení

## <a name="deploy-the-solution-by-using-azure-cli"></a>Nasazení řešení pomocí Azure CLI

Následující kroky ukazují postup vytvoření veřejného load balanceru úrovně pomocí Azure CLI. Pomocí rozhraní příkazového řádku, vytvoříte a nakonfigurujete každý objekt jednotlivě a potom se spojí dohromady a vytvoří prostředek je.

Pokud chcete nasadit nástroj pro vyrovnávání zatížení, vytvořte a nakonfigurujte následující objekty:

* **Front-endové konfigurace protokolu IP**: obsahuje veřejné IP adresy pro příchozí síťový provoz.
* **Back endový fond adres**: obsahuje síťová rozhraní (NIC) pro virtuální počítače přijímají síťový provoz z nástroje pro vyrovnávání zatížení.
* **Pravidla Vyrovnávání zatížení**: obsahuje pravidla, která mapují veřejný port v nástroji pro vyrovnávání zatížení na port ve fondu back endových adres.
* **Příchozí pravidla NAT**: obsahuje pravidel překladu adres (NAT), která mapují veřejný port v nástroji pro vyrovnávání zatížení na port konkrétního virtuálního počítače ve fondu back endových adres.
* **Sondy**: obsahuje testy stavu, které se používají ke kontrole dostupnosti instancí virtuálních počítačů ve fondu back endových adres.

## <a name="set-up-azure-cli"></a>Nastavení rozhraní příkazového řádku Azure

V tomto příkladu spustíte nástroje Azure CLI v příkazovém okně prostředí PowerShell. Pokud chcete zlepšit čitelnost a opakované použití, použijete možností skriptování Powershellu, ne rutiny Azure Powershellu.

1. [Instalace a konfigurace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pomocí následujících kroků v odkazovaném článku a přihlaste se ke svému účtu Azure.

2. Nastavte proměnné prostředí PowerShell pro použití s příkazy rozhraní příkazového řádku Azure:

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

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Vytvořit skupinu prostředků, nástroj pro vyrovnávání zatížení, virtuální sítě a podsítě

1. Vytvořte skupinu prostředků:

    ```azurecli
    az group create --name $rgName --location $location
    ```

2. Vytvoření load balanceru úrovně:

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

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Vytvoření veřejné IP adresy pro front-endový fond

1. Nastavte proměnné prostředí PowerShell:

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
    > Nástroje pro vyrovnávání zatížení používá název domény veřejné IP adresy jako svůj plně kvalifikovaný název domény (FQDN). To změnit z modelu nasazení classic, který používá cloudovou službu pojmenujte jako plně kvalifikovaný název domény pro vyrovnávání zatížení.
    >
    > V tomto příkladu plně kvalifikovaný název domény je *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Vytvořte front-endovými a back endové fondy

V této části vytvoříte tyto fondy IP adres:
* Front-endového fondu IP, která bude přijímat příchozí síťový provoz do nástroje pro vyrovnávání zatížení.
* Back endového fondu IP kam front-endový fond odesílá síťový provoz s vyrovnáváním zatížení.

1. Nastavte proměnné prostředí PowerShell:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Vytvořte front-endový fond IP adres a přidružte jej k veřejné IP adresy, kterou jste vytvořili v předchozím kroku a nástroje pro vyrovnávání zatížení.

    ```azurecli
    $frontendV4 = az network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-address $publicIpv4Name --lb-name $lbName
    $frontendV6 = az network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-address $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>Vytvoření testu, pravidla překladu adres a pravidla služby load balancer

Tento příklad vytvoří následující položky:

* Pravidlo testu, zkontrolujte připojení k portu TCP 80.
* Pravidlo NAT pro převod veškerý příchozí provoz na portu 3389 na port 3389 pro protokol RDP.\*
* Pravidlo NAT pro převod veškerý příchozí provoz na portu 3391 na port 3389 pro protokol remote desktop protocol (RDP).\*
* Pravidlo nástroje pro vyrovnávání zatížení, které veškerý příchozí provoz na portu 80 na port 80 na adresách v back endového fondu.

\* Pravidla NAT jsou spojeny s konkrétní instanci virtuálního počítače za nástrojem pro vyrovnávání zatížení. Síťový provoz přicházející na portu 3389 je odeslána do konkrétního virtuálního počítače a port, který má přidružená k pravidlu překladu adres. Pro pravidlo překladu adres (NAT) je nutné zadat protokol (UDP nebo TCP). Na stejný port nelze přiřadit oba protokoly.

1. Nastavte proměnné prostředí PowerShell:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Vytvořte test paměti.

    Následující příklad vytvoří sondu protokolu TCP, který kontroluje pro připojení k back-end port TCP 80 každých 15 sekund. Po dvou po sobě následujících selhání označí prostředek back-end jako nedostupné.

    ```azurecli
    $probeV4V6 = az network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --threshold 2 --lb-name $lbName
    ```

3. Vytvoření příchozích pravidel NAT, které umožňují připojení RDP k back endové prostředky:

    ```azurecli
    $inboundNatRuleRdp1 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Vytvoření pravidla nástroje pro vyrovnávání zatížení, odesílat provoz na různé back endové porty, v závislosti na front-endu, který přijal požadavek.

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

Vytvoření síťových rozhraní a přiřaďte je k pravidla NAT, pravidla nástroje pro vyrovnávání zatížení a testům.

1. Nastavte proměnné prostředí PowerShell:

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

2. Vytvořte síťové rozhraní pro každý back-end a přidání konfigurace protokolu IPv6:

    ```azurecli
    $nic1 = az network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet1Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule1V4Id
    $nic1IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = az network nic create --name $nic2Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet1Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule2V4Id
    $nic2IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Vytvoření back endové prostředky virtuálního počítače a připojení každé síťové rozhraní

Pokud chcete vytvořit virtuální počítače, musíte mít účet úložiště. Pro vyrovnávání zatížení virtuálních počítačů musí být členy skupiny dostupnosti. Další informace o vytváření virtuálních počítačů najdete v tématu [vytvoření virtuálního počítače Azure pomocí prostředí PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. Nastavte proměnné prostředí PowerShell:

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
    > Tento příklad používá uživatelské jméno a heslo pro virtuální počítače v nešifrovaném textu. Využijte opatrní při použití tyto přihlašovací údaje v nešifrovaném textu. Bezpečnější způsob zpracování pověření v prostředí PowerShell, najdete v článku [ `Get-Credential` ](https://technet.microsoft.com/library/hh849815.aspx) rutiny.

2. Vytvořte skupinu dostupnosti:

    ```azurecli
    $availabilitySet = az vm availability-set create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. Vytvoření virtuálních počítačů s přidružených síťových karet:

    ```azurecli
    az vm create --resource-group $rgname --name $vm1Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic1Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 

    az vm create --resource-group $rgname --name $vm2Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic2Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 
    ```

## <a name="next-steps"></a>Další postup

[Začínáme s konfigurací interního nástroje pro vyrovnávání zatížení](load-balancer-get-started-ilb-arm-cli.md)  
[Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md)  
[Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)
