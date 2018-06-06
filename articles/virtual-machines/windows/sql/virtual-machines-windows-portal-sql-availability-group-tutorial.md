---
title: Skupiny dostupnosti SQL serveru – virtuální počítače Azure – kurz | Microsoft Docs
description: Tento kurz ukazuje, jak vytvořit Server vždy na skupinu dostupnosti SQL ve virtuálních počítačích Azure.
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
ms.date: 05/09/2017
ms.author: mikeray
ms.openlocfilehash: 8796cd3224670c6d1c8b1b3c6da8d1c096b01d03
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716716"
---
# <a name="configure-always-on-availability-group-in-azure-vm-manually"></a>Konfigurovat vždy na skupiny dostupnosti ve virtuálním počítači Azure ručně

Tento kurz ukazuje, jak vytvořit Server vždy na skupinu dostupnosti SQL ve virtuálních počítačích Azure. Dokončení kurzu vytvoří skupinu dostupnosti s repliku databáze na dva servery SQL.

**Odhad času**: dokončení po splnění předpokladů trvá asi 30 minut.

Diagram znázorňuje, co vytvoříte v tomto kurzu.

![Skupiny dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Požadavky

Kurz předpokládá, že máte základní znalosti o SQL serveru skupin dostupnosti Always On. Pokud potřebujete další informace, přečtěte si [přehled o skupin dostupnosti Always On (SQL Server)](http://msdn.microsoft.com/library/ff877884.aspx).

Následující tabulka uvádí požadavky, které je potřeba provést před zahájením tohoto kurzu:

|  |Požadavek |Popis |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Dva servery SQL | -V nastavení dostupnosti Azure <br/> -V jedné doméně <br/> -S nainstalovanou funkcí Clustering převzetí služeb při selhání |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Sdílení souborů pro cluster s kopií clusteru |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Účet služby SQL Server | Účet domény |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Účet služby agenta systému SQL Server | Účet domény |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Otevřete porty brány firewall | -SQL Server: **1433** pro výchozí instanci <br/> -Koncový bod zrcadlení databáze: **5022** nebo jakýkoli dostupný port <br/> -Sondu nástroje pro vyrovnávání zatížení azure: **59999** nebo jakýkoli dostupný port |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Přidejte funkci Clustering převzetí služeb při selhání | Tato funkce vyžaduje oba servery SQL |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Účet domény instalace | – Místní správce na každém serveru SQL <br/> -Členem systému SQL Server pevné role serveru sysadmin pro každou instanci systému SQL Server  |


Než začnete tento kurz, budete muset [dokončení požadované součásti pro vytváření skupin dostupnosti Always On v Azure Virtual Machines](virtual-machines-windows-portal-sql-availability-group-prereq.md). Pokud tyto požadavky jsou již dokončena, můžete přejít na [vytvořením clusteru](#CreateCluster).


<!--**Procedure**: *This is the first “step”. Make titles H2’s and short and clear – H2’s appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## Vytvoření clusteru

Po dokončení požadavky prvním krokem je vytvoření clusteru převzetí služeb při selhání Windows serveru, který obsahuje dva servery SQL Server a server s kopií clusteru.

1. RDP k první systému SQL Server pomocí účtu domény, který je správcem na serverech SQL i na serveru s kopií clusteru.

   >[!TIP]
   >Pokud jste postupovali podle [požadovaný dokument](virtual-machines-windows-portal-sql-availability-group-prereq.md), vytvořili účet názvem **CORP\Install**. Pomocí tohoto účtu.

2. V **správce serveru** řídicí panel, vyberte **nástroje**a potom klikněte na **Správce clusteru převzetí služeb při selhání**.
3. V levém podokně klikněte pravým tlačítkem na **Správce clusteru převzetí služeb při selhání**a potom klikněte na **vytvoření clusteru s podporou**.
   ![Vytvoření clusteru](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. V Průvodci vytvořením clusteru vytvořte jednouzlového clusteru procházení stránek s nastavením v následující tabulce:

   | Stránka | Nastavení |
   | --- | --- |
   | Než začnete |Použít výchozí nastavení |
   | Vyberte servery |Zadejte první název systému SQL Server v **zadejte název serveru** a klikněte na tlačítko **přidat**. |
   | Upozornění ověření |Vyberte **ne. I nevyžadují podporu společnosti Microsoft pro tento cluster a proto nechcete, aby ke spuštění ověřovacích testů. Po klepnutí na tlačítko Další, pokračovat ve vytváření clusteru**. |
   | Přístupový bod pro správu clusteru |Zadejte název clusteru, například **SQLAGCluster1** v **název clusteru**.|
   | Potvrzení |Použít výchozí hodnoty, pokud nepoužíváte prostory úložiště. Přečtěte si poznámku za touto tabulkou. |

### <a name="set-the-cluster-ip-address"></a>Nastavte IP adresu clusteru

1. V **Správce clusteru převzetí služeb při selhání**, přejděte dolů k položce **základní prostředky clusteru** a rozbalte podrobnosti o clusteru. Měli byste vidět, jak **název** a **IP adresu** prostředky v **se nezdařilo** stavu. Prostředek IP adresy není možné připojit online, protože clusteru je přiřazen stejnou IP adresu jako počítač sám sebe, a proto je duplicitní adresu.

2. Klikněte pravým tlačítkem na chybných **IP adresu** prostředek a pak klikněte na tlačítko **vlastnosti**.

   ![Vlastnosti clusteru](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Vyberte **statickou IP adresu** a zadejte dostupnou adresu od privátní IP adresy () rozsahu AUTOMATIC: 169.254.0.1 až 169.254.255.254 do textového pole adresy. V tomto příkladu můžete použít libovolnou adresu v tomto rozsahu. Například `169.254.0.1`. Potom klikněte na **OK**.

4. V **základní prostředky clusteru** části, klikněte pravým tlačítkem na název clusteru a klikněte na tlačítko **přepnout do režimu Online**. Potom počkejte, dokud jsou obě prostředky online. Při přechodu prostředek názvu clusteru do režimu online, se nový účet počítače AD aktualizuje serveru řadiče domény. Pomocí tohoto účtu AD později spustit služba skupiny dostupnosti v clusteru.

### <a name="addNode"></a>Přidat SQL Server do clusteru

SQL Server přidáte do clusteru.

1. Ve stromu prohlížeče klikněte pravým tlačítkem na cluster a klikněte na tlačítko **přidat uzel**.

    ![Přidání uzlu do clusteru](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. V **Průvodce přidáním uzlu**, klikněte na tlačítko **Další**. V **zvolit servery** stránky, přidejte druhý Server SQL. Zadejte název serveru v **zadejte název serveru** a pak klikněte na **přidat**. Až budete hotovi, klikněte na tlačítko **Další**.

1. V **upozornění ověření** klikněte na tlačítko **ne** (v produkční scénář je vhodné provést testy pro ověření). Pak klikněte na **Další**.

8. V **potvrzení** stránky, pokud používáte prostory úložiště, zrušte zaškrtnutí tohoto políčka s názvem bez přípony **přidat do clusteru veškeré oprávněné úložiště.**

   ![Přidání uzlu potvrzení](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Pokud používáte prostory úložiště a není zrušte zaškrtnutí políčka **přidat do clusteru veškeré oprávněné úložiště**, systému Windows umožňuje odpojit virtuální disky během procesu clusteringu. V důsledku toho se v Správce disků nebo v Průzkumníku nezobrazí, dokud prostory úložiště se odebral z clusteru a znovu připojit pomocí prostředí PowerShell. Prostory úložiště skupiny víc disků ve do fondů úložišť. Další informace najdete v tématu [prostory úložiště](https://technet.microsoft.com/library/hh831739).

1. Klikněte na **Další**.

1. Klikněte na **Dokončit**.

   Správce clusteru převzetí služeb při selhání ukazuje, že má nový uzel clusteru a jsou uvedené v **uzly** kontejneru.

10. Odhlaste se z této relaci vzdálené plochy.

### <a name="add-a-cluster-quorum-file-share"></a>Přidejte sdílenou složku kvora clusteru

V tomto příkladu clusteru systému Windows používá sdílenou složku k vytvoření kvora clusteru. Tento kurz používá většina uzlů a sdílených sdílené složky kvora. Další informace najdete v tématu [Principy konfigurací kvora v clusteru s podporou převzetí služeb při selhání](http://technet.microsoft.com/library/cc731739.aspx).

1. Připojte k serveru člen určující sdílené složky souboru s relaci vzdálené plochy.

1. Na **správce serveru**, klikněte na tlačítko **nástroje**. Otevřete **Správa počítače**.

1. Klikněte na tlačítko **sdílených složek**.

1. Klikněte pravým tlačítkem na **sdílené složky**a klikněte na tlačítko **novou sdílenou složku...** .

   ![Nové sdílené složky](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Použití **Průvodce vytvořením sdílené složky** vytvořit sdílenou složku.

1. Na **cesta ke složce**, klikněte na tlačítko **Procházet** a najít nebo vytvořit cestu pro sdílenou složku. Klikněte na **Další**.

1. V **název, popis a nastavení** ověřte název sdílené složky a cesta. Klikněte na **Další**.

1. Na **sdílené složky oprávnění** nastavit **přizpůsobit oprávnění**. Klikněte na tlačítko **vlastní...** .

1. Na **upravit oprávnění**, klikněte na tlačítko **přidat...** .

1. Ujistěte se, že účet použitý k vytvoření clusteru má plnou kontrolu.

   ![Nové sdílené složky](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Klikněte na **OK**.

1. V **sdílené složky oprávnění**, klikněte na tlačítko **Dokončit**. Klikněte na tlačítko **Dokončit** znovu.  

1. Odhlaste se ze serveru

### <a name="configure-cluster-quorum"></a>Konfigurace kvora clusteru

Dále nastavte kvorum clusteru.

1. Připojte k prvním uzlu clusteru pomocí vzdálené plochy.

1. V **Správce clusteru převzetí služeb při selhání**, klikněte pravým tlačítkem cluster, přejděte na **další akce**a klikněte na tlačítko **konfigurovat nastavení kvora clusteru...** .

   ![Nové sdílené složky](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. V **Průvodce konfigurací kvora clusteru**, klikněte na tlačítko **Další**.

1. V **vybrat možnosti konfigurace kvora**, zvolte **vybrat určující disk kvora**a klikněte na tlačítko **Další**.

1. Na **vybrat určující disk kvora**, klikněte na tlačítko **nakonfigurovat určující sdílenou složku**.

   >[!TIP]
   >Windows Server 2016 podporuje určujícího cloudu. Pokud si zvolíte tento typ určující sdílené složky, není nutné soubor sdílet s kopií clusteru. Další informace najdete v tématu [nasazení cloudu určující pro Cluster s podporou převzetí služeb při selhání](http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). Tento kurz používá určující sdílenou složku souboru, který není podporován ve starších operačních systémech.

1. Na **nakonfigurovat určující sdílenou složku**, zadejte cestu pro sdílenou složku, které jste vytvořili. Klikněte na **Další**.

1. Ověřte nastavení na **potvrzení**. Klikněte na **Další**.

1. Klikněte na **Dokončit**.

Základní prostředky clusteru jsou nakonfigurovány s určující sdílenou složku.

## <a name="enable-availability-groups"></a>Povolte skupiny dostupnosti

Dál povolte **skupiny dostupnosti AlwaysOn** funkce. Proveďte tyto kroky na obou serverech SQL.

1. Z **spustit** obrazovky, spusťte **SQL Server Configuration Manager**.
2. Ve stromu prohlížeče klikněte na tlačítko **služby SQL Server**, klikněte pravým tlačítkem **serveru SQL (MSSQLSERVER)** služby a klikněte na tlačítko **vlastnosti**.
3. Klikněte **vysoké dostupnosti AlwaysOn** a potom vyberte **povolte skupiny dostupnosti AlwaysOn**, a to takto:

    ![Povolte skupiny dostupnosti AlwaysOn](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Klikněte na tlačítko **Použít**. Klikněte na tlačítko **OK** v dialogovém okně automaticky otevírané okno.

5. Restartujte službu SQL Server.

Opakujte tyto kroky na SQL serveru.

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

## <a name="create-a-database-on-the-first-sql-server"></a>Vytvořit databázi na prvním serveru SQL

1. Spusťte soubor RDP k první systému SQL Server pomocí účtu domény, který je členem pevné role serveru sysadmin.
1. Otevřete aplikaci SQL Server Management Studio a připojte se k první systému SQL Server.
7. V **Průzkumník objektů**, klikněte pravým tlačítkem na **databáze** a klikněte na tlačítko **novou databázi**.
8. V **název databáze**, typ **MyDB1**, pak klikněte na tlačítko **OK**.

### <a name="backupshare"></a> Vytvoří sdílenou složku zálohování

1. Na prvním serveru SQL v **správce serveru**, klikněte na tlačítko **nástroje**. Otevřete **Správa počítače**.

1. Klikněte na tlačítko **sdílených složek**.

1. Klikněte pravým tlačítkem na **sdílené složky**a klikněte na tlačítko **novou sdílenou složku...** .

   ![Nové sdílené složky](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Použití **Průvodce vytvořením sdílené složky** vytvořit sdílenou složku.

1. Na **cesta ke složce**, klikněte na tlačítko **Procházet** a najít nebo vytvořit cestu pro sdílenou složku zálohování databáze. Klikněte na **Další**.

1. V **název, popis a nastavení** ověřte název sdílené složky a cesta. Klikněte na **Další**.

1. Na **sdílené složky oprávnění** nastavit **přizpůsobit oprávnění**. Klikněte na tlačítko **vlastní...** .

1. Na **upravit oprávnění**, klikněte na tlačítko **přidat...** .

1. Ujistěte se, že mají účty služby SQL Server a agenta systému SQL Server pro oba servery úplné řízení.

   ![Nové sdílené složky](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Klikněte na **OK**.

1. V **sdílené složky oprávnění**, klikněte na tlačítko **Dokončit**. Klikněte na tlačítko **Dokončit** znovu.  

### <a name="take-a-full-backup-of-the-database"></a>Proveďte úplnou zálohu databáze

Potřebujete zálohovat databázi nové inicializace řetězce protokolu. Pokud neprovedete zálohování nové databáze, nemůže být součástí skupiny dostupnosti.

1. V **Průzkumník objektů**, klikněte pravým tlačítkem na databázi, přejděte na **úlohy...** , klikněte na tlačítko **zálohování**.

1. Klikněte na tlačítko **OK** provést úplné zálohování do výchozího umístění zálohy.

## <a name="create-the-availability-group"></a>Vytvoření skupiny dostupnosti
Nyní jste připraveni ke konfiguraci skupiny dostupnosti pomocí následujících kroků:

* Vytvořte databázi na prvním serveru SQL.
* Proveďte úplné zálohování a zálohování protokolu transakcí databáze
* Obnovit celý a protokolu zálohování na druhý Server SQL s **NORECOVERY** možnost
* Vytvoření skupiny dostupnosti (**AG1**) se synchronním potvrzováním, automatické převzetí služeb při selhání a čitelných místních replikách

### <a name="create-the-availability-group"></a>Vytvoření skupiny dostupnosti:

1. V relaci vzdálené plochy k první systému SQL Server. V **Průzkumník objektů** v aplikaci SSMS, klikněte pravým tlačítkem na **vysoké dostupnosti AlwaysOn** a klikněte na tlačítko **Průvodce novou skupinou dostupnosti**.

    ![Spusťte Průvodce novou skupinou dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. V **ÚVOD** klikněte na tlačítko **Další**. V **zadejte název skupiny dostupnosti** stránky, zadejte název skupiny dostupnosti, například **AG1**v **název skupiny dostupnosti**. Klikněte na **Další**.

    ![Průvodce novým AG, zadejte název skupiny AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. V **vyberte databáze** , vyberte svou databázi a klikněte na tlačítko **Další**.

   >[!NOTE]
   >Databáze splňuje předpoklady pro skupinu dostupnosti, protože jste provedli aspoň jednu úplnou zálohu na určený primární replice.

   ![Průvodce novým AG, vyberte možnost databáze](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. V **zadejte repliky** klikněte na tlačítko **přidat repliky**.

   ![Průvodce novým AG, zadejte repliky](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. **Připojit k serveru** objeví dialogové okno. Zadejte název druhý server v **název serveru**. Klikněte na **Připojit**.

   Zpět v **zadejte repliky** stránky, měli byste vidět druhý server uvedený v **replik dostupnosti**. Repliky nakonfigurujte následujícím způsobem.

   ![Průvodce novým AG, zadejte repliky (dokončeno)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Klikněte na tlačítko **koncové body** zobrazíte koncový bod zrcadlení pro tuto skupinu dostupnosti databáze. Použijte stejný port, který jste použili při nastavení [pravidlo brány firewall pro koncové body zrcadlení databáze](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

    ![Průvodce novým AG, vyberte Data počáteční synchronizace](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. V **vybrat počáteční synchronizaci dat** vyberte **úplné** a zadejte do sdíleného síťového umístění. K umístění, použijte [zálohování sdílené složky, kterou jste vytvořili](#backupshare). V příkladu byl **\\\\\<první systému SQL Server\>\Backup\**. Klikněte na **Další**.

   >[!NOTE]
   >Úplná synchronizace provede úplnou zálohu databáze na první instanci systému SQL Server a obnoví druhé instanci. Pro velké databáze úplná synchronizace nedoporučuje, protože to může trvat dlouhou dobu. Tentokrát lze zmenšit ručně zálohování databáze a její obnovení `NO RECOVERY`. Pokud byla databáze obnovena již s `NO RECOVERY` na druhém serveru SQL před konfigurací skupiny dostupnosti, vyberte **připojení pouze**. Pokud chcete provést zálohování po konfiguraci skupiny dostupnosti, vyberte **přeskočit počáteční data synchronizace**.

    ![Průvodce novým AG, vyberte Data počáteční synchronizace](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. V **ověření** klikněte na tlačítko **Další**. Tato stránka by měl vypadat podobně jako na následujícím obrázku:

    ![Průvodce novým AG, ověření](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >Protože jste nenakonfigurovali naslouchací proces skupiny dostupnosti je upozornění pro konfiguraci naslouchacího procesu. Toto upozornění můžete ignorovat, protože na virtuálních počítačích Azure můžete vytvořit naslouchací proces po vytvoření nástroje pro vyrovnávání zatížení Azure.

10. V **Souhrn** klikněte na tlačítko **Dokončit**, potom chvíli počkejte, než Průvodce nakonfiguruje do nové skupiny dostupnosti. V **průběh** stránky, můžete kliknout na **podrobnosti** zobrazíte podrobné průběh. Po dokončení průvodce zkontrolujte **výsledky** a ověřte, že skupina dostupnosti úspěšně vytvořil.

     ![Průvodce novým AG, výsledků](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Klikněte na tlačítko **Zavřít** ukončíte průvodce.

### <a name="check-the-availability-group"></a>Zkontrolujte skupiny dostupnosti

1. V **Průzkumník objektů**, rozbalte položku **vysoké dostupnosti AlwaysOn**, pak rozbalte **skupiny dostupnosti**. Teď byste měli vidět nové skupiny dostupnosti v tomto kontejneru. Klikněte pravým tlačítkem na skupinu dostupnosti a klikněte na tlačítko **zobrazit řídicí panel**.

   ![Řídicí panel zobrazit AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   Vaše **řídicí panel AlwaysOn** by měl vypadat podobně jako tento.

   ![Řídicí panel AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   Můžete zobrazit repliky, režim převzetí služeb při selhání každou repliku a stavu synchronizace.

2. V **Správce clusteru převzetí služeb při selhání**, klikněte na cluster. Vyberte **role**. Název skupiny dostupnosti, které jste použili je role v clusteru. Této skupiny dostupnosti nemá IP adresu pro připojení klientů, protože jste nenakonfigurovali naslouchací proces. Po vytvoření pro vyrovnávání zatížení Azure nakonfigurujete naslouchací proces.

   ![AG ve Správci clusteru převzetí služeb při selhání](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Nepokoušejte se převzít služby skupiny dostupnosti ze Správce clusteru převzetí služeb při selhání. Všechny operace převzetí služeb při selhání by měla provést uvnitř **řídicí panel AlwaysOn** v aplikaci SSMS. Další informace najdete v tématu [omezení na používání nástroje převzetí služeb při selhání clusteru Manager se skupinami dostupnosti](https://msdn.microsoft.com/library/ff929171.aspx).
    >

Nyní máte skupinu dostupnosti s replikami na dvě instance systému SQL Server. Skupiny dostupnosti můžete přesouvat mezi instancemi. Je nelze připojit ke skupině dostupnosti ještě vzhledem k tomu, že nemáte naslouchací proces. Naslouchací proces ve virtuálních počítačích Azure, vyžaduje nástroj pro vyrovnávání zatížení. Dalším krokem je vytvoření nástroje pro vyrovnávání zatížení v Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení Azure

Skupinu dostupnosti SQL Server na virtuálních počítačích Azure, vyžaduje nástroj pro vyrovnávání zatížení. Nástroje pro vyrovnávání zatížení obsahuje IP adresu pro naslouchací proces skupiny dostupnosti. Tento oddíl shrnuje, jak vytvořit nástroj pro vyrovnávání zatížení na portálu Azure.

1. Na portálu Azure přejděte do skupiny prostředků, kde jsou vaše servery SQL a klikněte na tlačítko **+ přidat**.
2. Vyhledejte **nástroj pro vyrovnávání zatížení**. Zvolte Nástroje pro vyrovnávání zatížení, který je publikovaný microsoftem.

   ![AG ve Správci clusteru převzetí služeb při selhání](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1.  Klikněte na možnost **Vytvořit**.
3. Nakonfigurujte následující parametry nástroje pro vyrovnávání zatížení.

   | Nastavení | Pole |
   | --- | --- |
   | **Název** |Použijte název textu pro danou službu Vyrovnávání zatížení, například **sqlLB**. |
   | **Typ** |Interní |
   | **Virtuální síť** |Použijte název virtuální síť Azure. |
   | **Podsíť** |Použijte název podsítě, která je virtuální počítač.  |
   | **Přiřazení IP adresy** |Statická |
   | **IP adresa** |Použijte dostupnou adresu z podsítě. Všimněte si, že se to neliší od vaší IP adresu clusteru |
   | **Předplatné** |Pomocí stejného předplatného jako virtuální počítač. |
   | **Umístění** |Použijte stejné umístění jako virtuální počítač. |

   Okno portálu Azure by měl vypadat takto:

   ![Vytvořit nástroj pro vyrovnávání zatížení](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Klikněte na tlačítko **vytvořit**, chcete-li vytvořit nástroj pro vyrovnávání zatížení.

Ke konfiguraci nástroje pro vyrovnávání zatížení, musíte vytvořit fond back-end, sondu a nastavte pravidla Vyrovnávání zatížení. Proveďte tyto na portálu Azure.

### <a name="add-backend-pool"></a>Přidejte fond back-end

1. V portálu Azure přejděte do vaší skupiny dostupnosti. Možná budete muset aktualizovat zobrazení, aby najdete v části nástroje pro vyrovnávání zatížení nově vytvořený.

   ![Najít nástroj pro vyrovnávání zatížení ve skupině prostředků](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Klikněte na nástroje pro vyrovnávání zatížení, klikněte na tlačítko **back-endové fondy**a klikněte na tlačítko **+ přidat**. 

1. Fond back-end přidružení skupiny dostupnosti, která obsahuje virtuální počítače.

1. V části **cíle konfigurace protokolu IP sítě**, zkontrolujte **virtuální počítač** a zvolit oba virtuální počítače, které budou hostiteli replik skupin dostupnosti. Nezahrnujte souborový server určující sdílené složky.

   >[!NOTE]
   >Pokud nejsou zadány oba virtuální počítače, připojení bude úspěšné pouze k primární replice.

1. Klikněte na tlačítko **OK** vytvoření fondu back-end.

### <a name="set-the-probe"></a>Nastavit sonda

1. Klikněte na nástroje pro vyrovnávání zatížení, klikněte na tlačítko **testy stavu**a klikněte na tlačítko **+ přidat**.

1. Test stavu nastavte takto:

   | Nastavení | Popis | Příklad:
   | --- | --- |---
   | **Název** | Text | SQLAlwaysOnEndPointProbe |
   | **Protokol** | Zvolte TCP | TCP |
   | **Port** | Všechny nepoužívaný port. | 59999 |
   | **Interval**  | Množství času mezi pokusy o sondování v sekundách |5 |
   | **Prahová hodnota špatného stavu** | Počet po sobě jdoucích kontroly chyb, které musí být stejné pro virtuální počítač, aby byla považována za není v pořádku  | 2 |

1. Klikněte na tlačítko **OK** nastavit test stavu.

### <a name="set-the-load-balancing-rules"></a>Nastavit pravidla Vyrovnávání zatížení

1. Klikněte na nástroje pro vyrovnávání zatížení, klikněte na tlačítko **pravidla Vyrovnávání zatížení**a klikněte na tlačítko **+ přidat**.

1. Nastavte pravidla takto Vyrovnávání zatížení.
   | Nastavení | Popis | Příklad:
   | --- | --- |---
   | **Název** | Text | SQLAlwaysOnEndPointListener |
   | **Adresa IP front-endu** | Zvolte adresu |Použijte adresu, kterou jste vytvořili, když jste vytvořili pro vyrovnávání zatížení. |
   | **Protokol** | Zvolte TCP |TCP |
   | **Port** | Použít port pro instanci systému SQL Server | 1433 |
   | **Back-endový Port** | Toto pole se nepoužívá při plovoucí IP adresa je nastavena pro přímý server návratový | 1433 |
   | **Test** |Název, který jste zadali pro kontrolu | SQLAlwaysOnEndPointProbe |
   | **Trvalost relace** | Rozevírací seznam | **None** |
   | **Časový limit nečinnosti** | Otevřete minut pro uchování připojení TCP | 4 |
   | **Plovoucí IP adresa (přímá odpověď ze serveru)** | |Povoleno |

   > [!WARNING]
   > Přímá odpověď ze serveru se nastavuje během vytváření. Název není možné změnit.

1. Klikněte na tlačítko **OK** nastavit pravidla Vyrovnávání zatížení.

## <a name="configure-listener"></a> Konfigurace naslouchací proces

Dalším krokem je konfigurace naslouchací proces skupiny dostupnosti v clusteru převzetí služeb při selhání.

> [!NOTE]
> Tento kurz ukazuje, jak vytvořit jeden naslouchací proces - s jednu ILB IP adresu. Pokud chcete vytvořit jeden nebo více naslouchací procesy pomocí jednoho nebo více IP adres, přečtěte si téma [skupiny dostupnosti vytvořit naslouchací proces a nástroj pro vyrovnávání zatížení | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Port naslouchacího procesu sady

V systému SQL Server Management Studio nastavte port naslouchacího procesu.

1. Spusťte SQL Server Management Studio a připojte se k primární replice.

1. Přejděte na **AlwaysOn vysokou dostupnost** | **skupiny dostupnosti** | **naslouchací procesy skupiny dostupnosti**.

1. Teď byste měli vidět název naslouchacího procesu, který jste vytvořili ve Správci clusteru převzetí služeb při selhání. Klikněte pravým tlačítkem na název naslouchacího procesu a klikněte na tlačítko **vlastnosti**.

1. V **Port** pole, zadejte číslo portu pro naslouchací proces skupiny dostupnosti pomocí $EndpointPort jste použili předtím (1433 se výchozí nastavení), pak klikněte na tlačítko **OK**.

Nyní máte skupinu dostupnosti SQL Server na virtuálních počítačích Azure spuštěna v režimu Resource Manager.

## <a name="test-connection-to-listener"></a>Testovací připojení pro naslouchací proces

K testování připojení:

1. RDP k systému SQL Server, který je ve stejné virtuální síti, ale není vlastníkem repliky. Můžete použít SQL Server v clusteru.

1. Použití **sqlcmd** nástroj k testování připojení. Například následující skript vytvoří **sqlcmd** připojení k primární replice prostřednictvím naslouchací proces s ověřováním systému Windows:

    ```
    sqlcmd -S <listenerName> -E
    ```

    Pokud naslouchací proces používá jiný port než výchozí port (1433), zadejte port v připojovacím řetězci. Například následující příkaz sqlcmd připojí k naslouchání na portu 1435:

    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

Připojení SQLCMD se automaticky připojí na kteroukoli instanci systému SQL Server hostitelem primární repliky.

> [!TIP]
> Ujistěte se, že je port, který zadáte otevřen v bráně firewall oba servery SQL. Oba servery vyžadují příchozí pravidlo pro port TCP, který používáte. Další informace najdete v tématu [přidat nebo upravit pravidlo brány Firewall](http://technet.microsoft.com/library/cc753558.aspx).
>
>



<!--**Notes**: *Notes provide just-in-time info: A Note is “by the way” info, an Important is info users need to complete a task, Tip is for shortcuts. Don’t overdo*.-->


<!--**Procedures**: *This is the second “step." They often include substeps. Again, use a short title that tells users what they’ll do*. *("Configure a new web project.")*-->

<!--**UI**: *Note the format for documenting the UI: bold for UI elements and arrow keys for sequence. (Ex. Click **File > New > Project**.)*-->

<!--**Screenshot**: *Screenshots really help users. But don’t include too many since they’re difficult to maintain. Highlight areas you are referring to in red.*-->

<!--**No. of steps**: *Make sure the number of steps within a procedure is 10 or fewer. Seven steps is ideal. Break up long procedure logically.*-->


<!--**Next steps**: *Reiterate what users have done, and give them interesting and useful next steps so they want to go on.*-->

## <a name="next-steps"></a>Další postup

- [Přidat IP adresu nástroji pro vyrovnávání zatížení pro skupinu dostupnosti druhý](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).
