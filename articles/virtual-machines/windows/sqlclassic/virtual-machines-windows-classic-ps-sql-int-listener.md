---
title: Konfigurace naslouchacího procesu ILB pro skupiny dostupnosti Always On v Azure | Dokumentace Microsoftu
description: Tento kurz používá prostředky vytvořené pomocí modelu nasazení classic a vytvoří stálé naslouchacího procesu skupiny dostupnosti v Azure, která používá interního nástroje load balancer.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: 0466265ad5a24e8ea6dc5079e2b4006d74e7dde0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452528"
---
# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Konfigurace naslouchacího procesu ILB pro skupiny dostupnosti Always On v Azure
> [!div class="op_single_selector"]
> * [Interního naslouchacího procesu](../classic/ps-sql-int-listener.md)
> * [Externí naslouchací proces](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Přehled

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Azure Resource Manageru a Klasický model](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se týká použití modelu nasazení classic. Doporučujeme, aby většina nových nasazení používala model Resource Manager.

Konfigurace naslouchacího procesu skupiny dostupnosti Always On v modelu Resource Manager, najdete v článku [nakonfigurovat nástroj pro vyrovnávání zatížení pro skupinu dostupnosti AlwaysOn v Azure](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

Vaše skupina dostupnosti může obsahovat replik, které jsou jen místně nebo v Azure pouze nebo, které trvají lokálně i Azure pro hybridní konfigurace. Azure repliky mohou být uloženy ve stejné oblasti nebo v několika oblastech, které používají více virtuálních sítí. V postupech v tomto článku se předpokládá, že jste již [nakonfigurovat skupinu dostupnosti](../classic/portal-sql-alwayson-availability-groups.md) , ale ještě neprovedli konfiguraci naslouchacího procesu.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Pokyny a omezení pro interních naslouchacích procesů
Používání interní nástroj pro vyrovnávání zatížení (ILB) pomocí naslouchací proces skupiny dostupnosti v Azure se řídí podle následujících pokynů:

* Naslouchací proces skupiny dostupnosti je podporován v systému Windows Server 2008 R2, Windows Server 2012 a Windows Server 2012 R2.
* Pouze jeden naslouchací proces skupiny dostupnosti interní je pro každou cloudovou službu, nepodporuje, protože je nakonfigurován naslouchací proces pro ILB a existuje pouze jeden ILB pro každou cloudovou službu. Nicméně je možné vytvořit několik externích naslouchacích procesů. Další informace najdete v tématu [konfigurace externího naslouchacího procesu pro skupiny dostupnosti Always On v Azure](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Zjistit dostupnost naslouchací proces
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Tento článek se zaměřuje na tvorbu naslouchací proces, který používá ILB. Pokud potřebujete naslouchací proces veřejné nebo externí, přečtěte si verzi tohoto článku, který popisuje nastavení až [externího naslouchacího procesu](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Vytvořit koncové body s vyrovnáváním zatížení virtuálního počítače se přímá odpověď ze serveru vrácené
Spuštěním skriptu později v této části nejprve vytvoříte ILB.

Vytvořte koncový bod s vyrovnáváním zatížení pro každý virtuální počítač, který je hostitelem repliky služby Azure. Pokud máte repliky v několika oblastech, každá replika pro tuto oblast musí být ve stejné cloudové služby v rámci stejné virtuální síti Azure. Vytváření dostupnosti replik skupin, které jsou rozmístěny v několika oblastech Azure vyžaduje konfiguraci několika virtuálními sítěmi. Další informace o konfiguraci pro různé připojení k virtuální síti, najdete v části [konfigurace virtuální sítě pro připojení k virtuální síti](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Na webu Azure Portal přejděte na každý virtuální počítač, který je hostitelem repliky zobrazíte podrobnosti.

2. Klikněte na tlačítko **koncové body** kartu pro každý virtuální počítač.

3. Ověřte, že **název** a **veřejný Port** naslouchací proces koncového bodu, který chcete použít nejsou již používá. V tomto příkladu v této části, je název *MyEndpoint*, a port, který je *1433*.

4. Na váš místní klient, stáhněte a nainstalujte nejnovější [modulu PowerShell](https://azure.microsoft.com/downloads/).

5. Spuštění prostředí Azure PowerShell.  
    Otevře se novou relaci Powershellu s Azure pro správu modulů.

6. Spusťte `Get-AzurePublishSettingsFile`. Tato rutina vás přesměruje do prohlížeče a stáhněte si soubor nastavení publikování do místního adresáře. Můžete být vyzváni pro vaše přihlašovací údaje pro vaše předplatné Azure.

7. Spusťte následující příkaz `Import-AzurePublishSettingsFile` příkazu s cestou, který jste stáhli soubor nastavení publikování:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    Po importu souboru nastavení publikování můžete spravovat vaše předplatné Azure v relaci Powershellu.

8. Pro *ILB*, přiřadit statickou IP adresu. Zkontrolujte aktuální konfigurace virtuální sítě spuštěním následujícího příkazu:

        (Get-AzureVNetConfig).XMLConfiguration
9. Poznámka: *podsítě* název pro podsíť, která obsahuje virtuální počítače, které hostují replik. Tento název se používá v parametru $SubnetName ve skriptu.

10. Poznámka: *VirtualNetworkSite* název a počáteční *AddressPrefix* pro podsíť, která obsahuje virtuální počítače, které jsou hostiteli replik. Najít dostupnou IP adresu předáním hodnoty do `Test-AzureStaticVNetIP` příkazu a tím zkontrolujete *AvailableAddresses*. Například, pokud je název virtuální sítě *MyVNet* a má rozsah adres podsítě, která začíná na *172.16.0.128*, následující příkaz by zobrazila seznam dostupných adres:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Vyberte jednu z dostupných adres a použít v parametru $ILBStaticIP skriptu v dalším kroku.

12. Zkopírujte následující skript prostředí PowerShell do textového editoru a nastavte hodnoty proměnné podle vašich potřeb. Výchozí hodnoty pro některé parametry byly zadány.  

    Stávající nasazení, které používají skupiny vztahů nelze přidat ILB. Další informace o požadavcích ILB, naleznete v tématu [interní služby load balancer – přehled](../../../load-balancer/load-balancer-internal-overview.md).

    Navíc pokud vaše skupina dostupnosti zahrnuje oblasti Azure, musíte spustit skript jednou v každé datové centrum pro cloudovou službu a uzly, které se nacházejí ve stejné datové centrum.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the virtual network
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load-balanced endpoint for each node in $AGNodes by using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

13. Po nastavení proměnných zkopírujte skript do relace prostředí PowerShell ji spustit z textového editoru. Pokud se stále zobrazí výzvu **>>**, stisknutím klávesy Enter Ujistěte se, že je skript spuštěn.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Ověřte, že KB2854082 je nainstalována v případě potřeby
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Otevřít porty brány firewall v uzlech skupiny dostupnosti
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Vytvoření naslouchacího procesu skupiny dostupnosti

Vytvořte naslouchací proces skupiny dostupnosti ve dvou krocích. Nejprve vytvořte prostředek clusteru bodu přístupu klienta a nakonfigurovat závislosti. Za druhé nakonfigurujte prostředky clusteru v prostředí PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Vytvořit klientský přístupový bod a nakonfigurovat závislosti clusteru
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurace prostředků clusteru v prostředí PowerShell
1. Pro ILB musíte použít IP adresu ILB, který jste vytvořili dříve. Pokud chcete získat tuto IP adresu v prostředí PowerShell, použijte tento skript:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. Na jednom z virtuálních počítačů zkopírujte skript prostředí PowerShell pro váš operační systém do textového editoru a pak nastavte proměnné na hodnoty, které jste si předtím poznamenali.

    Systém Windows Server 2012 nebo novější použijte tento skript:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    Windows Server 2008 R2 použijte tento skript:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. Po nastavení proměnných, otevřete okno Windows Powershellu se zvýšenými oprávněními, vložte skript z textového editoru do relace prostředí PowerShell k jeho spuštění. Pokud se stále zobrazí výzvu **>>**, stisknutím klávesy Enter znovu a ujistit se, že je skript spuštěn.

4. Zopakujte předchozí kroky pro každý virtuální počítač.  
    Tento skript nakonfiguruje prostředek IP adresy se IP adresa cloudové služby a nastaví další parametry, jako je například portu sondy. Pokud prostředek IP adresy se přepne do online režimu, může reagovat na dotazování na portu sondy z koncový bod s vyrovnáváním zatížení, který jste vytvořili dříve.

## <a name="bring-the-listener-online"></a>Přeneste naslouchacího procesu online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Následné akce položek
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>Testování naslouchacího procesu skupiny dostupnosti (v rámci stejné virtuální síti)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Další postup
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
