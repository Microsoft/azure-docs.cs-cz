---
title: Konfigurace naslouchacího procesu skupiny dostupnosti Always On – Microsoft Azure | Microsoft Docs
description: Nakonfigurujte naslouchací procesy skupiny dostupnosti u Azure Resource Manager modelu pomocí interního nástroje pro vyrovnávání zatížení s jednou nebo více IP adresami.
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
ms.openlocfilehash: 7d6427e88960ec3ff550affb1624dd82e561a6bb
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102180"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Konfigurace jednoho nebo více naslouchacích skupin dostupnosti Always On – Správce prostředků
V tomto tématu se dozvíte, jak:

* Vytvořte interní nástroj pro vyrovnávání zatížení pro SQL Server skupiny dostupnosti pomocí rutin prostředí PowerShell.
* Přidejte další IP adresy do nástroje pro vyrovnávání zatížení pro více než jednu skupinu dostupnosti. 

Naslouchací proces skupiny dostupnosti je název virtuální sítě, ke které se klienti připojují pro přístup k databázi. Na virtuálních počítačích Azure obsahuje nástroj pro vyrovnávání zatížení IP adresu pro naslouchací proces. Nástroj pro vyrovnávání zatížení směruje provoz do instance SQL Server, která naslouchá na portu sondy. Skupina dostupnosti obvykle používá interní nástroj pro vyrovnávání zatížení. Interní nástroj pro vyrovnávání zatížení Azure může hostovat jednu nebo více IP adres. Každá IP adresa používá konkrétní port testu paměti. V tomto dokumentu se dozvíte, jak pomocí PowerShellu vytvořit nástroj pro vyrovnávání zatížení nebo přidat IP adresy do stávajícího nástroje pro vyrovnávání zatížení pro SQL Server skupiny dostupnosti. 

