---
title: Kurz – Azure Virtual Machines – skupin dostupnosti pro SQL Server | Dokumentace Microsoftu
description: Tento kurz ukazuje, jak vytvořit SQL Server vždy skupinu dostupnosti v Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mikeray
ms.openlocfilehash: 65ccf45ea8ea1f8f553be0b2c599f5c1433fc3e8
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359710"
---
# <a name="configure-always-on-availability-group-in-azure-vm-manually"></a>Konfigurace skupiny dostupnosti Always On na virtuálním počítači Azure ručně

Tento kurz ukazuje, jak vytvořit SQL Server vždy skupinu dostupnosti v Azure Virtual Machines. Úplný kurz vytvoří skupinu dostupnosti u repliky databáze na dva servery SQL.

**Časový odhad**: K dokončení po splnění požadavků zabere přibližně 30 minut.

Diagram znázorňuje, co vytvoříte v tomto kurzu.

![Skupina dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Požadavky

Kurz předpokládá, že máte základní znalosti o SQL serveru skupin dostupnosti Always On. Pokud potřebujete další informace, přečtěte si [přehled vždy na skupin dostupnosti (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).

V následující tabulce jsou uvedeny požadavky, které je potřeba dokončit před zahájením tohoto kurzu:

|  |Požadavek |Popis |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Two SQL Servers | – Ve skupině dostupnosti Azure <br/> – V jedné doméně <br/> -S nainstalovanou funkcí Clustering převzetí služeb při selhání |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Sdílení souborů pro cluster s kopií clusteru |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Účet služby SQL Server | Účet domény |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Účet služby agenta systému SQL Server | Účet domény |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Otevřete porty brány firewall | - SQL Server: **1433** pro výchozí instanci <br/> -Koncovým bodem zrcadlení databáze: **5022** nebo jakýkoli dostupný port <br/> – Sonda stavu IP adresu nástroje pro vyrovnávání zatížení dostupnost skupiny: **59999** nebo jakýkoli dostupný port <br/> – Nástroje pro vyrovnávání zatížení core, IP sondu stavu adresy clusteru: **58888** nebo jakýkoli dostupný port |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Přidání funkce Clustering převzetí služeb při selhání | Tato funkce vyžaduje oba servery SQL |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Účet domény pro instalaci | – Místní správce na všech serverech SQL <br/> – Správce sysadmin systému SQL Server pevné role serveru pro každou instanci systému SQL Server  |


Před zahájením tohoto kurzu, budete muset [splnit požadavky pro vytváření skupin dostupnosti Always On v Azure Virtual Machines](virtual-machines-windows-portal-sql-availability-group-prereq.md). Pokud tyto požadavky jsou již dokončen, můžete přejít na [vytvořit Cluster](#CreateCluster).

  >[!NOTE]
  > Celá řada kroků v tomto kurzu je možné automatizovat pomocí šablony Azure Quickstart. Další informace najdete v tématu [vytvoření služby WSFC, naslouchací proces a nakonfigurujte ILB pro skupiny dostupnosti Always On na virtuálním počítači SQL serveru pomocí šablon Azure Quickstart](virtual-machines-windows-sql-availability-group-quickstart-template.md).


<!--**Procedure**: *This is the first “step”. Make titles H2’s and short and clear – H2’s appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## <a name="create-the-cluster"></a>Vytvoření clusteru

Po dokončení požadavků, prvním krokem je vytvoření clusteru převzetí služeb při selhání ve Windows serveru zahrnuje dva servery SQL Server a server s kopií clusteru.

1. RDP na prvním serveru SQL Server pomocí účtu domény, který je správcem na serverech SQL i na serveru s kopií clusteru.

   >[!TIP]
   >Pokud jste postupovali podle [dokument s požadavky](virtual-machines-windows-portal-sql-availability-group-prereq.md), vytvoří účet s názvem **CORP\Install**. Pomocí tohoto účtu.

2. V **správce serveru** řídicího panelu, vyberte **nástroje**a potom klikněte na tlačítko **Správce clusteru převzetí služeb při selhání**.
3. V levém podokně klikněte pravým tlačítkem na **Správce clusteru převzetí služeb při selhání**a potom klikněte na tlačítko **vytvořit Cluster**.
   ![Vytvoření clusteru](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. V Průvodci vytvořením clusteru s vytvořením clusteru s jedním uzlem krokování na stránkách s nastavením v následující tabulce:

   | Stránka | Nastavení |
   | --- | --- |
   | Než začnete |Použít výchozí nastavení |
   | Vybrat servery |Zadejte název prvního serveru SQL Server v **zadejte název serveru** a klikněte na tlačítko **přidat**. |
   | Upozornění ověření |Vyberte **číslo pro tento cluster nepožadujete podporu od Microsoftu a proto nechcete, aby ke spuštění ověřovacích testů. Když kliknu na další, pokračovat ve vytváření clusteru**. |
   | Přístupový bod pro správu clusteru |Zadejte název clusteru, například **SQLAGCluster1** v **název clusteru**.|
   | Potvrzení |Použít výchozí hodnoty, pokud používáte prostory úložiště. Přečtěte si poznámku za touto tabulkou. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Nastavit IP adresu clusteru převzetí služeb při selhání Windows serveru

1. V **Správce clusteru převzetí služeb při selhání**, přejděte dolů k položce **základní prostředky clusteru** a rozbalte podrobnosti o clusteru. Byste měli vidět oba **název** a **IP adresu** prostředky v **neúspěšné** stavu. Prostředek IP adresy nelze uvést do režimu online vzhledem k tomu, že cluster je přiřazen jako celý počítač stejnou IP adresu, a proto je duplicitní adresa.

2. Klikněte pravým tlačítkem na neúspěšný **IP adresu** prostředku a pak klikněte na tlačítko **vlastnosti**.

   ![Vlastnosti clusteru](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Vyberte **statickou IP adresu** a zadejte dostupnou adresu ze stejné podsítě jako vaše virtuální počítače.

4. V **základní prostředky clusteru** části, klikněte pravým tlačítkem na název clusteru a klikněte na tlačítko **přepnout do režimu Online**. Potom počkejte, dokud se oba prostředky jsou online. Když prostředek názvu clusteru převede do režimu online, aktualizace serveru řadiče domény s novým účtem AD počítače. Pomocí tohoto účtu AD později spuštění služby skupiny dostupnosti v clusteru.

### <a name="addNode"></a>Přidat do clusteru SQL serveru

Přidáte do clusteru SQL serveru.

1. Ve stromové struktuře prohlížeče, klikněte pravým tlačítkem na cluster a klikněte na tlačítko **přidat uzel**.

    ![Přidání uzlu do clusteru](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. V **Průvodce přidáním uzlu**, klikněte na tlačítko **Další**. V **zvolit servery** stránce přidat druhý Server SQL. Zadejte název serveru v **zadejte název serveru** a potom klikněte na tlačítko **přidat**. Jakmile budete hotovi, klikněte na tlačítko **Další**.

1. V **upozornění ověření** klikněte na **ne** (v případě produkčního prostředí byste měli provést ověřovací testy). Pak klikněte na **Další**.

8. V **potvrzení** stránky, pokud používáte prostory úložiště, zrušte zaškrtnutí políčka popisek **přidat do clusteru veškeré oprávněné úložiště.**

   ![Přidat uzel potvrzení](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Pokud používáte prostory úložiště a není zrušte zaškrtnutí políčka **přidat do clusteru veškeré oprávněné úložiště**, Windows odpojí virtuální disky se během procesu clusteringu. Proto dokud prostory úložiště se odebral z clusteru se nezobrazují ve Správci disků nebo Průzkumníka a znovu připojit pomocí Powershellu. Prostory úložiště seskupit několik disků do fondů úložiště. Další informace najdete v tématu [prostory úložiště](https://technet.microsoft.com/library/hh831739).

1. Klikněte na **Další**.

1. Klikněte na **Dokončit**.

   Správce clusteru převzetí služeb při selhání ukazuje, že se nový uzel clusteru a zobrazí ho v **uzly** kontejneru.

10. Odhlaste se z relace vzdálené plochy.

### <a name="add-a-cluster-quorum-file-share"></a>Přidání sdílené složky kvora clusteru

V tomto příkladu Windows cluster používá sdílené složky k vytvoření kvora clusteru. Tento kurz používá většina uzlů a sdílených sdílené složky kvora. Další informace najdete v tématu [Principy konfigurací kvora v clusteru převzetí služeb při selhání](https://technet.microsoft.com/library/cc731739.aspx).

1. Připojení k serveru soubor sdílenou složku s kopií clusteru člen pomocí relací vzdálené plochy.

1. Na **správce serveru**, klikněte na tlačítko **nástroje**. Otevřít **Správa počítače**.

1. Klikněte na tlačítko **sdílené složky**.

1. Klikněte pravým tlačítkem na **sdílené složky**a klikněte na tlačítko **novou sdílenou složku...** .

   ![Nová sdílená složka](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Použití **Průvodce vytvořením sdílené složky** vytvoření sdílené složky.

1. Na **cesta ke složce**, klikněte na tlačítko **Procházet** a najděte nebo vytvořte cestu ke sdílené složce. Klikněte na **Další**.

1. V **název, popis a nastavení** ověřte název sdílené složky a cesta. Klikněte na **Další**.

1. Na **oprávnění ke sdílené složce** nastavit **přizpůsobit oprávnění**. Klikněte na tlačítko **vlastní...** .

1. Na **upravit oprávnění**, klikněte na tlačítko **přidat...** .

1. Ujistěte se, že účet používaný k vytvoření clusteru má plnou kontrolu.

   ![Nová sdílená složka](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Klikněte na **OK**.

1. V **oprávnění ke sdílené složce**, klikněte na tlačítko **Dokončit**. Klikněte na tlačítko **Dokončit** znovu.  

1. Odhlaste se z serveru

### <a name="configure-cluster-quorum"></a>Konfigurace kvora clusteru

Dále nastavte kvorum clusteru.

1. Připojte se k prvním uzlu clusteru pomocí vzdálené plochy.

1. V **Správce clusteru převzetí služeb při selhání**, klikněte pravým tlačítkem na cluster, přejděte na **další akce**a klikněte na tlačítko **konfigurovat nastavení kvora clusteru...** .

   ![Nová sdílená složka](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. V **Průvodce konfigurací kvora clusteru**, klikněte na tlačítko **Další**.

1. V **vybrat možnosti konfigurace kvora**, zvolte **vybrat určující disk kvora**a klikněte na tlačítko **Další**.

1. Na **vybrat určující disk kvora**, klikněte na tlačítko **nakonfigurovat určující sdílenou složku souboru**.

   >[!TIP]
   >Windows Server 2016 podporuje Cloudová kopie clusteru. Pokud se rozhodnete tento typ určující sdílené složky, není nutné soubor sdílet s kopií clusteru. Další informace najdete v tématu [nasazení cloudové kopie clusteru pro převzetí služeb při selhání clusteru](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). Tento kurz používá určující sdílenou složku souboru, který není podporovaný ve starších operačních systémech.

1. Na **nakonfigurovat určující sdílenou složku**, zadejte cestu ke sdílené složce, které jste vytvořili. Klikněte na **Další**.

1. Ověřte nastavení na **potvrzení**. Klikněte na **Další**.

1. Klikněte na **Dokončit**.

Základní prostředky clusteru se nakonfigurují se určující sdílená složka.

## <a name="enable-availability-groups"></a>Povolte skupiny dostupnosti

Dál povolte **skupin dostupnosti AlwaysOn** funkce. Proveďte tyto kroky na obou serverech SQL.

1. Z **Start** obrazovky, spusťte **Správce konfigurace systému SQL Server**.
2. Ve stromové struktuře prohlížeče klikněte na tlačítko **služby SQL Server**, klepněte pravým tlačítkem myši **SQL Server (MSSQLSERVER)** služby a klikněte na tlačítko **vlastnosti**.
3. Klikněte na tlačítko **vysoká dostupnost AlwaysOn** kartu a potom vyberte **povolte skupiny dostupnosti AlwaysOn**, následujícím způsobem:

    ![Povolte skupiny dostupnosti AlwaysOn](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Klikněte na tlačítko **Použít**. Klikněte na tlačítko **OK** v místním dialogovém okně.

5. Restartujte službu SQL Server.

Opakujte tyto kroky na jiném SQL serveru.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an Availability Group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the Availability Groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, click **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and click **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and click **Next**.
6. In the **Profile** page, accept the default settings and click **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then click **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>Vytvoření databáze na prvním serveru SQL

1. Spusťte soubor RDP na prvním serveru SQL Server pomocí účtu domény, který je členem pevné role serveru sysadmin.
1. Otevřete SQL Server Management Studio a připojte se k první SQL serveru.
7. V **Průzkumník objektů**, klikněte pravým tlačítkem na **databází** a klikněte na tlačítko **novou databázi**.
8. V **název_databáze**, typ **MyDB1**, pak klikněte na tlačítko **OK**.

### <a name="backupshare"></a> Vytvoření sdílené složky záloh

1. Na prvním serveru SQL v **správce serveru**, klikněte na tlačítko **nástroje**. Otevřít **Správa počítače**.

1. Klikněte na tlačítko **sdílené složky**.

1. Klikněte pravým tlačítkem na **sdílené složky**a klikněte na tlačítko **novou sdílenou složku...** .

   ![Nová sdílená složka](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Použití **Průvodce vytvořením sdílené složky** vytvoření sdílené složky.

1. Na **cesta ke složce**, klikněte na tlačítko **Procházet** a vyhledat nebo vytvořit cestu pro sdílenou složku záloh databáze. Klikněte na **Další**.

1. V **název, popis a nastavení** ověřte název sdílené složky a cesta. Klikněte na **Další**.

1. Na **oprávnění ke sdílené složce** nastavit **přizpůsobit oprávnění**. Klikněte na tlačítko **vlastní...** .

1. Na **upravit oprávnění**, klikněte na tlačítko **přidat...** .

1. Ujistěte se, že účty služby SQL Server a agenta systému SQL Server pro oba servery mít plnou kontrolu.

   ![Nová sdílená složka](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Klikněte na **OK**.

1. V **oprávnění ke sdílené složce**, klikněte na tlačítko **Dokončit**. Klikněte na tlačítko **Dokončit** znovu.  

### <a name="take-a-full-backup-of-the-database"></a>Vytvořit úplnou zálohu databáze

Musíte zálohovat databázi nové inicializace řetězce protokolu. Pokud neprovedete zálohování nové databáze, nemůže být součástí skupiny dostupnosti.

1. V **Průzkumník objektů**, klikněte pravým tlačítkem na databázi, přejděte na **úlohy...** , klikněte na tlačítko **zálohování**.

1. Klikněte na tlačítko **OK** úplné zálohování pro výchozí záložní umístění.

## <a name="create-the-availability-group"></a>Vytvoření skupiny dostupnosti
Nyní jste připraveni ke konfiguraci skupiny dostupnosti. použijte následující postup:

* Vytvoření databáze na prvním serveru SQL.
* Provést úplnou zálohu a zálohu transakčního protokolu databáze
* Obnovit úplnou zálohu a zálohu protokolu na druhém serveru SQL Server s **NORECOVERY** možnost
* Vytvoření skupiny dostupnosti (**AG1**) se synchronním potvrzováním automatické převzetí služeb při selhání a čitelné sekundární repliky

### <a name="create-the-availability-group"></a>Vytvoření skupiny dostupnosti:

1. V relaci vzdálené plochy na prvním serveru SQL Server. V **Průzkumník objektů** v aplikaci SSMS klikněte pravým tlačítkem na **vysoká dostupnost AlwaysOn** a klikněte na tlačítko **Průvodce novou skupinou dostupnosti**.

    ![Spuštění Průvodce vytvořením skupiny dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. V **ÚVOD** klikněte na **Další**. V **zadejte název skupiny dostupnosti** stránky, zadejte název pro skupinu dostupnosti, například **AG1**v **název skupiny dostupnosti**. Klikněte na **Další**.

    ![Průvodce vytvořením skupin dostupnosti, zadejte název AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. V **vybrat databáze** stránky, vyberte svou databázi a klikněte na tlačítko **Další**.

   >[!NOTE]
   >Databáze splňuje požadavky pro skupinu dostupnosti, protože jste provedli aspoň jednu úplnou zálohu na zamýšlený primární replice.

   ![Průvodce novou skupinu dostupnosti, vyberte databáze](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. V **zadejte repliky** klikněte na **přidat repliky**.

   ![Průvodce vytvořením skupin dostupnosti, zadejte repliky](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. **Připojit k serveru** otevře se dialogové okno. Zadejte název druhého serveru v **název serveru**. Klikněte na **Připojit**.

   Zpátky **zadat repliky** stránky, měli byste vidět uvedené v druhém serveru **replik dostupnosti**. Konfigurace replik následujícím způsobem.

   ![Průvodce vytvořením skupin dostupnosti, zadejte repliky (dokončeno)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Klikněte na tlačítko **koncové body** zobrazíte koncovým bodem zrcadlení pro tuto skupinu dostupnosti databáze. Použijte stejný port, který jste použili při nastavení [pravidlo brány firewall pro koncové body zrcadlení databáze](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

    ![Průvodce novou skupinu dostupnosti, vyberte počáteční Data synchronizace](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. V **vybrat počáteční synchronizaci dat** stránce **úplné** a zadejte do sdíleného síťového umístění. Pro umístění, použijte [zálohování sdílené složky, kterou jste vytvořili](#backupshare). V příkladu pochází  **\\ \\ \<první Server SQL\>\Backup\\**. Klikněte na **Další**.

   >[!NOTE]
   >Úplná synchronizace trvá úplné zálohování databáze na první instanci systému SQL Server a obnoví k druhé instanci. U velkých databází úplnou synchronizaci nedoporučuje, protože to může trvat dlouhou dobu. Nyní můžete snížit ruční vytvoření zálohy databáze a obnovení s `NO RECOVERY`. Pokud již obnovení databáze s `NO RECOVERY` na druhém serveru SQL před konfigurací skupiny dostupnosti, zvolte **připojit pouze k**. Pokud chcete vytvořit zálohu po dokončení konfigurace skupiny dostupnosti, zvolte **přeskočit počáteční synchronizaci**.

    ![Průvodce novou skupinu dostupnosti, vyberte počáteční Data synchronizace](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. V **ověření** klikněte na **Další**. Tato stránka by měla vypadat podobně jako na následujícím obrázku:

    ![Průvodce novým AG, ověření](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >Protože jste nenakonfigurovali naslouchací proces skupiny dostupnosti existuje upozornění. pro konfiguraci naslouchacího procesu. Toto upozornění můžete ignorovat, protože ve službě Azure virtual machines můžete vytvořit naslouchací proces po vytvoření nástroje pro vyrovnávání zatížení Azure.

10. V **Souhrn** klikněte na **Dokončit**, počkejte, než Průvodce konfiguruje novou skupinu dostupnosti. V **průběh** stránky, můžete kliknout na **podrobnosti** zobrazíte podrobné průběh. Po dokončení průvodce se kontrolovat **výsledky** stránku k ověření úspěšného vytvoření skupiny dostupnosti.

     ![Výsledky průvodce AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Klikněte na tlačítko **Zavřít** ukončíte průvodce.

### <a name="check-the-availability-group"></a>Zkontrolujte skupiny dostupnosti

1. V **Průzkumník objektů**, rozbalte **vysoká dostupnost AlwaysOn**, potom rozbalte **skupin dostupnosti**. Teď byste měli vidět nové skupiny dostupnosti v tomto kontejneru. Klikněte pravým tlačítkem na skupinu dostupnosti a klikněte na tlačítko **zobrazit řídicí panel**.

   ![Zobrazit řídicí panel AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   Vaše **řídicí panel AlwaysOn** by měl vypadat nějak takto.

   ![Řídicí panel AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   Zobrazí se replik, režim převzetí služeb při selhání každou repliku a stav synchronizace.

2. V **Správce clusteru převzetí služeb při selhání**, klikněte na cluster. Vyberte **role**. Název skupiny dostupnosti, které jste použili je role v clusteru. Skupiny dostupnosti nemá IP adresu pro připojení klientů, protože jste nenakonfigurovali naslouchací proces. Po vytvoření služby Azure load balancer můžete nakonfigurovat naslouchací proces.

   ![Skupina dostupnosti v modulu Správce clusteru převzetí služeb při selhání](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Nepokoušejte se převzetí služeb při selhání skupiny dostupnosti ze Správce clusteru převzetí služeb při selhání. Všechny operace převzetí služeb při selhání by se provádět v rámci **řídicí panel AlwaysOn** v aplikaci SSMS. Další informace najdete v tématu [omezení na používání nástroje převzetí služeb při selhání clusteru Manager se skupinami dostupnosti](https://msdn.microsoft.com/library/ff929171.aspx).
    >

V tomto okamžiku máte skupinu dostupnosti s replikami na dvě instance systému SQL Server. Skupiny dostupnosti můžete přesunout mezi instancemi. Nemůžete připojit, do skupiny dostupnosti ještě protože není nutné naslouchací proces. Naslouchací proces ve službě Azure virtual machines, vyžaduje nástroj pro vyrovnávání zatížení. Dalším krokem je vytvoření nástroje pro vyrovnávání zatížení v Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení Azure

Skupiny dostupnosti SQL Server na virtuálních počítačích Azure, vyžaduje nástroj pro vyrovnávání zatížení. Nástroje pro vyrovnávání zatížení obsahuje IP adresy pro naslouchací procesy skupiny dostupnosti a převzetí služeb při selhání clusteru Windows serveru. Tento oddíl shrnuje, jak vytvořit nástroj pro vyrovnávání zatížení na webu Azure Portal.

Azure Load Balancer může být standardní nástroj pro vyrovnávání zatížení nebo základní nástroje pro vyrovnávání zatížení. Load balancer úrovně Standard nabízí víc funkcí než základní nástroj pro vyrovnávání zatížení. Pro skupinu dostupnosti je Load balancer úrovně Standard vyžaduje, když používáte zóně dostupnosti (namísto dostupnosti). Podrobnosti o rozdílech mezi typy nástroje pro vyrovnávání zatížení najdete v tématu [SKU nástroje pro vyrovnávání zatížení porovnání](../../../load-balancer/load-balancer-overview.md#skus).

1. Na webu Azure Portal, přejděte do skupiny prostředků, ve kterém jsou servery SQL a klikněte na **+ přidat**.
1. Vyhledejte **nástroj pro vyrovnávání zatížení**. Zvolte nástroj pro vyrovnávání zatížení vydávaný microsoftem.

   ![Skupina dostupnosti v modulu Správce clusteru převzetí služeb při selhání](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1. Klikněte na možnost **Vytvořit**.
1. Nakonfigurujte následující parametry nástroje pro vyrovnávání zatížení.

   | Nastavení | Pole |
   | --- | --- |
   | **Název** |Použít textový název nástroje pro vyrovnávání zatížení, například **sqlLB**. |
   | **Typ** |Interní |
   | **Virtuální síť** |Použijte název virtuální sítě Azure. |
   | **Podsíť** |Použijte název podsítě, která je virtuální počítač v.  |
   | **Přiřazení IP adresy** |Statická |
   | **IP adresa** |Použití dostupnou adresu z podsítě. Použijte tuto adresu pro vaše naslouchacího procesu skupiny dostupnosti. Všimněte si, že se liší od IP adresu clusteru.  |
   | **Předplatné** |Použijte stejné předplatné jako virtuální počítač. |
   | **Umístění** |Použijte stejné umístění jako virtuální počítač. |

   Okno Azure portal by měl vypadat nějak takto:

   ![Vytvoření nástroje pro vyrovnávání zatížení](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Klikněte na tlačítko **vytvořit**, k vytvoření nástroje pro vyrovnávání zatížení.

Ke konfiguraci nástroje pro vyrovnávání zatížení, budete muset vytvořit back-endový fond, test a nastavte pravidla Vyrovnávání zatížení. Provádějí na webu Azure Portal.

### <a name="add-backend-pool-for-the-availability-group-listener"></a>Přidat back-endový fond pro naslouchací proces skupiny dostupnosti

1. Na webu Azure Portal přejděte do skupiny dostupnosti. Vám může být nutné aktualizovat zobrazení, abyste viděli nástroje pro vyrovnávání zatížení nově vytvořený.

   ![Najít nástroj pro vyrovnávání zatížení ve skupině prostředků](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Klikněte na nástroj pro vyrovnávání zatížení, klikněte na tlačítko **back-endové fondy**a klikněte na tlačítko **+ přidat**.

1. Zadejte název pro back-endový fond.

1. Back-endového fondu přidružte skupinu dostupnosti, která obsahuje virtuální počítače.

1. V části **cílové konfigurace protokolu IP sítě**, zkontrolujte **VIRTUÁLNÍHO počítače** a zvolit oba virtuální počítače, které budou hostiteli replik skupin dostupnosti. Nezahrnují souborovém serveru sdílenou složku s kopií clusteru.

   >[!NOTE]
   >Pokud nejsou zadány oba virtuální počítače, připojení bude úspěšné pouze k primární replice.

1. Klikněte na tlačítko **OK** vytvořit back-endový fond.

### <a name="set-the-probe"></a>Nastavení testu

1. Klikněte na nástroj pro vyrovnávání zatížení, klikněte na tlačítko **sondy stavu**a klikněte na tlačítko **+ přidat**.

1. Sonda stavu naslouchací proces nastavte následujícím způsobem:

   | Nastavení | Popis | Příklad:
   | --- | --- |---
   | **Název** | Text | SQLAlwaysOnEndPointProbe |
   | **Protokol** | Zvolte možnost TCP | TCP |
   | **Port** | Žádné nevyužité portu | 59999 |
   | **Interval**  | Množství času mezi pokusy o testování v řádu sekund |5 |
   | **Prahová hodnota špatného stavu** | Počet po sobě jdoucích test selhání, které se musí vyskytovat pro virtuální počítač, aby se považoval za poškozený  | 2 |

1. Klikněte na tlačítko **OK** nastavit sondu stavu.

### <a name="set-the-load-balancing-rules"></a>Nastavte pravidla Vyrovnávání zatížení

1. Klikněte na nástroj pro vyrovnávání zatížení, klikněte na tlačítko **pravidla Vyrovnávání zatížení**a klikněte na tlačítko **+ přidat**.

1. Nastavte následujícím způsobem pravidel Vyrovnávání zatížení naslouchacího procesu.
   | Nastavení | Popis | Příklad:
   | --- | --- |---
   | **Název** | Text | SQLAlwaysOnEndPointListener |
   | **Front-endové IP adresy** | Zvolte adresu |Použijte adresu, kterou jste vytvořili při vytvoření nástroje pro vyrovnávání zatížení. |
   | **Protokol** | Zvolte možnost TCP |TCP |
   | **Port** | Použijte port pro naslouchací proces skupiny dostupnosti | 1433 |
   | **Back-endový Port** | Toto pole se nepoužívá při plovoucí IP adresa je nastavená pro přímá odpověď ze serveru vrácené | 1433 |
   | **Test paměti** |Název, který jste zadali pro test paměti | SQLAlwaysOnEndPointProbe |
   | **Trvalost relace** | Rozevírací seznam | **Žádné** |
   | **Časový limit nečinnosti** | Otevřete minut pro uchování připojení TCP | 4 |
   | **Plovoucí IP adresa (přímá odpověď ze serveru vrácené)** | |Povoleno |

   > [!WARNING]
   > Přímá odpověď ze serveru vrácené je nastavit během vytváření. Název není možné změnit.

1. Klikněte na tlačítko **OK** nastavit pravidla Vyrovnávání zatížení naslouchacího procesu.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Přidat IP adresu clusteru core pro Windows Server Failover Cluster (WSFC)

Služby WSFC IP adresa musí být také v nástroji pro vyrovnávání zatížení.

1. Na portálu na stejném Azure load balancer, klikněte na tlačítko **konfigurace protokolu IP front-endu** a klikněte na tlačítko **+ přidat**. Použijte IP adresu, které jste nakonfigurovali pro služby WSFC v základní prostředky clusteru. Nastavte adresu IP jako statické.

1. V nástroji pro vyrovnávání zatížení, klikněte na tlačítko **sondy stavu**a klikněte na tlačítko **+ přidat**.

1. Sonda stavu na IP adresu služby WSFC clusteru core nastavte následujícím způsobem:

   | Nastavení | Popis | Příklad:
   | --- | --- |---
   | **Název** | Text | WSFCEndPointProbe |
   | **Protokol** | Zvolte možnost TCP | TCP |
   | **Port** | Žádné nevyužité portu | 58888 |
   | **Interval**  | Množství času mezi pokusy o testování v řádu sekund |5 |
   | **Prahová hodnota špatného stavu** | Počet po sobě jdoucích test selhání, které se musí vyskytovat pro virtuální počítač, aby se považoval za poškozený  | 2 |

1. Klikněte na tlačítko **OK** nastavit sondu stavu.

1. Nastavte pravidla Vyrovnávání zatížení. Klikněte na tlačítko **pravidla Vyrovnávání zatížení**a klikněte na tlačítko **+ přidat**.

1. Nastavte následujícím způsobem pravidla Vyrovnávání zatížení adresa IP clusteru core.
   | Nastavení | Popis | Příklad:
   | --- | --- |---
   | **Název** | Text | WSFCEndPoint |
   | **Front-endové IP adresy** | Zvolte adresu |Použijte adresu, kterou jste vytvořili při konfiguraci služby WSFC IP adresu. Tím se liší od IP adresu naslouchacího procesu |
   | **Protokol** | Zvolte možnost TCP |TCP |
   | **Port** | Použijte port pro IP adresu clusteru. Toto je dostupný port, který se nepoužívá pro test paměti port naslouchacího procesu. | 58888 |
   | **Back-endový Port** | Toto pole se nepoužívá při plovoucí IP adresa je nastavená pro přímá odpověď ze serveru vrácené | 58888 |
   | **Test paměti** |Název, který jste zadali pro test paměti | WSFCEndPointProbe |
   | **Trvalost relace** | Rozevírací seznam | **Žádné** |
   | **Časový limit nečinnosti** | Otevřete minut pro uchování připojení TCP | 4 |
   | **Plovoucí IP adresa (přímá odpověď ze serveru vrácené)** | |Povoleno |

   > [!WARNING]
   > Přímá odpověď ze serveru vrácené je nastavit během vytváření. Název není možné změnit.

1. Klikněte na tlačítko **OK** nastavit pravidla Vyrovnávání zatížení.

## <a name="configure-listener"></a> Nakonfigurujte naslouchací proces

Dalším krokem je konfigurace naslouchacího procesu skupiny dostupnosti na clusteru pro převzetí služeb při selhání.

> [!NOTE]
> Tento kurz ukazuje, jak vytvořit jeden naslouchací proces – jeden ILB IP adresou. Vytvoření jednoho nebo víc naslouchacích procesů pomocí jednoho nebo víc IP adres najdete v tématu [skupiny dostupnosti vytvořit naslouchací proces a nástroje pro vyrovnávání zatížení | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Nastavit port naslouchacího procesu

V systému SQL Server Management Studio nastavte na port naslouchacího procesu.

1. Spusťte SQL Server Management Studio a připojte se k primární replice.

1. Přejděte do **AlwaysOn vysokou dostupnost** | **skupin dostupnosti** | **naslouchacích procesů skupin dostupnosti**.

1. Teď byste měli vidět název naslouchacího procesu, který jste vytvořili v modulu Správce clusteru převzetí služeb při selhání. Klikněte pravým tlačítkem na název naslouchacího procesu a klikněte na tlačítko **vlastnosti**.

1. V **Port** pole, zadejte číslo portu naslouchacího procesu skupiny dostupnosti. Výchozí je 1433, pak klikněte na tlačítko **OK**.

Nyní máte skupinu dostupnosti SQL Server v Azure virtuální počítače spuštěné v režimu Resource Manageru.

## <a name="test-connection-to-listener"></a>Test připojení k naslouchacímu procesu

Chcete-li otestovat připojení:

1. Připojení RDP k serveru SQL Server, který je ve stejné virtuální síti, ale není vlastníkem repliky. Použít jiný SQL Server v clusteru.

1. Použití **sqlcmd** nástroj k testování připojení. Například následující skript vytvoří **sqlcmd** připojení k primární replice prostřednictvím naslouchací proces s ověřováním Windows:

  ```cmd
  sqlcmd -S <listenerName> -E
  ```

  Pokud je naslouchací proces používá jiný port než výchozí port (1433), zadejte port, který v připojovacím řetězci. Například následující příkaz sqlcmd se připojí k naslouchání na portu 1435:

  ```cmd
  sqlcmd -S <listenerName>,1435 -E
  ```

Připojení SQLCMD se automaticky připojí k libovolným instance systému SQL Server hostuje primární repliku.

> [!TIP]
> Ujistěte se, že je otevřen v bráně firewall oba servery SQL port, který zadáte. Oba servery vyžadují příchozí pravidlo pro port TCP, který používáte. Další informace najdete v tématu [přidat nebo upravit pravidlo brány Firewall](https://technet.microsoft.com/library/cc753558.aspx).

## <a name="next-steps"></a>Další postup

- [Přidat IP adresu nástroji pro vyrovnávání zatížení pro skupinu dostupnosti druhý](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).
