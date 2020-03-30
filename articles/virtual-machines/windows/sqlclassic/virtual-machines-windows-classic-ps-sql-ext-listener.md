---
title: Konfigurace externího naslouchací hoschu pro skupiny dostupnosti
description: Tento kurz vás provede kroky vytvoření vždy na dostupnost i naslouchací proces skupiny v Azure, který je externě přístupný pomocí veřejné virtuální IP adresy přidružené cloudové služby.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978179"
---
# <a name="configure-an-external-listener-for-availability-groups-on-azure-sql-server-vms"></a>Konfigurace externího naslouchací hopro skupiny dostupnosti na virtuálních počítačích Azure SQL Server
> [!div class="op_single_selector"]
> * [Interní naslouchací proces](../classic/ps-sql-int-listener.md)
> * [Externí naslouchací proces](../classic/ps-sql-ext-listener.md)
> 
> 

Toto téma ukazuje, jak nakonfigurovat naslouchací proces pro skupinu vždy na dostupnosti, která je externě přístupná na internetu. To je možné spojením veřejné virtuální IP adresy **(VIP)** cloudové služby s naslouchacím procesem.

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../azure-resource-manager/management/deployment-models.md). Tento článek popisuje použití modelu klasické nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Vaše skupina dostupnosti může obsahovat repliky, které jsou jenom místní, jenom Azure nebo se rozprostírají v místním i v Azure pro hybridní konfigurace. Repliky Azure se můžou nasytit ve stejné oblasti nebo ve více oblastech pomocí více virtuálních sítí (virtuálních sítí). Následující kroky předpokládají, že jste již [nakonfigurovali skupinu dostupnosti,](../classic/portal-sql-alwayson-availability-groups.md) ale nenakonfigurovali naslouchací proces.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Pokyny a omezení pro externí posluchače
Všimněte si následujících pokynů o naslouchací proces skupiny dostupnosti v Azure při nasazování pomocí veřejné vip adresy cloudové služby:

