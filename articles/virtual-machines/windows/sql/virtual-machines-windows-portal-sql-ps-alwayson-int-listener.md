---
title: Konfigurace vždy na naslouchací procesy skupiny dostupnosti – Microsoft Azure | Microsoft Docs
description: Naslouchací procesy skupiny dostupnosti nakonfigurujte na modelu Azure Resource Manager, pomocí interní nástroj pro jednu nebo více IP adres.
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/22/2017
ms.author: mikeray
ms.openlocfilehash: 11aecd9b2bc1bc1521a0e27fc3cd06fe7426a26d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31600287"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Nakonfigurovat jeden nebo více vždy na dostupnosti naslouchací procesy skupiny - Resource Manager
Toto téma ukazuje, jak:

* Vytvořte interní nástroj pro skupiny dostupnosti systému SQL Server pomocí rutin prostředí PowerShell.
* Přidejte další IP adresy pro vyrovnávání zatížení pro víc než jedné skupiny dostupnosti. 

Naslouchací proces skupiny dostupnosti je název virtuální sítě, který klienti připojovat k pro přístup k databázi. Nástroj pro vyrovnávání zatížení na virtuálních počítačích Azure, obsahuje IP adresu pro naslouchací proces. Nástroje pro vyrovnávání zatížení směruje provoz do instance systému SQL Server, která naslouchá na portu testu. Skupiny dostupnosti se obvykle používá interní nástroj. K nástroji pro vyrovnávání zatížení interní Azure můžete hostovat jednu nebo více IP adres. Každou IP adresu používá port konkrétní test. Tento dokument ukazuje, jak pomocí prostředí PowerShell vytvořit nástroj pro vyrovnávání zatížení nebo přidejte IP adresy do existující Vyrovnávání zatížení pro skupiny dostupnosti systému SQL Server. 

