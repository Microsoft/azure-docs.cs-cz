---
title: Proveďte zálohu databáze SQL serveru do Azure | Dokumentace Microsoftu
description: Tento kurz vysvětluje zálohování SQL serveru do Azure. Tento článek také popisuje obnovení SQL serveru.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/19/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: 249f473d7318051e0ce27bcc47a9fde080c4c8f6
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160311"
---
# <a name="back-up-sql-server-database-in-azure"></a>Proveďte zálohu databáze serveru SQL Server v Azure

Databáze systému SQL Server jsou kritické úlohy vyžadující cíl bodu s nízkou obnovení (RPO) a dlouhodobé uchovávání. Azure Backup poskytuje řešení zálohování serveru SQL Server, který vyžaduje žádnou infrastrukturu, což znamená, že není komplexní záložní server, žádný agent pro správu nebo úložiště pro zálohování pro správu. Azure Backup poskytuje centralizovanou správu pro své zálohy přes všechny SQL servery nebo dokonce během různých úloh.

 V tomto článku se dozvíte:

> [!div class="checklist"]
> * Požadavky pro zálohování SQL serveru do Azure
> * Vytvořit a použít trezor služby Recovery Services
> * Konfigurace zálohování databáze serveru SQL Server
> * Nastavit zásady zálohování (nebo uchovávání) pro body obnovení
> * Postup při obnovení databáze

Než začnete postupy v tomto článku, měli byste mít SQL Server běžící v Azure. [SQL marketplace virtuálních počítačů můžete použít k rychlému vytvoření SQL serveru](../sql-database/sql-database-get-started-portal.md).

## <a name="public-preview-limitations"></a>Omezení veřejné verze Preview

Následující položky jsou známé omezení verze Public Preview.

