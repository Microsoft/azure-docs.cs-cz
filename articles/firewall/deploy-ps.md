---
title: Nasazení a konfiguraci brány Firewall Azure pomocí Azure Powershellu
description: V tomto článku se dozvíte, jak nasadit a nakonfigurovat Firewall služby Azure pomocí Azure Powershellu.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 4/10/2019
ms.author: victorh
ms.openlocfilehash: 7c30e0aa0ae9735f5d08e1a2c4d6e6d36d778e27
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410231"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-powershell"></a>Nasazení a konfiguraci brány Firewall Azure pomocí Azure Powershellu

Řízení odchozího síťového přístupu je důležitou součástí celkového plánu zabezpečení sítě. Můžete například omezit přístup k webovým stránkám. Nebo můžete chtít omezit odchozí IP adresy a porty, které mohou být přístupné.

Jedním ze způsobů, jak můžete řídit odchozí síťový přístup z podsítě Azure, je použít Azure Firewall. Azure Firewall umožňuje nakonfigurovat:

* Pravidla aplikace, která definují plně kvalifikované názvy domén, ke kterým je možné získat přístup z podsítě.
* Pravidla sítě, která definují zdrojovou adresu, protokol, cílový port a cílovou adresu.

Síťový provoz podléhá nakonfigurovaným pravidlům brány firewall, když ho směrujete na bránu firewall jako na výchozí bránu podsítě.

Pro účely tohoto článku vytvořte zjednodušené jedné virtuální sítě se třemi podsítěmi pro snadné nasazení. Pro nasazení v produkčním prostředí [model střed a paprsek](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) se doporučuje, pokud brána firewall je ve své vlastní virtuální sítě. Servery úlohy jsou v partnerských virtuálních sítích ve stejné oblasti pomocí jedné nebo několika podsítí.

* **AzureFirewallSubnet** – v této podsíti bude brána firewall.
* **Workload-SN** – v této podsíti bude server úloh. Provoz této podsítě bude procházet bránou firewall.
* **Jump-SN** – v této podsíti bude „jump“ server. Jump server má veřejnou IP adresu, ke které se můžete připojit pomocí vzdálené plochy. Odtud se pak můžete připojit k serveru úloh (pomocí další vzdálené plochy).

![Kurz síťové infrastruktury](media/tutorial-firewall-rules-portal/Tutorial_network.png)

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nastavit testovací síťové prostředí
> * Nasadit bránu firewall
> * Vytvořit výchozí trasu
> * Konfigurace pravidla aplikace pro povolení přístupu k www.google.com
> * Nakonfigurovat pravidlo sítě pro povolení přístupu k externím serverům DNS
> * Otestovat bránu firewall

Pokud dáváte přednost, můžete absolvovat s použitím tohoto postupu [webu Azure portal](tutorial-firewall-deploy-portal.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Tento postup vyžaduje místně spusťte PowerShell. Musíte mít nainstalovaný modul Azure PowerShell. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). Po ověření verze PowerShellu spusťte příkaz `Connect-AzAccount`, abyste vytvořili připojení k Azure.

## <a name="set-up-the-network"></a>Nastavit síť

Nejprve vytvořte skupinu prostředků obsahující prostředky potřebné k nasazení brány firewall. Potom vytvořte virtuální síť, podsítě a testovací servery.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků obsahuje všechny prostředky pro nasazení.

```azurepowershell
New-AzResourceGroup -Name Test-FW-RG -Location "East US"
```

### <a name="create-a-vnet"></a>Vytvoření virtuální sítě

Tato virtuální síť má tři podsítě:

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name AzureFirewallSubnet -AddressPrefix 10.0.1.0/24
$Worksub = New-AzVirtualNetworkSubnetConfig -Name Workload-SN -AddressPrefix 10.0.2.0/24
$Jumpsub = New-AzVirtualNetworkSubnetConfig -Name Jump-SN -AddressPrefix 10.0.3.0/24
```

> [!NOTE]
> Minimální velikost podsítě AzureFirewallSubnet je /26.

Teď vytvořte virtuální síť:

```azurepowershell
$testVnet = New-AzVirtualNetwork -Name Test-FW-VN -ResourceGroupName Test-FW-RG `
-Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $FWsub, $Worksub, $Jumpsub
```

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Teď vytvoříte virtuální počítače pro jump server a server úloh a umístíte je do příslušných podsítí.
Po zobrazení výzvy zadejte pro virtuální počítač uživatelské jméno a heslo.

Vytvoření virtuálního počítače Srv odkazů.

```azurepowershell
New-AzVm `
    -ResourceGroupName Test-FW-RG `
    -Name "Srv-Jump" `
    -Location "East US" `
    -VirtualNetworkName Test-FW-VN `
    -SubnetName Jump-SN `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

Vytvoření pracovního vytížení virtuálního počítače s žádná veřejná IP adresa.
Po zobrazení výzvy zadejte pro virtuální počítač uživatelské jméno a heslo.

```azurepowershell
#Create the NIC
$NIC = New-AzNetworkInterface -Name Srv-work -ResourceGroupName Test-FW-RG `
 -Location "East US" -Subnetid $testVnet.Subnets[1].Id 

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName Srv-Work -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Srv-Work -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName Test-FW-RG -Location "East US" -VM $VirtualMachine -Verbose
```

