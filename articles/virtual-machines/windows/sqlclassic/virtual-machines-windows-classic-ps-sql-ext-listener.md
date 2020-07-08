---
title: Konfigurace externího naslouchacího procesu pro skupiny dostupnosti
description: Tento kurz vás provede jednotlivými kroky při vytváření naslouchacího procesu skupiny dostupnosti Always On v Azure, který je externě přístupný pomocí veřejné virtuální IP adresy přidružené cloudové služby.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: ca13d5e8369d007188a17352913519172ed8744e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "75978179"
---
# <a name="configure-an-external-listener-for-availability-groups-on-azure-sql-server-vms"></a>Konfigurace externího naslouchacího procesu pro skupiny dostupnosti na virtuálních počítačích Azure SQL Server
> [!div class="op_single_selector"]
> * [Interní naslouchací proces](../classic/ps-sql-int-listener.md)
> * [Externí naslouchací proces](../classic/ps-sql-ext-listener.md)
> 
> 

V tomto tématu se dozvíte, jak nakonfigurovat naslouchací proces pro skupinu dostupnosti Always On, která je externě přístupná na internetu. To je umožněno přiřazením **veřejné virtuální IP adresy (VIP)** cloudové služby k naslouchacímu procesu.

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Správce prostředků a Classic](../../../azure-resource-manager/management/deployment-models.md). Tento článek popisuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Vaše skupina dostupnosti může obsahovat jenom repliky, které jsou jenom místní, jenom Azure, nebo v místním prostředí i v Azure pro hybridní konfigurace. Repliky Azure se můžou nacházet ve stejné oblasti nebo v několika oblastech pomocí více virtuálních sítí (virtuální sítě). Následující postup předpokládá, že jste už [nakonfigurovali skupinu dostupnosti](../classic/portal-sql-alwayson-availability-groups.md) , ale nenakonfigurovali jste naslouchací proces.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Pokyny a omezení pro externí naslouchací procesy
Při nasazení pomocí veřejné virtuální IP adresy cloudové služby si pamatujte na následující pokyny týkající se naslouchacího procesu skupiny dostupnosti v Azure:

* Naslouchací proces skupiny dostupnosti je podporovaný v systémech Windows Server 2008 R2, Windows Server 2012 a Windows Server 2012 R2.
* Klientská aplikace se musí nacházet v jiné cloudové službě než ta, která obsahuje vaše virtuální počítače skupiny dostupnosti. Azure nepodporuje přímé vrácení serveru s klientem a serverem ve stejné cloudové službě.
* Ve výchozím nastavení postup v tomto článku ukazuje, jak nakonfigurovat jeden naslouchací proces na použití virtuální IP adresy cloudové služby. Je ale možné rezervovat a vytvořit několik VIP adres pro cloudovou službu. To vám umožní použít kroky v tomto článku a vytvořit několik posluchačů, které jsou přidružené k jiné VIP. Informace o tom, jak vytvořit víc virtuálních IP adres, najdete v tématu [víc VIP na cloudové služby](../../../load-balancer/load-balancer-multivip.md).
* Pokud vytváříte naslouchací proces pro hybridní prostředí, musí mít místní síť připojení k veřejnému Internetu kromě sítě VPN typu Site-to-site s virtuální sítí Azure. Když je v podsíti Azure, naslouchací proces skupiny dostupnosti je dosažitelný jenom veřejnou IP adresou příslušné cloudové služby.
* Vytvoření externího naslouchacího procesu není podporované ve stejné cloudové službě, kde máte také interní naslouchací proces pomocí interního Load Balancer (interního nástroje).

## <a name="determine-the-accessibility-of-the-listener"></a>Určení dostupnosti naslouchacího procesu
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Tento článek se zaměřuje na vytvoření naslouchacího procesu, který používá **externí vyrovnávání zatížení**. Pokud chcete naslouchací proces, který je privátní pro vaši virtuální síť, přečtěte si verzi tohoto článku, která poskytuje kroky pro nastavení [naslouchacího procesu pomocí interního nástroje](../classic/ps-sql-int-listener.md) .

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Vytvoření koncových bodů virtuálních počítačů s vyrovnáváním zatížení s přímým vrácením serveru
Externí vyrovnávání zatížení používá virtuální veřejnou virtuální IP adresu cloudové služby, která je hostitelem vašich virtuálních počítačů. Takže v tomto případě nemusíte Nástroj pro vyrovnávání zatížení vytvářet ani konfigurovat.

