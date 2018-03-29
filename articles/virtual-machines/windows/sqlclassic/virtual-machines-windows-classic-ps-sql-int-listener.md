---
title: Konfigurace naslouchací proces ILB pro skupiny dostupnosti Always On v Azure | Microsoft Docs
description: Tento kurz používá prostředky, které jsou vytvořené pomocí modelu nasazení classic a vytvoří Always On naslouchací proces skupiny dostupnosti v Azure, která používá interní nástroj.
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
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Konfigurace naslouchací proces ILB pro skupiny dostupnosti Always On v Azure
> [!div class="op_single_selector"]
> * [Interní naslouchací proces](../classic/ps-sql-int-listener.md)
> * [Externí naslouchací proces](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Přehled

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Azure Resource Manager a klasický](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek popisuje použití modelu nasazení classic. Doporučujeme vám, že většina nových nasazení používala model Resource Manager.

Ke konfiguraci naslouchacího procesu pro skupiny dostupnosti Always On v modelu Resource Manager, najdete v části [konfigurace pro vyrovnávání zatížení pro skupinu dostupnosti Always On v Azure](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

Skupině dostupnosti může obsahovat repliky pouze místní nebo jenom Azure, které jsou, nebo které span místní a Azure pro hybridní konfigurace. Azure repliky mohou být uloženy ve stejné oblasti nebo v několika oblastech, které používají více virtuálních sítí. Postupy v tomto článku předpokládá, že máte již [nakonfigurovat skupinu dostupnosti](../classic/portal-sql-alwayson-availability-groups.md) , ale zatím nenakonfigurovali naslouchací proces.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Pokyny a omezení pro interní naslouchací procesy
Použití k interní pro vyrovnávání zatížení (ILB) s naslouchací proces skupiny dostupnosti v Azure se vztahují následující pokyny:

* Naslouchací proces skupiny dostupnosti je podporován v systému Windows Server 2008 R2, Windows Server 2012 a Windows Server 2012 R2.
* Pouze jeden naslouchací proces skupiny dostupnosti interní je podporována pro jednotlivých cloudových služeb, protože je nakonfigurován naslouchací proces pro ILB, a je pouze jeden ILB u každé cloudové služby. Nicméně je možné vytvořit více externí naslouchací procesy. Další informace najdete v tématu [konfigurace o externí naslouchací proces pro skupiny dostupnosti Always On v Azure](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Určení usnadnění naslouchacího procesu
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Tento článek se zaměřuje na tvorbu naslouchací proces, který používá ILB. Pokud potřebujete naslouchací proces veřejných nebo externí, přečtěte si verzi tohoto článku, který popisuje nastavení, až [externí naslouchací proces](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Vytvoření koncové body s vyrovnáváním zatížení virtuálních počítačů s přímý server návratový
Nejprve vytvoříte ILB později spuštěním skriptu v této části.

Vytvořte koncový bod Vyrovnávání zatížení sítě pro každý virtuální počítač, který je hostitelem Azure repliky. Pokud máte repliky v několika oblastech, každá replika pro danou oblast musí být v rámci stejné cloudové služby ve stejné virtuální síti Azure. Vytváření replik skupin, které jsou rozmístěny v několika oblastmi Azure dostupnosti vyžaduje konfiguraci více virtuálních sítí. Další informace o konfiguraci křížové připojení k virtuální síti, najdete v části [konfigurace virtuální sítě pro připojení k virtuální síti](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Na portálu Azure přejděte na každý virtuální počítač, který je hostitelem repliky zobrazíte podrobnosti.

2. Klikněte **koncové body** kartu pro každý virtuální počítač.

3. Ověřte, zda **název** a **veřejný Port** naslouchacího procesu koncový bod, který chcete použít nejsou již používán. V tomto příkladu v této části, je název *MyEndpoint*, a port, který je *1433*.

4. Na místního klienta, stáhněte a nainstalujte nejnovější [modulu PowerShell](https://azure.microsoft.com/downloads/).

5. Spusťte prostředí Azure PowerShell.  
    Novou relaci prostředí PowerShell otevře s Azure pro správu moduly načíst.

6. Spusťte `Get-AzurePublishSettingsFile`. Tato rutina vás přesměruje do prohlížeče a stáhněte soubor nastavení publikování do místního adresáře. Pro vaše předplatné Azure, může být vyzvání k zadání pověření přihlášení.

7. Spusťte následující `Import-AzurePublishSettingsFile` příkazu s cestou soubor nastavení publikování, který jste stáhli:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    Po importu se soubor nastavení publikování, můžete spravovat vaše předplatné Azure v relaci prostředí PowerShell.

8. Pro *ILB*, přiřadit statickou IP adresu. Zkontrolujte aktuální konfiguraci virtuální sítě tak, že spustíte následující příkaz:

        (Get-AzureVNetConfig).XMLConfiguration
9. Poznámka: *podsíť* název pro podsíť, která obsahuje virtuální počítače tohoto hostitele repliky. Tento název se používá v parametru $SubnetName ve skriptu.

10. Poznámka: *VirtualNetworkSite* název a počáteční *AddressPrefix* pro podsíť, která obsahuje virtuální počítače, které hostitele repliky. Hledat dostupnou adresu IP pomocí předání obě hodnoty `Test-AzureStaticVNetIP` příkazů a nástrojem prozkoumání *AvailableAddresses*. Například pokud je název virtuální sítě *MyVNet* a má rozsah adres podsítě, která se spouští v *172.16.0.128*, následující příkaz by seznam dostupných adres:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Vyberte jednu z dostupných adres a použít ho v parametru $ILBStaticIP skript v dalším kroku.

12. Zkopírujte následující skript PowerShell do textového editoru a nastavte hodnoty proměnné tak, aby odpovídaly vašemu prostředí. Výchozí hodnoty jsou uvedeny některé parametry.  

    Existující nasazení, které používají skupiny vztahů nelze přidat ILB. Další informace o požadavcích na ILB najdete v tématu [přehled nástroje pro vyrovnávání zatížení pro vnitřní](../../../load-balancer/load-balancer-internal-overview.md).

    Navíc pokud vaší skupiny dostupnosti zahrnuje oblasti Azure, musíte spustit skript jednou v každé datové centrum pro cloudové služby a uzly, které jsou umístěny ve stejné datové centrum.

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

13. Po nastavení proměnné, zkopírujte do relace prostředí PowerShell ji spustit skript z textového editoru. Pokud stále zobrazuje řádku **>>**, stisknutím klávesy Enter zajistěte, aby je skript spuštěn.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Ověřte, zda KB2854082 nainstalována v případě potřeby
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Otevřít porty brány firewall v uzlech skupiny dostupnosti
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Vytvoření naslouchacího procesu skupiny dostupnosti

Vytvořte naslouchací proces skupiny dostupnosti ve dvou krocích. Nejprve vytvořte prostředek clusteru bodu přístupu klienta a nakonfigurovat závislosti. Druhý nakonfigurujte prostředky clusteru v prostředí PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Vytvořit klientský přístupový bod a nakonfigurovat závislosti clusteru
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurace prostředků clusteru v prostředí PowerShell
1. Pro ILB musíte použít IP adresu ILB, který jste vytvořili. Chcete-li získat tuto IP adresu v prostředí PowerShell, použijte následující skript:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. Na jednom z virtuálních počítačů zkopírujte skript prostředí PowerShell pro váš operační systém do textového editoru a nastavte proměnné na hodnoty, které jste si předtím poznamenali.

    Pro Windows Server 2012 nebo novější použijte tento skript:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    Pro Windows Server 2008 R2 použijte následující skript:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. Po nastavení proměnné, otevřete okno prostředí Windows PowerShell se zvýšenými oprávněními, vložte do relace prostředí PowerShell ji spustit skript z textového editoru. Pokud stále zobrazuje řádku **>>**, stiskněte klávesu Enter znovu, abyste měli jistotu, že je skript spuštěn.

4. Opakujte předchozí kroky pro každý virtuální počítač.  
    Tento skript nakonfiguruje prostředek IP adresy s IP adresou cloudové služby a nastaví dalších parametrů, jako je port testu. Pokud prostředek IP adresy je uvést do režimu online, může reagovat na dotazování na port testu z koncového bodu Vyrovnávání zatížení sítě, který jste vytvořili dříve.

## <a name="bring-the-listener-online"></a>Přepněte naslouchací proces online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Položky následnou akci
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>Testování naslouchacího procesu skupiny dostupnosti (v rámci stejné virtuální síti)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Další postup
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
