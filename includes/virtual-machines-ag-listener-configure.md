Naslouchací proces skupiny dostupnosti se IP adresy a síťového názvu, které skupiny dostupnosti systému SQL Server naslouchá. Chcete-li vytvořit naslouchací proces skupiny dostupnosti, postupujte takto:

1. <a name="getnet"></a>Získejte název sítě prostředků clusteru.

    a. Pomocí protokolu RDP připojit k virtuálním počítači Azure, který je hostitelem primární repliky. 

    b. Otevřete Správce clusteru převzetí služeb při selhání.

    c. Vyberte **sítě** uzlu a poznamenejte si název sítě s clustery. Použijte tento název v `$ClusterNetworkName` proměnné ve skriptu prostředí PowerShell. Na následujícím obrázku je název sítě s clustery **clusteru sítě 1**:

   ![Název sítě s clustery](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

1. <a name="addcap"></a>Přidáte klientský přístupový bod.  
    Klientský přístupový bod je název sítě, které aplikace použít k připojení k databázím ve skupině dostupnosti. Vytvořte klientský přístupový bod v modulu Správce clusteru převzetí služeb při selhání.

    a. Rozbalte název clusteru a potom klikněte na tlačítko **role**.

    b. V **role** podokně klikněte pravým tlačítkem na název skupiny dostupnosti a pak vyberte **přidat prostředek** > **klientský přístupový bod**.

   ![Klientský přístupový bod](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. V **název** pole, vytvoření názvu pro tento nový naslouchací proces. 
   Název pro nový naslouchací proces je název sítě, které aplikace použít k připojení k databázím ve skupině dostupnosti systému SQL Server.

    d. Vytváření naslouchací proces dokončíte, klikněte na tlačítko **Další** dvakrát a potom klikněte na tlačítko **Dokončit**. Není přinášejí naslouchací proces nebo online prostředků v tomto okamžiku.

1. Přepněte roli clusteru skupiny dostupnosti do režimu offline. V **Správce clusteru převzetí služeb při selhání** pod **role**, klikněte pravým tlačítkem na roli a vyberte **zastavit roli**.

1. <a name="congroup"></a>Konfigurace IP prostředku pro skupinu dostupnosti.

    a. Klikněte na tlačítko **prostředky** kartu a potom rozbalte klientský přístupový bod jste vytvořili.  
    Klientský přístupový bod je v režimu offline.

   ![Klientský přístupový bod](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Klikněte pravým tlačítkem na prostředku IP adresy a pak klikněte na vlastnosti. Poznamenejte si název IP adresy a používat ho v `$IPResourceName` proměnné ve skriptu prostředí PowerShell.

    c. V části **IP adresu**, klikněte na tlačítko **statickou IP adresu**. Nastavte adresu IP jako stejnou adresu, který jste použili při nastavování adres nástroje pro vyrovnávání zatížení na webu Azure portal.

   ![Prostředek IP adresy](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

1. <a name = "dependencyGroup"></a>Ujistěte se, prostředek skupiny dostupnosti SQL serveru závisí na klientský přístupový bod.

    a. V modulu Správce clusteru převzetí služeb při selhání klikněte na tlačítko **role**a potom klikněte na skupiny dostupnosti.

    b. Na **prostředky** ve skupině **ostatní prostředky**, klikněte pravým tlačítkem na skupinu dostupnosti prostředků a potom klikněte na tlačítko **vlastnosti**. 

    c. Na kartě závislosti přidejte název prostředku klienta přístup k bodu (naslouchací proces).

   ![Prostředek IP adresy](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Klikněte na **OK**.

1. <a name="listname"></a>Zkontrolujte přístup k bodu klienta závislá na IP adresu prostředku.

    a. V modulu Správce clusteru převzetí služeb při selhání klikněte na tlačítko **role**a potom klikněte na skupiny dostupnosti. 

    b. Na **prostředky** kartu, klikněte pravým tlačítkem na zdroj bodu přístup klienta v části **název serveru**a potom klikněte na tlačítko **vlastnosti**. 

   ![Prostředek IP adresy](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Klikněte na tlačítko **závislosti** kartu. Ověřte, zda je IP adresa závislost. Pokud není, nastavte závislost na IP adresu. Pokud existuje více odkazů, ověřte, že IP adresy mají OR, not a závislosti. Klikněte na **OK**. 

   ![Prostředek IP adresy](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >Můžete ověřit, že je správně nakonfigurované závislosti. V modulu Správce clusteru převzetí služeb při selhání, přejděte k rolím, klikněte pravým tlačítkem na skupinu dostupnosti, klikněte na tlačítko **další akce**a potom klikněte na tlačítko **zobrazit sestavu závislostí**. Pokud závislosti jsou správně nakonfigurována, skupina dostupnosti je závislá na název sítě a síťový název je závislá na IP adresu. 


1. <a name="setparam"></a>Nastavení parametrů clusteru v prostředí PowerShell.

  a. Zkopírujte následující skript prostředí PowerShell do jednoho z vašich instancí systému SQL Server. Aktualizujte proměnné pro vaše prostředí.

  - `$ListenerILBIP` je IP adresa, kterou jste vytvořili v nástroji pro vyrovnávání zatížení Azure pro naslouchací proces skupiny dostupnosti.
    
  - `$ListenerProbePort` je port, který jste nakonfigurovali v nástroji pro vyrovnávání zatížení Azure pro naslouchací proces skupiny dostupnosti.

  ```PowerShell
  $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
  $IPResourceName = "<IPResourceName>" # the IP Address resource name
  $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
  [int]$ListenerProbePort = <nnnnn>
  
  Import-Module FailoverClusters

  Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
  ```

  b. Nastavení parametrů clusteru spuštěním skriptu prostředí PowerShell na jednom z uzlů clusteru.  

  > [!NOTE]
  > Pokud vaše instance SQL serveru jsou v oblastech, budete muset spustit skript prostředí PowerShell dvakrát. Při prvním použití `$ListenerILBIP` a `$ListenerProbePort` v první oblasti. Použít při druhém volání `$ListenerILBIP` a `$ListenerProbePort` z druhé oblasti. Síťový název clusteru a název prostředku IP clusteru se také liší pro každou oblast.

1. Přeneste online role clusteru skupiny dostupnosti. V **Správce clusteru převzetí služeb při selhání** pod **role**, klikněte pravým tlačítkem myši klikněte na roli a vyberte **Role spuštění**.

V případě potřeby opakujte tento postup k nastavení parametrů clusteru pro IP adresu clusteru služby WSFC.

1. Získejte název adresy IP adresa IP clusteru služby WSFC. V **Správce clusteru převzetí služeb při selhání** pod **základní prostředky clusteru**, vyhledejte **název serveru**.

1. Klikněte pravým tlačítkem na **IP adresu**a vyberte **vlastnosti**.

1. Kopírovat **název** IP adresy. To může být `Cluster IP Address`. 

1. <a name="setwsfcparam"></a>Nastavení parametrů clusteru v prostředí PowerShell.
  
  a. Zkopírujte následující skript prostředí PowerShell do jednoho z vašich instancí systému SQL Server. Aktualizujte proměnné pro vaše prostředí.

  - `$ClusterCoreIP` je IP adresa, kterou jste vytvořili v nástroji pro vyrovnávání zatížení Azure pro prostředek clusteru služby WSFC core. To se liší od IP adresu pro naslouchací proces skupiny dostupnosti.

  - `$ClusterProbePort` je port, který jste nakonfigurovali v nástroji pro vyrovnávání zatížení Azure pro sondy stavu služby WSFC. To se liší od testu paměti pro naslouchací proces skupiny dostupnosti.

  ```PowerShell
  $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
  $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
  $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
  [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability grouop listener probe port.
  
  Import-Module FailoverClusters
  
  Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
  ```

  b. Nastavení parametrů clusteru spuštěním skriptu prostředí PowerShell na jednom z uzlů clusteru.  

>[!WARNING]
>Port sondy stavu naslouchacího procesu skupiny dostupnosti musí být odlišný od port clusteru core IP adresu stavu testu. V těchto příkladech na port naslouchacího procesu je 59999 a IP adresu clusteru core je 58888. Vyžadovat oba porty brány firewall pro příchozí pravidlo povolení.