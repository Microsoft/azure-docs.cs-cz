---
title: Konfigurace externího naslouchacího procesu pro skupiny dostupnosti Always On | Dokumentace Microsoftu
description: Tento kurz vás provede kroky k vytvoření vždy na naslouchací proces skupiny dostupnosti v Azure, která je zvenku přístupný pomocí veřejné virtuální IP adresy přidružené cloudové služby.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 449df8e49eb63cb6e52cd4ec25dafc2bb0851347
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51241755"
---
# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Konfigurace externího naslouchacího procesu pro skupiny dostupnosti Always On v Azure
> [!div class="op_single_selector"]
> * [Interního naslouchacího procesu](../classic/ps-sql-int-listener.md)
> * [Externí naslouchací proces](../classic/ps-sql-ext-listener.md)
> 
> 

Toto téma ukazuje, jak konfigurace naslouchacího procesu pro skupiny dostupnosti Always On, které je dostupné externě na Internetu. To je možné tím, že přidružíte cloudové službě **veřejná virtuální IP (VIP)** adresu pro naslouchací proces.

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Skupiny dostupnosti mohou obsahovat replik, které jsou v místním, Azure, nebo span lokálně i Azure pro hybridní konfigurace. Azure repliky mohou být uloženy ve stejné oblasti nebo v několika oblastech pomocí více virtuálních sítí (VNets). Následující postup předpokládá, že jste již [nakonfigurovat skupinu dostupnosti](../classic/portal-sql-alwayson-availability-groups.md) ale neprovedli konfiguraci naslouchacího procesu.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Pokyny a omezení pro externí naslouchací procesy
Při nasazování pomocí adresy veřejných virtuálních IP adres cloudové služby, mějte na paměti následující pokyny o naslouchacího procesu skupiny dostupnosti v Azure:

* Naslouchací proces skupiny dostupnosti je podporován v systému Windows Server 2008 R2, Windows Server 2012 a Windows Server 2012 R2.
* Klientská aplikace se musí nacházet na jinou cloudovou službu než ten, který obsahuje skupiny dostupnosti virtuálních počítačů. Azure nepodporuje vrácení přímá odpověď ze serveru s klientem a serverem v rámci stejné cloudové služby.
* Ve výchozím nastavení kroky v tomto článku ukazují, jak nakonfigurovat jeden naslouchací proces použití cloudové služby virtuální IP adresy (VIP) adresy. Je však možné rezervovat a vytvořit více virtuální IP adresy pro cloudové služby. To umožňuje vytvořit několik naslouchacích procesů, které jsou přidružená ke různých virtuálních IP adres pomocí postupu v tomto článku. Informace o tom, jak vytvořit více virtuální IP adresy najdete v tématu [více virtuálních IP adres na jednu cloudovou službu](../../../load-balancer/load-balancer-multivip.md).
* Pokud vytváříte naslouchacího procesu pro hybridní prostředí, v místní síti musí mít připojení k veřejnému Internetu kromě site-to-site VPN s Azure virtual network. V podsíti Azure, je dostupná jenom pro veřejnou IP adresu odpovídající cloudové služby naslouchacího procesu skupiny dostupnosti.
* Není možné vytvořit o externí naslouchací proces ve stejné cloudové službě, kde budete mít taky interního naslouchacího procesu pomocí interního nástroje pro vyrovnávání zatížení (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Zjistit dostupnost naslouchací proces
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Tento článek se zaměřuje na tvorbu naslouchací proces, který používá **externího Vyrovnávání zatížení**. Pokud chcete naslouchací proces, který patří k virtuální síti, najdete v článku verze tohoto článku, který obsahuje postup pro nastavení [naslouchací proces s ILB](../classic/ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Vytvořit koncové body s vyrovnáváním zatížení virtuálního počítače se přímá odpověď ze serveru vrácené
Externí služba Vyrovnávání zatížení používá virtuální veřejné virtuální IP adresa cloudové služby, který je hostitelem vašich virtuálních počítačů. Není to nutné k vytvoření nebo konfigurace nástroje pro vyrovnávání zatížení v tomto případě.

Koncový bod s vyrovnáváním zatížení je třeba vytvořit pro každý virtuální počítač, který je hostitelem repliky služby Azure. Pokud máte repliky v několika oblastech, každá replika pro tuto oblast musí být ve stejné cloudové služby v rámci stejné virtuální síti. Vytváří se skupina dostupnosti repliky, které jsou rozmístěny v několika oblastech Azure vyžaduje konfigurace více virtuálních sítí. Další informace o konfiguraci křížové připojení k virtuální síti, najdete v části [konfigurace VNet-to-VNet připojení](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Na webu Azure Portal přejděte na každý virtuální počítač, který je hostitelem repliky a zobrazte podrobnosti.
2. Klikněte na tlačítko **koncové body** kartu pro každý virtuální počítač.
3. Ověřte, že **název** a **veřejný Port** naslouchacího procesu koncového bodu, který chcete použít se již nepoužívá. V následujícím příkladu je název "MyEndpoint" a port, který je "1433".
4. Na váš místní klient, stáhněte a nainstalujte [nejnovější modul Powershellu](https://azure.microsoft.com/downloads/).
5. Spuštění **prostředí Azure PowerShell**. Otevře novou relaci Powershellu s Azure pro správu modulů.
6. Spustit **Get-AzurePublishSettingsFile**. Tato rutina vás přesměruje do prohlížeče a stáhněte si soubor nastavení publikování do místního adresáře. Můžete být vyzváni k protokolu pověření pro vaše předplatné Azure.
7. Spustit **Import AzurePublishSettingsFile** příkazu s cestou, který jste stáhli soubor nastavení publikování:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    Po importu souboru nastavení publikování můžete spravovat vaše předplatné Azure v relaci Powershellu.
    
1. Zkopírujte níže uvedený Powershellový skript do textového editoru a nastavte hodnoty proměnné tak, aby odpovídala prostředí (výchozí hodnoty byly zadány pro některé parametry). Všimněte si, že pokud vaše skupina dostupnosti zahrnuje oblasti Azure, musíte spustit skript jednou v každé datové centrum pro cloudovou službu a uzly, které se nacházejí ve stejné datové centrum.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. Jakmile jednou nastavíte proměnné, zkopírujte skript z textového editoru do relace prostředí Azure PowerShell k jeho spuštění. Pokud se stále zobrazí výzvu >>, zadejte ENTER znovu a ujistěte se, že je skript spuštěn.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Ověřte, že KB2854082 je nainstalována v případě potřeby
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Otevřít porty brány firewall v uzlech skupiny dostupnosti
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Vytvoření naslouchacího procesu skupiny dostupnosti

Vytvořte naslouchací proces skupiny dostupnosti ve dvou krocích. Nejprve vytvořte prostředek clusteru bodu přístupu klienta a nakonfigurovat závislosti. Za druhé nakonfigurujte prostředky clusteru pomocí Powershellu.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Vytvořit klientský přístupový bod a nakonfigurovat závislosti clusteru
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurace prostředků clusteru v prostředí PowerShell
1. Pro externího Vyrovnávání zatížení, musíte získat veřejnou virtuální IP adresu z cloudové služby, který obsahuje repliky. Přihlaste se na webu Azure portal. Přejděte ke cloudové službě, která obsahuje skupiny dostupnosti virtuálních počítačů. Otevřít **řídicí panel** zobrazení.
2. Poznamenejte si adresu uvedené v části **veřejná virtuální IP (VIP) adres**. Pokud vaše řešení zahrnuje virtuální sítě, můžete tento krok opakujte pro každou cloudovou službu, která obsahuje virtuální počítač, který je hostitelem repliky.
3. Na jednom z virtuálních počítačů zkopírujte níže uvedený Powershellový skript do textového editoru a nastavte proměnné na hodnoty, které jste si předtím poznamenali.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Jednou budete jste nastavili proměnné, otevřete okno Windows Powershellu se zvýšenými oprávněními, pak zkopírujte skript z textového editoru a vložit do relace prostředí Azure PowerShell k jeho spuštění. Pokud se stále zobrazí výzvu >>, zadejte ENTER znovu a ujistěte se, že je skript spuštěn.
5. Tento postup opakujte na každém virtuálním počítači. Tento skript nakonfiguruje prostředek IP adresy se IP adresa cloudové služby a nastaví další parametry jako portu sondy. Pokud prostředek IP adresy se přepne do online režimu, může pak reagovat cyklického dotazování na portu sondy z s vyrovnáváním zatížení koncový bod vytvořený dříve v tomto kurzu.

## <a name="bring-the-listener-online"></a>Přeneste naslouchacího procesu online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Následné akce položek
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testování naslouchacího procesu skupiny dostupnosti (v rámci stejné virtuální síti)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Testování naslouchacího procesu skupiny dostupnosti (přes internet)
Aby bylo možné získat přístup k naslouchacího procesu od mimo virtuální síť, musíte používat externí/veřejný zátěže (popsané v tomto tématu) místo ILB, který je dostupný pouze z v rámci stejné virtuální síti. V připojovacím řetězci zadejte název cloudové služby. Například, pokud jste měli cloudovou službu s názvem *mycloudservice*, příkazu sqlcmd by měl vypadat takto:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Na rozdíl od předchozího příkladu ověřování SQL musí použít, protože volající nelze použít ověřování systému windows přes internet. Další informace najdete v tématu [skupiny dostupnosti Always On na virtuálním počítači Azure: scénáře připojení klienta](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Pokud používáte ověřování SQL, ujistěte se, že vytvoření stejné přihlašovací údaje na obou replikách. Další informace o řešení potíží s přihlášení se skupinami dostupnosti najdete v tématu [mapování přihlášení nebo použití obsažené uživatele databáze SQL pro připojení k ostatními replikami a mapovat do databází dostupnosti](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Pokud jsou repliky Always On v různých podsítích, budou muset klienti zadat **MultisubnetFailover = True** v připojovacím řetězci. Výsledkem je pokusy o paralelní připojení repliky v různých podsítích. Všimněte si, že tento scénář obsahuje nasazení skupiny dostupnosti Always On mezi oblastmi.

## <a name="next-steps"></a>Další postup
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