Pro každý virtuální počítač, který je hostitelem repliky Azure, musíte vytvořit koncový bod s vyrovnáváním zatížení. Pokud máte repliky v několika oblastech, každá replika pro tuto oblast musí být ve stejné cloudové službě ve stejné virtuální síti. Vytváření replik skupin dostupnosti, které pokrývají více oblastí Azure, vyžaduje konfiguraci více virtuální sítě. Další informace o konfiguraci připojení mezi virtuálními sítěmi najdete v tématu [Konfigurace připojení VNet-to-VNet](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. V Azure Portal přejděte do každého virtuálního počítače, který je hostitelem repliky, a Prohlédněte si podrobnosti.
2. Klikněte na kartu **koncové body** pro jednotlivé virtuální počítače.
3. Ověřte, že **název** a **veřejný port** koncového bodu naslouchacího procesu, který chcete použít, se již nepoužívá. V následujícím příkladu je název "MyEndpoint" a port je "1433".
4. V místním klientovi Stáhněte a nainstalujte [nejnovější modul prostředí PowerShell](https://azure.microsoft.com/downloads/).
5. Spusťte **Azure PowerShell**. Otevře se nová relace PowerShellu s načtenými moduly pro správu Azure.
6. Spusťte rutinu **Get-AzurePublishSettingsFile**. Tato rutina vás přesměruje na prohlížeč a stáhne soubor nastavení publikování do místního adresáře. Může se zobrazit výzva k zadání přihlašovacích údajů k vašemu předplatnému Azure.
7. Spusťte příkaz **Import-AzurePublishSettingsFile** s cestou k souboru nastavení publikování, který jste stáhli:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    Po importu souboru nastavení publikování můžete svoje předplatné Azure spravovat v relaci PowerShellu.
    
1. Zkopírujte skript prostředí PowerShell níže do textového editoru a nastavte hodnoty proměnných tak, aby vyhovovaly vašemu prostředí (pro některé parametry byly zadány výchozí hodnoty). Všimněte si, že pokud vaše skupina dostupnosti zahrnuje oblasti Azure, musíte skript spustit jednou v každém datovém centru pro cloudovou službu a uzly, které se nacházejí v tomto datovém centru.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. Po nastavení proměnných zkopírujte skript z textového editoru do relace Azure PowerShell a spusťte ho. Pokud se výzva stále zobrazuje >>, zadejte ENTER znovu, abyste se ujistili, že se skript začne spouštět.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Ověřte, jestli je v případě potřeby KB2854082 nainstalovaná.
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Otevřete porty brány firewall v uzlech skupin dostupnosti.
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Vytvoření naslouchacího procesu skupiny dostupnosti

Vytvořte naslouchací proces skupiny dostupnosti ve dvou krocích. Nejdřív vytvořte prostředek clusteru klientského přístupového bodu a nakonfigurujte závislosti. Potom nakonfigurujte prostředky clusteru pomocí PowerShellu.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Vytvoření klientského přístupového bodu a konfigurace závislostí clusteru
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurace prostředků clusteru v PowerShellu
1. Pro externí vyrovnávání zatížení musíte získat veřejnou virtuální IP adresu cloudové služby, která obsahuje vaše repliky. Přihlaste se k webu Azure Portal. Přejděte ke cloudové službě, která obsahuje virtuální počítač skupiny dostupnosti. Otevřete zobrazení **řídicího panelu** .
2. Poznamenejte si adresu uvedenou na **veřejné virtuální IP adrese (VIP)**. Pokud vaše řešení zahrnuje virtuální sítě, opakujte tento krok pro každou cloudovou službu, která obsahuje virtuální počítač, který je hostitelem repliky.
3. Na jednom z virtuálních počítačů zkopírujte skript PowerShellu níže do textového editoru a nastavte proměnné na hodnoty, které jste si poznamenali dříve.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Po nastavení proměnných otevřete okno Windows PowerShellu se zvýšenými oprávněními a potom zkopírujte skript z textového editoru a vložte ho do relace Azure PowerShell, abyste ho mohli spustit. Pokud se výzva stále zobrazuje >>, zadejte ENTER znovu, abyste se ujistili, že se skript začne spouštět.
5. Tento postup opakujte na každém virtuálním počítači. Tento skript nakonfiguruje prostředek IP adresy s IP adresou cloudové služby a nastaví další parametry, jako je například port testu. Když se prostředek IP adresy přepne do online režimu, může reagovat na cyklické dotazování na portu testu z koncového bodu s vyrovnáváním zatížení, který jste vytvořili dříve v tomto kurzu.

## <a name="bring-the-listener-online"></a>Převést naslouchací proces do online režimu
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Následné položky
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testování naslouchacího procesu skupiny dostupnosti (v rámci stejné virtuální sítě)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Testování naslouchacího procesu skupiny dostupnosti (přes Internet)
Aby bylo možné přistupovat k naslouchacímu procesu mimo virtuální síť, musíte použít externí/veřejné vyrovnávání zatížení (popsané v tomto tématu) místo interního nástroje, které je přístupné jenom v rámci stejné virtuální sítě. V připojovacím řetězci zadáte název cloudové služby. Pokud byste například měli cloudovou službu s názvem *mycloudservice*, bude příkaz Sqlcmd vypadat takto:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Na rozdíl od předchozího příkladu musí být použito ověřování SQL, protože volající nemůže používat ověřování systému Windows přes Internet. Další informace najdete v tématu [Skupina dostupnosti Always On na virtuálním počítači Azure: scénáře připojení klientů](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Pokud používáte ověřování SQL, ujistěte se, že jste v obou replikách vytvořili stejné přihlašovací údaje. Další informace o řešení potíží s přihlášeními pomocí skupin dostupnosti najdete v tématech [Postup mapování přihlášení nebo použití obsaženého uživatele SQL Database pro připojení k ostatním replikám a mapování na databáze dostupnosti](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Pokud jsou repliky Always On v různých podsítích, klienti musí v připojovacím řetězci zadat **MultiSubnetFailover = true** . Výsledkem je paralelní pokusy o připojení k replikám v různých podsítích. Všimněte si, že tento scénář zahrnuje nasazení skupiny dostupnosti AlwaysOn pro více oblastí.

## <a name="next-steps"></a>Další kroky
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

