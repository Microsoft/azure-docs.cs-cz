---
title: Konfigurace naslouchací proces ILB pro skupiny dostupnosti (Classic)
description: Tento kurz používá prostředky vytvořené pomocí klasického modelu nasazení a vytvoří naslouchací proces skupiny dostupnosti always on pro virtuální počítač SQL Server v Azure, který používá interní nástroje pro vyrovnávání zatížení.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77j
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: f26c5a6c6fc2774d19beaa021015357a1991f0ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978172"
---
# <a name="configure-an-ilb-listener-for-availability-groups-on-azure-sql-server-vms"></a>Konfigurace naslouchací proces ILB pro skupiny dostupnosti na virtuálních počítačích Azure SQL Server
> [!div class="op_single_selector"]
> * [Interní naslouchací proces](../classic/ps-sql-int-listener.md)
> * [Externí naslouchací proces](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Přehled

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Azure Resource Manager a klasické](../../../azure-resource-manager/management/deployment-models.md). Tento článek popisuje použití modelu klasické nasazení. Doporučujeme, aby většina nových nasazení používala model Správce prostředků.

Pokud chcete nakonfigurovat naslouchací proces pro skupinu dostupnosti always on v modelu Správce prostředků, přečtěte si informace [o konfiguraci nástroje pro vyrovnávání zatížení pro skupinu dostupnosti always on v Azure](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

Vaše skupina dostupnosti může obsahovat repliky, které jsou pouze místní nebo pouze Azure, nebo které se rozprostírají jak místně, tak v Azure pro hybridní konfigurace. Repliky Azure se můžou nasytit ve stejné oblasti nebo ve více oblastech, které používají více virtuálních sítí. Postupy v tomto článku předpokládají, že jste již [nakonfigurovali skupinu dostupnosti,](../classic/portal-sql-alwayson-availability-groups.md) ale ještě jste nenakonfigurovali naslouchací proces.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Pokyny a omezení pro interní posluchače
Použití interního systému vyrovnávání zatížení (ILB) s naslouchacím procesem skupiny dostupnosti v Azure podléhá následujícím pokynům:

* Naslouchací proces skupiny dostupnosti je podporován v systémech Windows Server 2008 R2, Windows Server 2012 a Windows Server 2012 R2.
* Pro každou cloudovou službu je podporován pouze jeden naslouchací proces skupiny interní dostupnosti, protože naslouchací proces je nakonfigurován pro Službu ILB a pro každou cloudovou službu existuje pouze jedna služba ILB. Je však možné vytvořit více externích naslouchacích procesů. Další informace najdete [v tématu Konfigurace externího naslouchací proces pro vždy zapnuté skupiny dostupnosti v Azure](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Určení přístupnosti naslouchací proces
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Tento článek se zaměřuje na vytvoření naslouchací proces, který používá ILB. Pokud potřebujete veřejný nebo externí naslouchací proces, naleznete v této verzi tohoto článku, která popisuje nastavení [externího naslouchací proces](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Vytvoření koncových bodů virtuálních montovek s vyrovnáváním zatížení s přímým návratem serveru
Nejprve vytvoříte ILB spuštěním skriptu dále v této části.

Vytvořte koncový bod s vyrovnáváním zatížení pro každý virtuální počítač, který hostuje repliku Azure. Pokud máte repliky ve více oblastech, každá replika pro tuto oblast musí být ve stejné cloudové službě ve stejné virtuální síti Azure. Vytvoření replik skupiny dostupnosti, které pokrývají více oblastí Azure, vyžaduje konfiguraci více virtuálních sítí. Další informace o konfiguraci připojení mezi virtuálními sítěmi najdete v [tématu Konfigurace připojení virtuální sítě k virtuální síti](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Na webu Azure Portal přejděte na každý virtuální počítač, který hostuje repliku a zobrazí podrobnosti.

2. Klikněte na kartu **Koncové body** pro každý virtuální virtuální ms.

3. Ověřte, zda **název** a **veřejný port** koncového bodu naslouchací procesu, který chcete použít, se již nepoužívají. V příkladu v této části je název *MyEndpoint*a port je *1433*.

4. V místním klientovi stáhněte a nainstalujte nejnovější [modul Prostředí PowerShell](https://azure.microsoft.com/downloads/).

5. Spusťte Azure PowerShell.  
    Otevře se nová relace PowerShellu s načtenými moduly pro správu Azure.

6. Spusťte `Get-AzurePublishSettingsFile`. Tato rutina vás přesměruje do prohlížeče a stáhne soubor nastavení publikování do místního adresáře. Můžete být vyzváni k zadání přihlašovacích přihlašovacích údajů pro vaše předplatné Azure.

7. Spusťte `Import-AzurePublishSettingsFile` následující příkaz s cestou staženého souboru nastavení publikování:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    Po importu souboru nastavení publikování můžete spravovat předplatné Azure v relaci PowerShellu.

8. Pro *ILB*přiřaďte statickou ADRESU IP. Zkontrolujte aktuální konfiguraci virtuální sítě spuštěním následujícího příkazu:

        (Get-AzureVNetConfig).XMLConfiguration
9. Všimněte si názvu *podsítě* pro podsíť, která obsahuje virtuální počítače, které jsou hostiteli replik. Tento název se používá v parametru $SubnetName ve skriptu.

10. Všimněte si názvu *VirtualNetworkSite* a *počátečního adresy Prefix* pro podsíť, která obsahuje virtuální počítače, které jsou hostiteli replik. Vyhledejte dostupnou adresu IP předáním `Test-AzureStaticVNetIP` obou hodnot příkazu a kontrolou *služby AvailableAddresses*. Pokud má například virtuální síť název *MyVNet* a má rozsah adres podsítě začínající na *172.16.0.128*, zobrazí se následující příkaz v seznamu dostupných adres:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Vyberte jednu z dostupných adres a použijte ji v $ILBStaticIP parametr skriptu v dalším kroku.

12. Zkopírujte následující skript prostředí PowerShell do textového editoru a nastavte hodnoty proměnných tak, aby vyhovovaly vašemu prostředí. Pro některé parametry byly poskytnuty výchozí hodnoty.  

    Existující nasazení, která používají skupiny spřažení, nemohou přidat ilb. Další informace o požadavcích ilb naleznete [v tématu Interní přehled nástroje pro vyrovnávání zatížení](../../../load-balancer/load-balancer-internal-overview.md).

    Také pokud vaše skupina dostupnosti zahrnuje oblasti Azure, musíte spustit skript jednou v každém datovém centru pro cloudovou službu a uzly, které jsou umístěny v tomto datovém centru.

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

13. Po nastavení proměnných zkopírujte skript z textového editoru do relace powershellu a spusťte jej. Pokud se výzva **>>** stále zobrazuje , zkontrolujte, zda se skript spustí, znovu stiskněte klávesu Enter .

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>V případě potřeby ověřte, zda je nainstalována kb2854082
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Otevření portů brány firewall v uzlech skupin y dostupnosti
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Vytvoření naslouchací proces skupiny dostupnosti

Vytvořte naslouchací proces skupiny dostupnosti ve dvou krocích. Nejprve vytvořte prostředek clusteru klientského přístupového bodu a nakonfigurujte závislosti. Za druhé nakonfigurujte prostředky clusteru v prostředí PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Vytvoření klientského přístupového bodu a konfigurace závislostí clusteru
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurace prostředků clusteru v Prostředí PowerShell
1. Pro ILB je nutné použít IP adresu ILB, která byla vytvořena dříve. Chcete-li získat tuto adresu IP v prostředí PowerShell, použijte následující skript:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. Na jednom z virtuálních počítačů zkopírujte skript Prostředí PowerShell pro operační systém do textového editoru a nastavte proměnné na hodnoty, které jste si poznamenali dříve.

    Pro Windows Server 2012 nebo novější použijte následující skript:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    V systému Windows Server 2008 R2 použijte následující skript:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. Po nastavení proměnných otevřete okno prostředí Windows PowerShell se zvýšenými oprávněními a vložte skript z textového editoru do relace prostředí PowerShell a spusťte jej. Pokud se výzva **>>** stále zobrazuje , stiskněte znovu Enter a ujistěte se, že se skript spustí.

4. Opakujte předchozí kroky pro každý virtuální virtuální ms.  
    Tento skript konfiguruje prostředek IP adresy s IP adresou cloudové služby a nastaví další parametry, například port sondy. Když je prostředek IP adresy přepnuto do režimu online, může reagovat na dotazování na portu sondy z koncového bodu s vyrovnáváním zatížení, který jste vytvořili dříve.

## <a name="bring-the-listener-online"></a>Převést posluchače do režimu online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Následné položky
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>Testování naslouchací proces skupiny dostupnosti (v rámci stejné virtuální sítě)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Další kroky
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
