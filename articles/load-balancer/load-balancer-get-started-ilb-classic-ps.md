---
title: Vytvoření interního Vyrovnávání zatížení – PowerShell classic
titlesuffix: Azure Load Balancer
description: Zjistěte, jak vytvořit interní nástroj pro vyrovnávání zatížení pomocí prostředí PowerShell v modelu nasazení Classic
services: load-balancer
documentationcenter: na
author: genlin
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms:custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: ef6aac0d97c38798f826304475779ea8059875c7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097977"
---
# <a name="get-started-creating-an-internal-load-balancer-classic-using-powershell"></a>Začínáme vytvářet interní nástroj pro vyrovnávání zatížení (Classic) pomocí prostředí PowerShell

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Cloudové služby](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../azure-resource-manager/resource-manager-deployment-model.md).  Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Zjistěte, jak [provést tento postup pomocí modelu Resource Manageru](load-balancer-get-started-ilb-arm-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-internal-load-balancer-set-for-virtual-machines"></a>Vytvoření sady interního nástroje pro vyrovnávání zatížení pro virtuální počítače

Pokud chcete vytvořit sadu interního nástroje pro vyrovnávání zatížení a servery, které do ní budou posílat provoz, musíte provést následující:

1. Vytvořte instanci interního vyrovnávání zatížení, která bude koncovým bodem příchozího provozu, u kterého se bude vyrovnávat zatížení napříč servery sady s vyrovnáváním zatížení.
2. Přidejte koncové body odpovídající virtuálním počítačům, které budou přijímat příchozí provoz.
3. Nakonfigurujte servery, které budou posílat provoz k vyrovnání zatížení, aby posílaly provoz na virtuální IP adresu instance interního vyrovnávání zatížení.

### <a name="step-1-create-an-internal-load-balancing-instance"></a>Krok 1: Vytvoření instance interního vyrovnávání zatížení

Pro existující cloudovou službu nebo cloudovou službu nasazenou v regionální virtuální síti můžete vytvořit instanci interního vyrovnávání zatížení pomocí následujících příkazů prostředí Windows PowerShell:

```powershell
$svc="<Cloud Service Name>"
$ilb="<Name of your ILB instance>"
$subnet="<Name of the subnet within your virtual network>"
$IP="<The IPv4 address to use on the subnet-optional>"

Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP
```

Všimněte si, že toto použití rutiny prostředí Windows PowerShell [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx) používá sadu parametrů DefaultProbe. Více informací o dalších sadách parametrů najdete v dokumentaci k rutině [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx).

### <a name="step-2-add-endpoints-to-the-internal-load-balancing-instance"></a>Krok 2: Přidání koncových bodů do instance interního vyrovnávání zatížení

Zde naleznete příklad:

```powershell
$svc="mytestcloud"
$vmname="DB1"
$epname="TCP-1433-1433"
$lbsetname="lbset"
$prot="tcp"
$locport=1433
$pubport=1433
$ilb="ilbset"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
```

### <a name="step-3-configure-your-servers-to-send-their-traffic-to-the-new-internal-load-balancing-endpoint"></a>Krok 3: Konfigurace serverů pro posílání provozu do nového koncového bodu interního vyrovnávání zatížení

Servery, u jejichž provozu se bude vyrovnávat zatížení, je nutné nakonfigurovat pro používání nové IP adresy (virtuální IP adresy) instance interního vyrovnávání zatížení. Jedná se o adresu, na které tato instance interního vyrovnávání zatížení naslouchá. Ve většině případů je třeba pouze přidat nebo upravit záznam DNS pro virtuální IP adresu instance interního vyrovnávání zatížení.

Pokud jste zadali IP adresu během vytváření instance interního vyrovnávání zatížení, pak již virtuální IP adresu máte. Jinak můžete virtuální IP adresu zjistit pomocí následujících příkazů:

```powershell
$svc="<Cloud Service Name>"
Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer
```

Pokud chcete použít tyto příkazy, vyplňte požadované hodnoty a odeberte znaky < a >. Zde naleznete příklad:

```powershell
$svc="mytestcloud"
Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer
```

Ze zobrazení příkazu Get-AzureInternalLoadBalancer si poznamenejte IP adresu a proveďte potřebné změny svých serverů nebo záznamů DNS, abyste se ujistili, že se provoz bude posílat na virtuální IP adresu.

> [!NOTE]
> Platforma Microsoft Azure používá pro řadu scénářů správy statickou, veřejně směrovatelnou IPv4 adresu. Jedná se o IP adresu 168.63.129.16. Tuto IP adresu by neměla blokovat žádná brána firewall, protože by to mohlo způsobit neočekávané chování.
> Vzhledem k internímu vyrovnávání zatížení Azure slouží tato IP adresa monitorovacím testům z nástroje pro vyrovnávání zatížení k určování stavu virtuálních počítačů v sadě s vyrovnáváním zatížení. Pokud se k omezení provozu na virtuální počítače Azure v sadě s interním vyrovnáváním zatížení používá skupina zabezpečení sítě nebo pokud je použita na podsíti virtuální sítě, ujistěte se, že je přidáno pravidlo zabezpečení sítě umožňující provoz z adresy 168.63.129.16.

## <a name="example-of-internal-load-balancing"></a>Příklad interního vyrovnávání zatížení

Jednotlivé kroky názorného postupu vytvoření sady s vyrovnáváním zatížení pro dvě ukázkové konfigurace najdete v následujících oddílech.

### <a name="an-internet-facing-multi-tier-application"></a>Internetová, vícevrstvá aplikace

Chcete poskytnout databázovou službu s vyrovnáváním zatížení sadě internetových webových serverů. Hostitelem obou sad serverů je jedna cloudová služba Azure. Provoz webového serveru posílaný na port TCP 1433 se musí distribuovat mezi dva virtuální počítače na databázové vrstvě. Taková konfigurace je znázorněna na obrázku 1.

![Interní sada s vyrovnáváním zatížení pro databázovou vrstvu](./media/load-balancer-internal-getstarted/IC736321.png)

Konfigurace se skládá z následujících částí:

* Existující cloudová služba hostující virtuální počítače má název mytestcloud.
* Dva existující databázové servery mají názvy DB1 a DB2.
* Webové servery na webové vrstvě se k databázovým serverům na databázové vrstvě připojují pomocí privátní IP adresy. Další možností je použít pro virtuální síť vlastní DNS a ručně zaregistrovat záznam A pro sadu interního nástroje pro vyrovnávání zatížení.

Následující příkazy nakonfigurují novou instanci interního vyrovnávání zatížení s názvem **ILBset** a přidají koncové body do virtuálních počítačů odpovídajících těmto dvěma databázovým serverům:

```powershell
$svc="mytestcloud"
$ilb="ilbset"
Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb
$prot="tcp"
$locport=1433
$pubport=1433
$epname="TCP-1433-1433"
$lbsetname="lbset"
$vmname="DB1"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

$epname="TCP-1433-1433-2"
$vmname="DB2"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
```

## <a name="remove-an-internal-load-balancing-configuration"></a>Odebrání konfigurace interního vyrovnávání zatížení

K odebrání virtuálního počítače jako koncového bodu z instance interního nástroje pro vyrovnávání zatížení použijte následující příkazy:

```powershell
$svc="<Cloud service name>"
$vmname="<Name of the VM>"
$epname="<Name of the endpoint>"
Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM
```

Pokud chcete použít tyto příkazy, vyplňte požadované hodnoty a odeberte znaky < a >.

Zde naleznete příklad:

```powershell
$svc="mytestcloud"
$vmname="DB1"
$epname="TCP-1433-1433"
Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM
```

K odebrání instance interního nástroje pro vyrovnávání zatížení z cloudové služby použijte následující příkazy:

```powershell
$svc="<Cloud service name>"
Remove-AzureInternalLoadBalancer -ServiceName $svc
```

Pokud chcete použít tyto příkazy, vyplňte požadovanou hodnotu a odeberte znaky < a >.

Zde naleznete příklad:

```powershell
$svc="mytestcloud"
Remove-AzureInternalLoadBalancer -ServiceName $svc
```

## <a name="additional-information-about-internal-load-balancer-cmdlets"></a>Další informace o rutinách interního nástroje pro vyrovnávání zatížení

Pokud chcete získat další informace o rutinách interního vyrovnávání zatížení, v příkazovém řádku prostředí Windows PowerShell spusťte následující příkazy:

```powershell
Get-Help New-AzureInternalLoadBalancerConfig -full
Get-Help Add-AzureInternalLoadBalancer -full
Get-Help Get-AzureInternalLoadbalancer -full
Get-Help Remove-AzureInternalLoadBalancer -full
```

## <a name="next-steps"></a>Další postup

[Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení pomocí spřažení se zdrojovou IP adresou](load-balancer-distribution-mode.md)

[Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)

