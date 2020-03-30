---
title: 'Kurz: Konfigurace skupiny dostupnosti'
description: Tento kurz ukazuje, jak vytvořit SQL Server vždy na dostupnost skupiny na virtuálních počítačích Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 426ba4c0ac84799b4d0e6bf9330508f928437fd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060176"
---
# <a name="tutorial-configure-availability-group-on-azure-sql-server-vm-manually"></a>Kurz: Konfigurace skupiny dostupnosti na virtuálním počítači Azure SQL Server ručně

Tento kurz ukazuje, jak vytvořit SQL Server vždy na dostupnost skupiny na virtuálních počítačích Azure. Kompletní kurz vytvoří skupinu dostupnosti s replikou databáze na dvou serverech SQL.

**Časový odhad**: Dokončení trvá přibližně 30 minut, jakmile jsou splněny požadavky.

Diagram znázorňuje, co vytvoříte v kurzu.

![Skupina dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Požadavky

Kurz předpokládá, že máte základní znalosti sql server vždy na dostupnost skupiny. Pokud potřebujete další informace, naleznete [v tématu Přehled skupin dostupnosti vždy na (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).

V následující tabulce jsou uvedeny požadavky, které je třeba před zahájením tohoto kurzu dokončit:

|  |Požadavek |Popis |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Dva servery SQL | - V sadě dostupnosti Azure <br/> - V jedné doméně <br/> - S nainstalovanou funkcí clusteringu s podporou převzetí služeb při selhání |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Sdílená složka pro cluster upozor, |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Účet služby serveru SQL Server | Účet domény |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Účet služby agenta serveru SQL Server | Účet domény |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Otevření portů brány firewall | - SQL Server: **1433** pro výchozí instanci <br/> - Koncový bod zrcadlení databáze: **5022** nebo jakýkoli dostupný port <br/> - Dostupnost skupiny load balancer IP adresa sondy: **59999** nebo jakýkoli dostupný port <br/> - Sonda stavu IP adresy jádra jádra clusteru: **58888** nebo jakýkoli dostupný port |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Přidat funkci clusteringu s podporou převzetí služeb při selhání | Oba servery SQL vyžadují tuto funkci |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Účet domény instalace | - Místní správce na každém SERVERU SQL <br/> - Člen SQL Server sysadmin pevná role serveru pro každou instanci SQL Server  |


Než začnete kurz, je třeba [dokončit předpoklady pro vytváření vždy na dostupnost skupiny ve virtuálních počítačích Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md). Pokud jsou tyto požadavky již dokončeny, můžete přejít na [vytvořit cluster](#CreateCluster).

  >[!NOTE]
  > Mnoho kroků uvedených v tomto kurzu lze nyní automatizovat pomocí [azure SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) a [Azure Quickstart šablony](virtual-machines-windows-sql-availability-group-quickstart-template.md).


<!--**Procedure**: *This is the first "step". Make titles H2's and short and clear – H2's appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## <a name="create-the-cluster"></a>Vytvoření clusteru

Po dokončení požadavků je prvním krokem vytvoření clusteru s podporou převzetí služeb při selhání systému Windows Server, který zahrnuje dva servery SQL Severs a server s kopií clusteru.

1. RDP na první SQL Server pomocí účtu domény, který je správcem na serverech SQL i serveru s kopií clusteru s kopií clusteru.

   >[!TIP]
   >Pokud jste postupovali [podle dokumentu požadavků](virtual-machines-windows-portal-sql-availability-group-prereq.md), vytvořili jste účet s názvem **CORP\Install**. Použijte tento účet.

2. Na řídicím panelu **Správce serveru** vyberte **nástroje**a klepněte na položku **Správce clusteru s podporou převzetí služeb při selhání**.
3. V levém podokně klepněte pravým tlačítkem myši na **Správce clusteru s podporou převzetí služeb při selhání**a potom **klepněte**na příkaz Vytvořit cluster .
   ![Vytvořit cluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. V Průvodci vytvořením clusteru vytvořte cluster s jedním uzlem krokování stránek s nastavením v následující tabulce:

   | stránka | Nastavení |
   | --- | --- |
   | Než začnete |Použít výchozí hodnoty |
   | Vybrat servery |Do pole **Zadejte název serveru Enter** zadejte název serveru a klepněte na tlačítko **Přidat**. |
   | Upozornění na ověření |Vyberte **ne. Nevyžadují podporu od společnosti Microsoft pro tento cluster, a proto nechcete spustit ověřovací testy. Po klepnutí na tlačítko Další pokračujte ve vytváření clusteru**. |
   | Přístupový bod pro správu clusteru |Zadejte název clusteru, například **SQLAGCluster1** v **názvu clusteru**.|
   | Potvrzení |Výchozí hodnoty používejte, pokud nepoužíváte prostory úložiště. Viz poznámka následující k této tabulce. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Nastavení adresy IP clusteru s podporou převzetí služeb při selhání systému Windows

  > [!NOTE]
  > V systému Windows Server 2019 vytvoří cluster **název distribuovaného serveru** namísto **síťového názvu clusteru**. Pokud používáte Windows Server 2019, přeskočte všechny kroky, které odkazují na název jádra clusteru v tomto kurzu. Pomocí prostředí PowerShell můžete vytvořit název sítě [clusteru](virtual-machines-windows-portal-sql-create-failover-cluster.md#windows-server-2019). Další informace naleznete v [blogovém clusteru s podporou převzetí služeb při selhání: Síťový objekt clusteru.](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) 

1. Ve **Správci clusterů s podporou převzetí služeb při selhání**přejděte dolů k **jádrovým prostředkům clusteru** a rozbalte podrobnosti clusteru. Měli byste vidět **název** a **IP adresu** prostředky ve stavu **Selhalo.** Prostředek adresy IP nelze přepojit do režimu online, protože clusteru je přiřazena stejná adresa IP jako samotnému počítači, proto se jedná o duplicitní adresu.

2. Klepněte pravým tlačítkem myši na neúspěšný prostředek **adresy IP** a potom klepněte na příkaz **Vlastnosti**.

   ![Vlastnosti clusteru](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Vyberte **Statická adresa IP** a zadejte dostupnou adresu ze stejné podsítě jako virtuální počítače.

4. V části **Základní prostředky clusteru** klikněte pravým tlačítkem myši na název clusteru a klikněte na **příkaz Převést do režimu online**. Potom počkejte, dokud nebudou oba prostředky online. Když je prostředek názvu clusteru online, aktualizuje server řadiče domény novým účtem počítače služby AD. Tento účet služby AD slouží k pozdějšímu spuštění clusterované služby Skupiny dostupnosti.

### <a name="add-the-other-sql-server-to-cluster"></a><a name="addNode"></a>Přidání dalšího serveru SQL Server do clusteru

Přidejte do clusteru další sql server.

1. Ve stromu prohlížeče klepněte pravým tlačítkem myši na cluster a klepněte na příkaz **Přidat uzel**.

    ![Přidání uzlu do clusteru](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. V **Průvodci přidáním uzlu**klepněte na tlačítko **Další**. Na stránce **Vybrat servery** přidejte druhý SQL Server. Zadejte název serveru do pole **Zadejte název serveru** a klepněte na tlačítko **Přidat**. Až budete hotovi, klepněte na tlačítko **Další**.

1. Na stránce **Upozornění na ověření** klikněte na **ne** (v produkčním scénáři byste měli provést ověřovací testy). Potom klepněte na tlačítko **Další**.

8. Pokud **Confirmation** používáte prostory úložiště, zrušte zaškrtnutí políčka **Přidat do clusteru všechna způsobilá úložiště.**

   ![Přidat potvrzení uzlu](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Pokud používáte prostory úložiště a neodškrtejte **políčko Přidat do clusteru všechna způsobilá úložiště**, systém Windows odpojí virtuální disky během procesu clusteringu. V důsledku toho se nezobrazují ve Správci disků nebo v Průzkumníkovi, dokud nebudou prostory úložiště odebrány z clusteru a znovu připojeny pomocí prostředí PowerShell. Prostory úložiště seskupí více disků do fondů úložiště. Další informace naleznete [v tématu Storage Spaces](https://technet.microsoft.com/library/hh831739).

1. Klikněte na **Další**.

1. Klikněte na **Finish** (Dokončit).

   Správce clusteru s podporou převzetí služeb při selhání ukazuje, že váš cluster má nový uzel, a zobrazí jej v kontejneru **Uzly.**

10. Odhlaste se z relace vzdálené plochy.

### <a name="add-a-cluster-quorum-file-share"></a>Přidání sdílené složky kvora clusteru

V tomto příkladu cluster windows používá sdílenou složku k vytvoření kvora clusteru. Tento kurz používá kvorum většinové většiny uzlů a sdílení souborů. Další informace najdete v článku [Principy konfigurací kvora v clusteru s podporou převzetí služeb při selhání](https://technet.microsoft.com/library/cc731739.aspx).

1. Připojte se k serveru členů sdílené složky pomocí relace vzdálené plochy.

1. Ve **Správci serveru**klepněte na tlačítko **Nástroje**. Otevřete **program Správa počítače**.

1. Klepněte na **položku Sdílené složky**.

1. Klepněte pravým tlačítkem myši na **položku Sdílené**položky a klepněte na příkaz **Nová sdílená položka.**

   ![Nová sdílená](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   K vytvoření sdílené složky použijte **Průvodce vytvořením sdílené složky.**

1. V **části Cesta ke složce**klepněte na tlačítko **Procházet** a vyhledejte nebo vytvořte cestu ke sdílené složce. Klikněte na **Další**.

1. V **části Název, Popis a Nastavení** ověřte název sdílené položky a cestu. Klikněte na **Další**.

1. V **nabídce Oprávnění ke sdíleným složkám** nastavte **možnost Přizpůsobit oprávnění**. Klikněte na **Vlastní...**.

1. V **části Přizpůsobit oprávnění**klepněte na tlačítko **Přidat...**.

1. Ujistěte se, že účet použitý k vytvoření clusteru má úplnou kontrolu.

   ![Nová sdílená](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Klikněte na tlačítko **OK**.

1. V části **Oprávnění ke sdíleným složkám**klepněte na **tlačítko Dokončit**. Klepněte znovu na **tlačítko Dokončit.**  

1. Odhlášení ze serveru

### <a name="configure-cluster-quorum"></a>Konfigurace kvora clusteru

Dále nastavte kvorum clusteru.

1. Připojte se k prvnímu uzlu clusteru pomocí vzdálené plochy.

1. Ve **Správci clusteru s podporou převzetí služeb při selhání**klepněte pravým tlačítkem myši na cluster, přejděte na příkaz Další **akce**a klepněte na příkaz Konfigurovat **nastavení kvora clusteru...**.

   ![Nová sdílená](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. V **průvodci konfigurací kvora clusteru**klepněte na tlačítko **Další**.

1. V **části Možnost vybrat konfiguraci kvora**zvolte Vybrat důkaz **kvora**a klepněte na tlačítko **Další**.

1. V **nabídce Vybrat důkaz kvora**klepněte na **tlačítko Konfigurovat důkaz sdílení souborů**.

   >[!TIP]
   >Windows Server 2016 podporuje cloud ovou svědkyně. Pokud zvolíte tento typ svědka, nepotřebujete důkaz sdílení souborů. Další informace naleznete [v tématu Nasazení cloudu důkaz pro cluster s podporou převzetí služeb při selhání](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). Tento kurz používá důkaz čehož připojili se sdílená složka, která je podporována předchozími operačními systémy.

1. V **části Konfigurovat důkaz pro sdílení souborů**zadejte cestu pro sdílenou složku, kterou jste vytvořili. Klikněte na **Další**.

1. Ověřte nastavení v **potvrzovacím programu**. Klikněte na **Další**.

1. Klikněte na **Finish** (Dokončit).

Základní prostředky clusteru jsou nakonfigurovány s důkaz čehožpřipojili se sdílená složka.

## <a name="enable-availability-groups"></a>Povolit skupiny dostupnosti

Dále povolte funkci **AlwaysOn Availability Groups.** Proveďte tyto kroky na obou serverech SQL.

1. Na **úvodní** obrazovce spusťte **nástroj SQL Server Configuration Manager**.
2. Ve stromu prohlížeče klepněte na položku **SQL Server Services**, klepněte pravým tlačítkem myši na službu SQL Server **(MSSQLSERVER)** a klepněte na příkaz **Vlastnosti**.
3. Klikněte na kartu **AlwaysOn High Availability** a pak vyberte **Povolit skupiny dostupnosti AlwaysOn**, a to následovně:

    ![Povolit skupiny dostupnosti Alwayson](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Klikněte na **Použít**. V rozbalovacím dialogovém okně klikněte na **OK.**

5. Restartujte službu SQL Server.

Opakujte tyto kroky na jiném serveru SQL Server.

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

## <a name="create-a-database-on-the-first-sql-server"></a>Vytvoření databáze na prvním serveru SQL Server

1. Spusťte soubor RDP na první SQL Server s účtem domény, který je členem pevné role serveru sysadmin.
1. Otevřete SQL Server Management Studio a připojte se k prvnímu serveru SQL Server.
7. V **Průzkumníkovi objektů**klepněte pravým tlačítkem myši na **položku Databáze** a klepněte na příkaz Nová **databáze**.
8. Do **pole Název databáze**zadejte příkaz **MyDB1**a klepněte na tlačítko **OK**.

### <a name="create-a-backup-share"></a><a name="backupshare"></a>Vytvoření záložní sdílené složky

1. Na prvním serveru SQL Server ve **Správci serveru**klepněte na **položku Nástroje**. Otevřete **program Správa počítače**.

1. Klepněte na **položku Sdílené složky**.

1. Klepněte pravým tlačítkem myši na **položku Sdílené**položky a klepněte na příkaz **Nová sdílená položka.**

   ![Nová sdílená](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   K vytvoření sdílené složky použijte **Průvodce vytvořením sdílené složky.**

1. V **části Cesta ke složce**klepněte na tlačítko **Procházet** a vyhledejte nebo vytvořte cestu pro sdílenou složku záloha databáze. Klikněte na **Další**.

1. V **části Název, Popis a Nastavení** ověřte název sdílené položky a cestu. Klikněte na **Další**.

1. V **nabídce Oprávnění ke sdíleným složkám** nastavte **možnost Přizpůsobit oprávnění**. Klikněte na **Vlastní...**.

1. V **části Přizpůsobit oprávnění**klepněte na tlačítko **Přidat...**.

1. Ujistěte se, že účty služby SQL Server a SQL Server Agent pro oba servery mají úplnou kontrolu.

   ![Nová sdílená](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Klikněte na tlačítko **OK**.

1. V části **Oprávnění ke sdíleným složkám**klepněte na **tlačítko Dokončit**. Klepněte znovu na **tlačítko Dokončit.**  

### <a name="take-a-full-backup-of-the-database"></a>Úplné zálohování databáze

Chcete-li inicializovat řetězec protokolů, je třeba zálohovat novou databázi. Pokud neprovedete zálohu nové databáze, nelze ji zahrnout do skupiny dostupnosti.

1. V **Průzkumníkovi objektů**klepněte pravým tlačítkem myši na databázi, přejděte na **úkoly...** a klepněte na příkaz **Zálohovat**.

1. Klepnutím na **tlačítko OK** přenesete úplnou zálohu do výchozího umístění zálohy.

## <a name="create-the-availability-group"></a>Vytvoření skupiny dostupnosti
Nyní jste připraveni nakonfigurovat skupinu dostupnosti pomocí následujících kroků:

* Vytvořte databázi na prvním serveru SQL Server.
* Provést úplnou zálohu i zálohu transakčního protokolu databáze
* Obnovení úplného a protokolování záloh na druhý sql server pomocí možnosti **NORECOVERY**
* Vytvoření skupiny dostupnosti **(AG1)** se synchronním potvrzením, automatickým převzetím služeb při selhání a čitelnými sekundárními replikami

### <a name="create-the-availability-group"></a>Vytvořte skupinu dostupnosti:

1. V relaci vzdálené plochy na první SQL Server. V **Průzkumníku objektů** v SSMS klikněte pravým tlačítkem myši **na položku AlwaysOn High Availability** a klepněte na příkaz Průvodce **skupinou dostupnosti**.

    ![Průvodce spuštěním nové skupiny dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. Na stránce **Introduction** klikněte na **Další**. Na stránce **Zadat název skupiny dostupnosti** zadejte název skupiny dostupnosti, například **AG1**, do **názvu skupiny Dostupnost**. Klikněte na **Další**.

    ![Nový Průvodce AG, zadejte název AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. Na stránce **Vybrat databáze** vyberte databázi a klepněte na tlačítko **Další**.

   >[!NOTE]
   >Databáze splňuje požadavky pro skupinu dostupnosti, protože jste provedli alespoň jednu úplnou zálohu na zamýšlené primární replice.

   ![Nový Průvodce ag, Vybrat databáze](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. Na stránce **Zadat repliky** klepněte na **tlačítko Přidat repliku**.

   ![Nový Průvodce ag, zadat repliky](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. Zobrazí se dialogové okno **Připojit k serveru.** Zadejte název druhého serveru do **pole Název serveru**. Klikněte na **Připojit**.

   Na stránce **Zadat repliky** byste nyní měli vidět druhý server uvedený v **části Repliky dostupnosti**. Nakonfigurujte repliky následujícím způsobem.

   ![Nový Průvodce ag, zadat repliky (kompletní)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Kliknutím na **Koncové body** zobrazíte koncový bod zrcadlení databáze pro tuto skupinu dostupnosti. Použijte stejný port, který jste použili při nastavení [pravidla brány firewall pro koncové body zrcadlení databáze](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

    ![Nový Průvodce ag, vybrat počáteční synchronizaci dat](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. Na stránce **Vybrat počáteční synchronizaci dat** vyberte Možnost **Úplné** a určete sdílené síťové umístění. Pro umístění použijte [záložní sdílenou složku, kterou jste vytvořili](#backupshare). V příkladu to ** \\ \\ \<bylo,\>První\\SQL Server \Backup**. Klikněte na **Další**.

   >[!NOTE]
   >Úplná synchronizace trvá úplnou zálohu databáze na první instanci SQL Server a obnoví ji do druhé instance. U velkých databází se nedoporučuje úplná synchronizace, protože může trvat dlouhou dobu. Tuto dobu můžete zkrátit ručním zálohováním databáze a obnovením pomocí aplikace `NO RECOVERY`. Pokud je databáze již `NO RECOVERY` obnovena na druhém serveru SQL Server před konfigurací skupiny dostupnosti, zvolte **pouze připojit .** Pokud chcete po konfiguraci skupiny dostupnosti provést zálohu, zvolte **Přeskočit počáteční synchronizaci dat**.

    ![Nový Průvodce ag, vybrat počáteční synchronizaci dat](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. Na stránce **Ověření** klikněte na **další**. Tato stránka by měla vypadat podobně jako na následujícím obrázku:

    ![Nový Průvodce AG, ověření](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >Pro konfiguraci posluchače je upozornění, protože jste nenakonfigurovali naslouchací proces skupiny dostupnosti. Toto upozornění můžete ignorovat, protože na virtuálních počítačích Azure vytvoříte naslouchací proces po vytvoření nástroje pro vyrovnávání zatížení Azure.

10. Na stránce **Souhrn** klikněte na **tlačítko Dokončit**a počkejte, než průvodce nakonfiguruje novou skupinu dostupnosti. Na stránce **Průběh** můžete kliknutím na **Další podrobnosti** zobrazit podrobný průběh. Po dokončení průvodce zkontrolujte stránku **Výsledky** a ověřte, zda byla skupina dostupnosti úspěšně vytvořena.

     ![Nový Průvodce AG, Výsledky](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Průvodce ukončíte kliknutím na **Zavřít** .

### <a name="check-the-availability-group"></a>Zkontrolujte skupinu dostupnosti

1. V **Průzkumníkovi objektů**rozbalte **položku AlwaysOn High Availability**a potom **rozbalte položku Skupiny dostupnosti**. Nyní byste měli vidět novou skupinu dostupnosti v tomto kontejneru. Klikněte pravým tlačítkem myši na skupinu dostupnosti a klikněte na **zobrazit řídicí panel**.

   ![Zobrazit řídicí panel AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   AlwaysOn **Dashboard** by měl vypadat podobně jako tento.

   ![Řídicí panel AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   Můžete zobrazit repliky, režim převzetí služeb při selhání každé repliky a stav synchronizace.

2. Ve **Správci clusteru s podporou převzetí služeb při selhání**klepněte na cluster. Vyberte **role**. Název skupiny dostupnosti, který jste použili, je role v clusteru. Tato skupina dostupnosti nemá adresu IP pro připojení klientů, protože jste nenakonfigurovali naslouchací proces. Nakonfigurujete naslouchací proces po vytvoření azure vyrovnávání zatížení.

   ![AG ve Správci clusterů s podporou převzetí služeb při selhání](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Nepokoušejte se převzetí služeb při selhání skupiny dostupnosti ze Správce clusteru s podporou převzetí služeb při selhání. Všechny operace převzetí služeb při selhání by měly být prováděny z v rámci **AlwaysOn řídicího panelu** v SSMS. Další informace naleznete [v tématu Omezení používání Správce clusteru s podporou převzetí služeb při selhání se skupinami dostupnosti](https://msdn.microsoft.com/library/ff929171.aspx).
    >

V tomto okamžiku máte skupinu dostupnosti s replikami na dvou instancích serveru SQL Server. Skupinu dostupnosti můžete přesunout mezi instancemi. Skupinu dostupnosti se zatím nelze připojit, protože nemáte naslouchací proces. Ve virtuálních počítačích Azure naslouchací proces vyžaduje nástroj pro vyrovnávání zatížení. Dalším krokem je vytvoření vykladače zatížení v Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení Azure

Ve virtuálních počítačích Azure vyžaduje skupina dostupnosti serveru SQL Server nástroj pro vyrovnávání zatížení. Správce zatížení obsahuje adresy IP pro posluchače skupiny dostupnosti a cluster s podporou převzetí služeb při selhání systému Windows Server. Tato část shrnuje, jak vytvořit vyrovnávání zatížení na webu Azure Portal.

Azure Balancer může být standardní vyrovnávání zatížení nebo základní vyrovnávání zatížení. Standardní vyrovnávání zatížení má více funkcí než základní vyrovnávání zatížení. Pro skupinu dostupnosti je vyžadován standardní vyrovnávání zatížení, pokud používáte zónu dostupnosti (namísto skupiny dostupnosti). Podrobnosti o rozdílu mezi typy nástrojů pro vyrovnávání zatížení naleznete v tématu [porovnání skladových položk nástrojů pro vyrovnávání zatížení](../../../load-balancer/concepts-limitations.md#skus).

1. Na webu Azure Portal přejděte do skupiny prostředků, kde jsou vaše servery SQL, a klikněte na **+ Přidat**.
1. Vyhledejte **vyvažovače zatížení**. Zvolte systém vyrovnávání zatížení publikovaný společností Microsoft.

   ![AG ve Správci clusterů s podporou převzetí služeb při selhání](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1. Klikněte na **Vytvořit**.
1. Nakonfigurujte následující parametry pro vykladač zatížení.

   | Nastavení | Pole |
   | --- | --- |
   | **Název** |Použijte textový název pro nástroje pro vyrovnávání zatížení, například **sqlLB**. |
   | **Typ** |Interní |
   | **Virtuální síť** |Použijte název virtuální sítě Azure. |
   | **Podsíť** |Použijte název podsítě, ve které se virtuální počítač nachází.  |
   | **Přiřazení IP adresy** |Statická |
   | **IP adresa** |Použijte dostupnou adresu z podsítě. Tuto adresu použijte pro posluchače skupiny dostupnosti. Všimněte si, že se liší od adresy IP clusteru.  |
   | **Předplatné** |Použijte stejné předplatné jako virtuální počítač. |
   | **Umístění** |Použijte stejné umístění jako virtuální počítač. |

   Okno portálu Azure by mělo vypadat takto:

   ![Vytvořit balancer zatížení](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Klepnutím na **tlačítko Vytvořit**vytvořte položku Vyrovnávání zatížení.

Chcete-li nakonfigurovat vyrovnávání zatížení, musíte vytvořit back-endový fond, sondu a nastavit pravidla vyrovnávání zatížení. Udělejte to na webu Azure Portal.

### <a name="add-backend-pool-for-the-availability-group-listener"></a>Přidat back-endový fond pro naslouchací proces skupiny dostupnosti

1. Na webu Azure Portal přejděte do skupiny dostupnosti. Možná budete muset aktualizovat zobrazení, abyste viděli nově vytvořený vyrovnávání zatížení.

   ![Najít vyhledání vyhledání ve skupině prostředků](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Klikněte na balancer, klikněte na **Fondy back-endu**a klikněte na **+Přidat**.

1. Zadejte název back-endového fondu.

1. Přidružte back-endový fond k množině dostupnosti, která obsahuje virtuální servery.

1. V části **Cílové konfigurace IP adres sítě**zkontrolujte virtual **machine** a zvolte oba virtuální počítače, které budou hostovat repliky skupin dostupnosti. Nezahrnejte server s kopií clusteru.

   >[!NOTE]
   >Pokud nejsou zadány oba virtuální počítače, připojení budou úspěšné pouze k primární replice.

1. Chcete-li vytvořit fond back-endu, klepněte na tlačítko **OK.**

### <a name="set-the-probe"></a>Nastavení sondy

1. Klikněte na systém vyrovnávání zatížení, klikněte na **sondy stavu**a klikněte na **+Přidat**.

1. Nastavte sondu stavu naslouchací procesu takto:

   | Nastavení | Popis | Příklad
   | --- | --- |---
   | **Název** | Text | SQLAlwaysOnEndPointProbe |
   | **Protokol** | Zvolte TCP. | TCP |
   | **Port** | Jakýkoli nevyužitý port | 59999 |
   | **Interval**  | Doba mezi pokusy o sondu v sekundách |5 |
   | **Prahová hodnota pro poškozený stav** | Počet po sobě jdoucích selhání sondy, ke kterým musí dojít, aby byl virtuální počítač považován za nefunkční  | 2 |

1. Klepnutím na **tlačítko OK** nastavte sondu stavu.

### <a name="set-the-load-balancing-rules"></a>Nastavení pravidel vyrovnávání zatížení

1. Klikněte na balancer, klikněte na **Pravidla vyrovnávání zatížení**a klikněte na **+Přidat**.

1. Nastavte pravidla vyrovnávání zatížení naslouchací hosti takto.

   | Nastavení | Popis | Příklad
   | --- | --- |---
   | **Název** | Text | SQLAlwaysOnEndPointListener |
   | **IP adresa front-endu** | Zvolte adresu |Použijte adresu, kterou jste vytvořili při vytváření dobytku načítání. |
   | **Protokol** | Zvolte TCP. |TCP |
   | **Port** | Použití portu pro naslouchací proces skupiny dostupnosti | 1433 |
   | **Back-endový port** | Toto pole se nepoužívá, pokud je plovoucí IP adresa nastavena pro přímé vrácení serveru. | 1433 |
   | **Sonda** |Název, který jste zadali pro sondu | SQLAlwaysOnEndPointProbe |
   | **Trvalosti relace** | Rozevírací seznam | **Žádné** |
   | **Časový limit nečinnosti** | Minuty pro zachování otevřeného připojení TCP | 4 |
   | **Plovoucí IP (přímý návrat serveru)** | |Povoleno |

   > [!WARNING]
   > Přímý návrat serveru je nastaven během vytváření. Název není možné změnit.

1. Klepnutím na **tlačítko OK** nastavte pravidla vyrovnávání zatížení posluchače.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Přidání základní adresy IP clusteru pro cluster WSFC s podporou převzetí služeb při selhání systému Windows Server

Adresa IP wsfc musí být také na vyrovnávání zatížení.

1. Na portálu klikněte na stejném nástroji pro vyrovnávání zatížení Azure na **front-endovou konfiguraci IP adres** a na **+Přidat**. Použijte adresu IP, kterou jste nakonfigurovali pro wsfc v základních prostředcích clusteru. Nastavte adresu IP jako statickou.

1. V systému vyrovnávání zatížení klepněte na **položku Sondy stavu**a klepněte na tlačítko **+Přidat**.

1. Sonda stavu IP adresy jádra clusteru WSFC nastavte takto:

   | Nastavení | Popis | Příklad
   | --- | --- |---
   | **Název** | Text | WSFCEndPointProbe |
   | **Protokol** | Zvolte TCP. | TCP |
   | **Port** | Jakýkoli nevyužitý port | 58888 |
   | **Interval**  | Doba mezi pokusy o sondu v sekundách |5 |
   | **Prahová hodnota pro poškozený stav** | Počet po sobě jdoucích selhání sondy, ke kterým musí dojít, aby byl virtuální počítač považován za nefunkční  | 2 |

1. Klepnutím na **tlačítko OK** nastavte sondu stavu.

1. Nastavte pravidla vyrovnávání zatížení. Klepněte na **položku Pravidla vyrovnávání zatížení**a klepněte na tlačítko **+Přidat**.

1. Nastavte pravidla vyrovnávání zatížení jádra IP adresy clusteru následujícím způsobem.

   | Nastavení | Popis | Příklad
   | --- | --- |---
   | **Název** | Text | WSFCEndPoint |
   | **IP adresa front-endu** | Zvolte adresu |Adresu, kterou jste vytvořili při konfiguraci adresy IP wsfc. To se liší od IP adresy posluchače |
   | **Protokol** | Zvolte TCP. |TCP |
   | **Port** | Použijte port pro adresu IP clusteru. Toto je dostupný port, který se nepoužívá pro port sondy naslouchací proces. | 58888 |
   | **Back-endový port** | Toto pole se nepoužívá, pokud je plovoucí IP adresa nastavena pro přímé vrácení serveru. | 58888 |
   | **Sonda** |Název, který jste zadali pro sondu | WSFCEndPointProbe |
   | **Trvalosti relace** | Rozevírací seznam | **Žádné** |
   | **Časový limit nečinnosti** | Minuty pro zachování otevřeného připojení TCP | 4 |
   | **Plovoucí IP (přímý návrat serveru)** | |Povoleno |

   > [!WARNING]
   > Přímý návrat serveru je nastaven během vytváření. Název není možné změnit.

1. Klepnutím na **tlačítko OK** nastavte pravidla vyrovnávání zatížení.

## <a name="configure-the-listener"></a><a name="configure-listener"></a>Konfigurace naslouchací proces

Další věcí, kterou je třeba udělat, je nakonfigurovat naslouchací proces skupiny dostupnosti v clusteru s podporou převzetí služeb při selhání.

> [!NOTE]
> Tento kurz ukazuje, jak vytvořit jeden naslouchací proces – s jednou IP adresou ILB. Pokud chcete vytvořit jeden nebo více naslouchacích procesů pomocí jedné nebo více adres IP, přečtěte si část [Vytvoření naslouchací proces skupiny dostupnosti a vyrovnávání zatížení | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Nastavit port posluchače

V aplikaci SQL Server Management Studio nastavte port naslouchací procesu.

1. Spusťte sql server management studio a připojte se k primární replice.

1. Přejděte **na alwayson dostupnost** | **skupiny dostupnosti skupiny** | **naslouchací procesy**.

1. Nyní byste měli vidět název posluchače, který jste vytvořili ve Správci clusteru s podporou převzetí služeb při selhání. Klepněte pravým tlačítkem myši na název naslouchací procesu a klepněte na příkaz **Vlastnosti**.

1. V poli **Port** zadejte číslo portu pro naslouchací proces Skupiny dostupnosti. 1433 je výchozí a klepněte na tlačítko **OK**.

Nyní máte skupinu dostupnosti serveru SQL Server ve virtuálních počítačích Azure spuštěnou v režimu Správce prostředků.

## <a name="test-connection-to-listener"></a>Otestovat připojení k naslouchací proces

Chcete-li otestovat připojení:

1. RDP na SQL Server, který je ve stejné virtuální síti, ale nevlastní repliku. Můžete použít jiný SQL Server v clusteru.

1. Pomocí nástroje **sqlcmd** otestujte připojení. Například následující skript vytvoří **sqlcmd** připojení k primární replice prostřednictvím naslouchacího procesu s ověřováním systému Windows:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Pokud naslouchací proces používá jiný port než výchozí port (1433), zadejte port v připojovacím řetězci. Například následující příkaz sqlcmd se připojí k naslouchací procesu na portu 1435:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

Připojení SQLCMD se automaticky připojí k kterékoli instanci serveru SQL Server, která je hostitelem primární repliky.

> [!TIP]
> Ujistěte se, že zadaný port je otevřený na bráně firewall obou serverů SQL. Oba servery vyžadují příchozí pravidlo pro port TCP, který používáte. Další informace naleznete v tématu [Přidání nebo úprava pravidla brány firewall](https://technet.microsoft.com/library/cc753558.aspx).

## <a name="next-steps"></a>Další kroky

- [Přidejte adresu IP do programu pro vyrovnávání zatížení pro druhou skupinu dostupnosti](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).
