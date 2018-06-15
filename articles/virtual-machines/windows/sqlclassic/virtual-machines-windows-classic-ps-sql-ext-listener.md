---
title: Konfigurace o externí naslouchací proces pro skupiny dostupnosti Always On | Microsoft Docs
description: Tento kurz vás provede kroky k vytvoření vždy na naslouchací proces skupiny dostupnosti v Azure, které je externě přístupné pomocí veřejná virtuální IP adresy přidružené cloudové služby.
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
ms.openlocfilehash: 38bb77c6b1d083bd6b52b785a991f24965d00e12
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29398858"
---
# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Konfigurace o externí naslouchací proces pro skupiny dostupnosti Always On v Azure
> [!div class="op_single_selector"]
> * [Interní naslouchací proces](../classic/ps-sql-int-listener.md)
> * [Externí naslouchací proces](../classic/ps-sql-ext-listener.md)
> 
> 

Toto téma ukazuje, jak nakonfigurovat naslouchací proces pro vždy na skupiny dostupnosti, která je přístupná externě na Internetu. To je umožněno díky přidružení cloudové službě **veřejná virtuální IP (VIP)** adresu naslouchací proces.

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Vaší skupiny dostupnosti může obsahovat replik, které jsou pouze, místní, Azure nebo span místní a Azure pro hybridní konfigurace. Azure repliky mohou být uloženy ve stejné oblasti nebo nad několika oblastmi pomocí více virtuálních sítí (virtuální sítě). Následující postup předpokládá, že už máte [nakonfigurovat skupinu dostupnosti](../classic/portal-sql-alwayson-availability-groups.md) ale nenakonfigurovali naslouchací proces.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Pokyny a omezení pro externí naslouchací procesy
Všimněte si následující pokyny o naslouchacího procesu skupiny dostupnosti v Azure při nasazení pomocí adresy stydké VIP cloudové služby:

