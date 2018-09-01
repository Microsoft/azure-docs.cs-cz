---
title: Konfigurace AlwaysOn naslouchacích procesů skupin dostupnosti – Microsoft Azure | Dokumentace Microsoftu
description: Nakonfigurujte naslouchacích procesů skupin dostupnosti pro model Azure Resource Manageru pomocí interního nástroje pro jednu nebo více IP adres.
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
ms.openlocfilehash: b4641c847db817df905f056847a26d003ac25fd1
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2018
ms.locfileid: "43381791"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Nakonfigurujte jeden nebo více Always On naslouchacích procesů skupin dostupnosti - Resource Manageru
Toto téma ukazuje, jak:

* Vytvoření interního nástroje pro skupiny dostupnosti SQL serveru pomocí rutin prostředí PowerShell.
* Přidejte další IP adresy na nástroj pro vyrovnávání zatížení pro víc než jednu skupinu dostupnosti. 

Naslouchací proces skupiny dostupnosti je název virtuální sítě, které se klienti připojovat k pro přístup k databázi. Nástroj pro vyrovnávání zatížení na virtuálních počítačích Azure, obsahuje IP adresu pro naslouchací proces. Nástroje pro vyrovnávání zatížení směruje provoz do instance systému SQL Server, která naslouchá na portu sondy. Skupiny dostupnosti se obvykle používá interního nástroje load balancer. Interní Azure load balancer můžete hostovat jeden nebo více IP adres. Každou IP adresu používá port konkrétního testu. Tento dokument ukazuje, jak pomocí prostředí PowerShell k vytvoření nástroje pro vyrovnávání zatížení nebo přidat IP adresy existující nástroj pro vyrovnávání zatížení pro skupiny dostupnosti systému SQL Server. 