- Virtuální počítač SQL vyžaduje připojení k Internetu pro přístup k veřejným IP adresám Azure. Další podrobnosti najdete v části [navázat připojení k síti](backup-azure-sql-database.md#establish-network-connectivity).
- Dokáže chránit až 2000 databází SQL v jeden trezor služby Recovery Services. Další databáze SQL by měla být uložena v samostatném trezoru služby Recovery Services.
- [Distribuované dostupnosti skupiny backup má omezení](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017).
- SQL instance pro Cluster převzetí služeb při selhání (FCI) nejsou podporovány.
- Nakonfigurujte zálohování Azure pro ochranu databáze SQL serveru pomocí webu Azure portal. Podpora pro Azure PowerShell, rozhraní příkazového řádku a rozhraní REST API není aktuálně k dispozici.

## <a name="supported-azure-geos"></a>Podporované zeměpisných oblastech Azure

- Austrálie – jihovýchod (ASE) 
- Brazílie – jih (BRS)
- Kanada – střed (CNC)
- Kanada – východ (CE)
- USA – střed (CUS)
- Východní Asie (EA)
- Východní Austrálie (AE) 
- USA – východ (EUS)
- USA – východ 2 (EUS2)
- Japonsko – východ (JPE)
- Japonsko – západ (JPW)
- Indie – střed (INC) 
- Indie – jih (INS)
- Jižní Korea – střed (KRC)
- Jižní Korea – jih (KRS)
- Střed USA – sever (NCUS) 
- Severní Evropa (NE) 
- Střed USA – jih (SCUS) 
- Jihovýchodní Asie (SEA)
- Velká Británie – jih (UKS) 
- Velká Británie – západ (UKW) 
- Západní Evropa (WE) 
- USA – západ (WUS)
- Střed USA – západ (WCUS)
- USA – západ 2 (WUS 2) 

## <a name="supported-operating-systems-and-versions-of-sql-server"></a>Podporované operační systémy a verze SQL serveru

Podporovány jsou následující operační systémy. SQL-Tržiště Azure virtual machines a virtuálních počítačů mimo marketplace (kde je nainstalován SQL Server ručně), jsou podporovány.

### <a name="supported-operating-systems"></a>Podporované operační systémy

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

Linuxu se aktuálně nepodporuje.

### <a name="supported-versionseditions-of-sql-server"></a>Podporované verze a edice systému SQL Server

- SQL 2012 Enterprise, Standard, Web, Developer, Express
- SQL 2014 Enterprise, Standard, Web, Developer, Express
- SQL 2016 Enterprise, Standard, Web, Developer, Express
- SQL 2017 Enterprise, Standard, Web, Developer, Express


## <a name="prerequisites-for-using-azure-backup-to-protect-sql-server"></a>Předpoklady pro použití Azure Backup k ochraně SQL serveru 

Než budete zálohovat databáze SQL serveru, zkontrolujte následující podmínky. :

- Identifikovat nebo [vytvořte trezor služby Recovery Services](backup-azure-sql-database.md#create-a-recovery-services-vault) ve stejné oblasti, nebo národní prostředí, protože virtuální počítač hostitelem systému SQL Server.
- [Zkontrolujte oprávnění na virtuálním počítači](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms) potřebné k zálohování databází SQL.
- [Virtuální počítač SQL má připojení k síti](backup-azure-sql-database.md#establish-network-connectivity).

> [!NOTE]
> Postupně, aby zálohování databází systému SQL Server může mít pouze jeden záložní řešení. Zakažte další zálohu SQL před použitím této funkce, jiný zálohy budou v konfliktu a selhání. Můžete povolit Azure Backup pro virtuální počítač IaaS spolu s zálohování SQL bez jakéhokoli konfliktu 
>

Při splnění těchto podmínek ve vašem prostředí, přejděte k části [konfigurace trezoru pro ochranu databáze SQL](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database). Pokud některý z požadovaných součástí neexistuje, pokračujte ve čtení v této části.


## <a name="establish-network-connectivity"></a>Vytvoření připojení k síti

Pro všechny operace musí virtuální počítač SQL připojení k veřejným IP adresám Azure. Operace virtuálního počítače SQL (jako je například zjišťování databází, konfigurování zálohování, plánované zálohy, obnovení body obnovení a tak dále) selhání bez připojení k veřejné IP adresy. Použijte některou z následujících možností a poskytne jasný pro provoz zálohování:

- Rozsahy adres Azure datacenter IP seznamu povolených IP adres – na seznam povolených Azure rozsahy IP adres datacentra, použijte [center stránce najdete podrobnosti o rozsahy IP adres a pokyny pro stažení](https://www.microsoft.com/download/details.aspx?id=41653). 
- Nasazení proxy server HTTP pro směrování provozu – Pokud zálohujete databáze SQL na virtuálním počítači, rozšíření zálohování na virtuálním počítači používá rozhraní API pro protokol HTTPS pro příkazy pro správu odesílat data do služby Azure Storage a Azure Backup. Rozšíření zálohování také používá pro ověřování Azure Active Directory (AAD). Směrování provozu linka záložního telefonu pro tyto tři služby přes proxy server HTTP, protože se jedná o jedinou komponentou nakonfigurovaný pro přístup k veřejnému Internetu.

Jsou kompromisy mezi volby: možnosti správy, podrobnou kontrolu a náklady.

> [!NOTE]
>Značky služby Azure Backup by měla být podle obecné dostupnosti k dispozici.
>

| Možnost | Výhody | Nevýhody |
| ------ | ---------- | ------------- |
| Rozsahy seznamu povolených IP adres | Žádné další náklady. <br/> Pro otevření přístup v skupinu zabezpečení sítě, použijte **Set-AzureNetworkSecurityRule** rutiny. | Složitou správu jako ovlivněný rozsahy IP adres v průběhu času měnit. <br/>Poskytuje přístup k celé Azure, ne jenom úložiště.|
| Použít proxy server HTTP   | Detailní kontrola v proxy serveru úložiště je povolené adresy URL. <br/>Jeden bod internetový přístup k virtuálním počítačům. <br/> Není v souladu s Azure IP adresa změní. | Další náklady pro spuštění virtuálního počítače se softwarem proxy serveru. |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>Nastavení oprávnění pro virtuální počítače s SQL mimo marketplace

Zálohování virtuálního počítače, vyžaduje Azure Backup **AzureBackupWindowsWorkload** nainstalovat rozšíření. Pokud používáte virtuální počítače Azure marketplace, přeskočte k části [databáze serveru SQL zjistit](backup-azure-sql-database.md#discover-sql-server-databases). Pokud virtuální počítač hostujícího vaše databáze SQL nebyl vytvořen na webu Azure Marketplace, dokončete následující část, abyste nainstalovali rozšíření a nastavte příslušná oprávnění. Kromě **AzureBackupWindowsWorkload** rozšíření, Azure Backup vyžaduje oprávnění správce systému SQL pro ochranu databáze SQL. Při zjišťování databází na virtuálním počítači Azure Backup vytvoří účet, NT Service\AzureWLBackupPluginSvc. Azure Backup se zjistit databáze SQL, musí mít účet NT Service\AzureWLBackupPluginSvc SQL a SQL oprávnění správce. Následující postup vysvětluje, jak poskytnout tato oprávnění.

Ke konfiguraci oprávnění:

1. V [webu Azure portal](https://portal.azure.com), otevřete trezor služby Recovery Services, které používáte pro ochranu databáze SQL.
2. V nabídce řídicího panelu trezoru klikněte na tlačítko **+ zálohování** otevřít **cíl zálohování** nabídky.

   ![Klikněte na + zálohování a otevřete nabídku cíle zálohování](./media/backup-azure-sql-database/open-backup-menu.png)

3. Na **cíl zálohování** nabídky v **ve kterém je spuštěná vaše úloha?** nabídky, ponechejte tuto položku **Azure** jako výchozí.

4. Na **co chcete zálohovat** nabídku, rozbalte rozevírací nabídku a vyberte **systému SQL Server na virtuálním počítači Azure**.

    ![Klikněte na + zálohování a otevřete nabídku cíle zálohování](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    **Cíl zálohování** nabídce se zobrazí dvě nové kroky: **zjistit databáze ve virtuálních počítačích** a **konfigurace zálohování**. **Zjistit databáze ve virtuálních počítačích** spustí vyhledávání pro virtuální počítače Azure.

    ![Zobrazuje nové kroky cíle zálohování](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. Klikněte na tlačítko **spustit zjišťování** vyhledat nechráněné virtuální počítače v rámci předplatného. V závislosti na počtu nechráněných virtuálních počítačů v rámci předplatného může trvat nějakou kvůli tomu provádět všechny virtuální počítače.

    ![Zálohování čeká na zpracování](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    Po zjištění nechráněného virtuálního počítače se zobrazí v seznamu. Nechráněné virtuální počítače jsou uvedené v jejich virtuálních počítačů a skupinou prostředků. Je možné pro více virtuálních počítačů se stejným názvem. Virtuální počítače se stejným názvem, ale patří do různých skupin prostředků. Očekávaný virtuálního počítače se nezobrazí v seznamu, najdete v článku Pokud virtuální počítač už chráněný do trezoru.

6. Ze seznamu virtuálních počítačů vyberte virtuální počítač s SQL database, kterou chcete zálohovat a klikněte na **zjišťování databází**. 

    Proces zjišťování nainstaluje **AzureBackupWindowsWorkload** rozšíření na virtuálním počítači. Rozšíření umožňuje službě Azure Backup pro komunikaci s virtuálním počítačem, můžete zálohovat databáze SQL. Po instalaci rozšíření Azure Backup vytváří účet virtuální služby Windows **NT Service\AzureWLBackupPluginSvc**, na virtuálním počítači. Účet virtuální služby vyžaduje oprávnění správce systému SQL. Během procesu účet instalace virtuální služby, pokud se zobrazí chyba **UserErrorSQLNoSysadminMembership**, naleznete v části [oprávnění sysadmin SQL stanovení](backup-azure-sql-database.md#fixing-sql-sysadmin-permissions).

    Oznamovací oblast zobrazuje průběh zjišťování databází. V závislosti na tom, kolik databází jsou na virtuálním počítači může trvat nějakou dobu se úloha dokončí. Když byly zjištěny ve vybraných databázích, zobrazí se zpráva o úspěchu.

    ![úspěšné nasazení oznámení](./media/backup-azure-sql-database/notifications-db-discovered.png)

Když přidružíte databáze v trezoru služby Recovery Services, dalším krokem je [konfiguraci zálohování](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

### <a name="fixing-sql-sysadmin-permissions"></a>Oprava oprávnění správce systému SQL

Během procesu instalace, pokud se zobrazí chyba **UserErrorSQLNoSysadminMembership**, použijte účet s oprávněními správce systému SQL pro přihlášení k serveru SQL Server Management Studio (SSMS). Pokud potřebujete speciální oprávnění, by měla fungovat ověřování Windows.

1. Na serveru SQL Server, otevřete **zabezpečení/přihlášení** složky.

    ![Otevření složky systému SQL Server a zabezpečení a přihlašovací účty zobrazíte](./media/backup-azure-sql-database/security-login-list.png)

2. Ve složce pro přihlášení, klikněte pravým tlačítkem a vyberte **nového přihlašovacího jména**a v části přihlášení – nové dialogové okno, klikněte na tlačítko **vyhledávání**

    ![Otevřete dialogové okno hledání v přihlášení – nové](./media/backup-azure-sql-database/new-login-search.png)

3. Od účet virtuální služby Windows **NT Service\AzureWLBackupPluginSvc** již byl vytvořen při registraci virtuálního počítače a fázi zjišťování SQL, zadejte název účtu, jak se zobrazí v  **Zadejte název objektu k výběru** dialogového okna. Klikněte na tlačítko **Kontrola názvů** přeložit název. 

    ![Klikněte na tlačítko Zkontrolovat jména k přeložení názvu služby neznámý](./media/backup-azure-sql-database/check-name.png)

4. Klikněte na tlačítko **OK** zavřete dialogové okno Vybrat uživatele nebo skupinu.

5. V **role serveru** dialogového okna, ujistěte se, že **sysadmin** je vybrána role. Pak klikněte na tlačítko **OK** zavřete **přihlášení – nové**.

    ![Ujistěte se, že je vybrána role serveru sysadmin](./media/backup-azure-sql-database/sysadmin-server-role.png)

    Teď by měla existovat požadovaná oprávnění.

6. I když jste opravili chybu oprávnění, je stále potřeba přidružit databázi k trezoru služby Recovery Services. Na webu Azure Portal **chráněné servery** seznamu, klikněte pravým tlačítkem na server v chybě a vyberte **opětovné zjištění databází**.

    ![Ověřte, že server má příslušná oprávnění.](./media/backup-azure-sql-database/check-erroneous-server.png)

    Oznamovací oblast zobrazuje průběh zjišťování databází. V závislosti na tom, kolik databází jsou na virtuálním počítači může trvat nějakou dobu se úloha dokončí. Nalezený ve vybraných databázích v oznamovací oblasti se zobrazí zpráva o úspěchu.

    ![úspěšné nasazení oznámení](./media/backup-azure-sql-database/notifications-db-discovered.png)

Když přidružíte databáze v trezoru služby Recovery Services, dalším krokem je [konfiguraci zálohování](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

[!INCLUDE [Section explaining how to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Zjistit databáze systému SQL Server

Azure Backup můžete zjistit všechny databáze na instanci systému SQL Server, tak budete moci chránit podle požadavků vaší zálohy. Následující postup použijte k identifikaci virtuálního počítače, který je hostitelem databáze SQL. Jakmile identifikujete virtuální počítače, Azure Backup nainstaluje jednoduché rozšíření se zjistit databáze SQL serveru.

1. Přihlaste se k vašemu předplatnému [webu Azure portal](https://portal.azure.com/).
2. V nabídce vlevo vyberte **všechny služby**.

    ![V hlavní nabídce zvolte možnost všechny služby](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. V dialogovém okně všechny služby, zadejte *služby Recovery Services*. Když začnete psát, váš vstup filtruje seznam prostředků. Jakmile se zobrazí, vyberte **trezory služby Recovery Services**.

    ![Zadejte do dialogového okna všechny služby Recovery Services](./media/backup-azure-sql-database/all-services.png) <br/>

    Zobrazí se seznam trezorů služby Recovery Services v rámci předplatného. 

4. Ze seznamu trezorů služby Recovery Services vyberte trezor, který chcete použít k ochraně vašich databází SQL.

5. V nabídce řídicího panelu trezoru klikněte na tlačítko **+ zálohování** otevřít **cíl zálohování** nabídky.

   ![Klikněte na + zálohování a otevřete nabídku cíle zálohování](./media/backup-azure-sql-database/open-backup-menu.png)

6. Na **cíl zálohování** nabídky v **ve kterém je spuštěná vaše úloha?** nabídky, ponechejte tuto položku **Azure** jako výchozí.

7. Na **co chcete zálohovat** nabídku, rozbalte rozevírací nabídku a vyberte **systému SQL Server na virtuálním počítači Azure**.

    ![Klikněte na + zálohování a otevřete nabídku cíle zálohování](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Po výběru **cíl zálohování** nabídce se zobrazí dva kroky: zjistit databáze ve virtuálních počítačích a nakonfigurovat zálohování. 

    ![Zobrazuje nové kroky cíle zálohování](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. Klikněte na tlačítko **spustit zjišťování** vyhledat nechráněné virtuální počítače v rámci předplatného. V závislosti na počtu nechráněných virtuálních počítačů v rámci předplatného může trvat nějakou kvůli tomu provádět všechny virtuální počítače.

    ![Zálohování čeká na zpracování](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    Po zjištění nechráněného virtuálního počítače se zobrazí v seznamu. Několik virtuálních počítačů může mít stejný název. Několik virtuálních počítačů se stejným názvem, ale patří do různých skupin prostředků. Nechráněné virtuální počítače jsou uvedené v jejich virtuálních počítačů a skupinou prostředků. Pokud není uveden očekávané virtuálního počítače, naleznete v tématu Pokud je tento virtuální počítač už chráněný do trezoru.

9. Ze seznamu virtuálních počítačů, zaškrtněte políčko virtuálního počítače, který obsahuje databáze SQL, kterou chcete chránit a klikněte na tlačítko **zjišťování databází**.

    Azure Backup zjistí všechny databáze SQL na virtuálním počítači. Informace o tom, co se stane během fáze zjištění databáze, najdete v následující části [back-endové operace při zjišťování databází SQL](backup-azure-sql-database.md#backend-operations-when-discovering-sql-databases). Po zjištění databází SQL, budete chtít [konfigurace úlohy zálohování](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

### <a name="backend-operations-when-discovering-sql-databases"></a>Back-endové operace při zjišťování databází SQL

Při použití **zjišťování databází** nástroj Azure Backup provádí následující operace na pozadí:

- virtuální počítač zaregistruje v trezoru služby Recovery Services úlohu zálohování. Všechny databáze na registrované virtuálního počítače lze pouze zálohovat do trezoru služby Recovery Services. 

- nainstaluje **AzureBackupWindowsWorkload** rozšíření na virtuálním počítači. Zálohování SQL database je řešení typu bez agentů, tedy s příponou nainstalovaný na virtuálním počítači se neinstaluje žádný agent pro SQL database.

- vytvoří účet služby **NT Service\AzureWLBackupPluginSvc**, na virtuálním počítači. Všechny operace zálohování a obnovení pomocí účtu služby. **NT Service\AzureWLBackupPluginSvc** potřebuje oprávnění správce systému SQL. Všechny virtuální počítače SQL Marketplace součástí SqlIaaSExtension nainstalovaný a AzureBackupWindowsWorkload pomocí SQLIaaSExtension automaticky získá požadovaná oprávnění. Pokud se váš virtuální počítač nemá nainstalován SqlIaaSExtension zjistit databáze nezdaří a zobrazí chybová zpráva **UserErrorSQLNoSysAdminMembership**. Chcete-li přidat oprávnění správce systému pro zálohování, postupujte podle pokynů v [nastavení oprávnění pro zálohování Azure pro virtuální počítače SQL mimo marketplace](backup-azure-sql-database.md#set-permissions-for-non--marketplace-sql-vms).

    ![Vyberte virtuální počítač a databáze](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-database"></a>Konfigurace zálohování pro databázi systému SQL Server

Azure Backup poskytuje služby správy pro ochranu databáze SQL serveru a Správa úloh zálohování. Správa a monitorování funkce závisí na trezoru služby Recovery Services. 

> [!NOTE]
> Postupně, aby zálohování databází systému SQL Server může mít pouze jeden záložní řešení. Zakažte další zálohu SQL před použitím této funkce, jiný zálohy budou v konfliktu a selhání. Můžete povolit Azure Backup pro virtuální počítač IaaS spolu s zálohování SQL bez jakéhokoli konfliktu 
>

Konfigurace ochrany pro SQL database:

1. Otevřete trezor služby Recovery Services zaregistrovaný u virtuálního počítače SQL.

2. V nabídce řídicího panelu trezoru klikněte na tlačítko **+ zálohování** otevřít **cíl zálohování** nabídky.

    ![Klikněte na + zálohování a otevřete nabídku cíle zálohování](./media/backup-azure-sql-database/open-backup-menu.png)

3. Na **cíl zálohování** nabídky v **ve kterém je spuštěná vaše úloha?** nabídky, ponechejte tuto položku **Azure** jako výchozí.

4. Na **co chcete zálohovat** nabídku, rozbalte rozevírací nabídku a vyberte **systému SQL Server na virtuálním počítači Azure**.

    ![Klikněte na + zálohování a otevřete nabídku cíle zálohování](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Po výběru **cíl zálohování** nabídce se zobrazí dva kroky: zjistit databáze ve virtuálních počítačích a nakonfigurovat zálohování. Pokud jste prošli Tento článek v pořadí, jste již zjištěných nechráněných virtuálních počítačů, a tento trezor se registruje s virtuálním počítačem. Teď jste připravení nakonfigurovat ochranu pro databáze SQL.

5. V nabídce Cíl zálohování klikněte na tlačítko **konfigurace zálohování**.

    ![Zobrazuje nové kroky cíle zálohování](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Služba Azure Backup se zobrazí všechny instance SQL s samostatné databáze, jakož i skupiny dostupnosti SQL AlwaysOn. Chcete-li zobrazit samostatné databáze v instanci SQL serveru, klikněte na dvojitou šipku vedle názvu instance zobrazte jeho databáze. Následující obrázky znázorňují příklady samostatná a skupiny dostupnosti Always On.

    > [!NOTE]
    > V případě SQL skupiny dostupnosti Always On jsme případném dalším sdílení dodržovat předvolby zálohování SQL. Ale kvůli omezením platformy SQL, musí dojít z primárního uzlu úplné a rozdílové zálohy. Záloha protokolu můžou probíhat na základě na vaše předvolby zálohování. Z důvodu tohoto omezení musí být vždy zaregistrovaný primárního uzlu pro skupiny dostupnosti.
    >

    ![Seznam databází v instanci serveru SQL](./media/backup-azure-sql-database/discovered-databases.png)

    Klikněte na dvojitou šipku vedle skupiny dostupnosti AlwaysOn, chcete-li zobrazit seznam databází.

    ![Seznam databází ve skupině dostupnosti AlwaysOn](./media/backup-azure-sql-database/discovered-database-availability-group.png)

6. V seznamu databází, vyberte vše, co chcete chránit a klikněte na tlačítko **OK**.

    ![Vyberte více databází k jejich ochraně](./media/backup-azure-sql-database/select-multiple-database-protection.png)

    Najednou můžete vybrat až 50 databázemi. Pokud chcete chránit více než 50 databázemi, proveďte několik průchodů. Po prvních 50 databází chráníte, opakujte tento krok k ochraně další sadu databází.
    > [!Note] 
    > K optimalizaci zálohování zatížení, Azure Backup rozdělí rozsáhlé úlohy zálohování do několika dávek. Maximální počet databází ve jediná úloha zálohování je 50.
    >
    >

7. K vytvoření nebo výběr zásady zálohování, v **zálohování** nabídce vyberte možnost **zásady zálohování**, otevřete nabídku.

    ![Vyberte možnost zásady zálohování](./media/backup-azure-sql-database/select-backup-policy.png)

8. Z **výběr zásady zálohování** rozevírací nabídky vyberte zásadu zálohování a klikněte na **OK**. Informace o vytváření zásady zálohování, naleznete v části, [Definujte zásadu zálohování](backup-azure-sql-database.md#define-a-backup-policy).

    ![Vyberte zásady zálohování v rozevírací nabídce](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    V nabídce zásady zálohování z **výběr zásady zálohování** rozevírací nabídky můžete zvolit: 
    - Výchozí HourlyLogBackup zásady 
    - existující zásady zálohování, která dříve vytvořili pro server SQL,
    - k [definovat novou zásadu](backup-azure-sql-database.md#define-a-backup-policy) na základě plánovaného bodu obnovení (RPO) a rozsahu uchování. 

    > [!Note]
    > Azure Backup podporuje dlouhodobé uchovávání informací podle dědečka ze strany otce SYN schéma zálohování optimalizovat spotřebu úložiště back-endu současně splnit požadavky na dodržování předpisů.
    >

9. Po výběru zásady zálohování v **nabídka zálohování** klikněte na tlačítko **povolit zálohování**.

    ![Povolit vybrané zásady zálohování](./media/backup-azure-sql-database/enable-backup-button.png)

    Můžete sledovat průběh konfigurace v oznamovací oblasti na portálu.

    ![zobrazení oznamovací oblasti](./media/backup-azure-sql-database/notifications-area.png)


### <a name="define-a-backup-policy"></a>Definice zásady zálohování

Zásady zálohování definují pořizování zálohování a jak dlouho jsou uchovávány zálohy. Azure Backup můžete použít k naplánování tři typy zálohování pro SQL databáze:

* Úplné zálohování - úplnou zálohu databáze vytvoří zálohu celé databáze. Úplné zálohování obsahuje dostatek protokolu obnovení těchto dat a všechna data v konkrétní databázi nebo sadu skupiny souborů nebo souborů. Maximálně můžete aktivovat jednu úplnou zálohu denně. Můžete provést úplné zálohování na denní nebo týdenní interval. 
* Rozdílová záloha - rozdílové zálohy je založená na nejnovější, předchozí úplná záloha. Rozdílové zálohování zaznamená pouze data, která se změnil od úplného zálohování. Maximálně můžete aktivovat jeden rozdílové zálohy za den. Úplné zálohování a rozdílovou zálohu nelze konfigurovat ve stejný den.
* Zálohování protokolu transakcí – záloha protokolu obnovení bodu v čase až po konkrétní povolí druhé. Maximálně můžete nakonfigurovat zálohy transakčního protokolu každých 15 minut.

Zásada se vytvoří v trezoru služby Recovery Services úrovně. Pokud máte více trezorů trezory služby můžete použít stejné zásady zálohování, ale musíte použít zásadu zálohování, která pro každý trezor. Při vytváření zásady zálohování denně, úplné zálohování je výchozí hodnota. Rozdílovou zálohu, ale pouze můžete přidat, pokud přejdete týdně úplné zálohy. Následující postup vysvětluje, jak vytvořit zásady zálohování pro SQL server ve virtuálním počítači Azure.

Chcete-li vytvořit zásadu zálohování

1. V nabídce zásady zálohování z **výběr zásady zálohování** rozevírací nabídky vyberte **vytvořit nový**.

   ![Vytvoření nové zásady zálohování](./media/backup-azure-sql-database/create-new-backup-policy.png)

    Nabídka zásady zálohování přepne na poskytují pole potřebná pro všechny nové zásady zálohování serveru SQL.

   ![Nová pole zásady zálohování](./media/backup-azure-sql-database/blank-new-policy.png)

2. V **Název_zásady**, zadejte název. 

3. Úplné zálohování je povinný. Můžete přijmout výchozí hodnoty pro úplné zálohování nebo klikněte na tlačítko **úplná záloha** pro editaci zásady.

    ![Nová pole zásady zálohování](./media/backup-azure-sql-database/full-backup-policy.png)

    V rámci zásad úplné zálohování Zvolte frekvenci denně nebo týdně. Pokud zvolíte denní, vyberte hodiny a časové pásmo, po zahájení úlohy zálohování. Pokud si vyberete každodenní úplnou zálohu, nebude možné vytvořit rozdílové zálohy.

   ![denní interval nastavení](./media/backup-azure-sql-database/daily-interval.png)

    Pokud se rozhodnete každý týden, zvolte den v týdnu, hodiny a časové pásmo po zahájení úlohy zálohování.

   ![týdenní interval nastavení](./media/backup-azure-sql-database/weekly-interval.png)

4. Standardně jsou vybrané všechny možnosti rozsah uchování (denní, týdenní, měsíční a roční). Zrušte zaškrtnutí políčka některý limit rozsahu uchování není chcete a nastavených intervalech používat. V nabídce Zásady úplné zálohování, klikněte na tlačítko **OK** potvrďte nastavení.

   ![nastavení intervalu rozsah uchování](./media/backup-azure-sql-database/retention-range-interval.png)

    Body obnovení jsou označené pro uchovávání dat, na základě jejich rozsahu uchování. Například pokud zvolíte denní, úplné zálohování jenom jeden úplné zálohování se aktivuje každý den. V závislosti na vaší týdenní uchovávání, konkrétní den zálohování je příznakem a uchovávají na základě týdenní rozsahu uchování. Rozsah uchování měsíční a roční chová podobně jako.

5. Chcete-li přidat zásadu pro rozdílové zálohování, klikněte na tlačítko **rozdílové zálohování** otevřete nabídku. 

   ![Otevřete rozdílové zásad](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    V nabídce zásady rozdílovou zálohu, vyberte **povolit** otevřete ovládací prvky frekvence a uchování. Můžete aktivovat, maximálně jeden rozdílové zálohy za den.
    > [!Important] 
    > Rozdílové zálohování můžou uchovávat maximálně po dobu 180 dnů. Pokud budete potřebovat delší dobu uchování, je nutné použít úplné zálohy, rozdílové zálohování nelze použít.
    >

   ![upravit zásady rozdílové](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    Klikněte na tlačítko **OK** zásadu uložte a vraťte se do hlavní nabídky zásady zálohování.

6. Chcete-li přidat zásadu zálohování protokolu transakcí, klikněte na tlačítko **záloha protokolu** otevřete nabídku. V nabídce záloha protokolu, vyberte **povolit**a nastavte četnost a uchování ovládací prvky. Zálohy protokolů může dojít, tak často, jak každých 15 minut a můžou uchovávat až 35 dnů. Klikněte na tlačítko **OK** zásadu uložte a vraťte se do hlavní nabídky zásady zálohování.

   ![upravit zásady zálohování protokolu](./media/backup-azure-sql-database/log-backup-policy-editor.png)

7. Zvolte, jestli se má povolit kompresi zálohování SQL. Komprese je ve výchozím nastavení zakázána.

    Na back-endu Azure Backup použije nativní kompresi záloh SQL.

8. Pokud jste provedli všechny změny zásady zálohování, klikněte na tlačítko **OK**. 

   ![přijmout nové zásady](./media/backup-azure-sql-database/backup-policy-click-ok.png)

## <a name="restore-a-sql-database"></a>Obnovení databáze SQL

Azure Backup poskytuje funkce pro obnovení jednotlivých databází na konkrétní datum nebo čas, až po konkrétní druhé pomocí zálohování transakčního protokolu. Podle časů obnovení, které zadáte, Azure Backup automaticky určuje odpovídající úplného, rozdílového a řetězec potřebné k obnovení dat záloh protokolu.

Alternativně můžete vybrat konkrétní úplné nebo rozdílové zálohy obnovit na konkrétní bod obnovení než určený čas.
 > [!Note]
 > Před aktivací obnovení "hlavní" databáze spusťte SQL Server v režimu jednoho uživatele s možností spuštění "-m AzureWorkloadBackup". Argument -m je název klienta, bude povolen pouze tento klient k otevření připojení. Pro všechny systémové databáze (master, model, msdb) zastavte službu agenta SQL před aktivace operace obnovení. Zavřete všechny aplikace, které se mohou pokusit o ukrást připojení k jakémukoli z těchto databází.
>

Chcete-li obnovit databázi

1. Otevřete trezor služby Recovery Services zaregistrovaný u virtuálního počítače SQL.

2. V řídicím panelu trezoru vyberte **využití** zálohování položek otevřete nabídku zálohování položek.

    ![Klikněte na + zálohování a otevřete nabídku cíle zálohování](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. V **zálohované položky** nabídku, vyberte typ správy zálohování **SQL na virtuálním počítači Azure**. 

    ![Klikněte na + zálohování a otevřete nabídku cíle zálohování](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    Zálohování položek seznamu upraví zobrazíte seznam databází SQL. 

4. V seznamu databází SQL vyberte databázi, kterou chcete obnovit.

    ![Vyberte ze seznamu SQL na virtuálním počítači Azure](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Když vyberete databázi, otevře se jeho nabídku. Tato nabídka poskytuje podrobnosti zálohování pro databázi, včetně:

    * nejstarší a nejnovější bodů obnovení
    * Stav zálohování protokolů za posledních 24 hodin (pro databáze v úplné a hromadně protokolovaný modelu obnovení, pokud nakonfigurované pro transakční protokol)

5. V nabídce vybrané databáze, klikněte na tlačítko **obnovení DB** otevřete nabídku obnovení.

    ![Vyberte obnovit databázi](./media/backup-azure-sql-database/restore-db-button.png)

    **Obnovení** otevře nabídka a fakturuje se tedy **obnovit konfiguraci** nabídky. **Obnovit konfiguraci** nabídky je prvním krokem při konfiguraci obnovení. V této nabídce vyberte, kam chcete data obnovit. Dostupné možnosti:
    * Alternativní umístění – tuto možnost použijte, pokud chcete obnovit databázi do alternativního umístění a zároveň zachovat původní zdrojové databáze.
    * Přepište databázi – obnovení dat na stejnou instanci systému SQL Server jako původní zdroj. Důsledkem tohoto je, že přepisovat původní databázi.

    > [!Important]
    > Pokud vybraná databáze patří do skupiny dostupnosti Always On, neumožňuje SQL databáze, kterou chcete přepsat. V takovém případě pouze **alternativního umístění** je povolená možnost.
    >

    ![Kliknutím na konfigurovat otevřete nabídku konfigurace obnovení](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Obnovení do alternativního umístění

Tento postup vás provede obnovení dat do alternativního umístění. Pokud chcete přepsat databázi po obnovení, přejděte do části [obnovení a přepsat databázi](backup-azure-sql-database.md#restore-and-overwrite-the-database). Tento postup předpokládá mají vaše otevřený trezor služby Recovery Services a jsou v nabídce konfigurace obnovení. Pokud si nejste, začněte částí, [obnovit databázi SQL](backup-azure-sql-database.md#restore-a-sql-database).

> [!NOTE]
> Databázi můžete obnovit na SQL Server ve stejné oblasti Azure a cílový server musí být zaregistrovaný do trezoru služby Recovery Services. 
>

**Server** rozevírací nabídce se zobrazuje pouze SQL servery zaregistrované do trezoru služby Recovery Services. Pokud chcete, aby server není v **Server** seznamu, naleznete v části [databáze serveru SQL zjistit](backup-azure-sql-database.md#discover-sql-server-databases) najít server. Během procesu zjišťování databáze všech nových serverů zaregistrováni do trezoru služby Recovery Services.

1. V **obnovit konfiguraci** nabídky:

    * Vyberte **alternativního umístění**,
    * pro **Server**, zvolte SQL server, ve které chcete obnovit databázi.
    * v **Instance** rozevírací nabídce vyberte instanci SQL
    * v **název databáze obnovit** dialogové okno, zadejte název cílové databáze.
    * Pokud je k dispozici, vyberte **přepsat, pokud ve vybrané instanci SQL existuje databáze se stejným názvem**.
    * Klikněte na tlačítko **OK** k dokončení konfigurace cíle a přesunout do vyberete bod obnovení.

    ![v nabídce konfigurace obnovení vyberte alternativní umístění, instanci a názvu](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. V **vyberte bod obnovení** nabídce můžete zvolit protokoly (časový okamžik) nebo úplné a rozdílové bod obnovení. Pokud chcete obnovit do konkrétního bodu v čase protokolu, pokračujte v tomto kroku. Pokud chcete obnovit bod obnovení úplné nebo rozdílové, přeskočte ke kroku 3.

    ![Vyberte nabídku se bod obnovení](./media/backup-azure-sql-database/recovery-point-menu.png)

    Bod v čase je dostupná jenom pro zálohy protokolů pro databáze s plnou & hromadné protokoluje model obnovení. Chcete-li obnovit konkrétního bodu za běhu:

    1. Vyberte **protokoly (časový okamžik)** jako možnost obnovení.

        ![Zvolte typ bodu obnovení](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. V části **datum a čas obnovení**, klepněte na ikonu kalendáře a otevřete v kalendáři. Kalendářních dat tučným písmem obsahovat body obnovení a zvýrazní se aktuální datum. Vyberte datum v kalendáři se body obnovení. Data se žádné body obnovení nemůžete vybrat.

        ![Otevřít kalendář](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Jakmile vyberete datum, časová osa grafu zobrazuje dostupné body obnovení v průběžné rozsahu.

    3. Pomocí časové osy grafu nebo dialogovém okně čas zadat bod obnovení a klikněte na určitou dobu **OK** k dokončení kroku bod obnovení.
    
       ![Otevřít kalendář](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        **Vyberte bod obnovení** zavře nabídky a **Upřesnit konfiguraci** otevře se nabídka.

       ![Nabídka pokročilou konfiguraci](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Z **Upřesnit konfiguraci** nabídky:

        * Chcete-li zachovat databázi nefunkční po obnovení, na **obnovit s NORECOVERY** nabídce vyberte možnost **povoleno**.
        * Pokud chcete změnit umístění pro obnovení na cílovém serveru, zadat novou cestu v **cílové** sloupce.
        * Klikněte na tlačítko **OK** schválit nastavení a zavřete **Upřesnit konfiguraci**.

    5. Na **obnovení** nabídky, klikněte na tlačítko **obnovení** spustit úlohu obnovení. Průběh můžete sledovat v oznamovací oblasti. Můžete také sledovat průběh úlohy obnovení databáze.

       ![Nabídka pokročilou konfiguraci](./media/backup-azure-sql-database/restore-job-notification.png)

3. V **vyberte bod obnovení** nabídku, vyberte bod obnovení. Můžete také protokoly (časový okamžik) nebo úplné a rozdílové. Pokud chcete obnovit protokol bodu v čase, vraťte se ke kroku 2. Tento krok obnoví bod konkrétní úplnou nebo rozdílovou obnovení. Pomocí této možnosti se zobrazí všechny úplné a rozdílové bodů obnovení za posledních 30 dní. Pokud chcete zobrazit body obnovení starší než 30 dní, klikněte na tlačítko **filtr** otevřít **body obnovení filtr** nabídky. Pokud zvolíte bod obnovení rozdílové, Azure Backup nejprve obnoví odpovídající úplný bod obnovení a poté použije vybraný rozdílové bod obnovení.

    ![Vyberte nabídku se bod obnovení](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. V **vyberte bod obnovení** nabídku, vyberte **úplné a rozdílové**.

       ![Vyberte nabídku se bod obnovení](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        Zobrazí se seznam dostupných bodů obnovení.

    2. Ze seznamu bodů obnovení vyberte bod obnovení a klikněte na tlačítko **OK** postup bod obnovení. 

        ![Vyberte bod úplné obnovení](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        **Bod obnovení** zavře nabídky a **Upřesnit konfiguraci** otevře se nabídka.

        ![Nabídka pokročilou konfiguraci](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Z **Upřesnit konfiguraci** nabídky:

        * Chcete-li zachovat databázi nefunkční po obnovení, na **obnovit s NORECOVERY** nabídce vyberte možnost **povoleno**. **Obnovit s NORECOVERY** je ve výchozím nastavení zakázané.
        * Pokud chcete změnit umístění pro obnovení na cílovém serveru, zadat novou cestu v **cílové** sloupce.
        * Klikněte na tlačítko **OK** schválit nastavení a zavřete **Upřesnit konfiguraci**.

    4. Na **obnovení** nabídky, klikněte na tlačítko **obnovení** spustit úlohu obnovení. Průběh můžete sledovat v oznamovací oblasti. Můžete také sledovat průběh úlohy obnovení databáze.

       ![Nabídka pokročilou konfiguraci](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>Obnovení a přepsat databázi

Tento postup vás provede obnovení dat a přepsáním databáze. Pokud chcete obnovit do alternativního umístění, přejděte do části [obnovení do alternativního umístění](backup-azure-sql-database.md#restore-to-an-alternate-location). Tento postup předpokládá mají vaše otevřený trezor služby Recovery Services a jsou na **obnovit konfiguraci** nabídky (viz následující obrázek). Pokud si nejste, začněte částí, [obnovit databázi SQL](backup-azure-sql-database.md#restore-a-sql-database).

![Kliknutím na konfigurovat otevřete nabídku konfigurace obnovení](./media/backup-azure-sql-database/restore-overwrite-db.png)

**Server** rozevírací nabídce se zobrazuje pouze SQL servery zaregistrované do trezoru služby Recovery Services. Pokud chcete, aby server není v **Server** seznamu, naleznete v části [databáze serveru SQL zjistit](backup-azure-sql-database.md#discover-sql-server-databases) najít server. Během procesu zjišťování databáze všech nových serverů zaregistrováni do trezoru služby Recovery Services.

1. V **obnovit konfiguraci** nabídce vyberte možnost **přepsat DB** a klikněte na tlačítko **OK** k dokončení konfigurace cíle. 

   ![Klikněte na tlačítko Přepsat DB](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    **Server**, **Instance**, a **název databáze obnovit** dialogových oken nejsou nutné.

2. V **vyberte bod obnovení** nabídce můžete zvolit protokoly (časový okamžik) nebo úplné a rozdílové bod obnovení. Pokud chcete obnovit protokol bodu v čase, pokračujte v tomto kroku. Pokud chcete úplné a rozdílové obnovení bodu obnovení, přeskočte ke kroku 3.

    ![Vyberte nabídku se bod obnovení](./media/backup-azure-sql-database/recovery-point-menu.png)

    Bod v čase je dostupná jenom pro zálohy protokolů pro databáze s plnou & hromadné protokoluje model obnovení. Zvolit bod v čase na konkrétní druhé:

    1. Vyberte **protokoly (časový okamžik)** jako možnost obnovení.

        ![Zvolte typ bodu obnovení](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. V části **datum a čas obnovení**, klepněte na ikonu kalendáře a otevřete v kalendáři. Kalendářních dat tučným písmem obsahovat body obnovení a zvýrazní se aktuální datum. Vyberte datum v kalendáři se body obnovení. Data se žádné body obnovení nemůžete vybrat.

        ![Otevřít kalendář](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Jakmile vyberete datum, časová osa grafu zobrazí dostupné body obnovení.

    3. Pomocí časové osy grafu nebo dialogovém okně čas zadat bod obnovení a klikněte na určitou dobu **OK** k dokončení kroku bod obnovení.
    
       ![Otevřít kalendář](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        **Vyberte bod obnovení** zavře nabídky a **Upřesnit konfiguraci** otevře se nabídka.

       ![Nabídka pokročilou konfiguraci](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Z **Upřesnit konfiguraci** nabídky:

        * Chcete-li zachovat databázi nefunkční po obnovení, na **obnovit s NORECOVERY** nabídce vyberte možnost **povoleno**.
        * Pokud chcete změnit umístění pro obnovení na cílovém serveru, zadat novou cestu v **cílové** sloupce.
        * Klikněte na tlačítko **OK** schválit nastavení a zavřete **Upřesnit konfiguraci**.

    5. Na **obnovení** nabídky, klikněte na tlačítko **obnovení** spustit úlohu obnovení. Průběh můžete sledovat v oznamovací oblasti. Můžete také sledovat průběh úlohy obnovení databáze.

       ![Nabídka pokročilou konfiguraci](./media/backup-azure-sql-database/restore-job-notification.png)

3. V **vyberte bod obnovení** nabídku, vyberte bod obnovení. Můžete také protokoly (časový okamžik) nebo úplné a rozdílové. Pokud chcete obnovit protokol bodu v čase, vraťte se ke kroku 2. Tento krok obnoví bod konkrétní úplnou nebo rozdílovou obnovení. Pomocí této možnosti se zobrazí všechny úplné a rozdílové bodů obnovení za posledních 30 dní. Pokud chcete zobrazit body obnovení starší než 30 dní, klikněte na tlačítko **filtr** otevřít **body obnovení filtr** nabídky. Pokud zvolíte bod obnovení rozdílové, Azure Backup nejprve obnoví odpovídající úplný bod obnovení a poté použije vybraný rozdílové bod obnovení.

    ![Vyberte nabídku se bod obnovení](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. V **vyberte bod obnovení** nabídku, vyberte **úplné a rozdílové**.

       ![Vyberte nabídku se bod obnovení](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        Zobrazí se seznam dostupných bodů obnovení.

    2. Ze seznamu bodů obnovení vyberte bod obnovení a klikněte na tlačítko **OK** postup bod obnovení. 

        ![Vyberte bod úplné obnovení](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        **Bod obnovení** zavře nabídky a **Upřesnit konfiguraci** otevře se nabídka.

        ![Nabídka pokročilou konfiguraci](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Z **Upřesnit konfiguraci** nabídky:

        * Chcete-li zachovat databázi nefunkční po obnovení, na **obnovit s NORECOVERY** nabídce vyberte možnost **povoleno**. **Obnovit s NORECOVERY** je ve výchozím nastavení zakázané.
        * Pokud chcete změnit umístění pro obnovení na cílovém serveru, zadat novou cestu v **cílové** sloupce.
        * Klikněte na tlačítko **OK** schválit nastavení a zavřete **Upřesnit konfiguraci**.

    4. Na **obnovení** nabídky, klikněte na tlačítko **obnovení** spustit úlohu obnovení. Průběh můžete sledovat v oznamovací oblasti. Můžete také sledovat průběh úlohy obnovení databáze.

       ![Nabídka pokročilou konfiguraci](./media/backup-azure-sql-database/restore-job-notification.png)


## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Správa operací zálohování Azure pro SQL na virtuálních počítačích Azure

Tato část obsahuje informace o různých Azure Backup správy operací k dispozici pro SQL na virtuálních počítačích Azure. Existují následující základní operace:

* Monitorování úloh
* Výstrahy záloh
* Zastavili ochranu databáze SQL
* Pokračovat v ochraně databáze SQL
* Spustit úlohu zálohování ad hoc
* Zrušení registrace serveru SQL

### <a name="monitor-jobs"></a>Monitorování úloh
Azure Backup je podnikové řešení třída poskytuje pokročilé zálohování výstrahy a oznámení pro všechny chyby (najdete níže v části výstrahy zálohování). Pokud chcete monitorovat konkrétní úlohy můžete použít některý z následujících možností na základě vašich požadavků:

#### <a name="use-azure-portal-for-all-adhoc-operations"></a>Pomocí webu Azure portal pro všechny operace ad hoc
Azure Backup zobrazí všechny ručně aktivované, nebo ad hoc, úlohy na portálu pro úlohy zálohování. K dispozici v portálu zahrnout úlohy: všechny konfigurace zálohování, ručně aktivované zálohování, obnovení operace, registrace a zjistit databázových operací a zastavit zálohování. 
![Nabídka pokročilou konfiguraci](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Všechny naplánované úlohy zálohování včetně úplné, rozdílového a protokolu zálohování se nezobrazí na portálu a je možné monitorovat pomocí SQL Server Management Studio, jak je popsáno níže.
>

#### <a name="use-sql-server-management-studio-for-backup-jobs"></a>Pomocí SQL Server Management Studio pro úlohy zálohování
Azure Backup používá nativní rozhraní API SQL pro všechny operace zálohování. Pomocí nativních rozhraní API, můžete načíst všechny informace o úlohách z [tabulku záloh SQL](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) v databázi msdb.

V následujícím příkladu je dotaz pro načtení všechny úlohy zálohování pro databázi s názvem, **DB1**. Upravte dotaz pro rozšířené monitorování.
```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  
 
```

### <a name="backup-alerts"></a>Výstrahy záloh

U zálohy protokolu, ke kterým dochází každých 15 minut může být čas od času monitorování úlohy zálohování tedious. Azure Backup plánované v této situaci potenciálně tedious zadáním e-mailová upozornění aktivované testováním jakékoli chyby zálohování. Upozornění konsolidované podle kódu chyby na úrovni databáze. Například pokud databáze obsahuje více selhání zálohování, místo dostanete výstrahu pro každou chybu, obdržíte e-mailu pro první selhání. Na webu Azure portal můžete potom přihlásit ke sledování dalších chyb pro tuto databázi. 

Chcete-li monitorovat výstrahy zálohování:

1. Přihlaste se k předplatnému Azure v [webu Azure portal](https://portal.azure.com).

2. Otevřete trezor služby Recovery Services zaregistrovaný u virtuálního počítače SQL.

3. V nabídce trezoru služby Recovery Services vyberte **výstrahy a události**. 

   ![Nabídka pokročilou konfiguraci](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. V **výstrahy a události** nabídce vyberte možnost **výstrahy zálohování** zobrazíte seznam výstrah.

   ![Nabídka pokročilou konfiguraci](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-on-a-sql-server-database"></a>Zastavili ochranu databáze SQL serveru

Chcete-li zrušit ochranu databáze SQL serveru Azure Backup zeptá, jestli chcete zachovat body obnovení. Existují dva způsoby, jak zastavit ochranu databáze SQL:

* Zastavit všechny budoucí úlohy zálohování a odstranit všechny body obnovení
* Zastavit všechny budoucí úlohy zálohování, ale ponechat body obnovení. 

Ponechání bodů obnovení představuje náklady, jak body obnovení pro SQL provádění chráněné instance SQL, ceny za poplatek plus využité úložiště. Další informace o cenách zálohování Azure pro SQL, najdete v článku [stránce s cenami za Azure Backup](https://azure.microsoft.com/pricing/details/backup/). Ukončit ochranu pro databázi:

1. Otevřete trezor služby Recovery Services zaregistrovaný u virtuálního počítače SQL.

2. V řídicím panelu trezoru vyberte **využití** zálohování položek otevřete nabídku zálohování položek.

    ![Klikněte na + zálohování a otevřete nabídku cíle zálohování](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. V **zálohované položky** nabídku, vyberte typ správy zálohování **SQL na virtuálním počítači Azure**. 

    ![Klikněte na + zálohování a otevřete nabídku cíle zálohování](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    Zálohování položek seznamu upraví zobrazíte seznam databází SQL. 

4. V seznamu databází SQL vyberte databázi, kterou chcete ukončit ochranu.

    ![Vyberte ze seznamu SQL na virtuálním počítači Azure](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Když vyberete databázi, otevře se jeho nabídku. 

5. V nabídce vybrané databáze, klikněte na tlačítko **Zastavit zálohování** zastavit ochranu databáze.

    ![Vyberte obnovit databázi](./media/backup-azure-sql-database/stop-db-button.png)

    **Zastavit zálohování** otevře se nabídka.

6. V **Zastavit zálohování** nabídce zvolte možnost zachovat zálohovaná Data nebo odstranit zálohování – uložená Data. Volitelně můžete zadat důvod k zastavení ochrany a komentář.

    ![Vyberte obnovit databázi](./media/backup-azure-sql-database/stop-backup-button.png)

7. Klikněte na tlačítko **Zastavit zálohování** ukončit ochranu databáze. 

### <a name="resume-protection-for-a-sql-database"></a>Pokračovat v ochraně databáze SQL

Pokud **zachovat zálohovaná Data** jste vybrali možnost při zastavování ochrany pro SQL database, je možné ochranu obnovit. Pokud se data záloh se uchovávají, nelze obnovit ochranu. 

1. Pokud chcete obnovit ochranu databáze SQL, otevřete zálohovaná položka a klikněte na **pokračovat v zálohování**.

    ![obnovení ochrany databáze](./media/backup-azure-sql-database/resume-backup-button.png)

   Otevře se nabídka zásady zálohování.

2. Z **zásady zálohování** nabídku, vyberte zásadu a klikněte na tlačítko **Uložit**.

### <a name="trigger-an-adhoc-backup"></a>Aktivační událost zálohování ad hoc

Vždy, když chcete, můžete aktivovat zálohování ad hoc. Existují čtyři typy zálohování ad hoc. Podrobnosti o jednotlivých typů najdete v článku [záloh typy SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups).

* Úplné zálohování
* Zkopírujte pouze úplné zálohování
* Rozdílová záloha
* Zálohování protokolu

### <a name="unregister-a-sql-server"></a>Zrušení registrace serveru SQL

Zrušit registraci serveru SQL server po odebrání ochrany, ale před odstraněním trezoru

1. Otevřete trezor služby Recovery Services zaregistrovaný u virtuálního počítače SQL.

2. V **spravovat** části v nabídce trezoru klikněte na tlačítko **infrastruktura zálohování**.  

   ![obnovení ochrany databáze](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. V **servery pro správu** klikněte na tlačítko **chráněné servery**.

   ![obnovení ochrany databáze](./media/backup-azure-sql-database/protected-servers.png)

    Otevře se nabídka chráněné servery. 

4. V **chráněné servery** nabídku, vyberte server, které chcete zrušit registraci. Pokud chcete trezor odstranit, musíte zrušit registraci všech serverů.

5. V nabídce chráněné servery, klikněte pravým tlačítkem na chráněném serveru a vyberte **odstranit**. 

   ![obnovení ochrany databáze](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="sql-database-backup-faq"></a>Nejčastější dotazy SQL database

Následující část obsahuje další informace o zálohování databáze SQL.

### <a name="can-i-throttle-the-speed-of-the-sql-backup-policy-so-it-minimizes-impact-on-the-sql-server"></a>Je možné omezovat rychlost zásady zálohování SQL, minimalizuje dopad na SQL server

Ano, můžete omezit rychlost, jakou zásadu zálohování, která spustí. Chcete-li změnit nastavení:

1. Na serveru SQL Server v `C:\Program Files\Azure Workload Backup\bin` složku, otevřete **TaskThrottlerSettings.json**.

2. V **TaskThrottlerSettings.json** změňte **DefaultBackupTasksThreshold** na nižší hodnotu, například 5.

3. Uložte změny a zavřete soubor.

4. Na serveru SQL Server otevřete Správce úloh a restartujte **služby Koordinátor úloh Azure Backup**.

### <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Ze sekundární repliky, spusťte úplné zálohování

Ne, tato funkce není podporována.

### <a name="do-successful-backup-jobs-create-alerts"></a>Úspěšné úlohy zálohování vytvářejí výstrahy

Ne. Úspěšné úlohy zálohování negenerují výstrahy. Oznámení se odešlou jenom pro úlohy zálohování, které selžou.

### <a name="are-scheduled-backup-job-details-shown-in-the-jobs-menu"></a>Zobrazují podrobnosti úlohy zálohování v nabídce úlohy

Ne. V nabídce úlohy se zobrazí podrobnosti úlohy ad hoc, ale nezobrazuje naplánovaných úloh zálohování. Pokud selžou i všechny naplánované úlohy zálohování, všechny podrobnosti najdete u neúspěšné úlohy výstrah. Pokud chcete monitorování všechny plánované a úlohy zálohování ad hoc [pomocí SQL Server Management Studio](backup-azure-sql-database.md#use-sql-server-management-studio-for-backup-jobs).

### <a name="if-i-select-a-sql-server-will-future-databases-automatically-be-added"></a>Při výběru serveru SQL server se budoucí databáze automaticky přidají

Ne. Při konfiguraci ochrany pro SQL server, pokud zaškrtnete políčko na úrovni serveru se přidá všechny databáze. Nicméně pokud chcete přidat databáze na SQL server po dokončení konfigurace ochrany, je třeba ručně přidat nové databáze před nimi chránit. Databáze nejsou automaticky součástí nakonfigurovanou ochranu.

### <a name="if-i-change-the-recovery-model-how-do-i-restart-protection"></a>Jak se v případě, že mi Změna modelu obnovení restartovat ochrany

Pokud změníte model obnovení, aktivujte úplné zálohování a zálohování protokolů začne podle očekávání.

### <a name="can-i-protect-sql-always-on-availability-groups-where-the-primary-replica-is-on-premises"></a>Může chránit SQL skupin dostupnosti Always On se primární repliky v místním prostředí

Ne. Azure Backup chrání servery SQL v Azure. Pokud se skupina dostupnosti (AG) se pak rozdělí mezi Azure a v místním počítači, skupinu dostupnosti se dají chránit jenom v případě, že primární replikou je spuštěná v Azure. Kromě toho Azure Backup chrání pouze uzly spuštěnými ve stejné oblasti Azure jako trezor služby Recovery Services.

## <a name="next-steps"></a>Další postup

Další informace o službě Azure Backup najdete v ukázce PowerShellu pro zálohování šifrovaných virtuálních počítačů.

> [!div class="nextstepaction"]
> [Zálohování šifrovaného virtuálního počítače](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
