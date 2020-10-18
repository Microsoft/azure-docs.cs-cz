---
title: 'Kurz: Konfigurace skupiny dostupnosti Always On SQL Server'
description: V tomto kurzu se dozvíte, jak ve službě Azure Virtual Machines vytvořit skupinu dostupnosti Always On SQL Server.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: ee249a33187c3f8776cfc8fc750590c58f74579e
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168133"
---
# <a name="tutorial-manually-configure-an-availability-group-sql-server-on-azure-vms"></a>Kurz: ruční konfigurace skupiny dostupnosti (SQL Server na virtuálních počítačích Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

V tomto kurzu se dozvíte, jak vytvořit skupinu dostupnosti Always On pro SQL Server v Azure Virtual Machines (VM). Úplný kurz vytvoří skupinu dostupnosti s replikou databáze na dvou serverech SQL.

I když tento článek konfiguruje prostředí skupiny dostupnosti ručně, je možné to provést také pomocí [Azure Portal](availability-group-azure-portal-configure.md), [PowerShellu nebo Azure CLI](availability-group-az-commandline-configure.md)nebo [šablon Azure pro rychlý Start](availability-group-quickstart-template-configure.md) . 


**Časový odhad**: po dokončení splnění [požadavků](availability-group-manually-configure-prerequisites-tutorial.md) zabere asi 30 minut.


## <a name="prerequisites"></a>Předpoklady