Možnost přiřadit několik IP adres do interního nástroje noví a je dostupná pouze v modelu Resource Manager. Tento úkol provést, musíte mít skupinu dostupnosti systému SQL Server nasazený na virtuální počítače Azure v modelu Resource Manager. Oba virtuální počítače systému SQL Server musí patřit do stejné skupiny dostupnosti. Můžete použít [šablony aplikace Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) automaticky vytvoření skupiny dostupnosti v Azure Resource Manageru. Tato šablona automaticky vytvoří skupinu dostupnosti, včetně interního nástroje pro vás. Pokud dáváte přednost, můžete si [ruční konfigurace skupiny dostupnosti Always On](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Toto téma vyžaduje, že vaše skupiny dostupnosti jsou už nakonfigurovaná.  

Související témata:

* [Konfigurace skupin dostupnosti AlwaysOn na virtuálním počítači Azure (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Konfigurace připojení typu VNet-to-VNet pomocí Azure Resource Manageru a Powershellu](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="configure-the-windows-firewall"></a>Konfigurace brány Windows Firewall
Konfigurace Windows Firewall a povolit přístup k SQL serveru. Pravidla brány firewall umožňují připojení TCP ke portů používá instanci systému SQL Server a naslouchacího procesu kontroly. Podrobné pokyny najdete v tématu [konfigurovat bránu Windows Firewall pro přístup k databázovému stroji](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Vytvořte příchozí pravidlo pro port SQL serveru a portu sondy.

Pokud jste omezení přístupu se skupinou zabezpečení sítě Azure, zajistěte, aby pravidla povolit zahrnují adres back-end IP adresy virtuálního počítače SQL serveru a nástroje pro vyrovnávání zatížení plovoucí IP adres pro naslouchacího procesu AG a IP adresu clusteru core Pokud je k dispozici.

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Ukázkový skript: Vytvoření interního nástroje pomocí prostředí PowerShell
> [!NOTE]
> Pokud jste vytvořili vaší skupiny dostupnosti s [šablony aplikace Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md), interní služby load balancer už byl vytvořen. 
> 
> 

Následující skript Powershellu vytvoří interního nástroje, nakonfiguruje pravidla Vyrovnávání zatížení a nastaví IP adresu nástroje pro vyrovnávání zatížení. Pro spuštění skriptu, otevřete Windows PowerShell ISE a vložte skript v podokně skriptu. Použití `Connect-AzureRmAccount` pro přihlášení k prostředí PowerShell. Pokud máte více předplatných Azure, použijte `Select-AzureRmSubscription ` nastavte předplatné. 

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

## <a name="Add-IP"></a> Ukázkový skript: Přidat IP adresu existující nástroj pro vyrovnávání zatížení pomocí Powershellu
Pokud chcete použít více než jednu skupinu dostupnosti, přidáte další IP adresu nástroji pro vyrovnávání zatížení. Každou IP adresu vyžaduje svou vlastní pravidla Vyrovnávání zatížení, port testu a front-port.

Front-endový port je port, který se aplikace používají pro připojení k instanci systému SQL Server. IP adresy pro skupiny dostupnosti jinou můžete použít stejný port front-endu.

> [!NOTE]
> Každou IP adresu pro skupiny dostupnosti systému SQL Server, vyžaduje port konkrétního testu. Například pokud jedna IP adresa nástroje pro vyrovnávání zatížení používá port testu 59999, žádné další IP adresy v tomto nástroji pro vyrovnávání zatížení můžete použít port testu 59999.

* Informace o omezeních nástroje pro vyrovnávání zatížení najdete v tématu **privátní front-endové IP adresy na nástroj pro vyrovnávání zatížení** pod [omezení sítě – Azure Resource Manageru](../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Informace o omezeních skupiny dostupnosti, najdete v tématu [omezení (skupiny dostupnosti)](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

Následující skript přidá novou IP adresu existující nástroj pro vyrovnávání zatížení. Port naslouchacího procesu ILB používá pro front-endový port pro vyrovnávání zatížení. Tento port může být port, který naslouchá SQL Server. Pro výchozí instance SQL serveru port je 1433. Pravidlo pro skupinu dostupnosti Vyrovnávání zatížení vyžaduje plovoucí IP (přímá odpověď ze serveru vrácené), takže port back-end je stejný jako front-endový port. Aktualizujte proměnné pro vaše prostředí. 

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

## <a name="configure-the-listener"></a>Nakonfigurujte naslouchací proces

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>Nastavte na port naslouchacího procesu v aplikaci SQL Server Management Studio

1. Spusťte SQL Server Management Studio a připojte se k primární replice.

1. Přejděte do **AlwaysOn vysokou dostupnost** | **skupin dostupnosti** | **naslouchacích procesů skupin dostupnosti**. 

1. Teď byste měli vidět název naslouchacího procesu, který jste vytvořili v modulu Správce clusteru převzetí služeb při selhání. Klikněte pravým tlačítkem na název naslouchacího procesu a klikněte na tlačítko **vlastnosti**.

1. V **Port** pole, zadejte číslo portu pro naslouchací proces skupiny dostupnosti s využitím $EndpointPort jste použili dříve (1433 se výchozí hodnota), potom klikněte na tlačítko **OK**.

## <a name="test-the-connection-to-the-listener"></a>Test připojení k naslouchacímu procesu

Chcete-li otestovat připojení:

1. Připojení RDP k serveru SQL Server, který je ve stejné virtuální síti, ale není vlastníkem repliky. To může být SQL Server v clusteru.

1. Použití **sqlcmd** nástroj k testování připojení. Například následující skript vytvoří **sqlcmd** připojení k primární replice prostřednictvím naslouchací proces s ověřováním Windows:
   
    ```
    sqlmd -S <listenerName> -E
    ```
   
    Pokud je naslouchací proces používá jiný port než výchozí port (1433), zadejte port, který v připojovacím řetězci. Například následující příkaz sqlcmd se připojí k naslouchání na portu 1435: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

Připojení SQLCMD se automaticky připojí k libovolným instance systému SQL Server hostuje primární repliku. 

> [!NOTE]
> Ujistěte se, že je otevřen v bráně firewall oba servery SQL port, který zadáte. Oba servery vyžadují příchozí pravidlo pro port TCP, který používáte. Zobrazit [přidat nebo upravit pravidlo brány Firewall](http://technet.microsoft.com/library/cc753558.aspx) Další informace. 
> 
> 

## <a name="guidelines-and-limitations"></a>Pokyny a omezení
Všimněte si, že následující pokyny pro naslouchací proces skupiny dostupnosti v Azure s využitím interní nástroj pro vyrovnávání zatížení:

* Se interní nástroj pro vyrovnávání zatížení můžete přístup jenom k naslouchací proces z v rámci stejné virtuální síti.

* Pokud jste omezení přístupu se skupinou zabezpečení sítě Azure, zajistěte, aby pravidla povolit zahrnují adres back-end IP adresy virtuálního počítače SQL serveru a nástroje pro vyrovnávání zatížení plovoucí IP adres pro naslouchacího procesu AG a IP adresu clusteru core Pokud je k dispozici.

## <a name="for-more-information"></a>Další informace
Další informace najdete v tématu [skupiny dostupnosti nakonfigurujte Always On na virtuálním počítači Azure ručně](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

## <a name="powershell-cmdlets"></a>Rutiny prostředí PowerShell
Pomocí následujících rutin prostředí PowerShell k vytvoření interního nástroje pro Azure virtual machines.

* [Nový-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt619450.aspx) vytvoří nástroj pro vyrovnávání zatížení. 
* [Nový-AzureRMLoadBalancerFrontendIpConfig](http://msdn.microsoft.com/library/mt603510.aspx) vytvoří konfiguraci front-end IP adresy nástroje pro vyrovnávání zatížení. 
* [Nový-AzureRmLoadBalancerRuleConfig](http://msdn.microsoft.com/library/mt619391.aspx) vytvoří konfiguraci pravidla nástroje pro vyrovnávání zatížení. 
* [Nový-AzureRmLoadBalancerBackendAddressPoolConfig](http://msdn.microsoft.com/library/mt603791.aspx) vytvoří konfiguraci fondu adres back-end pro nástroj pro vyrovnávání zatížení. 
* [Nový-AzureRmLoadBalancerProbeConfig](http://msdn.microsoft.com/library/mt603847.aspx) vytvoří konfiguraci sondy pro nástroj pro vyrovnávání zatížení.
* [Remove-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx) odebere nástroj pro vyrovnávání zatížení ze skupiny prostředků Azure.
