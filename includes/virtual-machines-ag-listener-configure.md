---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: fd635d4c0563c35979f8d85c33dfbde35f05f9e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400976"
---
Naslouchací proces skupiny dostupnosti je IP adresa a název sítě, na kterých SQL Server Skupina dostupnosti naslouchá. Chcete-li vytvořit naslouchací proces skupiny dostupnosti, postupujte následovně:

1. <a name="getnet"></a>Získejte název prostředku sítě s clustery.

    a. Pomocí protokolu RDP se připojte k virtuálnímu počítači Azure, který je hostitelem primární repliky. 

    b. Otevřete Správce clusteru s podporou převzetí služeb při selhání.

    c. Vyberte uzel **sítě** a poznamenejte si název sítě s clustery. Použijte tento název v `$ClusterNetworkName` proměnné ve skriptu PowerShellu. V následujícím obrázku je název sítě clusteru **Síťová síť 1**:

   ![Název sítě s clustery](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

1. <a name="addcap"></a>Přidejte Klientský přístupový bod.  
    Klientský přístupový bod je síťový název, který aplikace používá pro připojení k databázím ve skupině dostupnosti. Vytvoření klientského přístupového bodu v Správce clusteru s podporou převzetí služeb při selhání.

    a. Rozbalte název clusteru a klikněte na **role**.

    b. V podokně **role** klikněte pravým tlačítkem myši na název skupiny dostupnosti a pak vyberte **Přidat prostředek**  >  **Klientský přístupový bod**.

   ![Snímek obrazovky zobrazující možnost nabídky bodu přístupu klienta](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. V poli **název** vytvořte název pro tento nový naslouchací proces. 
   Název nového naslouchacího procesu je síťový název, který aplikace používá pro připojení k databázím ve skupině dostupnosti SQL Server.

    d. Chcete-li dokončit vytváření naslouchacího procesu, klikněte dvakrát na tlačítko **Další** a potom klikněte na tlačítko **Dokončit**. V tuto chvíli nepřineste naslouchací proces nebo prostředek online.

1. Převeďte roli clusteru skupiny dostupnosti do režimu offline. V **Správce clusteru s podporou převzetí služeb při selhání** v části **role**klikněte pravým tlačítkem na roli a vyberte **zastavit roli**.

1. <a name="congroup"></a>Nakonfigurujte prostředek IP pro skupinu dostupnosti.

    a. Klikněte na kartu **prostředky** a potom rozbalte Klientský přístupový bod, který jste vytvořili.  
    Klientský přístupový bod je offline.

   ![Klientský přístupový bod](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Klikněte pravým tlačítkem na prostředek IP a pak klikněte na vlastnosti. Poznamenejte si název IP adresy a použijte ji v `$IPResourceName` proměnné ve skriptu PowerShellu.

    c. V části **IP adresa**klikněte na **statická IP adresa**. Nastavte IP adresu jako stejnou adresu, kterou jste použili při nastavování adresy nástroje pro vyrovnávání zatížení na Azure Portal.

   ![Snímek obrazovky, na kterém se zobrazuje, kde můžete nastavit IP adresu](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

1. <a name = "dependencyGroup"></a>Zajistěte, aby byl prostředek skupiny dostupnosti SQL Server závislý na klientském přístupovém bodu.

    a. V Správce clusteru s podporou převzetí služeb při selhání klikněte na **role**a potom klikněte na svou skupinu dostupnosti.

    b. Na kartě **prostředky** v části **jiné prostředky**klikněte pravým tlačítkem na skupinu prostředků dostupnosti a potom klikněte na **vlastnosti**. 

    c. Na kartě závislosti přidejte název prostředku klientského přístupového bodu (naslouchacího procesu).

   ![Snímek obrazovky, který ukazuje, kde přidat název na kartě závislosti](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Klikněte na **OK**.

1. <a name="listname"></a>Nastavte prostředek klientského přístupového bodu na základě IP adresy.

    a. V Správce clusteru s podporou převzetí služeb při selhání klikněte na **role**a potom klikněte na svou skupinu dostupnosti. 

    b. Na kartě **prostředky** klikněte pravým tlačítkem na prostředek klientského přístupového bodu v části **název serveru**a pak klikněte na **vlastnosti**. 

   ![Snímek obrazovky, který zobrazuje možnost nabídky vlastnosti pro název serveru](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Klikněte na kartu **závislosti** . Ověřte, že IP adresa je závislá. Pokud tomu tak není, nastavte závislost na IP adrese. Pokud je v seznamu uvedeno více prostředků, ověřte, zda IP adresa obsahuje nebo, nikoli a, závislosti. Klikněte na **OK**. 

   ![Prostředek IP](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >Můžete ověřit, zda jsou závislosti správně nakonfigurovány. V Správce clusteru s podporou převzetí služeb při selhání přejděte na role, klikněte pravým tlačítkem na skupinu dostupnosti, klikněte na **Další akce**a pak klikněte na  **Zobrazit sestavu závislostí**. Pokud jsou závislosti správně nakonfigurovány, je skupina dostupnosti závislá na názvu sítě a název sítě závisí na IP adrese. 


1. <a name="setparam"></a>Nastavte parametry clusteru v prostředí PowerShell.

   a. Zkopírujte následující skript prostředí PowerShell do jedné z vašich SQL Serverch instancí. Aktualizujte proměnné pro vaše prostředí.

   - `$ListenerILBIP` je IP adresa, kterou jste vytvořili v nástroji pro vyrovnávání zatížení Azure pro naslouchací proces skupiny dostupnosti.
    
   - `$ListenerProbePort` je port, který jste nakonfigurovali v nástroji pro vyrovnávání zatížení Azure pro naslouchací proces skupiny dostupnosti.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ListenerProbePort = <nnnnn>
  
   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Nastavte parametry clusteru spuštěním skriptu PowerShellu na jednom z uzlů clusteru.  

   > [!NOTE]
   > Pokud jsou vaše SQL Server instance v samostatných oblastech, je potřeba PowerShellový skript spustit dvakrát. Při prvním použití `$ListenerILBIP` a `$ListenerProbePort` z první oblasti. Podruhé použijte `$ListenerILBIP` a `$ListenerProbePort` z druhé oblasti. Název sítě clusteru a název prostředku IP adresy clusteru se také liší pro každou oblast.

1. Přepněte roli clusteru skupiny dostupnosti do online režimu. V **Správce clusteru s podporou převzetí služeb při selhání** v části **role**klikněte pravým tlačítkem na roli a vyberte **Spustit roli**.

V případě potřeby opakujte výše uvedené kroky a nastavte parametry clusteru pro IP adresu clusteru služby WSFC.

1. Získejte název IP adresy pro IP adresu clusteru služby WSFC. V **Správce clusteru s podporou převzetí služeb při selhání** pod položkou **základní prostředky clusteru**Najděte **název serveru**.

1. Klikněte pravým tlačítkem na **IP adresu**a vyberte **vlastnosti**.

1. Zkopírujte **název** IP adresy. Může to být `Cluster IP Address` . 

1. <a name="setwsfcparam"></a>Nastavte parametry clusteru v prostředí PowerShell.
  
   a. Zkopírujte následující skript prostředí PowerShell do jedné z vašich SQL Serverch instancí. Aktualizujte proměnné pro vaše prostředí.

   - `$ClusterCoreIP` je IP adresa, kterou jste vytvořili v nástroji pro vyrovnávání zatížení Azure pro prostředek clusteru Core služby WSFC. Liší se od IP adresy pro naslouchací proces skupiny dostupnosti.

   - `$ClusterProbePort` je port, který jste nakonfigurovali v nástroji pro vyrovnávání zatížení Azure pro sondu stavu služby WSFC. Liší se od sondy pro naslouchací proces skupiny dostupnosti.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
   $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability group listener probe port.
  
   Import-Module FailoverClusters
  
   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Nastavte parametry clusteru spuštěním skriptu PowerShellu na jednom z uzlů clusteru.  

>[!WARNING]
>Port testu stavu naslouchacího procesu skupiny dostupnosti musí být jiný než port testu stavu základní IP adresy clusteru. V těchto příkladech je port naslouchacího procesu 59999 a port testu stavu IP adresy jádra clusteru je 58888. Oba porty vyžadují pravidlo brány firewall povolit příchozí připojení.
