---
title: Konfigurace naslouchacích procesů skupiny dostupnosti & vyrovnávání zatížení (PowerShell)
description: Nakonfigurujte posluchače skupiny dostupnosti v modelu Azure Resource Manager pomocí interního nástroje pro vyrovnávání zatížení s jednou nebo více ADRESAMI IP.
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/06/2019
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: f7d14da6c7436120e013c979b108f61b82640d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647879"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Konfigurace jednoho nebo více naslouchacích procesů skupiny dostupnosti always on – Správce prostředků
Toto téma ukazuje, jak:

* Vytvořte interní nástroje pro vyrovnávání zatížení pro skupiny dostupnosti serveru SQL Server pomocí rutin prostředí PowerShell.
* Přidejte další adresy IP do programu pro vyrovnávání zatížení pro více než jednu skupinu dostupnosti. 

Naslouchací proces skupiny dostupnosti je název virtuální sítě, ke kterému se klienti připojují pro přístup k databázi. Ve virtuálních počítačích Azure nástroj pro vyrovnávání zatížení obsahuje IP adresu pro naslouchací proces. Nástroje pro vyrovnávání zatížení směruje provoz na instanci SQL Server, který naslouchá na portu sondy. Skupina dostupnosti obvykle používá interní systém vyrovnávání zatížení. Interní správce zatížení Azure může hostovat jednu nebo více IP adres. Každá adresa IP používá konkrétní port sondy. Tento dokument ukazuje, jak pomocí prostředí PowerShell vytvořit nástroje pro vyrovnávání zatížení nebo přidat adresy IP do existujícího nástroje pro vyrovnávání zatížení pro skupiny dostupnosti serveru SQL Server. 

