Naslouchací proces skupiny dostupnosti je IP adresy a síťového názvu objektu který skupiny dostupnosti systému SQL Server naslouchá na. Pokud chcete vytvořit naslouchací proces skupiny dostupnosti, postupujte takto:

1. <a name="getnet"></a>Získání názvu síťovému prostředku clusteru.

    a. Pomocí protokolu RDP pro připojení k virtuální počítač Azure, který je hostitelem primární repliky. 

    b. Otevřete Správce clusteru převzetí služeb při selhání.

    c. Vyberte **sítě** uzlu a poznamenejte si název sítě s clustery. Tento název v používat `$ClusterNetworkName` proměnné ve skriptu prostředí PowerShell. Na následujícím obrázku je název sítě s clustery **clusteru sítě 1**:

   ![Název sítě s clustery](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

2. <a name="addcap"></a>Přidáte klientský přístupový bod.  
    Klientský přístupový bod je název sítě, kterou aplikace použít pro připojení k databázím ve skupině dostupnosti. Vytvořte klientský přístupový bod, ve Správci clusteru převzetí služeb při selhání.

    a. Rozbalte název clusteru a pak klikněte na tlačítko **role**.

    b. V **role** podokně klikněte pravým tlačítkem na název skupiny dostupnosti a potom vyberte **přidat prostředek** > **klientský přístupový bod**.

   ![Klientský přístupový bod](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. V **název** pole, vytvořte název pro tento nový naslouchací proces. 
   Název pro nový naslouchací proces je název sítě, kterou aplikace použít pro připojení k databázím ve skupině dostupnosti systému SQL Server.
   
    d. Vytváření naslouchací proces dokončíte, klikněte na tlačítko **Další** dvakrát a potom klikněte na **Dokončit**. Nelze zobrazit naslouchací proces nebo prostředek do online režimu v tomto okamžiku.

3. <a name="congroup"></a>Nakonfigurujte IP prostředků pro skupiny dostupnosti.

    a. Klikněte **prostředky** kartě a potom rozbalte klientský přístupový bod jste vytvořili.  
    Klientský přístupový bod je offline.

   ![Klientský přístupový bod](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Klikněte pravým tlačítkem na prostředek IP a potom klikněte na položku Vlastnosti. Poznamenejte si název IP adresu a použít ho v `$IPResourceName` proměnné ve skriptu prostředí PowerShell.

    c. V části **IP adresu**, klikněte na tlačítko **statickou IP adresu**. Nastavte adresu IP jako stejnou adresu, který jste použili při nastavení adresa služby Vyrovnávání zatížení na portálu Azure.

   ![Prostředek IP](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

4. <a name = "dependencyGroup"></a>Vytvořte prostředek skupiny dostupnosti systému SQL Server závislost na klientský přístupový bod.

    a. Ve Správci clusteru převzetí služeb při selhání, klikněte na tlačítko **role**a potom klikněte na vaší skupiny dostupnosti.

    b. Na **prostředky** v části **další prostředky**, klikněte pravým tlačítkem na skupinu dostupnosti prostředků a pak klikněte na tlačítko **vlastnosti**. 

    c. Na kartě závislosti přidejte název klienta prostředku přístup k bodu (naslouchací proces).

   ![Prostředek IP](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Klikněte na **OK**.

5. <a name="listname"></a>Zkontrolujte bodu prostředků přístupu klienta závislá na IP adresu.

    a. Ve Správci clusteru převzetí služeb při selhání, klikněte na tlačítko **role**a potom klikněte na vaší skupiny dostupnosti. 

    b. Na **prostředky** kartě, klikněte pravým tlačítkem na prostředek bodu přístupu klienta pod **název serveru**a potom klikněte na **vlastnosti**. 

   ![Prostředek IP](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Klikněte **závislosti** kartě. Ověřte, že IP adresa je závislost. Pokud není, nastavte závislost na IP adresu. Pokud uvedený více prostředků, ověřte, že IP adresy nebo Ne a závislosti. Klikněte na **OK**. 

   ![Prostředek IP](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    d. Klikněte pravým tlačítkem na název naslouchacího procesu a pak klikněte na **přepnout do režimu Online**. 

    >[!TIP]
    >Můžete ověřit, zda jsou správně nakonfigurovány závislosti. Přejděte ve Správci clusteru převzetí služeb při selhání pro role, klikněte pravým tlačítkem na skupinu dostupnosti, klikněte na tlačítko **další akce**a potom klikněte na **zobrazit sestavu závislostí**. Pokud jsou správně nakonfigurovány závislosti, skupina dostupnosti je závislá na název sítě a síťový název je závislá na IP adresu. 


6. <a name="setparam"></a>Nastavení parametrů clusteru v prostředí PowerShell.
    
    a. Zkopírujte následující skript prostředí PowerShell vaší instance systému SQL Server. Aktualizujte proměnné pro vaše prostředí.     
    
    ```PowerShell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>
    
    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```

    b. Nastavení parametrů clusteru spuštěním skriptu prostředí PowerShell na jednom z uzlů clusteru.  

    > [!NOTE]
    > Pokud vaše instance systému SQL Server v oblastech, musíte spustit skript prostředí PowerShell dvakrát. Při prvním použití `$ILBIP` a `$ProbePort` z první oblasti. Při druhém použít `$ILBIP` a `$ProbePort` z oblasti druhý. Název sítě clusteru a název prostředku IP clusteru jsou stejné. 