* Naslouchací proces skupiny dostupnosti je podporován v systému Windows Server 2008 R2, Windows Server 2012 a Windows Server 2012 R2.
* Klientská aplikace se musí nacházet v jiné cloudové službě než ten, který obsahuje vaší skupiny dostupnosti virtuálních počítačů. Azure nepodporuje přímá odpověď ze serveru s klientem a serverem v rámci stejné cloudové služby.
* Ve výchozím kroky v tomto článku ukazují, jak nakonfigurovat jeden naslouchací proces použít adresu cloudové služby virtuální IP (VIP). Nicméně je možné vyhradit a vytvořte více VIP adres pro cloudové služby. Můžete vytvořit více naslouchací procesy, které se každý souvisejí s jinou virtuální IP adresy pomocí kroků v tomto článku. Informace o tom, jak vytvořit více virtuálních IP adres najdete v tématu [několika virtuálními IP adresami za cloudové služby](../../../load-balancer/load-balancer-multivip.md).
* Pokud chcete vytvořit naslouchací proces pro hybridní prostředí, do místní sítě musí mít připojení k veřejnému Internetu kromě site-to-site VPN s virtuální síť Azure. V podsíti Azure, je dostupná pouze pro veřejnou IP adresu odpovídající cloudové služby naslouchacího procesu skupiny dostupnosti.
* Není možné vytvořit o externí naslouchací proces ve stejné cloudové služby, kde máte také naslouchací proces interní pomocí interní nástroj pro vyrovnávání zatížení (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Určení usnadnění naslouchacího procesu
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Tento článek se zaměřuje na tvorbu naslouchací proces, který používá **Vyrovnávání zatížení externí**. Pokud chcete naslouchací proces, který je privátní k virtuální síti, najdete v části verzi tohoto článku, který obsahuje postup pro nastavení [naslouchací proces s ILB](../classic/ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Vytvoření koncové body s vyrovnáváním zatížení virtuálních počítačů s přímý server návratový
Externí Vyrovnávání zatížení využívá virtuální veřejná virtuální IP adresa cloudové služby, který hostuje virtuální počítače. Proto není nutné k vytváření a konfiguraci nástroje pro vyrovnávání zatížení v tomto případě.

Koncový bod Vyrovnávání zatížení sítě je nutné vytvořit pro každý virtuální počítač Azure repliky hostování. Pokud máte repliky v několika oblastech, každá replika pro danou oblast musí být v rámci stejné cloudové služby ve stejné virtuální síti. Vytvoření skupiny dostupnosti replik, které jsou rozmístěny v několika oblastmi Azure vyžaduje konfiguraci více virtuálních sítí. Další informace o konfiguraci křížové připojení virtuální sítě najdete v tématu [konfigurace virtuální sítě pro připojení k virtuální síti](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Na portálu Azure přejděte na každý virtuální počítač, který je hostitelem repliky a zobrazení podrobností.
2. Klikněte **koncové body** kartu pro každý z virtuálních počítačů.
3. Ověřte, zda **název** a **veřejný Port** naslouchacího procesu koncový bod, který chcete použít není již používán. V následujícím příkladu je název "MyEndpoint" a port je "1433".
4. Na místního klienta, stáhněte a nainstalujte [nejnovější modul PowerShell](https://azure.microsoft.com/downloads/).
5. Spusťte **prostředí Azure PowerShell**. Novou relaci prostředí PowerShell, je otevřené s Azure pro správu moduly načíst.
6. Spustit **Get-AzurePublishSettingsFile**. Tato rutina vás přesměruje do prohlížeče a stáhněte soubor nastavení publikování do místního adresáře. Budete vyzváni k zadání pověření přihlašování pro vaše předplatné Azure.
7. Spustit **Import AzurePublishSettingsFile** příkazu s cestou soubor nastavení publikování, který jste stáhli:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    Po importu se soubor nastavení publikování, můžete spravovat vaše předplatné Azure v relaci prostředí PowerShell.
    
1. Do textového editoru, zkopírujte níže uvedený skript prostředí PowerShell a nastavte hodnoty proměnné tak, aby odpovídaly vašemu prostředí (výchozí hodnoty byly zadány pro některé parametry). Všimněte si, že pokud vaše skupina dostupnosti zahrnuje oblasti Azure, musíte spustit skript jednou v každé datové centrum pro cloudové služby a uzly, které jsou umístěny ve stejné datové centrum.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. Jakmile jednou nastavíte proměnné, zkopírujte z textového editoru do relace prostředí Azure PowerShell ji spustit skript. Pokud stále zobrazuje řádku >>, zadejte zadejte znovu a ujistěte se, je skript spuštěn.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Ověřte, zda KB2854082 nainstalována v případě potřeby
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Otevřít porty brány firewall v uzlech skupiny dostupnosti
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Vytvoření naslouchacího procesu skupiny dostupnosti

Vytvořte naslouchací proces skupiny dostupnosti ve dvou krocích. Nejprve vytvořte prostředek clusteru bodu přístupu klienta a nakonfigurovat závislosti. Druhý nakonfigurujte prostředky clusteru pomocí prostředí PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Vytvořit klientský přístupový bod a nakonfigurovat závislosti clusteru
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurace prostředků clusteru v prostředí PowerShell
1. Pro externí zátěže, musíte získat veřejnou virtuální IP adresu cloudové služby, který obsahuje repliky. Přihlaste se k portálu Azure. Přejděte do cloudové služby, která obsahuje vaší skupiny dostupnosti virtuálních počítačů. Otevřete **řídicí panel** zobrazení.
2. Poznamenejte si adresu v části **veřejná virtuální IP (VIP) Adresa**. Pokud vaše řešení zahrnuje virtuální sítě, můžete tento krok opakujte pro každou cloudovou službu, která obsahuje virtuální počítač, který je hostitelem repliky.
3. Na jednom z virtuálních počítačů do textového editoru, zkopírujte níže uvedený skript prostředí PowerShell a nastavte proměnné na hodnoty, které jste si předtím poznamenali.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Jednou jste nastavili proměnné, otevřete okno se zvýšenými oprávněními prostředí Windows PowerShell, pak zkopírujte skript z textového editoru a vložte do relace prostředí Azure PowerShell ji spustit. Pokud stále zobrazuje řádku >>, zadejte zadejte znovu a ujistěte se, je skript spuštěn.
5. Tento postup opakujte pro každý virtuální počítač. Tento skript nakonfiguruje prostředků IP adresu s IP adresou cloudové služby a nastaví další parametry, jako je port testu. Pokud IP adresa prostředků do online režimu, může pak reagovat dotazování na port testu z vyrovnávání zatížení koncového bodu v tomto kurzu vytvořili.

## <a name="bring-the-listener-online"></a>Přepněte naslouchací proces online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Položky následnou akci
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testování naslouchacího procesu skupiny dostupnosti (v rámci stejné virtuální)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Testování naslouchacího procesu skupiny dostupnosti (přes internet)
Chcete-li získat přístup k naslouchání z mimo virtuální síť, musíte používat vyrovnávání zatížení externí nebo veřejné (popsaný v tomto tématu) místo ILB, který je dostupný jenom z v rámci stejnou virtuální síť. V připojovacím řetězci zadejte název cloudové služby. Například pokud jste měli cloudové služby s názvem *mycloudservice*, sqlcmd příkaz vypadat takto:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Na rozdíl od předchozí příklad musí být použít ověřování SQL, protože volající nelze použít ověřování systému windows přes internet. Další informace najdete v tématu [vždy na skupiny dostupnosti ve virtuálním počítači Azure: případech připojení klienta](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Když pomocí ověřování SQL. Ujistěte se, že můžete vytvořit stejnou přihlášení na obou replikách. Další informace o odstraňování potíží s přihlášení se skupinami dostupnosti najdete v tématu [mapování přihlášení nebo použití obsažené uživatele databáze SQL pro připojení k jiné repliky a mapovat do databází dostupnosti,](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Pokud jsou repliky Always On v různých podsítích, musí klienti zadat **MultisubnetFailover = True** v připojovacím řetězci. Výsledkem pokusy o připojení paralelní replikami v různých podsítích. Všimněte si, že tento scénář zahrnuje nasazení mezi oblastmi vždy na skupiny dostupnosti.

## <a name="next-steps"></a>Další postup
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