Možnost přiřazení více IP adres k internímu nástroji pro vyrovnávání zatížení je pro Azure novinkou a je dostupná jenom v Správce prostředkům modelu. K dokončení této úlohy musíte mít na virtuálních počítačích Azure nasazenou skupinu dostupnosti SQL Server v modelu Správce prostředků. Oba SQL Server virtuální počítače musí patřit do stejné skupiny dostupnosti. K automatickému vytvoření skupiny dostupnosti v Azure Resource Manager můžete použít [šablonu společnosti Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) . Tato šablona automaticky vytvoří skupinu dostupnosti, včetně interního nástroje pro vyrovnávání zatížení. Pokud budete chtít, můžete [ručně nakonfigurovat skupinu dostupnosti Always On](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Toto téma vyžaduje, aby byly vaše skupiny dostupnosti již nakonfigurovány.  

Mezi související témata patří:

* [Konfigurace Skupiny dostupnosti AlwaysOn na virtuálním počítači Azure (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Konfigurace připojení typu VNet-to-VNet pomocí Azure Resource Manager a PowerShellu](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>Ověřit verzi PowerShellu

Příklady v tomto článku jsou testovány pomocí nástroje Azure PowerShell 5.4.1 verze modulu.

Ověřte, že je modul prostředí PowerShell 5.4.1 nebo novější.

Přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="configure-the-windows-firewall"></a>Konfigurace brány Windows Firewall

Nakonfigurujte bránu Windows Firewall, aby povolovala přístup k SQL Server. Pravidla brány firewall umožňují připojení TCP k portům, které používá instance SQL Server, a sondu naslouchacího procesu. Podrobné pokyny najdete v tématu [Konfigurace brány Windows Firewall pro přístup k databázovému stroji](https://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Vytvořte příchozí pravidlo pro port SQL Server a pro port testu.

Pokud omezíte přístup ke skupině zabezpečení sítě Azure, zajistěte, aby pravidla povolení zahrnovala IP adresy back-endu SQL Server virtuálních počítačů, a v případě potřeby i IP adresy vyrovnávání zátěže pro naslouchací proces AG a základní IP adresu clusteru.

## <a name="determine-the-load-balancer-sku-required"></a>Určení požadované SKU nástroje pro vyrovnávání zatížení

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) je k dispozici ve 2 SKU: Základní & Standard. Doporučuje se standardní nástroj pro vyrovnávání zatížení. Pokud jsou virtuální počítače ve skupině dostupnosti, je povolený nástroj Load Balancer úrovně Basic. Load Balancer úrovně Standard vyžaduje, aby všechny IP adresy virtuálních počítačů používaly standardní IP adresy.

Aktuální [Šablona společnosti Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) pro skupinu dostupnosti používá základní nástroj pro vyrovnávání zatížení se základními IP adresami.

Příklady v tomto článku určují standardní nástroj pro vyrovnávání zatížení. V příkladech obsahuje `-sku Standard`skript.

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

Pokud chcete vytvořit nástroj pro vyrovnávání zatížení `-sku Standard` Basic, odeberte z řádku, který vytváří nástroj pro vyrovnávání zatížení. Příklad:

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Ukázkový skript: Vytvoření interního nástroje pro vyrovnávání zatížení pomocí PowerShellu

> [!NOTE]
> Pokud jste vytvořili skupinu dostupnosti se šablonou [Microsoftu](virtual-machines-windows-portal-sql-alwayson-availability-groups.md), interní nástroj pro vyrovnávání zatížení už je vytvořený.

Následující skript prostředí PowerShell vytvoří interní nástroj pro vyrovnávání zatížení, nakonfiguruje pravidla vyrovnávání zatížení a nastaví IP adresu nástroje pro vyrovnávání zatížení. Chcete-li spustit skript, otevřete Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell a vložte skript do podokna skript. Použijte `Connect-AzAccount` k přihlášení do PowerShellu. Pokud máte více předplatných Azure, `Select-AzSubscription` použijte k nastavení předplatného. 

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

## <a name="Add-IP"></a>Ukázkový skript: Přidání IP adresy do existujícího nástroje pro vyrovnávání zatížení pomocí PowerShellu
Chcete-li použít více než jednu skupinu dostupnosti, přidejte do nástroje pro vyrovnávání zatížení další IP adresu. Každá IP adresa vyžaduje vlastní pravidlo vyrovnávání zatížení, port testu a front-port.

Front-end port je port, který aplikace používá pro připojení k instanci SQL Server. IP adresy pro různé skupiny dostupnosti můžou používat stejný front-end port.

> [!NOTE]
> U SQL Server skupin dostupnosti vyžaduje Každá IP adresa konkrétní port testu paměti. Například pokud jedna IP adresa v nástroji pro vyrovnávání zatížení používá testový port 59999, nemůžou žádné další IP adresy v tomto nástroji pro vyrovnávání zatížení používat port testu 59999.

* Informace o limitech pro vyrovnávání zatížení najdete v tématu **privátní IP adresa front-endu na Vyrovnávání zatížení** v části [omezení sítě – Azure Resource Manager](../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Informace o omezeních skupiny dostupnosti najdete v tématu [omezení (skupiny dostupnosti)](https://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

Následující skript přidá do stávajícího nástroje pro vyrovnávání zatížení novou IP adresu. INTERNÍHO nástroje používá port naslouchacího procesu pro front-end port pro vyrovnávání zatížení. Tento port může být port, na kterém SQL Server naslouchá. Pro výchozí instance SQL Server Port je 1433. Pravidlo vyrovnávání zatížení pro skupinu dostupnosti vyžaduje plovoucí IP adresu (přímou návratovou hodnotu serveru), aby byl back-end port stejný jako front-end port. Aktualizujte proměnné pro vaše prostředí. 

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

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>Nastavte port naslouchacího procesu v SQL Server Management Studio

1. Spusťte SQL Server Management Studio a připojte se k primární replice.

1. Přejděte | na**naslouchací procesy**skupin dostupnosti **AlwaysOn vysoké dostupnosti** | . 

1. Nyní byste měli vidět název naslouchacího procesu, který jste vytvořili v Správce clusteru s podporou převzetí služeb při selhání. Klikněte pravým tlačítkem myši na název naslouchacího procesu a klikněte na **vlastnosti**.

1. Do pole **port** zadejte číslo portu pro naslouchací proces skupiny dostupnosti pomocí $EndpointPort, který jste použili dříve (výchozí nastavení je 1433), a pak klikněte na **OK**.

## <a name="test-the-connection-to-the-listener"></a>Otestování připojení k naslouchacímu procesu

Otestování připojení:

1. Protokol RDP na SQL Server, který je ve stejné virtuální síti, ale nevlastní repliku. Může to být druhý SQL Server v clusteru.

1. K otestování připojení použijte nástroj **Sqlcmd** . Například následující skript vytvoří připojení **Sqlcmd** k primární replice prostřednictvím naslouchacího procesu s ověřováním systému Windows:
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    Pokud naslouchací proces používá jiný port než výchozí port (1433), zadejte port v připojovacím řetězci. Například následující příkaz Sqlcmd se připojí k naslouchacího procesu na portu 1435: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

Připojení SQLCMD se automaticky připojí k jakékoli instanci SQL Server hostuje primární repliku. 

> [!NOTE]
> Ujistěte se, že port, který zadáte, je otevřený v bráně firewall obou serverů SQL. Oba servery vyžadují příchozí pravidlo pro port TCP, který používáte. Další informace najdete v tématu [Přidání nebo úprava pravidla brány firewall](https://technet.microsoft.com/library/cc753558.aspx) . 
> 
> 

## <a name="guidelines-and-limitations"></a>Pokyny a omezení
Všimněte si následujících pokynů pro naslouchací proces skupiny dostupnosti v Azure pomocí interního nástroje pro vyrovnávání zatížení:

* S interním nástrojem pro vyrovnávání zatížení získáte přístup pouze k naslouchacímu procesu ze stejné virtuální sítě.

* Pokud omezíte přístup ke skupině zabezpečení sítě Azure, zajistěte, aby pravidla povolení zahrnovala IP adresy back-endu SQL Server virtuálních počítačů, a v případě potřeby i IP adresy vyrovnávání zátěže pro naslouchací proces AG a základní IP adresu clusteru.

## <a name="for-more-information"></a>Další informace
Další informace najdete v tématu [Konfigurace skupiny dostupnosti Always On na virtuálním počítači Azure ručně](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

## <a name="powershell-cmdlets"></a>Rutiny prostředí PowerShell
K vytvoření interního nástroje pro vyrovnávání zatížení pro virtuální počítače Azure použijte následující rutiny PowerShellu.

* [New-AzLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) vytvoří nástroj pro vyrovnávání zatížení. 
* [New-AzLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) vytvoří konfiguraci front-endové IP adresy pro nástroj pro vyrovnávání zatížení. 
* [New-AzLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) vytvoří konfiguraci pravidla pro nástroj pro vyrovnávání zatížení. 
* [New-AzLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) vytvoří konfiguraci fondu adres back-endu pro nástroj pro vyrovnávání zatížení. 
* [New-AzLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) vytvoří konfiguraci testu paměti pro nástroj pro vyrovnávání zatížení.
* [Příkaz Remove-AzLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) odebere nástroj pro vyrovnávání zatížení ze skupiny prostředků Azure.
