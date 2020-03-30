---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 73ba78eca710f0b98b2a209494519cb8003e554b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75468352"
---
Naslouchací proces skupiny dostupnosti je adresa IP a název sítě, na kterých naslouchá skupina dostupnosti serveru SQL Server. Chcete-li vytvořit naslouchací proces skupiny dostupnosti, postupujte takto:

1. <a name="getnet"></a>Získejte název síťového prostředku clusteru.

    a. Pomocí rdp se můžete připojit k virtuálnímu počítači Azure, který je hostitelem primární repliky. 

    b. Otevřete Správce clusteru s podporou převzetí služeb při selhání.

    c. Vyberte uzel **Sítě** a poznamenejte si název sítě clusteru. Tento název použijte `$ClusterNetworkName` v proměnné ve skriptu Prostředí PowerShell. Na následujícím obrázku je síťový název **clusteru Cluster Network 1**:

   ![Název sítě clusteru](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

1. <a name="addcap"></a>Přidejte přístupový bod klienta.  
    Klientský přístupový bod je síťový název, který aplikace používají k připojení k databázím ve skupině dostupnosti. Vytvořte klientský přístupový bod ve Správci clusterů s podporou převzetí služeb při selhání.

    a. Rozbalte název clusteru a klikněte na **Role**.

    b. V podokně **Role** klikněte pravým tlačítkem myši na název skupiny dostupnosti a potom vyberte přidat**klientský přístupový bod** **prostředků** > .

   ![Přístupový bod klienta](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. V poli **Název** vytvořte název pro tento nový naslouchací proces. 
   Název nového naslouchací proces u měl název sítě, který aplikace používají k připojení k databázím ve skupině dostupnosti serveru SQL Server.

    d. Pokud chcete dokončit vytváření naslouchací proces, klepněte na **tlačítko Další** dvakrát a potom klepněte na tlačítko **Dokončit**. Nepřenesete naslouchací proces nebo prostředek do režimu online v tomto okamžiku.

1. Převeďte roli clusteru skupiny dostupnosti do úřádku. Ve **Správci clusteru s podporou převzetí služeb při selhání** v části **Role**klepněte pravým tlačítkem myši na roli a vyberte příkaz **Zastavit roli**.

1. <a name="congroup"></a>Nakonfigurujte prostředek IP pro skupinu dostupnosti.

    a. Klikněte na kartu **Prostředky** a rozbalte klientský přístupový bod, který jste vytvořili.  
    Klientský přístupový bod je offline.

   ![Přístupový bod klienta](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Klikněte pravým tlačítkem myši na prostředek IP a potom klikněte na vlastnosti. Poznamenejte si název adresy IP `$IPResourceName` a použijte ji v proměnné ve skriptu Prostředí PowerShell.

    c. V části **ADRESA IP**klepněte na **položku Statická adresa IP**. Nastavte IP adresu jako stejnou adresu, kterou jste použili při nastavovací masce na webu Azure Portal.

   ![Zdroj IP](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

1. <a name = "dependencyGroup"></a>Uvažte prostředek skupiny dostupnosti serveru SQL Server závislý na přístupovém bodu klienta.

    a. Ve Správci clusterů s podporou převzetí služeb při selhání klikněte na **Role**a potom klikněte na skupinu dostupnosti.

    b. Na kartě **Zdroje** klikněte v části **Jiné zdroje**pravým tlačítkem myši na skupinu prostředků dostupnosti a potom klikněte na **příkaz Vlastnosti**. 

    c. Na kartě závislosti přidejte název prostředku klientského přístupového bodu (naslouchací proces).

   ![Zdroj IP](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Klikněte na tlačítko **OK**.

1. <a name="listname"></a>V závislosti na ip adrese je prostředek klientského přístupového bodu závislý.

    a. Ve Správci clusterů s podporou převzetí služeb při selhání klikněte na **Role**a potom klikněte na skupinu dostupnosti. 

    b. Na kartě **Prostředky** klepněte pravým tlačítkem myši na prostředek klientského přístupového bodu v části **Název serveru**a potom klepněte na příkaz **Vlastnosti**. 

   ![Zdroj IP](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Klikněte na kartu **Závislosti.** Ověřte, zda je adresa IP závislost. Pokud tomu tak není, nastavte závislost na adrese IP. Pokud je v seznamu uvedeno více prostředků, ověřte, zda adresy IP mají závislosti NEBO, nikoli AND. Klikněte na tlačítko **OK**. 

   ![Zdroj IP](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >Můžete ověřit, zda jsou závislosti správně nakonfigurovány. Ve Správci clusterů s podporou převzetí služeb při selhání přejděte na Role, klikněte pravým tlačítkem myši na skupinu dostupnosti, klikněte na **Další akce**a potom klikněte na **Zobrazit sestavu závislostí**. Pokud jsou závislosti správně nakonfigurovány, je skupina dostupnosti závislá na názvu sítě a název sítě je závislý na adrese IP. 


1. <a name="setparam"></a>Nastavte parametry clusteru v PowerShellu.

   a. Zkopírujte následující skript prostředí PowerShell do jedné z instancí serveru SQL Server. Aktualizujte proměnné pro vaše prostředí.

   - `$ListenerILBIP`je IP adresa, kterou jste vytvořili v centru vyrovnávání zatížení Azure pro naslouchací proces skupiny dostupnosti.
    
   - `$ListenerProbePort`je port, který jste nakonfigurovali v zařízení Pro vyrovnávání zatížení Azure pro naslouchací proces skupiny dostupnosti.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ListenerProbePort = <nnnnn>
  
   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Nastavte parametry clusteru spuštěním skriptu PowerShell u jednoho z uzlů clusteru.  

   > [!NOTE]
   > Pokud jsou instance serveru SQL Server v samostatných oblastech, je třeba spustit skript prostředí PowerShell dvakrát. Při prvním použití `$ListenerILBIP` a `$ListenerProbePort` z první oblasti. Podruhé použijte `$ListenerILBIP` a `$ListenerProbePort` z druhé oblasti. Název sítě clusteru a název prostředku IP clusteru se také liší pro každou oblast.

1. Přenesete roli clusteru skupiny dostupnosti do režimu online. Ve **Správci clusteru s podporou převzetí služeb při selhání** v části **Role**klikněte pravým tlačítkem myši na roli a vyberte příkaz **Spustit roli**.

V případě potřeby opakujte výše uvedené kroky a nastavte parametry clusteru pro adresu IP clusteru WSFC.

1. Získejte název IP adresy adresy IP adresy clusteru WSFC. Ve **Správci clusteru s podporou převzetí služeb při selhání** v části Základní prostředky **clusteru**vyhledejte **název serveru**.

1. Klepněte pravým tlačítkem myši na **adresu IP**a vyberte příkaz **Vlastnosti**.

1. Zkopírujte **název** adresy IP. To může `Cluster IP Address`být . 

1. <a name="setwsfcparam"></a>Nastavte parametry clusteru v PowerShellu.
  
   a. Zkopírujte následující skript prostředí PowerShell do jedné z instancí serveru SQL Server. Aktualizujte proměnné pro vaše prostředí.

   - `$ClusterCoreIP`je IP adresa, kterou jste vytvořili v centru vyrovnávání zatížení Azure pro prostředek základního clusteru WSFC. Liší se od adresy IP pro naslouchací proces skupiny dostupnosti.

   - `$ClusterProbePort`je port, který jste nakonfigurovali v zařízení Pro vyrovnávání zatížení Azure pro sondu stavu WSFC. Liší se od sondy pro naslouchací proces skupiny dostupnosti.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
   $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability group listener probe port.
  
   Import-Module FailoverClusters
  
   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Nastavte parametry clusteru spuštěním skriptu PowerShell u jednoho z uzlů clusteru.  

>[!WARNING]
>Port sondy stavu naslouchací proces skupiny dostupnosti se musí lišit od portu sondy stavu základní IP adresy clusteru. V těchto příkladech je port naslouchací proces 59999 a port sondy stavu adresy IP jádra clusteru je 58888. Oba porty vyžadují pravidlo povolit příchozí bránu firewall.