Možnost přiřadit více IP adres internímu nástroje pro vyrovnávání zatížení je pro Azure nová a je dostupná jenom v modelu Resource Manageru. K dokončení tohoto úkolu, musíte mít sql server skupiny dostupnosti nasazené na virtuálních počítačích Azure v modelu Správce prostředků. Oba virtuální počítače SQL Server musí patřit do stejné skupiny dostupnosti. Pomocí šablony [Microsoftu](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) můžete automaticky vytvořit skupinu dostupnosti ve Správci prostředků Azure. Tato šablona automaticky vytvoří skupinu dostupnosti, včetně interního systému vyrovnávání zatížení pro vás. Pokud chcete, můžete [ručně nakonfigurovat skupinu dostupnosti vždy zapnuto](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Toto téma vyžaduje, aby vaše skupiny dostupnosti jsou již nakonfigurovány.  

Mezi související témata patří:

* [Konfigurace skupin dostupnosti AlwaysOn ve virtuálním počítači Azure (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Konfigurace propojení VNet-to-VNet s použitím Azure Resource Manageru a prostředí PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>Ověření verze PowerShellu

Příklady v tomto článku jsou testovány pomocí modulu Azure PowerShell verze 5.4.1.

Ověřte, zda je váš modul PowerShellu 5.4.1 nebo novější.

Viz [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="configure-the-windows-firewall"></a>Konfigurace brány Windows Firewall

Nakonfigurujte bránu Windows Firewall tak, aby umožňovala přístup k serveru SQL Server. Pravidla brány firewall umožňují připojení TCP k portům, které používá instance serveru SQL Server, a sonda naslouchací proces. Podrobné pokyny naleznete v [tématu Konfigurace brány Windows Firewall pro přístup k databázovému stroji](https://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Vytvořte příchozí pravidlo pro port serveru SQL Server a pro port sondy.

Pokud omezujete přístup pomocí skupiny zabezpečení sítě Azure, ujistěte se, že pravidla povolení zahrnují ip adresy IP v back-endu SQL Server a plovoucí IP adresy nástroje pro vyrovnávání zatížení pro naslouchací proces AG a případně základní IP adresu clusteru.

## <a name="determine-the-load-balancer-sku-required"></a>Určení požadované skladové položky pro vyrovnávání zatížení

[Azure vyrovnávání zatížení](../../../load-balancer/load-balancer-overview.md) je k dispozici ve 2 SKU: Základní & Standard. Doporučuje se standardní nástroje pro vyrovnávání zatížení. Pokud jsou virtuální počítače v sadě dostupnosti, je povolen základní nástroj pro vyrovnávání zatížení. Standardní vyrovnávání zatížení vyžaduje, aby všechny IP adresy virtuálního počítačů používaly standardní IP adresy.

Aktuální [šablona společnosti Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) pro skupinu dostupnosti používá základní vyrovnávání zatížení se základními adresami IP.

Příklady v tomto článku určují standardní vyrovnávání zatížení. V příkladech skript obsahuje `-sku Standard`.

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

Chcete-li vytvořit základní systém `-sku Standard` vyrovnávání zatížení, odeberte z řádku, který vytváří systém vyrovnávání zatížení. Například:

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Příklad skriptu: Vytvoření interního systému vyrovnávání zatížení pomocí prostředí PowerShell

> [!NOTE]
> Pokud jste vytvořili skupinu dostupnosti pomocí [šablony společnosti Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md), byl již vytvořen interní systém vyrovnávání zatížení.

Následující skript prostředí PowerShell vytvoří interní systém vyrovnávání zatížení, nakonfiguruje pravidla vyrovnávání zatížení a nastaví adresu IP pro systém vyrovnávání zatížení. Chcete-li skript spustit, otevřete prostředí Windows PowerShell ISE a vložte jej do podokna Skript. Slouží `Connect-AzAccount` k přihlášení k Prostředí PowerShell. Pokud máte více předplatných `Select-AzSubscription` Azure, použijte k nastavení předplatného. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

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

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzNetworkInterface -NetworkInterface $NIC
        start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a><a name="Add-IP"></a>Příklad skriptu: Přidání ADRESY IP do existujícího vykladače zatížení pomocí prostředí PowerShell
Chcete-li použít více než jednu skupinu dostupnosti, přidejte do programu pro vyrovnávání zatížení další adresu IP. Každá adresa IP vyžaduje vlastní pravidlo vyrovnávání zatížení, port sondy a přední port.

Front-end port je port, který aplikace používají pro připojení k instanci serveru SQL Server. Ip adresy pro různé skupiny dostupnosti mohou používat stejný front-end port.

> [!NOTE]
> Pro skupiny dostupnosti serveru SQL Server každá adresa IP vyžaduje konkrétní port sondy. Například pokud jedna adresa IP v provyčitadlo na zatížení používá port sondy 59999, žádné jiné IP adresy v tomto vykladače zatížení můžete použít probe port 59999.

* Informace o limitech nástroje pro vyrovnávání zatížení najdete **v tématu Privátní front-end IP na nástroje pro vyrovnávání zatížení** v části [Limity sítě – Azure Resource Manager](../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Informace o omezeních skupin dostupnosti naleznete v [tématu Omezení (skupiny dostupnosti).](https://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG)

Následující skript přidá novou adresu IP do existujícího vykladače zatížení. ILB používá port naslouchací proces pro front-end port vyrovnávání zatížení. Tento port může být port, který SQL Server naslouchá. Pro výchozí instance SQL Server port je 1433. Pravidlo vyrovnávání zatížení pro skupinu dostupnosti vyžaduje plovoucí IP (přímé vrácení serveru), takže port back-end je stejný jako port front-end. Aktualizujte proměnné pro vaše prostředí. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzLoadBalancer 

$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzLoadBalancer   
```

## <a name="configure-the-listener"></a>Konfigurace naslouchacího procesu

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>Nastavení portu naslouchací procesu ve správě serveru SQL Server

1. Spusťte sql server management studio a připojte se k primární replice.

1. Přejděte **na alwayson dostupnost** | **skupiny dostupnosti skupiny** | **naslouchací procesy**. 

1. Nyní byste měli vidět název posluchače, který jste vytvořili ve Správci clusteru s podporou převzetí služeb při selhání. Klepněte pravým tlačítkem myši na název naslouchací procesu a klepněte na příkaz **Vlastnosti**.

1. V poli **Port** zadejte číslo portu pro posluchače skupiny dostupnosti pomocí $EndpointPort, který jste použili dříve (1433 byl výchozí), pak klepněte na tlačítko **OK**.

## <a name="test-the-connection-to-the-listener"></a>Otestovat připojení k naslouchací proces

Chcete-li otestovat připojení:

1. RDP na SQL Server, který je ve stejné virtuální síti, ale nevlastní repliku. Může se jedná o další server SQL V clusteru.

1. Pomocí nástroje **sqlcmd** otestujte připojení. Například následující skript vytvoří **sqlcmd** připojení k primární replice prostřednictvím naslouchacího procesu s ověřováním systému Windows:
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    Pokud naslouchací proces používá jiný port než výchozí port (1433), zadejte port v připojovacím řetězci. Například následující příkaz sqlcmd se připojí k naslouchací procesu na portu 1435: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

Připojení SQLCMD se automaticky připojí k kterékoli instanci serveru SQL Server, která je hostitelem primární repliky. 

> [!NOTE]
> Ujistěte se, že zadaný port je otevřený na bráně firewall obou serverů SQL. Oba servery vyžadují příchozí pravidlo pro port TCP, který používáte. Další informace naleznete v tématu [Přidání nebo úprava pravidla brány firewall.](https://technet.microsoft.com/library/cc753558.aspx) 
> 
> 

## <a name="guidelines-and-limitations"></a>Pokyny a omezení
Všimněte si následujících pokynů pro naslouchací proces skupiny dostupnosti v Azure pomocí interního systému vyrovnávání zatížení:

* S interní nástroj pro vyrovnávání zatížení, přístup k naslouchací proces pouze ze stejné virtuální sítě.

* Pokud omezujete přístup pomocí skupiny zabezpečení sítě Azure, ujistěte se, že pravidla povolení zahrnují ip adresy IP v back-endu SQL Server a plovoucí IP adresy nástroje pro vyrovnávání zatížení pro naslouchací proces AG a případně základní IP adresu clusteru.

## <a name="for-more-information"></a>Další informace
Další informace najdete [v tématu Konfigurace skupiny dostupnosti vždy v aplikaci Azure v aplikaci Azure ručně](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

## <a name="powershell-cmdlets"></a>Rutiny prostředí PowerShell
Pomocí následujících rutin Prostředí PowerShell vytvořte interní nástroj pro vyrovnávání zatížení pro virtuální počítače Azure.

* [New-AzLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) vytvoří vyvažovač zatížení. 
* [New-AzLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) vytvoří front-end konfiguraci IP pro nástroj pro vyrovnávání zatížení. 
* [New-AzLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) vytvoří konfiguraci pravidla pro nástroj pro vyrovnávání zatížení. 
* [New-AzLoadBalancerBackendAddressComfig](https://msdn.microsoft.com/library/mt603791.aspx) vytvoří konfiguraci fondu back-endových adres pro nástroj pro vyrovnávání zatížení. 
* [New-AzLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) vytvoří konfiguraci sondy pro nástroj pro vyrovnávání zatížení.
* [Odebrat AzLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) odebere balancer zatížení ze skupiny prostředků Azure.