Umožňuje přiřadit k nástroji pro vyrovnávání zatížení pro vnitřní více IP adres je nové do Azure a je dostupný jenom v modelu Resource Manager. Tuto úlohu dokončit, musíte mít skupinu dostupnosti systému SQL Server, který je nasazen na virtuálních počítačích Azure v modelu Resource Manager. Virtuální počítače systému SQL Server musí patřit do stejné skupiny dostupnosti. Můžete použít [šablony aplikace Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) pro automatické vytvoření skupiny dostupnosti ve službě Správce prostředků Azure. Tato šablona automaticky vytvoří skupiny dostupnosti, včetně nástroje pro vyrovnávání zatížení pro vnitřní za vás. Pokud dáváte přednost, můžete [ruční konfigurace skupiny dostupnosti Always On](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Toto téma vyžaduje vaše skupiny dostupnosti jsou již nakonfigurována.  

Související témata:

* [Konfigurace skupin dostupnosti AlwaysOn na virtuálním počítači Azure (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Nakonfigurujte připojení VNet-to-VNet s použitím Azure Resource Manageru a prostředí PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="configure-the-windows-firewall"></a>Konfigurace brány Windows Firewall
Konfigurace brány Windows Firewall pro povolení přístupu k SQL serveru. Pravidla brány firewall povolit připojení TCP pro porty používané instance systému SQL Server a kontroly naslouchací proces. Podrobné pokyny najdete v tématu [konfigurace brány Windows Firewall pro přístup k databázovému stroji](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Vytvoření příchozího pravidla pro port serveru SQL Server a port testu.

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Ukázkový skript: Vytvoření Vyrovnávání zatížení interní pomocí prostředí PowerShell
> [!NOTE]
> Pokud jste vytvořili vaší skupiny dostupnosti s [šablony aplikace Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md), nástroje pro vyrovnávání zatížení pro vnitřní již bylo vytvořeno. 
> 
> 

Následující skript prostředí PowerShell vytvoří interní nástroj, nakonfiguruje pravidla Vyrovnávání zatížení a nastaví IP adresu pro nástroj pro vyrovnávání zatížení. Pokud chcete spustit skript, otevřete Windows PowerShell ISE a vložte skript v podokně skriptu. Použití `Connect-AzureRmAccount` pro přihlášení k prostředí PowerShell. Pokud máte víc předplatných Azure, použijte `Select-AzureRmSubscription ` nastavte předplatné. 

```powershell
# Connect-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the front-end configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the back-end configuration

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzureRMLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="Add-IP"></a> Ukázkový skript: Přidat IP adresu existující Vyrovnávání zatížení s prostředí PowerShell
Pokud chcete použít více než jedné skupiny dostupnosti, přidejte další IP adresu nástroji pro vyrovnávání zatížení. Každou IP adresu vyžaduje vlastní pravidla Vyrovnávání zatížení, port testu a front port.

Front-end port je port, který aplikace použít pro připojení k instanci systému SQL Server. IP adresy pro skupiny dostupnosti jinou můžete použít stejný port front-endu.

> [!NOTE]
> Pro skupiny dostupnosti systému SQL Server vyžaduje každou IP adresu, port konkrétní testu. Například pokud jednu IP adresu zařízení na Vyrovnávání zatížení používá port testu 59999, žádné jiné IP adresy na tento nástroj pro vyrovnávání zatížení můžete použít port testu 59999.

* Informace o omezeních pro vyrovnávání zatížení najdete v tématu **privátní front-endu IP adresu na nástroj pro vyrovnávání zatížení** pod [omezení sítě - Správce Azure Resource Manager](../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Informace o omezeních skupiny dostupnosti naleznete v tématu [omezení (skupiny dostupnosti)](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

Následující skript přidá novou IP adresu do existující pro vyrovnávání zatížení. ILB používá port naslouchacího procesu pro front-end port Vyrovnávání zatížení. Port, který SQL Server naslouchá na může být tento port. Pro výchozí instance systému SQL Server je port 1433. Pravidlo pro skupinu dostupnosti Vyrovnávání zatížení vyžaduje plovoucí IP (přímá odpověď ze serveru), tak back-end port je stejný jako front-end port. Aktualizujte proměnné pro vaše prostředí. 

```powershell
# Connect-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzureRmLoadBalancer 

$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzureRmLoadBalancer   
```

## <a name="configure-the-listener"></a>Konfigurace naslouchací proces

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>Nastavit port naslouchacího procesu v systému SQL Server Management Studio

1. Spusťte SQL Server Management Studio a připojte se k primární replice.

1. Přejděte na **AlwaysOn vysokou dostupnost** | **skupiny dostupnosti** | **naslouchací procesy skupiny dostupnosti**. 

1. Teď byste měli vidět název naslouchacího procesu, který jste vytvořili ve Správci clusteru převzetí služeb při selhání. Klikněte pravým tlačítkem na název naslouchacího procesu a klikněte na tlačítko **vlastnosti**.

1. V **Port** pole, zadejte číslo portu pro naslouchací proces skupiny dostupnosti pomocí $EndpointPort jste použili předtím (1433 se výchozí nastavení), pak klikněte na tlačítko **OK**.

## <a name="test-the-connection-to-the-listener"></a>Test připojení k naslouchací proces

K testování připojení:

1. RDP k systému SQL Server, který je ve stejné virtuální síti, ale není vlastníkem repliky. To může být SQL Server v clusteru.

1. Použití **sqlcmd** nástroj k testování připojení. Například následující skript vytvoří **sqlcmd** připojení k primární replice prostřednictvím naslouchací proces s ověřováním systému Windows:
   
    ```
    sqlmd -S <listenerName> -E
    ```
   
    Pokud naslouchací proces používá jiný port než výchozí port (1433), zadejte port v připojovacím řetězci. Například následující příkaz sqlcmd připojí k naslouchání na portu 1435: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

Připojení SQLCMD se automaticky připojí na kteroukoli instanci systému SQL Server hostitelem primární repliky. 

> [!NOTE]
> Ujistěte se, že je port, který zadáte otevřen v bráně firewall oba servery SQL. Oba servery vyžadují příchozí pravidlo pro port TCP, který používáte. V tématu [přidat nebo upravit pravidlo brány Firewall](http://technet.microsoft.com/library/cc753558.aspx) Další informace. 
> 
> 

## <a name="guidelines-and-limitations"></a>Pokyny a omezení
Všimněte si, že následující pokyny na naslouchací proces skupiny dostupnosti v Azure pomocí interní nástroj pro vyrovnávání zatížení:

* S nástrojem pro vyrovnávání zatížení pro vnitřní můžete přístup jenom k naslouchání z v rámci stejné virtuální síti.


## <a name="for-more-information"></a>Další informace
Další informace najdete v tématu [skupiny dostupnosti nakonfigurujte Always On ve virtuálním počítači Azure ručně](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

## <a name="powershell-cmdlets"></a>Rutiny prostředí PowerShell
Pomocí následující rutiny prostředí PowerShell k vytvoření interní nástroj pro virtuální počítače Azure.

* [Nové AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt619450.aspx) vytvoří nástroj pro vyrovnávání zatížení. 
* [Nové AzureRMLoadBalancerFrontendIpConfig](http://msdn.microsoft.com/library/mt603510.aspx) vytvoří konfiguraci front-end IP adresy pro vyrovnávání zatížení. 
* [Nové AzureRmLoadBalancerRuleConfig](http://msdn.microsoft.com/library/mt619391.aspx) vytvoří pravidlo konfigurace pro vyrovnávání zatížení. 
* [Nové AzureRmLoadBalancerBackendAddressPoolConfig](http://msdn.microsoft.com/library/mt603791.aspx) vytvoří konfiguraci fondu adres back-end pro vyrovnávání zatížení. 
* [Nové AzureRmLoadBalancerProbeConfig](http://msdn.microsoft.com/library/mt603847.aspx) vytvoří test konfigurace pro vyrovnávání zatížení.
* [Odebrat AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx) Odebere skupinu prostředků Azure pro vyrovnávání zatížení.