## <a name="deploy-the-firewall"></a>Nasazení brány firewall

Nyní nasazení brány do virtuální sítě.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName Test-FW-RG `
  -Location "East US" -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name Test-FW01 -ResourceGroupName Test-FW-RG -Location "East US" -VirtualNetworkName Test-FW-VN -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP
```

Poznamenejte si privátní IP adresu. Budete ji potřebovat později při vytváření výchozí trasy.

## <a name="create-a-default-route"></a>Vytvořit výchozí trasu

Vytvoření tabulky, se zakázaným šířením BGP trasy

```azurepowershell
$routeTableDG = New-AzRouteTable `
  -Name Firewall-rt-table `
  -ResourceGroupName Test-FW-RG `
  -location "East US" `
  -DisableBgpRoutePropagation

#Create a route
 Add-AzRouteConfig `
  -Name "DG-Route" `
  -RouteTable $routeTableDG `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $testVnet `
  -Name Workload-SN `
  -AddressPrefix 10.0.2.0/24 `
  -RouteTable $routeTableDG | Set-AzVirtualNetwork
```

## <a name="configure-an-application-rule"></a>Konfigurace pravidla aplikace

Pravidlo brány application umožňuje odchozí přístup k www.google.com.

```azurepowershell
$AppRule1 = New-AzFirewallApplicationRule -Name Allow-Google -SourceAddress 10.0.2.0/24 `
  -Protocol http, https -TargetFqdn www.google.com

$AppRuleCollection = New-AzFirewallApplicationRuleCollection -Name App-Coll01 `
  -Priority 200 -ActionType Allow -Rule $AppRule1

$Azfw.ApplicationRuleCollections = $AppRuleCollection

Set-AzFirewall -AzureFirewall $Azfw
```

Brána Azure Firewall obsahuje předdefinovanou kolekci pravidel pro infrastrukturu plně kvalifikovaných názvů domén, které jsou ve výchozím nastavení povolené. Tyto plně kvalifikované názvy domén jsou specifické pro tuto platformu a pro jiné účely je nelze použít. Další informace najdete v tématu [Plně kvalifikované názvy domén infrastruktury](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Konfigurace pravidla sítě

Pravidlo sítě umožňuje odchozí přístup na dvě IP adresy na port 53 (DNS).

```azurepowershell
$NetRule1 = New-AzFirewallNetworkRule -Name "Allow-DNS" -Protocol UDP -SourceAddress 10.0.2.0/24 `
   -DestinationAddress 209.244.0.3,209.244.0.4 -DestinationPort 53

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 200 `
   -Rule $NetRule1 -ActionType "Allow"

$Azfw.NetworkRuleCollections = $NetRuleCollection

Set-AzFirewall -AzureFirewall $Azfw
```

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Změna primární a sekundární adresy DNS u síťového rozhraní **Srv-Work**

Pro účely testování v tomto postupu konfigurace adresy serveru primární a sekundární DNS. Není to povinné obecné Brána Firewall služby Azure.

```azurepowershell
$NIC.DnsSettings.DnsServers.Add("209.244.0.3")
$NIC.DnsSettings.DnsServers.Add("209.244.0.4")
$NIC | Set-AzNetworkInterface
```

## <a name="test-the-firewall"></a>Otestovat bránu firewall

Teď otestujte bránu firewall, aby ověřili, že funguje podle očekávání.

1. Všimněte si privátní IP adresu pro **Srv pracovní** virtuálního počítače:

   ```
   $NIC.IpConfigurations.PrivateIpAddress
   ```

1. Připojení k vzdálené plochy **Srv Jump** virtuálního počítače a přihlášení. Odtud otevření připojení ke vzdálené ploše **Srv pracovní** privátní IP adresy a přihlašování.

3. Na **SRV pracovní**, otevřete okno Powershellu a spusťte následující příkazy:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Oba příkazy by měl vrátit odpovědi znázorňující, že vaše dotazy DNS se zobrazuje přes bránu firewall.

1. Spusťte následující příkazy:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   Uspěli www.google.com požadavky a požadavky www.microsoft.com by selhat. Tento příklad ukazuje, že pravidla brány firewall fungují podle očekávání.

Teď jste ověřili funkčnost pravidla brány firewall:

* Názvy DNS můžete přeložit pomocí nakonfigurovaného externího serveru DNS.
* Můžete přejít na jediný povolený plně kvalifikovaný název domény, ale jinam už ne.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Můžete ponechat prostředky brány firewall k dalšímu kurzu, nebo pokud už je nepotřebujete, odstraňte **Test-FW-RG** skupinu prostředků odstraňte všechny prostředky související s brány firewall:

```azurepowershell
Remove-AzResourceGroup -Name Test-FW-RG
```

## <a name="next-steps"></a>Další postup

* [Kurz: Monitorujte protokoly brány Firewall na Azure](./tutorial-diagnostics.md)