* Naslouchací proces skupiny dostupnosti je podporován v systémech Windows Server 2008 R2, Windows Server 2012 a Windows Server 2012 R2.
* Klientská aplikace musí být umístěna v jiné cloudové službě, než je ta, která obsahuje virtuální počítače skupiny dostupnosti. Azure nepodporuje přímé vrácení serveru s klientem a serverem ve stejné cloudové službě.
* Ve výchozím nastavení kroky v tomto článku ukazují, jak nakonfigurovat jeden naslouchací proces pro použití adresy virtuální IP (virtualIP) cloudové služby. Je však možné rezervovat a vytvořit více VIP adres pro vaši cloudovou službu. To umožňuje použít kroky v tomto článku k vytvoření více naslouchacích procesy, které jsou přidruženy k jiné VIP. Informace o tom, jak vytvořit více ADRES VIP, najdete [v tématu více VIP na cloudovou službu](../../../load-balancer/load-balancer-multivip.md).
* Pokud vytváříte naslouchací proces pro hybridní prostředí, místní síť musí mít připojení k veřejnému Internetu kromě sítě VPN site-to-site s virtuální sítí Azure. Když v podsíti Azure naslouchací proces skupiny dostupnosti je dosažitelný pouze veřejnou IP adresou příslušné cloudové služby.
* Není podporováno vytvoření externího naslouchací proces ve stejné cloudové službě, kde máte také interní naslouchací proces pomocí vnitřní vyrovnávání zatížení (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Určení přístupnosti naslouchací proces
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Tento článek se zaměřuje na vytvoření naslouchací proces, který používá **externí vyrovnávání zatížení**. Pokud chcete naslouchací proces, který je privátní pro vaši virtuální síť, naleznete v části verze tohoto článku, která obsahuje kroky pro nastavení [naslouchací proces s ILB](../classic/ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Vytvoření koncových bodů virtuálních montovek s vyrovnáváním zatížení s přímým návratem serveru
Externí vyrovnávání zatížení používá virtuální veřejnou virtuální IP adresu cloudové služby, která hostuje vaše virtuální počítače. Takže v tomto případě není nutné vytvářet ani konfigurovat vyrovnávání zatížení.

Je nutné vytvořit koncový bod s vyrovnáváním zatížení pro každý virtuální počítač hostující repliku Azure. Pokud máte repliky ve více oblastech, každá replika pro tuto oblast musí být ve stejné cloudové službě ve stejné virtuální síti. Vytvoření replik skupiny dostupnosti, které pokrývají více oblastí Azure, vyžaduje konfiguraci více virtuálních sítí. Další informace o konfiguraci připojení mezi virtuálními sítěmi najdete [v tématu Konfigurace](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)připojení virtuální sítě k virtuální síti .

1. Na webu Azure Portal přejděte na každý virtuální počítač hostující repliku a zobrazte podrobnosti.
2. Klikněte na kartu **Koncové body** pro každý z virtuálních discích.
3. Ověřte, zda **název** a **veřejný port** koncového bodu naslouchací procesu, který chcete použít, ještě není používán. V níže uvedeném příkladu je název "MyEndpoint" a port je "1433".
4. V místním klientovi stáhněte a nainstalujte [nejnovější modul Prostředí PowerShell](https://azure.microsoft.com/downloads/).
5. Spusťte **Azure PowerShell**. Otevře se nová relace PowerShellu s načtenými moduly pro správu Azure.
6. Spusťte **Get-AzurePublishSettingsFile**. Tato rutina vás přesměruje do prohlížeče a stáhne soubor nastavení publikování do místního adresáře. Můžete být vyzváni k zadání přihlašovacích údajů pro vaše předplatné Azure.
7. Spusťte příkaz **Import-AzurePublishSettingsFile** s cestou staženého souboru nastavení publikování:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    Po importu souboru nastavení publikování můžete spravovat předplatné Azure v relaci PowerShellu.
    
1. Zkopírujte skript Prostředí PowerShell níže do textového editoru a nastavte hodnoty proměnných tak, aby vyhovovaly vašemu prostředí (pro některé parametry byly poskytnuty výchozí hodnoty). Všimněte si, že pokud vaše skupina dostupnosti zahrnuje oblasti Azure, musíte spustit skript jednou v každém datovém centru pro cloudovou službu a uzly, které jsou umístěny v tomto datovém centru.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. Jakmile nastavíte proměnné, zkopírujte skript z textového editoru do relace Azure PowerShellu a spusťte ho. Pokud se na výzvu stále zobrazuje >>, zadejte příkaz ENTER znovu, abyste se ujistili, že skript začne běžet.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>V případě potřeby ověřte, zda je nainstalována kb2854082
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Otevření portů brány firewall v uzlech skupin y dostupnosti
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Vytvoření naslouchací proces skupiny dostupnosti

Vytvořte naslouchací proces skupiny dostupnosti ve dvou krocích. Nejprve vytvořte prostředek clusteru klientského přístupového bodu a nakonfigurujte závislosti. Za druhé, nakonfigurujte prostředky clusteru pomocí prostředí PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Vytvoření klientského přístupového bodu a konfigurace závislostí clusteru
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurace prostředků clusteru v Prostředí PowerShell
1. Pro externí vyrovnávání zatížení je nutné získat veřejnou virtuální IP adresu cloudové služby, která obsahuje vaše repliky. Přihlaste se k webu Azure Portal. Přejděte na cloudovou službu, která obsahuje váš virtuální počítač skupiny dostupnosti. Otevřete zobrazení **Řídicí panel.**
2. Poznamenejte si adresu zobrazenou v části **Veřejná virtuální IP adresa (VIP).** Pokud vaše řešení zahrnuje virtuální sítě, opakujte tento krok pro každou cloudovou službu, která obsahuje virtuální počítač, který je hostitelem repliky.
3. Na jednom z virtuálních počítačů zkopírujte skript Prostředí PowerShell níže do textového editoru a nastavte proměnné na hodnoty, které jste si poznamenali dříve.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Jakmile nastavíte proměnné, otevřete okno Windows PowerShell se zvýšenými oprávněními, zkopírujte skript z textového editoru a vložte do relace Azure PowerShellu a spusťte ho. Pokud se na výzvu stále zobrazuje >>, zadejte příkaz ENTER znovu, abyste se ujistili, že skript začne běžet.
5. Opakujte to na každém virtuálním počítači. Tento skript konfiguruje prostředek IP adresy s IP adresou cloudové služby a nastaví další parametry, jako je port sondy. Když je prostředek IP adresy přepnuto do režimu online, pak může reagovat na dotazování na portu sondy z koncového bodu s vyrovnáváním zatížení vytvořeného dříve v tomto kurzu.

## <a name="bring-the-listener-online"></a>Převést posluchače do režimu online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Následné položky
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Otestujte naslouchací proces skupiny dostupnosti (v rámci stejné virtuální sítě)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Otestujte posluchače skupiny dostupnosti (přes internet)
Chcete-li získat přístup k naslouchacímu procesu mimo virtuální síť, musíte používat externí/veřejné vyrovnávání zatížení (popsané v tomto tématu) spíše než ILB, která je přístupná jenom v rámci stejné virtuální sítě. V připojovacím řetězci zadáte název cloudové služby. Například pokud jste měli cloudovou službu s názvem *mycloudservice*, sqlcmd příkaz by být následující:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Na rozdíl od předchozího příkladu musí být použito ověřování SQL, protože volající nemůže používat ověřování systému Windows přes Internet. Další informace najdete [v tématu Vždy na dostupnost skupiny v virtuálním počítači Azure: Scénáře připojení klienta](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Při použití ověřování SQL, ujistěte se, že vytvoříte stejné přihlášení na obou replikách. Další informace o řešení potíží s přihlášením pomocí skupin dostupnosti naleznete v tématu [Mapování přihlášení nebo použití obsaženého uživatele databáze SQL pro připojení k jiným replikám a mapování databází dostupnosti](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Pokud jsou repliky Always On v různých podsítích, klienti musí v připojovacím řetězci zadat **MultisubnetFailover=True.** Výsledkem jsou pokusy o paralelní připojení repliky v různých podsítích. Všimněte si, že tento scénář zahrnuje nasazení skupiny always on availability group mezi oblastmi.

## <a name="next-steps"></a>Další kroky
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

