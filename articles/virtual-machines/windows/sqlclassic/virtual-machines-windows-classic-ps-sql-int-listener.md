---
title: Konfigurace naslouchacího procesu interního nástroje pro skupiny dostupnosti (Classic)
description: V tomto kurzu se používají prostředky vytvořené pomocí modelu nasazení Classic a vytvoří se naslouchací proces skupiny dostupnosti Always On v pro SQL Server virtuální počítač v Azure, který používá interní nástroj pro vyrovnávání zatížení.
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
ms.openlocfilehash: 29aaedeafb3995cc09e221d2e049dd538808904a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032661"
---
# <a name="configure-an-ilb-listener-for-availability-groups-on-azure-sql-server-vms"></a>Konfigurace naslouchacího procesu interního nástroje pro skupiny dostupnosti na virtuálních počítačích Azure SQL Server
> [!div class="op_single_selector"]
> * [Interní naslouchací proces](../classic/ps-sql-int-listener.md)
> * [Externí naslouchací proces](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Přehled

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Azure Resource Manager a Classic](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek popisuje použití klasického modelu nasazení. Doporučujeme, aby většina nových nasazení používala model Správce prostředků.

Pokud chcete nakonfigurovat naslouchací proces pro skupinu dostupnosti Always On v modelu Správce prostředků, přečtěte si téma [Konfigurace nástroje pro vyrovnávání zatížení pro skupinu dostupnosti Always On v Azure](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

Vaše skupina dostupnosti může obsahovat jenom repliky, které jsou jenom místní, nebo jenom Azure, nebo které se nacházejí v místním prostředí i v Azure pro hybridní konfigurace. Repliky Azure se můžou nacházet ve stejné oblasti nebo v několika oblastech, které používají víc virtuálních sítí. Postupy v tomto článku předpokládají, že jste již [nakonfigurovali skupinu dostupnosti](../classic/portal-sql-alwayson-availability-groups.md) , ale dosud jste nenakonfigurovali naslouchací proces.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Pokyny a omezení pro interní naslouchací procesy
Používání interního nástroje pro vyrovnávání zatížení (interního nástroje) s naslouchacím členem skupiny dostupnosti v Azure se řídí následujícími pokyny:

* Naslouchací proces skupiny dostupnosti je podporovaný v systémech Windows Server 2008 R2, Windows Server 2012 a Windows Server 2012 R2.
* Pro každou cloudovou službu je podporovaná jenom jedna interní naslouchací proces skupiny dostupnosti, protože naslouchací proces je nakonfigurovaný na interního nástroje a pro každou cloudovou službu existuje jenom jeden interního nástroje. Je ale možné vytvořit několik externích posluchačů. Další informace najdete v tématu [Konfigurace externího naslouchacího procesu pro skupiny dostupnosti Always On v Azure](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Určení dostupnosti naslouchacího procesu
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Tento článek se zaměřuje na vytvoření naslouchacího procesu, který používá interního nástroje. Pokud potřebujete veřejný nebo externí naslouchací proces, přečtěte si verzi tohoto článku, která se zabývá nastavením [externího naslouchacího procesu](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Vytvoření koncových bodů virtuálních počítačů s vyrovnáváním zatížení s přímým vrácením serveru
Nejprve vytvořte interního nástroje spuštěním skriptu dále v této části.

Vytvořte koncový bod s vyrovnáváním zatížení pro každý virtuální počítač, který je hostitelem repliky Azure. Pokud máte repliky ve více oblastech, každá replika pro tuto oblast musí být ve stejné cloudové službě ve stejné virtuální síti Azure. Vytváření replik skupin dostupnosti, které jsou rozloženy do více oblastí Azure, vyžaduje konfiguraci více virtuálních sítí. Další informace o konfiguraci připojení mezi virtuálními sítěmi najdete v tématu [Konfigurace virtuální sítě na připojení k virtuální síti](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. V Azure Portal přejdete na každý virtuální počítač, který je hostitelem repliky, a zobrazíte podrobnosti.

2. Klikněte na kartu **koncové body** pro každý virtuální počítač.

3. Ověřte, že **název** a **veřejný port** koncového bodu naslouchacího procesu, který chcete použít, se již nepoužívají. V příkladu v této části je název *myEndpoint*a port je *1433*.

4. V místním klientovi Stáhněte a nainstalujte nejnovější [modul prostředí PowerShell](https://azure.microsoft.com/downloads/).

5. Spusťte Azure PowerShell.  
    Otevře se nová relace PowerShellu s načtenými moduly pro správu Azure.

6. Spusťte `Get-AzurePublishSettingsFile`. Tato rutina vás přesměruje na prohlížeč a stáhne soubor nastavení publikování do místního adresáře. Může se zobrazit výzva k zadání přihlašovacích údajů k vašemu předplatnému Azure.

7. Spusťte následující příkaz `Import-AzurePublishSettingsFile` s cestou k souboru nastavení publikování, který jste stáhli:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    Po importu souboru s nastaveními publikování můžete svoje předplatné Azure spravovat v relaci PowerShellu.

8. Pro *interního nástroje*Přiřaďte statickou IP adresu. Projděte si aktuální konfiguraci virtuální sítě spuštěním následujícího příkazu:

        (Get-AzureVNetConfig).XMLConfiguration
9. Poznamenejte si název *podsítě* , která obsahuje virtuální počítače, které hostí repliky. Tento název se používá ve skriptu $SubnetName parametrem.

10. Poznamenejte si název *VirtualNetworkSite* a spouštěcí *AddressPrefix* pro podsíť, která obsahuje virtuální počítače, které hostí repliky. Vyhledejte dostupnou IP adresu předáním obou hodnot do příkazu `Test-AzureStaticVNetIP` a prozkoumáním *AvailableAddressesu*. Pokud má například virtuální síť název *MyVNet* a rozsah adres podsítě, který začíná na *172.16.0.128*, následující příkaz zobrazí seznam dostupných adres:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Vyberte jednu z dostupných adres a použijte ji v parametru $ILBStaticIP skriptu v dalším kroku.

12. Zkopírujte následující skript PowerShellu do textového editoru a nastavte hodnoty proměnných tak, aby odpovídaly vašemu prostředí. Pro některé parametry byly zadány výchozí hodnoty.  

    Existující nasazení, která používají skupiny vztahů, nemůžou přidat interního nástroje. Další informace o požadavcích na interního nástroje najdete v tématu [Přehled interního nástroje pro vyrovnávání zatížení](../../../load-balancer/load-balancer-internal-overview.md).

    Pokud vaše skupina dostupnosti zahrnuje oblasti Azure, musíte skript spustit jednou v každém datovém centru pro cloudovou službu a uzly, které se nacházejí v tomto datovém centru.

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

13. Po nastavení proměnných zkopírujte skript z textového editoru do relace PowerShellu a spusťte ho. Pokud se výzva stále zobrazuje **>>** , stiskněte ENTER znovu a ujistěte se, že se skript začne spouštět.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Ověřte, jestli je v případě potřeby KB2854082 nainstalovaná.
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Otevřete porty brány firewall v uzlech skupin dostupnosti.
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Vytvoření naslouchacího procesu skupiny dostupnosti

Vytvořte naslouchací proces skupiny dostupnosti ve dvou krocích. Nejdřív vytvořte prostředek clusteru klientského přístupového bodu a nakonfigurujte závislosti. Potom nakonfigurujte prostředky clusteru v PowerShellu.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Vytvoření klientského přístupového bodu a konfigurace závislostí clusteru
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurace prostředků clusteru v PowerShellu
1. Pro interního nástroje je nutné použít IP adresu interního nástroje, která byla vytvořena dříve. K získání této IP adresy v PowerShellu použijte tento skript:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. Na jednom z virtuálních počítačů zkopírujte skript PowerShellu pro váš operační systém do textového editoru a pak nastavte proměnné na hodnoty, které jste si poznamenali dříve.

    Pro Windows Server 2012 nebo novější použijte tento skript:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    Pro Windows Server 2008 R2 použijte tento skript:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. Po nastavení proměnných otevřete okno Windows PowerShellu se zvýšenými oprávněními, vložte skript z textového editoru do relace PowerShellu a spusťte ho. Pokud se výzva stále zobrazuje **>>** , znovu stiskněte klávesu ENTER a ujistěte se, že se skript začne spouštět.

4. Předchozí kroky opakujte pro každý virtuální počítač.  
    Tento skript nakonfiguruje prostředek IP adresy s IP adresou cloudové služby a nastaví další parametry, jako je například port testu. Když se prostředek IP adresy přepne do online režimu, může reagovat na cyklické dotazování na portu sondy z koncového bodu s vyrovnáváním zatížení, který jste vytvořili dříve.

## <a name="bring-the-listener-online"></a>Převést naslouchací proces do online režimu
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Následné položky
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>Testování naslouchacího procesu skupiny dostupnosti (v rámci stejné virtuální sítě)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Další kroky
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