V tomto kurzu se předpokládá základní znalost skupin dostupnosti Always On SQL Server. Pokud potřebujete další informace, přečtěte si téma [Přehled skupin dostupnosti Always On (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).

Než začnete tento kurz, musíte [Dokončit požadavky na vytváření skupin dostupnosti Always On v Azure Virtual Machines](availability-group-manually-configure-prerequisites-tutorial.md). Pokud jsou tyto požadavky již dokončeny, můžete přejít na příkaz [vytvořit cluster](#CreateCluster).

V následující tabulce jsou uvedeny předpoklady, které je třeba provést před zahájením tohoto kurzu:

| Požadavek |Popis |
|----- |----- |----- |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Dvě instance SQL Server** | – V sadě dostupnosti Azure <br/> – V jedné doméně <br/> – Je nainstalovaná funkce clusteringu s podporou převzetí služeb při selhání |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Windows Server** | Sdílená složka pro disk s kopií clusteru |  
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Účet služby SQL Server** | Účet domény |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Účet služby agenta SQL Server** | Účet domény |  
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Otevřené porty brány firewall** | -SQL Server: **1433** pro výchozí instanci <br/> -Koncový bod zrcadlení databáze: **5022** nebo libovolný dostupný port <br/> – Sonda stavu IP adres služby Vyrovnávání zatížení skupiny dostupnosti: **59999** nebo jakýkoli dostupný port <br/> – Sonda stavu IP jádra pro vyrovnávání zatížení clusteru: **58888** nebo jakýkoli dostupný port |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Přidat funkci clusteringu s podporou převzetí služeb při selhání** | Tato funkce vyžaduje tyto instance SQL Server. |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Instalační účet domény** | – Místní správce na každé SQL Server <br/> – Člen pevné role serveru sysadmin SQL Server pro každou instanci SQL Server  |

>[!NOTE]
> Mnohé z kroků uvedených v tomto kurzu teď můžete automatizovat pomocí [Azure Portal](availability-group-azure-portal-configure.md), [PowerShellu a šablon pro](availability-group-az-cli-configure.md) [rychlý Start](availability-group-quickstart-template-configure.md)pro příkaz AZ CLI a Azure.


<!--**Procedure**: *This is the first "step". Make titles H2's and short and clear – H2's appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>

## <a name="create-the-cluster"></a>Vytvoření clusteru

Po dokončení požadovaných součástí je prvním krokem vytvoření clusteru s podporou převzetí služeb při selhání se systémem Windows Server, který obsahuje dva servery SQL a monitorovací Server.

1. K prvnímu SQL Server se připojte pomocí protokol RDP (Remote Desktop Protocol) (RDP). Použijte doménový účet, který je správcem na serverech SQL Server a monitorovacím serveru.

   >[!TIP]
   >Pokud jste postupovali podle [požadavků dokumentu](availability-group-manually-configure-prerequisites-tutorial.md), vytvořili jste účet s názvem **CORP\Install**. Použijte tento účet.

2. Na řídicím panelu **Správce serveru** vyberte **nástroje**a pak vyberte **Správce clusteru s podporou převzetí služeb při selhání**.
3. V levém podokně klikněte pravým tlačítkem na **Správce clusteru s podporou převzetí služeb při selhání**a pak vyberte **vytvořit cluster**.

   ![Vytvořit cluster](./media/availability-group-manually-configure-tutorial/40-createcluster.png)

4. V Průvodci vytvořením clusteru vytvořte cluster s jedním uzlem procházením stránek s nastavením v následující tabulce:

   | Stránka | Nastavení |
   | --- | --- |
   | Než začnete |Použít výchozí |
   | Vybrat servery |Zadejte první SQL Server název do pole **Zadejte název serveru** a vyberte **Přidat**. |
   | Upozornění ověření |Vyberte **ne. pro tento cluster nepotřebujete podporu od Microsoftu, a proto nechcete spouštět ověřovací testy. Po výběru další pokračujte v vytváření clusteru**. |
   | Přístupový bod pro správu clusteru |Zadejte název clusteru, například **SQLAGCluster1** v **názvu clusteru**.|
   | Confirmation (Potvrzení) |Použijte výchozí hodnoty, pokud nepoužíváte prostory úložiště. Podívejte se na poznámku za touto tabulkou. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Nastavit IP adresu clusteru s podporou převzetí služeb při selhání Windows serveru

  > [!NOTE]
  > V systému Windows Server 2019 vytvoří cluster místo **názvu sítě clusteru** **název distribuovaného serveru** . Pokud používáte Windows Server 2019, přeskočte všechny kroky, které v tomto kurzu odkazují na základní název clusteru. Název sítě clusteru můžete vytvořit pomocí [PowerShellu](failover-cluster-instance-storage-spaces-direct-manually-configure.md#create-failover-cluster). Další informace najdete v [clusteru s podporou převzetí služeb při selhání na blogu: objekt sítě clusteru](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) . 

1. V **Správce clusteru s podporou převzetí služeb při selhání**přejděte dolů na **základní prostředky clusteru** a rozbalte podrobnosti o clusteru. Měl by se zobrazit **název** a prostředky **IP adresy** ve stavu **selhání** . Prostředek IP adresy nelze uvést do režimu online, protože cluster má přiřazenou stejnou IP adresu jako samotný počítač, proto je duplicitní adresa.

2. Klikněte pravým tlačítkem na prostředek neúspěšné **IP adresy** a pak vyberte **vlastnosti**.

   ![Vlastnosti clusteru](./media/availability-group-manually-configure-tutorial/42_IPProperties.png)

3. Vyberte možnost **statická IP adresa** a zadejte dostupnou adresu ze stejné podsítě jako vaše virtuální počítače.

4. V části **základní prostředky clusteru** klikněte pravým tlačítkem na název clusteru a vyberte **převést do režimu online**. Počkejte, dokud budou oba prostředky online. Když prostředek názvu clusteru přepne do online režimu, aktualizuje Server řadiče domény (DC) na nový účet počítače služby Active Directory (AD). Pomocí tohoto účtu AD můžete později spustit Clusterovou službu skupiny dostupnosti.

### <a name="add-the-other-sql-server-to-cluster"></a><a name="addNode"></a>Přidání druhého SQL Server do clusteru

Přidejte ostatní SQL Server do clusteru.

1. Ve stromové struktuře prohlížeče klikněte pravým tlačítkem na cluster a vyberte **přidat uzel**.

    ![Přidat uzel do clusteru](./media/availability-group-manually-configure-tutorial/44-addnode.png)

1. V **Průvodci přidáním uzlu**vyberte **Další**. Na stránce **Vybrat servery** přidejte druhý SQL Server. Do pole **Zadejte název serveru** zadejte název serveru a pak vyberte **Přidat**. Až budete hotovi, vyberte **Další**.

1. Na stránce **Upozornění na ověření** vyberte možnost **ne** (v produkčním scénáři byste měli provádět ověřovací testy). Pak vyberte **Další**.

8. Pokud používáte prostory úložiště, na stránce **potvrzení** zrušte zaškrtnutí políčka **Přidat do clusteru veškeré oprávněné úložiště.**

   ![Přidat potvrzení uzlu](./media/availability-group-manually-configure-tutorial/46-addnodeconfirmation.png)

   >[!WARNING]
   >Pokud používáte prostory úložiště a nechcete zrušit kontrolu **Přidat do clusteru všechny opravňující úložiště**, systém Windows virtuální disky během procesu clusteringu odpojí. V důsledku toho se nebudou zobrazovat ve Správci disků nebo v Průzkumníkovi, dokud se prostory úložiště z clusteru neodstraní a znovu nepřipojí přes PowerShell. Prostory úložiště seskupují více disků do fondů úložiště. Další informace najdete v tématu [prostory úložiště](https://technet.microsoft.com/library/hh831739).
   >

1. Vyberte **Další**.

1. Vyberte **Dokončit**.

   Správce clusteru s podporou převzetí služeb při selhání ukazuje, že váš cluster má nový uzel a zobrazí ho v kontejneru **uzly** .

10. Odhlaste se z relace vzdálené plochy.

### <a name="add-a-cluster-quorum-file-share"></a>Přidat sdílenou složku kvora clusteru

V tomto příkladu cluster Windows používá ke vytvoření kvora clusteru sdílenou složku. V tomto kurzu se používá kvorum Většina uzlů a sdílených souborů. Další informace najdete v článku [Principy konfigurací kvora v clusteru s podporou převzetí služeb při selhání](https://technet.microsoft.com/library/cc731739.aspx).

1. Připojte se k členskému serveru určující sdílené složky s relací vzdálené plochy.

1. V **Správce serveru**vyberte **nástroje**. Otevřete **správu počítače**.

1. Vyberte **sdílené složky**.

1. Klikněte pravým tlačítkem na **sdílené složky**a vyberte **Nová sdílená složka...**.

   ![Klikněte pravým tlačítkem na sdílené složky a vyberte Nová sdílená složka.](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   Pomocí **Průvodce vytvořením sdílené složky** vytvořte sdílenou složku.

1. V části **cesta ke složce**vyberte **Procházet** a vyhledejte nebo vytvořte cestu pro sdílenou složku. Vyberte **Další**.

1. V části **název, popis a nastavení** ověřte název a cestu ke sdílené složce. Vyberte **Další**.

1. V nastavení **oprávnění ke sdílené složce** nastavte **oprávnění přizpůsobit**. Vybrat **vlastní...**.

1. V nabídce **přizpůsobit oprávnění**vyberte **Přidat...**.

1. Ujistěte se, že účet použitý k vytvoření clusteru má úplné řízení.

   ![Ujistěte se, že účet použitý k vytvoření clusteru má úplné řízení.](./media/availability-group-manually-configure-tutorial/50-filesharepermissions.png)

1. Vyberte **OK**.

1. V **oprávněních ke sdílené složce**vyberte **Dokončit**. Vyberte **Dokončit** znovu.  

1. Odhlášení ze serveru

### <a name="configure-the-cluster-quorum"></a>Konfigurace kvora clusteru

V dalším kroku nastavte kvorum clusteru.

1. Připojte se k prvnímu uzlu clusteru pomocí vzdálené plochy.

1. V **Správce clusteru s podporou převzetí služeb při selhání**klikněte pravým tlačítkem na cluster, přejděte na **Další akce**a vyberte **Konfigurovat nastavení kvora clusteru..**..

   ![Vyberte konfigurovat nastavení kvora clusteru.](./media/availability-group-manually-configure-tutorial/52-configurequorum.png)

1. V **Průvodci konfigurací kvora clusteru**vyberte **Další**.

1. V části **Vybrat konfiguraci kvora**zvolte **Vybrat určující disk kvora**a **pak vyberte další**.

1. Na **výběru vybrat určující disk kvora**vyberte **Konfigurovat určující sdílenou složku**.

   >[!TIP]
   >Windows Server 2016 podporuje cloudový disk s kopií clusteru. Pokud zvolíte tento typ určujícího umístění, nepotřebujete určující sdílenou složku. Další informace najdete v tématu [nasazení určujícího cloudu pro cluster s podporou převzetí služeb při selhání](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). V tomto kurzu se používá určující sdílená složka, která je podporovaná v předchozích operačních systémech.
   >

1. V části **Konfigurovat určující sdílenou složku**zadejte cestu pro sdílenou složku, kterou jste vytvořili. Vyberte **Další**.

1. Ověřte nastavení při **potvrzení**. Vyberte **Další**.

1. Vyberte **Dokončit**.

Základní prostředky clusteru jsou nakonfigurovány s určující sdílenou složkou.

## <a name="enable-availability-groups"></a>Povolit skupiny dostupnosti

Potom povolte funkci **skupiny dostupnosti AlwaysOn** . Proveďte tyto kroky na serverech SQL.

1. Z obrazovky **Start** spusťte **SQL Server Configuration Manager**.
2. Ve stromové struktuře prohlížeče vyberte **SQL Server Services**, klikněte pravým tlačítkem na službu **SQL Server (MSSQLSERVER)** a vyberte **vlastnosti**.
3. Vyberte kartu **Vysoká dostupnost AlwaysOn** a pak vyberte **Povolit skupiny dostupnosti AlwaysOn**následujícím způsobem:

    ![Povolit skupiny dostupnosti AlwaysOn](./media/availability-group-manually-configure-tutorial/54-enableAlwaysOn.png)

4. Vyberte **Použít**. V automaticky otevíraném okně vyberte **OK** .

5. Restartujte službu SQL Server.

Opakujte tyto kroky na ostatních SQL Server.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an availability group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the availability groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, select **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and select **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and select **Next**.
6. In the **Profile** page, accept the default settings and select **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then select **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>Vytvořit databázi v prvním SQL Server

1. Spusťte soubor RDP na první SQL Server s účtem domény, který je členem pevné role serveru sysadmin.
1. Otevřete SQL Server Management Studio a připojte se k první SQL Server.
7. V **Průzkumník objektů**klikněte pravým tlačítkem na **databáze** a vyberte **Nová databáze**.
8. Do **název databáze**zadejte **MyDB1**a pak vyberte **OK**.

### <a name="create-a-backup-share"></a><a name="backupshare"></a> Vytvoření sdílené složky zálohy

1. V prvním SQL Server v **Správce serveru**vyberte **nástroje**. Otevřete **správu počítače**.

1. Vyberte **sdílené složky**.

1. Klikněte pravým tlačítkem na **sdílené složky**a vyberte **Nová sdílená složka...**.

   ![Vybrat novou sdílenou složku](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   Pomocí **Průvodce vytvořením sdílené složky** vytvořte sdílenou složku.

1. V části **cesta ke složce**vyberte **Procházet** a vyhledejte nebo vytvořte cestu pro sdílenou složku zálohy databáze. Vyberte **Další**.

1. V části **název, popis a nastavení** ověřte název a cestu ke sdílené složce. Vyberte **Další**.

1. V nastavení **oprávnění ke sdílené složce** nastavte **oprávnění přizpůsobit**. Vybrat **vlastní...**.

1. V nabídce **přizpůsobit oprávnění**vyberte **Přidat...**.

1. Ujistěte se, že účty služby Agent SQL Server a SQL Server pro oba servery mají úplné řízení.

   ![Ujistěte se, že účty služby Agent SQL Server a SQL Server pro oba servery mají úplné řízení.](./media/availability-group-manually-configure-tutorial/68-backupsharepermission.png)

1. Vyberte **OK**.

1. V **oprávněních ke sdílené složce**vyberte **Dokončit**. Vyberte **Dokončit** znovu.  

### <a name="take-a-full-backup-of-the-database"></a>Proveďte úplnou zálohu databáze.

Pro inicializaci řetězce protokolu je nutné zálohovat novou databázi. Pokud neprovedete zálohu nové databáze, nelze ji zahrnout do skupiny dostupnosti.

1. V **Průzkumník objektů**klikněte pravým tlačítkem na databázi, přejděte na **úlohy...**, vyberte **zálohovat**.

1. Vyberte **OK** , chcete-li vytvořit úplnou zálohu do výchozího umístění zálohy.

## <a name="create-the-availability-group"></a>Vytvoření skupiny dostupnosti

Nyní jste připraveni ke konfiguraci skupiny dostupnosti pomocí následujících kroků:

* Vytvořte databázi na prvním SQL Server.
* Proveďte úplnou zálohu a zálohu protokolu transakcí databáze.
* Obnovte úplné a Zaprotokolujte zálohy do druhé SQL Server pomocí možnosti **NORECOVERY** .
* Vytvořte skupinu dostupnosti (**AG1**) s synchronním potvrzením, automatickým převzetím služeb při selhání a čitelnými sekundárními replikami.

### <a name="create-the-availability-group"></a>Vytvořte skupinu dostupnosti:

1. V relaci vzdálené plochy k prvnímu SQL Server. V **Průzkumník objektů** v SSMS klikněte pravým tlačítkem na možnost **AlwaysOn High Availability** a vyberte **Průvodce novou skupinou dostupnosti**.

    ![Spustit Průvodce novou skupinou dostupnosti](./media/availability-group-manually-configure-tutorial/56-newagwiz.png)

2. Na stránce **Úvod** vyberte **Další**. Na stránce **zadat název skupiny dostupnosti** zadejte název skupiny dostupnosti do pole **název skupiny**dostupnosti. Například **AG1**. Vyberte **Další**.

    ![Průvodce novou skupinou dostupnosti, zadejte název skupiny dostupnosti.](./media/availability-group-manually-configure-tutorial/58-newagname.png)

3. Na stránce **Vybrat databáze** vyberte svou databázi a potom vyberte **Další**.

   >[!NOTE]
   >Databáze splňuje předpoklady pro skupinu dostupnosti, protože jste převzali alespoň jednu úplnou zálohu na zamýšlené primární replice.
   >

   ![Průvodce novou skupinou dostupnosti, vybrat databáze](./media/availability-group-manually-configure-tutorial/60-newagselectdatabase.png)

4. Na stránce **zadat repliky** vyberte **Přidat repliku**.

   ![Průvodce novou skupinou dostupnosti, zadání replik](./media/availability-group-manually-configure-tutorial/62-newagaddreplica.png)

5. Zobrazí se dialogové okno **připojit k serveru** . Do pole **název serveru**zadejte název druhého serveru. Vyberte **Připojit**.

   Zpátky na stránce **zadat repliky** by se teď měl zobrazit druhý server uvedený v **replikách dostupnosti**. Repliky nakonfigurujte následujícím způsobem.

   ![Průvodce novou skupinou dostupnosti, zadejte repliky (dokončeno).](./media/availability-group-manually-configure-tutorial/64-newagreplica.png)

6. Vyberte **koncové body** pro zobrazení koncového bodu zrcadlení databáze pro tuto skupinu dostupnosti. Použijte stejný port, který jste použili při nastavování [pravidla brány firewall pro koncové body zrcadlení databáze](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall).

    ![Průvodce novou skupinou dostupnosti, vyberte počáteční synchronizaci dat.](./media/availability-group-manually-configure-tutorial/66-endpoint.png)

8. Na stránce **Vyberte počáteční synchronizaci dat** vyberte možnost **úplné** a zadejte sdílené síťové umístění. Pro toto umístění použijte [sdílenou složku zálohy, kterou jste vytvořili](#backupshare). V tomto příkladu se ** \\ \\<nejdřív SQL Server \> \Backup \\ **. Vyberte **Další**.

   >[!NOTE]
   >Úplná synchronizace provede úplnou zálohu databáze na první instanci SQL Server a obnoví ji do druhé instance. U rozsáhlých databází se Úplná synchronizace nedoporučuje, protože může trvat dlouhou dobu. Tuto dobu můžete zkrátit ručním vytvořením zálohy databáze a jejím obnovením `NO RECOVERY` . Pokud je databáze již `NO RECOVERY` v druhém SQL Server obnovena před konfigurací skupiny dostupnosti, vyberte možnost **pouze připojit**. Pokud chcete zálohování provést po konfiguraci skupiny dostupnosti, vyberte možnost **Přeskočit počáteční synchronizaci dat**.
   >

   ![Výběr možnosti přeskočit počáteční synchronizaci dat](./media/availability-group-manually-configure-tutorial/70-datasynchronization.png)

9. Na stránce **ověřování** vyberte **Další**. Tato stránka by měla vypadat podobně jako na následujícím obrázku:

    ![Průvodce novou skupinou dostupnosti, ověřování](./media/availability-group-manually-configure-tutorial/72-validation.png)

    >[!NOTE]
    >Pro konfiguraci naslouchacího procesu existuje upozornění, protože jste nenakonfigurovali naslouchací proces skupiny dostupnosti. Toto upozornění můžete ignorovat, protože na virtuálních počítačích Azure vytváříte naslouchací proces po vytvoření nástroje pro vyrovnávání zatížení Azure.

10. Na stránce **Souhrn** vyberte **Dokončit**a potom počkejte, než průvodce nakonfiguruje novou skupinu dostupnosti. Na stránce **průběh** můžete vybrat **Další podrobnosti** a zobrazit podrobný průběh. Po dokončení průvodce Zkontrolujte stránku **výsledků** a ověřte, jestli je skupina dostupnosti úspěšně vytvořená.

     ![Průvodce novou skupinou dostupnosti, výsledky](./media/availability-group-manually-configure-tutorial/74-results.png)

11. Kliknutím na **Zavřít** ukončíte průvodce.

### <a name="check-the-availability-group"></a>Ověřit skupinu dostupnosti

1. V **Průzkumník objektů**rozbalte možnost **vysoce dostupná dostupnost AlwaysOn**a pak rozbalte položku **skupiny dostupnosti**. Nyní by se měla zobrazit nová skupina dostupnosti v tomto kontejneru. Klikněte pravým tlačítkem na skupinu dostupnosti a vyberte **zobrazit řídicí panel**.

   ![Zobrazit řídicí panel skupiny dostupnosti](./media/availability-group-manually-configure-tutorial/76-showdashboard.png)

   **Řídicí panel AlwaysOn** by měl vypadat podobně jako na následujícím snímku obrazovky:

   ![Řídicí panel skupiny dostupnosti](./media/availability-group-manually-configure-tutorial/78-agdashboard.png)

   Můžete zobrazit repliky, režim převzetí služeb při selhání každé repliky a stav synchronizace.

2. V **Správce clusteru s podporou převzetí služeb při selhání**vyberte svůj cluster. Vyberte **role**. Název skupiny dostupnosti, který jste použili, je role v clusteru. Tato skupina dostupnosti nemá IP adresu pro připojení klienta, protože jste nenakonfigurovali naslouchací proces. Naslouchací proces budete konfigurovat po vytvoření nástroje pro vyrovnávání zatížení Azure.

   ![Skupina dostupnosti v Správce clusteru s podporou převzetí služeb při selhání](./media/availability-group-manually-configure-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Nepokoušejte se převzít služby při selhání skupiny dostupnosti z Správce clusteru s podporou převzetí služeb při selhání. Všechny operace převzetí služeb při selhání by se měly provádět v rámci **řídicího panelu AlwaysOn** v SSMS. Další informace najdete v tématu [omezení používání Správce clusteru s podporou převzetí služeb při selhání se skupinami dostupnosti](https://msdn.microsoft.com/library/ff929171.aspx).
    >

V tomto okamžiku máte skupinu dostupnosti s replikami na dvou instancích SQL Server. Skupinu dostupnosti můžete přesunout mezi instancemi. Nemůžete se připojit ke skupině dostupnosti, protože nemáte naslouchací proces. Naslouchací proces vyžaduje nástroj pro vyrovnávání zatížení ve virtuálních počítačích Azure. Dalším krokem je vytvoření nástroje pro vyrovnávání zatížení v Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení Azure

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Na virtuálních počítačích Azure vyžaduje Skupina dostupnosti SQL Server službu pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení uchovává IP adresy pro naslouchací procesy skupiny dostupnosti a cluster s podporou převzetí služeb při selhání systému Windows Server. Tato část shrnuje, jak vytvořit nástroj pro vyrovnávání zatížení v Azure Portal.

Nástroj pro vyrovnávání zatížení v Azure může být buď Standard Load Balancer, nebo základní Load Balancer. Standard Load Balancer má více funkcí než základní Load Balancer. V případě skupiny dostupnosti se Standard Load Balancer vyžaduje, pokud použijete zónu dostupnosti (místo skupiny dostupnosti). Podrobnosti o rozdílu mezi SKU nástroje pro vyrovnávání zatížení najdete v tématu [Load Balancer porovnání skladové](../../../load-balancer/skus.md)položky.

1. V Azure Portal otevřete skupinu prostředků, kde jsou vaše servery SQL, a vyberte **+ Přidat**.
1. Vyhledejte **Load Balancer**. Vyberte nástroj pro vyrovnávání zatížení publikovaný Microsoftem.

   ![Volba nástroje pro vyrovnávání zatížení publikovaného Microsoftem](./media/availability-group-manually-configure-tutorial/82-azureloadbalancer.png)

1. Vyberte **Vytvořit**.
1. Pro nástroj pro vyrovnávání zatížení nakonfigurujte následující parametry.

   | Nastavení | Pole |
   | --- | --- |
   | **Název** |Použijte textový název nástroje pro vyrovnávání zatížení, například **sqlLB**. |
   | **Typ** |Interní |
   | **Virtuální síť** |Použijte název virtuální sítě Azure. |
   | **Podsíť** |Použijte název podsítě, ve které se nachází virtuální počítač.  |
   | **Přiřazení IP adresy** |Static |
   | **IP adresa** |Použijte dostupnou adresu z podsítě. Tuto adresu použijte pro naslouchací proces skupiny dostupnosti. Všimněte si, že se liší od IP adresy vašeho clusteru.  |
   | **Předplatné** |Použijte stejné předplatné jako virtuální počítač. |
   | **Umístění** |Použijte stejné umístění jako virtuální počítač. |

   Okno Azure Portal by mělo vypadat takto:

   ![Vytvořit Load Balancer](./media/availability-group-manually-configure-tutorial/84-createloadbalancer.png)

1. Vyberte **vytvořit**, chcete-li vytvořit nástroj pro vyrovnávání zatížení.

Pokud chcete nakonfigurovat nástroj pro vyrovnávání zatížení, musíte vytvořit back-end fond, test a nastavit pravidla vyrovnávání zatížení. Udělejte to Azure Portal.

### <a name="add-a-backend-pool-for-the-availability-group-listener"></a>Přidejte back-end fond pro naslouchací proces skupiny dostupnosti.

1. V Azure Portal přejdete do skupiny dostupnosti. Možná budete muset zobrazení aktualizovat, aby se zobrazil nově vytvořený Nástroj pro vyrovnávání zatížení.

   ![Najít Load Balancer ve skupině prostředků](./media/availability-group-manually-configure-tutorial/86-findloadbalancer.png)

1. Vyberte nástroj pro vyrovnávání zatížení, vyberte **fondy back-endu**a vyberte **+ Přidat**.

1. Zadejte název back-end fondu.

1. Přidružte back-end fond ke skupině dostupnosti, která obsahuje virtuální počítače.

1. V části **cílové síťové konfigurace IP adresy**zaškrtněte **virtuální počítač** a vyberte oba virtuální počítače, které budou hostovat repliky skupin dostupnosti. Nezahrnujte souborový server sdílené složky.

   >[!NOTE]
   >Pokud nejsou zadány oba virtuální počítače, připojení budou úspěšná pouze pro primární repliku.

1. Výběrem **OK** vytvořte fond back-end.

### <a name="set-the-probe"></a>Nastavení testu paměti

1. Vyberte nástroj pro vyrovnávání zatížení, zvolte **sondy stavu**a pak vyberte **+ Přidat**.

1. Nastavte sondu stavu naslouchacího procesu následujícím způsobem:

   | Nastavení | Popis | Příklad
   | --- | --- |---
   | **Název** | Text | SQLAlwaysOnEndPointProbe |
   | **Protokol** | Zvolte TCP. | TCP |
   | **Port** | Libovolný nepoužitý port | 59999 |
   | **Interval**  | Doba mezi pokusy o sondu v sekundách |5 |
   | **Prahová hodnota pro poškozený stav** | Počet po sobě jdoucích selhání testu, které se musí vyskytnout, když se virtuální počítač považuje za poškozený  | 2 |

1. Vyberte **OK** a nastavte sondu stavu.

### <a name="set-the-load-balancing-rules"></a>Nastavení pravidel vyrovnávání zatížení

1. Vyberte nástroj pro vyrovnávání zatížení, zvolte možnost **pravidla vyrovnávání zatížení**a vyberte **+ Přidat**.

1. Nastavte pravidla vyrovnávání zatížení naslouchacího procesu následujícím způsobem.

   | Nastavení | Popis | Příklad
   | --- | --- |---
   | **Název** | Text | SQLAlwaysOnEndPointListener |
   | **IP adresa front-endu** | Zvolit adresu |Použijte adresu, kterou jste vytvořili při vytváření nástroje pro vyrovnávání zatížení. |
   | **Protokol** | Zvolte TCP. |TCP |
   | **Port** | Použijte port pro naslouchací proces skupiny dostupnosti. | 1433 |
   | **Port back-endu** | Toto pole se nepoužívá, je-li pro přímé vrácení serveru nastavená plovoucí IP adresa. | 1433 |
   | **Sonda** |Název, který jste zadali pro test paměti | SQLAlwaysOnEndPointProbe |
   | **Trvalost relace** | Rozevírací seznam | **Žádný** |
   | **Časový limit nečinnosti** | Počet minut, po který se má připojení TCP nechat otevřené | 4 |
   | **Plovoucí IP adresa (přímá návrat ze serveru)** | |Povoleno |

   > [!WARNING]
   > Při vytváření se nastaví přímá návrat serveru. Název není možné změnit.
   >

1. Vyberte **OK** a nastavte pravidla vyrovnávání zatížení naslouchacího procesu.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Přidání základní IP adresy clusteru pro cluster s podporou převzetí služeb při selhání systému Windows Server (WSFC)

IP adresa služby WSFC také musí být v nástroji pro vyrovnávání zatížení.

1. V Azure Portal navštivte stejný nástroj pro vyrovnávání zatížení Azure. Vyberte možnost **Konfigurace IP adresy front-endu** a vyberte **+ Přidat**. Použijte IP adresu, kterou jste nakonfigurovali pro služby WSFC v základních prostředcích clusteru. Nastavte IP adresu jako statickou.

1. V nástroji pro vyrovnávání zatížení vyberte **sondy stavu**a pak vyberte **+ Přidat**.

1. Následujícím způsobem nastavte test stavu hlavní IP adresy clusteru služby WSFC:

   | Nastavení | Popis | Příklad
   | --- | --- |---
   | **Název** | Text | WSFCEndPointProbe |
   | **Protokol** | Zvolte TCP. | TCP |
   | **Port** | Libovolný nepoužitý port | 58888 |
   | **Interval**  | Doba mezi pokusy o sondu v sekundách |5 |
   | **Prahová hodnota pro poškozený stav** | Počet po sobě jdoucích selhání testu, které se musí vyskytnout, když se virtuální počítač považuje za poškozený  | 2 |

1. Vyberte **OK** a nastavte sondu stavu.

1. Nastavte pravidla vyrovnávání zatížení. Vyberte **pravidla vyrovnávání zatížení**a vyberte **+ Přidat**.

1. Následujícím způsobem nastavte pravidla vyrovnávání zatížení základních IP adres clusteru.

   | Nastavení | Popis | Příklad
   | --- | --- |---
   | **Název** | Text | WSFCEndPoint |
   | **IP adresa front-endu** | Zvolit adresu |Použijte adresu, kterou jste vytvořili při konfiguraci IP adresy služby WSFC. To se liší od IP adresy naslouchacího procesu. |
   | **Protokol** | Zvolte TCP. |TCP |
   | **Port** | Použijte port pro IP adresu clusteru. Toto je dostupný port, který se nepoužívá pro port testu naslouchacího procesu. | 58888 |
   | **Port back-endu** | Toto pole se nepoužívá, je-li pro přímé vrácení serveru nastavená plovoucí IP adresa. | 58888 |
   | **Sonda** |Název, který jste zadali pro test paměti | WSFCEndPointProbe |
   | **Trvalost relace** | Rozevírací seznam | **Žádný** |
   | **Časový limit nečinnosti** | Počet minut, po který se má připojení TCP nechat otevřené | 4 |
   | **Plovoucí IP adresa (přímá návrat ze serveru)** | |Povoleno |

   > [!WARNING]
   > Při vytváření se nastaví přímá návrat serveru. Název není možné změnit.
   >

1. Vyberte **OK** a nastavte pravidla vyrovnávání zatížení.

## <a name="configure-the-listener"></a><a name="configure-listener"></a> Konfigurace naslouchacího procesu

Dalším krokem je konfigurace naslouchacího procesu skupiny dostupnosti v clusteru s podporou převzetí služeb při selhání.

> [!NOTE]
> V tomto kurzu se dozvíte, jak vytvořit jeden naslouchací proces s jednou IP adresou interního nástroje. Pokud chcete vytvořit jeden nebo více posluchačů pomocí jedné nebo více IP adres, přečtěte si téma [Vytvoření naslouchacího procesu skupiny dostupnosti a nástroje pro vyrovnávání zatížení | Azure](availability-group-listener-powershell-configure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Nastavit port naslouchacího procesu

V SQL Server Management Studio nastavte port naslouchacího procesu.

1. Spusťte SQL Server Management Studio a připojte se k primární replice.

1. Přejděte na **AlwaysOn High Availability**  >  **availability groups**  >  **naslouchací procesy**skupin dostupnosti AlwaysOn vysoké dostupnosti.

1. Nyní byste měli vidět název naslouchacího procesu, který jste vytvořili v Správce clusteru s podporou převzetí služeb při selhání. Klikněte pravým tlačítkem myši na název naslouchacího procesu a vyberte možnost **vlastnosti**.

1. Do pole **port** zadejte číslo portu pro naslouchací proces skupiny dostupnosti. Výchozí hodnota je 1433. Vyberte **OK**.

Teď máte SQL Server skupinu dostupnosti ve virtuálních počítačích Azure, které běží v režimu Správce prostředků.

## <a name="test-connection-to-listener"></a>Testovat připojení ke službě Listener

Otestování připojení:

1. Pomocí protokolu RDP se připojte k SQL Server, která se nachází ve stejné virtuální síti, ale nevlastní repliku. V clusteru můžete použít jiné SQL Server.

1. K otestování připojení použijte nástroj **Sqlcmd** . Například následující skript vytvoří připojení **Sqlcmd** k primární replice prostřednictvím naslouchacího procesu s ověřováním systému Windows:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Pokud naslouchací proces používá jiný port než výchozí port (1433), zadejte port v připojovacím řetězci. Například následující `sqlcmd` příkaz se připojí k naslouchacího procesu na portu 1435:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

Připojení SQLCMD se automaticky připojí k jakékoli instanci SQL Server hostuje primární repliku.

> [!TIP]
> Ujistěte se, že port, který zadáte, je otevřený v bráně firewall obou serverů SQL. Oba servery vyžadují příchozí pravidlo pro port TCP, který používáte. Další informace najdete v tématu [Přidání nebo úprava pravidla brány firewall](https://technet.microsoft.com/library/cc753558.aspx).
>

## <a name="next-steps"></a>Další kroky

- [Přidejte IP adresu do nástroje pro vyrovnávání zatížení pro druhou skupinu dostupnosti](availability-group-listener-powershell-configure.md#Add-IP).
