---
title: Zálohování databáze SQL serveru do Azure | Microsoft Docs
description: Tento kurz vysvětluje zálohování serveru SQL Azure. Tento článek vysvětluje, obnovení systému SQL Server.
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
ms.date: 6/29/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: 89a1df607c220e5dc12bc6263955d6e445e529bd
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37115963"
---
# <a name="back-up-sql-server-database-in-azure"></a>Zálohování databáze systému SQL Server v Azure

Databáze systému SQL Server jsou důležité procesy vyžadující cíl bodu nízkou obnovení (RPO) a dlouhodobé uchovávání. Azure Backup poskytuje řešení SQL Serverbackup, které vyžaduje nulové infrastrukturu, což znamená žádné komplexní zálohování serveru, žádný agent pro správu nebo úložiště záloh ke správě. Azure Backup poskytuje centralizovanou správu pro zálohování mezi všechny servery SQL Server, nebo i jiné úlohy.

 V tomto článku dozvíte:

> [!div class="checklist"]
> * Předpoklady pro zálohování serveru SQL Server do Azure
> * Vytváření a používání trezoru služeb zotavení
> * Konfigurace zálohování databáze systému SQL Server
> * Nastavit zásady zálohování (nebo uchování) pro body obnovení
> * Postup obnovení databáze

Před zahájením postupy v tomto článku, měli byste mít SQL Server běží v Azure. [SQL marketplace virtuální počítače můžete použít k rychlému vytvoření systému SQL Server](../sql-database/sql-database-get-started-portal.md).

## <a name="public-preview-limitations"></a>Veřejné omezení verze Preview

Následující položky jsou známá omezení pro verzi Public Preview.

- Virtuální počítač SQL vyžaduje připojení k Internetu pro přístup k veřejným IP adresám Azure. Další podrobnosti najdete v části [vytvoření připojení k síti](backup-azure-sql-database.md#establish-network-connectivity).
- Můžete chránit až 2 000 databází SQL v jeden trezor služeb zotavení. Další databáze SQL by měly být uložené v samostatné trezoru služeb zotavení.
- [Distribuované dostupnosti skupiny zálohování má omezení](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017).
- SQL instance pro Cluster převzetí služeb při selhání (FCI) nejsou podporovány.
- Použití portálu Azure ke konfiguraci Azure Backup pro ochranu databází systému SQL Server. Podpora pro Azure PowerShell, rozhraní příkazového řádku a rozhraní REST API není aktuálně k dispozici.

## <a name="supported-azure-geos"></a>Podporované oblastech Azure

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

## <a name="supported-operating-systems-and-versions-of-sql-server"></a>Podporované operační systémy a verze systému SQL server

Následující podporované operační systémy a verze SQL serveru použít SQL marketplace virtuálních počítačích Azure a jiný marketplace virtuální počítače (kde je nainstalován SQL Server ručně).

### <a name="supported-operating-systems"></a>Podporované operační systémy

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

Linux se aktuálně nepodporuje.

### <a name="supported-versionseditions-of-sql-server"></a>Podporované verze systému SQL Server

- SQL 2012 Enterprise, Standard, Web, Developer, Express
- SQL 2014 Enterprise, Standard, Web, Developer, Express
- SQL 2016 Enterprise, Standard, Web, Developer, Express
- SQL 2017 Enterprise, Standard, Web, Developer, Express


## <a name="prerequisites-for-using-azure-backup-to-protect-sql-server"></a>Předpoklady pro použití zálohování Azure k ochraně SQL serveru 

Předtím, než můžete zálohovat databázi SQL serveru, zkontrolujte následující podmínky. :

- Identifikovat nebo [vytvoření trezoru služeb zotavení](backup-azure-sql-database.md#create-a-recovery-services-vault) ve stejné oblasti, nebo národní prostředí, jako virtuální počítač hostitelem SQL serveru.
- [Zkontrolujte oprávnění na virtuálním počítači](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms) potřebné k zálohování databází SQL.
- [SQL virtuální stroj má síťové připojení](backup-azure-sql-database.md#establish-network-connectivity).

> [!NOTE]
> Může mít pouze jeden řešení zálohování najednou k zálohování databází systému SQL Server. Před použitím této funkce, zakažte jiné zálohování SQL, else zálohování bude konfliktu a selhání. Můžete povolit zálohování Azure pro virtuální počítač IaaS společně s zálohování SQL bez jakéhokoli konfliktu 
>

Pokud tyto podmínky existují ve vašem prostředí, přejít k části [nakonfigurovat svůj trezor chránit databázi SQL](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database). Pokud některé požadované součásti nejsou k dispozici, pokračujte ve čtení v této části.


## <a name="establish-network-connectivity"></a>Vytvoření připojení k síti

Virtuální počítač SQL pro všechny operace vyžaduje připojení k veřejným IP adresám Azure. Operace virtuálního počítače SQL (například databáze zjišťování, konfigurace zálohování, plánované zálohy, obnovení body obnovení a tak dále) nezdaří bez připojení k veřejné IP adresy. K zajištění zrušte cestu pro provoz zálohování můžete použijte některý z následujících možností:

- Seznam povolených adres Azure datacenter IP rozsahy – do seznamu povolených IP adres Azure datacenter IP rozsahy, použijte [center stránce Podrobnosti o rozsahy IP adres a pokyny pro stažení](https://www.microsoft.com/download/details.aspx?id=41653). 
- Nasazení proxy server HTTP pro směrování provozu – Pokud zálohujete databáze SQL ve virtuálním počítači, rozšíření zálohování na virtuální počítač používá rozhraní API HTTPS odesílat příkazy pro správu Azure Backup a data do služby Azure Storage. Rozšíření zálohování také používá Azure Active Directory (AAD) pro ověřování. Směrujte provoz rozšíření zálohování pro tyto tři služby prostřednictvím proxy serveru HTTP, protože se jedná o jedinou komponentou nakonfigurovaný pro přístup do veřejného Internetu.

Jsou kompromisy mezi volby: možnosti správy, přesná kontrola a náklady.

> [!NOTE]
>Značky služby pro Azure Backup musí být dostupné podle obecné dostupnosti.
>

| Možnost | Výhody | Nevýhody |
| ------ | ---------- | ------------- |
| Rozsahy povolených IP adres | Žádné dodatečné poplatky. <br/> Pro přístup k otevření v skupinu NSG, použijte **Set-AzureNetworkSecurityRule** rutiny. | Komplexní správa jako ovlivněný časem změnit rozsahy IP adres. <br/>Poskytuje přístup k celé Azure, ne jenom úložiště.|
| Použít proxy server HTTP   | Přesná kontrola v proxy přes úložiště adresy URL je povolen. <br/>Jeden bod internet přístup k virtuálním počítačům. <br/> Není v souladu změny Azure IP adresy. | Další náklady pro spuštění virtuálního počítače s proxy software. |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>Nastavte oprávnění pro virtuální počítače SQL-webu marketplace

Zálohování virtuálního počítače, vyžaduje Azure Backup **AzureBackupWindowsWorkload** rozšíření nainstalovat. Pokud používáte Azure marketplace virtuálních počítačů, přeskočit na [databáze serveru SQL zjistit](backup-azure-sql-database.md#discover-sql-server-databases). Pokud virtuální počítač hostujícího vaše databáze SQL nebyl vytvořen z Azure marketplace, proveďte v následující části k instalaci rozšíření a nastavit příslušná oprávnění. Kromě **AzureBackupWindowsWorkload** rozšíření, zálohování Azure vyžaduje oprávnění správce systému SQL k ochraně databází SQL. Při zjišťování databází na virtuálním počítači, Azure Backup vytvoří účet, NT Service\AzureWLBackupPluginSvc. Azure Backup za účelem zjištění databází SQL musí mít účet NT Service\AzureWLBackupPluginSvc protokolu databáze SQL v a oprávnění správce SQL. Následující postup vysvětluje, jak poskytnout tato oprávnění.

Jak nakonfigurovat oprávnění:

1. V [portál Azure](https://portal.azure.com), otevřete trezor služeb zotavení, který používáte k ochraně databází SQL.
2. V nabídce řídícího panelu trezoru, klikněte na **+ zálohování** otevřete **cíl zálohování** nabídky.

   ![Klikněte na tlačítko + zálohování otevřete nabídku cíle zálohování](./media/backup-azure-sql-database/open-backup-menu.png)

3. Na **cíl zálohování** nabídky v **kde běží vaše úloha?** nabídky, ponechejte **Azure** jako výchozí.

4. Na **co chcete zálohovat** nabídky, rozbalte rozevírací nabídky a vyberte **systému SQL Server ve virtuálním počítači Azure**.

    ![Klikněte na tlačítko + zálohování otevřete nabídku cíle zálohování](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    **Cíl zálohování** nabídky zobrazí dva nové kroky: **zjistit Operations Manager ve virtuálních počítačích** a **konfigurace zálohování**. **Zjistit Operations Manager ve virtuálních počítačích** spustí vyhledávání pro virtuální počítače Azure.

    ![Zobrazuje nové kroky cíle zálohování](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. Klikněte na tlačítko **spustit zjišťování** k vyhledání nechráněných virtuálních počítačů v rámci předplatného. V závislosti na počtu nechráněných virtuálních počítačů v rámci předplatného může trvat nějakou dobu projít všechny virtuální počítače.

    ![Zálohování čeká na zpracování](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    Po zjištění nechráněné virtuální počítač se zobrazí v seznamu. Nechráněné virtuální počítače jsou uvedeny podle jejich skupiny název a prostředky virtuálního počítače. Je možné pro víc virtuálních počítačů do mají stejný název. Virtuální počítače se stejným názvem však patří do jiné skupiny prostředků. Pokud očekávaný virtuálního počítače v seznamu nezobrazí, zobrazit, pokud je virtuální počítač už chráněný do trezoru.

6. Ze seznamu virtuálních počítačů, vyberte virtuální počítač, který obsahuje databázi SQL, které chcete zálohovat a klikněte na **zjistit databází**. 

    Proces zjišťování nainstaluje **AzureBackupWindowsWorkload** rozšíření ve virtuálním počítači. Rozšíření umožňuje službě Azure Backup ke komunikaci s virtuálním počítačem, můžete zálohovat databáze SQL. Po rozšíření instaluje, Azure Backup vytvoří účet virtuální služby systému Windows, **NT Service\AzureWLBackupPluginSvc**, na virtuálním počítači. Účet služby virtuální vyžaduje oprávnění správce systému SQL. Během virtuální službu účet instalace, pokud se zobrazí chyba **UserErrorSQLNoSysadminMembership**, najdete v části [oprávnění správce systému SQL opravě](backup-azure-sql-database.md#fixing-sql-sysadmin-permissions).

    Oblast oznámení zobrazí průběh zjišťování databáze. V závislosti na tom, kolik databáze jsou na virtuálním počítači může trvat nějakou dobu na dokončení úlohy. Pokud byly zjištěny vybrané databáze, zobrazí se zpráva o úspěšném provedení.

    ![úspěšné nasazení oznámení](./media/backup-azure-sql-database/notifications-db-discovered.png)

Jakmile je databáze přidružit trezor služeb zotavení, dalším krokem je [nakonfigurujte zálohování](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

### <a name="fixing-sql-sysadmin-permissions"></a>Oprava oprávnění správce systému SQL

Během procesu instalace, pokud se zobrazí chyba **UserErrorSQLNoSysadminMembership**, přihlaste do serveru SQL Server Management Studio (SSMS) pomocí účtu, který má oprávnění správce systému SQL. Pokud budete potřebovat speciální oprávnění, nyní byste měli mít rozpoznat účet pomocí ověřování systému Windows.

1. Na serveru SQL Server, otevřete **zabezpečení nebo přihlášení** složky.

    ![Otevření složky systému SQL Server a zabezpečení a přihlašovací účty zobrazíte](./media/backup-azure-sql-database/security-login-list.png)

2. Na složku, přihlášení, klikněte pravým tlačítkem a vyberte **nového přihlašovacího jména**a v části přihlášení – dialogové okno Nový, klikněte na tlačítko **vyhledávání**

    ![Otevřete dialogové okno hledání přihlášení – nové](./media/backup-azure-sql-database/new-login-search.png)

3. Od služby virtuální účet systému Windows **NT Service\AzureWLBackupPluginSvc** již bylo vytvořené během registrace virtuálního počítače a fáze zjišťování SQL, zadejte název účtu, jak se objevuje v  **Zadejte název objektu k výběru** dialogové okno. Klikněte na tlačítko **Kontrola názvů** přeložit název. 

    ![Klikněte na tlačítko Zkontrolovat jména přeložit název neznámé služby](./media/backup-azure-sql-database/check-name.png)

4. Klikněte na tlačítko **OK** zavřete dialogové okno Vybrat uživatele nebo skupinu.

5. V **role serveru** dialogové okno, zajistěte, aby **sysadmin** je vybraná role. Pak klikněte na tlačítko **OK** zavřete **přihlášení – nové**.

    ![Ujistěte se, že je vybraná role serveru sysadmin](./media/backup-azure-sql-database/sysadmin-server-role.png)

    Teď by měla existovat požadovaná oprávnění.

6. Když jste pevné chybě oprávnění, přesto potřebujete přidružit k trezoru služeb zotavení databáze. Na portálu Azure **chráněné servery** seznamu, klikněte pravým tlačítkem na server v chyba a vyberte **opakované zjišťování databází**.

    ![Ověřte, že server má příslušná oprávnění.](./media/backup-azure-sql-database/check-erroneous-server.png)

    Oblast oznámení zobrazí průběh zjišťování databáze. V závislosti na tom, kolik databáze jsou na virtuálním počítači může trvat nějakou dobu na dokončení úlohy. Když byly zjištěny vybrané databáze, zobrazí se zpráva o úspěšném provedení v oznamovací oblasti.

    ![úspěšné nasazení oznámení](./media/backup-azure-sql-database/notifications-db-discovered.png)

Jakmile je databáze přidružit trezor služeb zotavení, dalším krokem je [nakonfigurujte zálohování](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

[!INCLUDE [Section explaining how to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Zjistit databáze systému SQL Server

Zálohování Azure můžete zjistit všechny databáze na instance systému SQL Server, budete moci chránit na požadavky zálohy. Následujícím postupem identifikovat virtuální počítač, který je hostitelem databáze SQL. Po identifikování virtuální počítač Azure Backup nainstaluje lightweight rozšíření pro zjišťování databáze serveru SQL Server.

1. Přihlaste se k vašemu předplatnému v [portál Azure](https://portal.azure.com/).
2. V levé nabídce vyberte **všechny služby**.

    ![Vyberte možnost všechny služby v hlavní nabídce](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. V dialogovém okně všechny služby, zadejte *služeb zotavení*. Během zadávání váš vstup filtrování seznamu prostředků. Jakmile se zobrazí, vyberte **trezory služeb zotavení**.

    ![Typ služeb zotavení v dialogovém okně všechny služby](./media/backup-azure-sql-database/all-services.png) <br/>

    Zobrazí se seznam trezorů služeb zotavení v rámci předplatného. 

4. V seznamu trezor služeb zotavení vyberte trezor, který chcete použít k ochraně vašich databází SQL.

5. V nabídce řídícího panelu trezoru, klikněte na **+ zálohování** otevřete **cíl zálohování** nabídky.

   ![Klikněte na tlačítko + zálohování otevřete nabídku cíle zálohování](./media/backup-azure-sql-database/open-backup-menu.png)

6. Na **cíl zálohování** nabídky v **kde běží vaše úloha?** nabídky, ponechejte **Azure** jako výchozí.

7. Na **co chcete zálohovat** nabídky, rozbalte rozevírací nabídky a vyberte **systému SQL Server ve virtuálním počítači Azure**.

    ![Klikněte na tlačítko + zálohování otevřete nabídku cíle zálohování](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Vybraná jednou, **cíl zálohování** nabídky zobrazí dva kroky: zjistit databází v virtuální počítače a konfigurace služby Backup. 

    ![Zobrazuje nové kroky cíle zálohování](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. Klikněte na tlačítko **spustit zjišťování** k vyhledání nechráněných virtuálních počítačů v rámci předplatného. V závislosti na počtu nechráněných virtuálních počítačů v rámci předplatného může trvat nějakou dobu projít všechny virtuální počítače.

    ![Zálohování čeká na zpracování](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    Po zjištění nechráněné virtuální počítač se zobrazí v seznamu. Více virtuálních počítačů může mít stejný název. Více virtuálních počítačů se stejným názvem ale patří do jiné skupiny prostředků. Nechráněné virtuální počítače jsou uvedeny podle jejich skupiny název a prostředky virtuálního počítače. Pokud očekávaný virtuálního počítače není uvedený, najdete v článku do trezoru, pokud je tento virtuální počítač už chráněný.

9. Ze seznamu virtuálních počítačů, zaškrtněte políčko virtuálního počítače, který obsahuje databází SQL, které chcete chránit a klikněte na tlačítko **zjistit databází**.

    Zálohování Azure zjišťuje všechny databáze SQL na virtuálním počítači. Informace o co se stane, že během fáze zjišťování databáze, najdete v následující části [back-end operace při zjišťování databází SQL](backup-azure-sql-database.md#backend-operations-when-discovering-sql-databases). Po zjištění databází SQL, budete chtít [konfigurovat úlohu zálohování](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

### <a name="backend-operations-when-discovering-sql-databases"></a>Back-end operace při zjišťování databází SQL

Při použití **zjistit databází** nástroj Azure Backup provede následující operace na pozadí:

- zaregistruje virtuální počítač se trezor služeb zotavení pro úlohu zálohování. Všechny databáze na registrované virtuálního počítače lze pouze zálohovat do trezoru služeb zotavení. 

- nainstaluje **AzureBackupWindowsWorkload** rozšíření ve virtuálním počítači. Zálohování databáze SQL je bez agentů řešení, tedy s příponou nainstalovaný na virtuálním počítači není nainstalovaný agent v databázi SQL.

- vytvoří účet služby, **NT Service\AzureWLBackupPluginSvc**, na virtuálním počítači. Všechny operace zálohování a obnovení pomocí účtu služby. **NT Service\AzureWLBackupPluginSvc** vyžaduje oprávnění správce SQL. Všechny virtuální počítače SQL Marketplace dodávají s SqlIaaSExtension nainstalován a AzureBackupWindowsWorkload používá SQLIaaSExtension automaticky získat požadovaná oprávnění. Pokud virtuální počítač nemá nainstalován SqlIaaSExtension, dojde k selhání operace zjišťování DB a získání chybové zprávy, **UserErrorSQLNoSysAdminMembership**. Chcete-li přidat oprávnění sysadmin pro zálohování, postupujte podle pokynů v [nastavení služby Azure Backup oprávnění pro virtuální počítače bez marketplace SQL](backup-azure-sql-database.md#set-permissions-for-non--marketplace-sql-vms).

    ![Vyberte virtuální počítač a databáze](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-database"></a>Konfigurace zálohování pro databázi systému SQL Server

Azure Backup poskytuje služby správy k ochraně vašich databází systému SQL Server a spravovat úlohy zálohování. Správa a monitorování možnosti závisí na svůj trezor služeb zotavení. 

> [!NOTE]
> Může mít pouze jeden řešení zálohování najednou k zálohování databází systému SQL Server. Před použitím této funkce, zakažte jiné zálohování SQL, else zálohování bude konfliktu a selhání. Můžete povolit zálohování Azure pro virtuální počítač IaaS společně s zálohování SQL bez jakéhokoli konfliktu 
>

Konfigurace ochrany pro vaši databázi SQL:

1. Otevřený trezor služeb zotavení registrován u virtuálního počítače systému SQL.

2. V nabídce řídícího panelu trezoru, klikněte na **+ zálohování** otevřete **cíl zálohování** nabídky.

    ![Klikněte na tlačítko + zálohování otevřete nabídku cíle zálohování](./media/backup-azure-sql-database/open-backup-menu.png)

3. Na **cíl zálohování** nabídky v **kde běží vaše úloha?** nabídky, ponechejte **Azure** jako výchozí.

4. Na **co chcete zálohovat** nabídky, rozbalte rozevírací nabídky a vyberte **systému SQL Server ve virtuálním počítači Azure**.

    ![Klikněte na tlačítko + zálohování otevřete nabídku cíle zálohování](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Vybraná jednou, **cíl zálohování** nabídky zobrazí dva kroky: zjistit databází v virtuální počítače a konfigurace služby Backup. Pokud jste prošli Tento článek v pořadí, už jste se seznámili nechráněné virtuální počítače a tento trezor je registrován s virtuálním počítačem. Nyní jste připraveni k nastavení ochrany pro databáze SQL.

5. V nabídce Cíl zálohování, klikněte na tlačítko **konfigurace zálohování**.

    ![Zobrazuje nové kroky cíle zálohování](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Služba Azure Backup se zobrazí všechny instance SQL s samostatné databáze a také skupiny dostupnosti SQL AlwaysOn. Zobrazit samostatné databáze v instanci SQL, kliknutím na dvojitou šipku vedle názvu instance zobrazíte databáze. Následující obrázky znázorňují příklady samostatné instance a skupiny dostupnosti Always On.

    > [!NOTE]
    > Úplné a rozdílové zálohy dojít z primárního uzlu SQL platformy má tohoto omezení. Záloha protokolu může dojít na základě zúčastníte zálohování. Kvůli tomuto omezení musí být zaregistrován primárního uzlu.
    >

    ![Seznam databází v instanci systému SQL](./media/backup-azure-sql-database/discovered-databases.png)

    Kliknutím na dvojitou šipku vedle skupiny dostupnosti AlwaysOn, chcete-li zobrazit seznam databází.

    ![Seznam databází ve skupině dostupnosti AlwaysOn](./media/backup-azure-sql-database/discovered-database-availability-group.png)

6. Ze seznamu databází, vyberte všechny, které chcete chránit a klikněte na tlačítko **OK**.

    ![Vyberte více databází je chránit](./media/backup-azure-sql-database/select-multiple-database-protection.png)

    Můžete vybrat až 50 databází v jednom okamžiku. Pokud chcete chránit databáze více než 50, použít víc průchodů. Až budete chránit prvních 50 databází, opakujte tento krok k ochraně další sadu databází.
    > [!Note] 
    > K optimalizaci zálohování zatížením, Azure Backup dělí velkých úloh zálohování do několika dávek. Maximální počet databází v jedné úloha zálohování je 50.
    >
    >

7. K vytvoření nebo výběr zásady zálohování, v **zálohování** nabídce vyberte možnost **zálohování zásad**, otevřete nabídku.

    ![možnost zásady zálohování](./media/backup-azure-sql-database/select-backup-policy.png)

8. Z **vyberte zásady zálohování** rozevírací nabídky vyberte zásadu zálohování a klikněte na **OK**. Informace o vytvoření vlastní zásady zálohování, najdete v části [definovat zásady zálohování](backup-azure-sql-database.md#define-a-backup-policy).

    ![zásady zálohování vyberte z rozevírací nabídky](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    V nabídce zásady zálohování z **vyberte zásady zálohování** rozevírací nabídky, můžete: 
    - Výchozí zásady HourlyLogBackup, 
    - existující zásady zálohování dříve vytvořili pro server SQL,
    - k [definovat nové zásady](backup-azure-sql-database.md#define-a-backup-policy) na základě plánovaného bodu obnovení (RPO) a rozsah uchování. 

    > [!Note]
    > Azure Backup podporuje dlouhodobé uchovávání založené na schéma dědečka. otec SYN zálohování za účelem optimalizace back-end spotřeba úložiště zároveň pokrývat dodržování předpisů.
    >

9. Jakmile jste vybrali zásadu zálohování v **zálohování nabídky** klikněte na tlačítko **povolit zálohování**.

    ![Povolit vybrané zásady zálohování](./media/backup-azure-sql-database/enable-backup-button.png)

    Můžete sledovat průběh konfigurace v oznamovací oblasti portálu.

    ![zobrazení oznamovací oblasti](./media/backup-azure-sql-database/notifications-area.png)


### <a name="define-a-backup-policy"></a>Definice zásady zálohování

Zásady zálohování definují při se zálohování provést, a jak dlouho jsou uchovávány v zálohování. Azure Backup můžete použít při plánování tři typy zálohování u databází SQL:

* Úplné zálohování - úplnou zálohu databáze bude zálohovat celou databázi. Úplná záloha obsahuje dostatek protokolu k obnovení dat a všechna data v konkrétní databázi nebo sady nebo skupiny souborů. Maximálně můžete aktivovat jeden úplné zálohování za den. Můžete provést úplné zálohování na denní nebo týdenní interval. 
* Rozdílovou zálohu - rozdílové zálohy je založena na poslední, předchozí úplné data zálohování. Rozdílovou zálohu zaznamená pouze data, která se změnil od provedení úplného zálohování. Maximálně můžete aktivovat jeden rozdílové zálohy za den. Úplné zálohování a rozdílovou zálohu nelze konfigurovat ve stejný den.
* Zálohování protokolu transakcí – vytvoření zálohy protokolu v okamžiku obnovení až konkrétní umožňuje druhý. Maximálně můžete nakonfigurovat zálohy transakčního protokolu každých 15 minut.

Zásady se vytvoří v trezoru služeb zotavení úrovně. Pokud máte více trezorů trezory můžete použít stejné zásady zálohování, ale musíte použít zásady zálohování pro každý trezor. Při vytváření zásady zálohování denně, úplné zálohování je výchozí. Rozdílovou zálohu, ale pouze můžete přidat, pokud přejdete dojít týdně úplné zálohování. Následující postup vysvětluje, jak vytvořit zásady zálohování pro SQL server ve virtuálním počítači Azure.

Chcete-li vytvořit zásady zálohování

1. V nabídce zásady zálohování z **vyberte zásady zálohování** rozevírací nabídky vyberte **vytvořit nový**.

   ![Vytvoření nové zásady zálohování](./media/backup-azure-sql-database/create-new-backup-policy.png)

    V nabídce zásady zálohování přepínačů k poskytnutí polí, které jsou potřebné pro všechny nové zásady zálohování serveru SQL.

   ![nové zásady zálohování pole](./media/backup-azure-sql-database/blank-new-policy.png)

2. V **název zásady**, zadejte název. 

3. Úplné zálohování je povinný. Můžete přijmout výchozí hodnoty pro úplné zálohování, nebo klikněte na tlačítko **úplné zálohování** upravit zásady.

    ![nové zásady zálohování pole](./media/backup-azure-sql-database/full-backup-policy.png)

    V rámci zásady úplné zálohování Zvolte frekvenci denně nebo týdně. Pokud si zvolíte denně, vyberte hodiny a časové pásmo, když začne úloha zálohování. Pokud si zvolíte denní úplné zálohy, nelze vytvořit rozdílové zálohy.

   ![denní interval nastavení](./media/backup-azure-sql-database/daily-interval.png)

    Pokud zvolíte Týdenní, zvolte den v týdnu, hodiny a časové pásmo po zahájení úlohy zálohování.

   ![Týdenní nastavení intervalu](./media/backup-azure-sql-database/weekly-interval.png)

4. Standardně jsou vybrané všechny možnosti uchování (denní, týdenní, měsíční a roční). Zrušte zaškrtnutí políčka některý limit rozsahu uchování nemáte mají a nastavit intervaly používat. V nabídce Zásady úplné zálohování, klikněte na tlačítko **OK** tak, aby přijímal nastavení.

   ![nastavení intervalu rozsah uchování](./media/backup-azure-sql-database/retention-range-interval.png)

    Body obnovení jsou označené pro uchovávání dat, na základě jejich rozsahu uchování. Například pokud vyberete každý den, se aktivuje úplné zálohování jenom jedna úplná záloha každý den. V závislosti na vaší týdenního uchovávání určitý den zálohování označené a zachovají na základě týdenní rozsahu uchování. Rozsah uchování měsíční a roční se chová podobně jako.

5. Chcete-li přidat zásadu rozdílové zálohování, klikněte na tlačítko **rozdílové zálohy** otevřete nabídku. 

   ![Otevřete rozdílové zásad](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    V nabídce zásady rozdílovou zálohu, vyberte **povolit** otevřete ovládací prvky četnost a jejich uchovávání. Můžete aktivovat, maximálně jeden rozdílové zálohy za den.
    > [!Important] 
    > Maximálně rozdílové uchovávání záloh může být na 180 dní. Pokud potřebujete uchování delší, je nutné použít úplné zálohy, rozdílové zálohy nelze použít.
    >

   ![Úprava rozdílové zásady](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    Klikněte na tlačítko **OK** pro uložení zásady a návrat do hlavní nabídky zásady zálohování.

6. Chcete-li přidat zásadu zálohování protokolu transakcí, klikněte na tlačítko **zálohy protokolu** otevřete nabídku. V nabídce zálohy protokolu vyberte **povolit**a nastavte ovládací prvky četnost a jejich uchovávání. Zálohy protokolů může dojít, tak často, jak každých 15 minut a uchovávání může být až 35 dnů. Klikněte na tlačítko **OK** pro uložení zásady a návrat do hlavní nabídky zásady zálohování.

   ![upravit zásady zálohování protokolu](./media/backup-azure-sql-database/log-backup-policy-editor.png)

7. Vyberte, zda chcete povolit kompresi zálohování SQL. Komprese je standardně zakázáno.

    Na back-end používá kompresi zálohy nativní SQL Azure Backup.

8. Pokud jste provedli všechny úpravy zásad zálohování, klikněte na tlačítko **OK**. 

   ![rozdílovou uchování](./media/backup-azure-sql-database/differential-backup-policy.png)

## <a name="restore-a-sql-database"></a>Obnovení databáze SQL

Azure Backup poskytuje funkce obnovit jednotlivé databáze pro konkrétní datum nebo čas, až konkrétní druhý, pomocí zálohování transakčního protokolu. Založené na časů obnovení, které zadáte, Azure Backup automaticky určuje odpovídající úplné, rozdíl a řetěz záloh protokolu, které jsou potřebné k obnovení vaše data.

Alternativně můžete vybrat konkrétní plná nebo rozdíl zálohu k obnovení do konkrétní bod obnovení, nikoli určitý čas.

Obnovení databáze

1. Otevřený trezor služeb zotavení registrován u virtuálního počítače systému SQL.

2. V řídícím panelu trezoru vyberte **využití** zálohování položek otevřete nabídku zálohování položek.

    ![Klikněte na tlačítko + zálohování otevřete nabídku cíle zálohování](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. V **položky zálohování** nabídce vyberte typ zálohování správu **SQL ve virtuálním počítači Azure**. 

    ![Klikněte na tlačítko + zálohování otevřete nabídku cíle zálohování](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    Zálohování položek seznamu upraví zobrazíte seznam databází SQL. 

4. Ze seznamu databází SQL vyberte databázi, kterou chcete obnovit.

    ![Vyberte ze seznamu SQL ve virtuálním počítači Azure](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Když vyberete databázi, otevře se jeho nabídky. Tato nabídka poskytuje zálohování podrobnosti pro databáze, včetně:

    * nejstarší a nejnovější obnovit body
    * protokol zálohy stavu za posledních 24 hodin (u databází v úplné a hromadně protokolu model obnovení, pokud je nakonfigurován pro zálohy protokolu transakcí)

5. V nabídce vybrané databáze, klikněte na tlačítko **obnovit DB** otevřete nabídku obnovení.

    ![Vyberte možnost obnovení databáze](./media/backup-azure-sql-database/restore-db-button.png)

    **Obnovení** nabídky otevře a Ano nemá **obnovení konfigurace** nabídky. **Obnovení konfigurace** nabídka je prvním krokem při konfiguraci obnovení. V této nabídce vyberte, kam chcete obnovit data. Dostupné možnosti:
    * Alternativní umístění – tuto možnost použijte, pokud chcete obnovit databázi do alternativního umístění a zároveň zachovat původní zdrojové databáze.
    * Přepište DB - obnovení dat na stejnou instanci systému SQL Server jako původní zdroj. Důsledkem tohoto je že přepisovat původní databázi.

    > [!Important]
    > Pokud vybraná databáze patří do skupiny dostupnosti Always On, neumožňuje SQL databázi přepsání. V tomto případě pouze **alternativního umístění** možnost je povolená.
    >

    ![Kliknutím na tlačítko Konfigurovat a v nabídce konfigurace obnovení](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Obnovit do alternativního umístění

Tento postup vás provede obnovení dat do alternativního umístění. Pokud chcete přepsat databázi při obnovování, přejít k části [obnovit a přepsat databázi](backup-azure-sql-database.md#restore-and-overwrite-the-database). Tento postup předpokládá můžete mít vaše otevřený trezor služeb zotavení a jsou v nabídce obnovení konfigurace. Pokud si nejste, začněte částí, [obnovení databáze SQL](backup-azure-sql-database.md#restore-a-sql-database).

> [!NOTE]
> Můžete obnovit databázi k serveru SQL Server ve stejné oblasti Azure a cílový server musí být zaregistrovaný do trezoru služeb zotavení. 
>

**Server** rozevírací nabídky se zobrazí pouze servery SQL Server, který je zaregistrován v trezoru služeb zotavení. Pokud chcete, aby server není v **Server** seznamu, najdete v části [databáze serveru SQL zjistit](backup-azure-sql-database.md#discover-sql-server-databases) najít server. Během procesu zjišťování databáze jsou registrované všechny nové servery do trezoru služeb zotavení.

1. V **obnovení konfigurace** nabídky:

    * Vyberte **alternativního umístění**,
    * pro **Server**, zvolte server SQL, ve které chcete obnovit databázi.
    * v **Instance** rozevírací nabídce vyberte instanci SQL
    * v **obnovit název databáze** dialogové okno, zadejte název cílové databáze.
    * Pokud je k dispozici, vyberte **přepsání, pokud databáze se stejným názvem již existuje ve vybrané instanci SQL**.
    * Klikněte na tlačítko **OK** k dokončení konfigurace cílové a přesunout do vyberete bod obnovení.

    ![Vyberte alternativní umístění, instanci a názvu v nabídce konfigurace obnovení](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. V **vyberte bod obnovení** nabídky, můžete protokoly (bod v čase) nebo úplné & rozdíl bod obnovení. Pokud chcete obnovit do konkrétní protokolů v okamžiku, pokračujte v tomto kroku. Pokud chcete obnovit bod obnovení úplné nebo rozdíl, přeskočte ke kroku 3.

    ![Vyberte nabídku bodu obnovení](./media/backup-azure-sql-database/recovery-point-menu.png)

    Bod v době obnovení je dostupná jenom pro zálohy protokolů pro databáze s úplným & hromadné protokolována model obnovení. Chcete-li obnovit konkrétní bod v čase:

    1. Vyberte **protokoly (bod v čase)** jako možnost obnovení.

        ![Zvolte typ bodu obnovení](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. V části **obnovit data a času**, klikněte na ikonu kalendáře otevřete kalendáři. Kalendářních dat tučným písmem obsahovat body obnovení a zvýrazní aktuální datum. Vyberte datum v kalendáři se body obnovení. Data se žádné body obnovení nemůžete vybrat.

        ![Otevřete kalendáře](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Jakmile vyberete datum, časová osa grafu zobrazí dostupné body obnovení v průběžné rozsahu.

    3. Pomocí časové osy grafu nebo dialogovém okně čas zadejte určité doby pro bod obnovení a klikněte na tlačítko **OK** k dokončení kroku bod obnovení.
    
       ![Otevřete kalendáře](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        **Vyberte bod obnovení** nabídky zavře a **Upřesnit konfiguraci** otevře se nabídka.

       ![upřesňující konfigurace nabídky](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Z **Upřesnit konfiguraci** nabídky:

        * Zachovat databázi nefunkční po obnovení, na **obnovit pomocí NORECOVERY** nabídce vyberte možnost **povoleno**.
        * Pokud chcete změnit umístění pro obnovení na cílovém serveru, zadat novou cestu v **cíl** sloupce.
        * Klikněte na tlačítko **OK** schválit nastavení a zavřete **Upřesnit konfiguraci**.

    5. Na **obnovení** nabídky, klikněte na tlačítko **obnovení** při spuštění úlohy obnovení. Průběh můžete sledovat v oblasti oznámení. Můžete také sledovat průběh úlohy obnovení databáze.

       ![upřesňující konfigurace nabídky](./media/backup-azure-sql-database/restore-job-notification.png)

3. V **vyberte bod obnovení** nabídce vyberte bod obnovení. Můžete zvolit protokoly (bod v čase) nebo úplné & rozdíl. Pokud chcete obnovit protokolu v okamžiku, přejděte zpátky ke kroku 2. Tento krok obnoví bod konkrétní úplnou nebo rozdílovou obnovení. Pomocí této možnosti se zobrazí všechny úplné a rozdíl bodů obnovení za posledních 30 dní. Pokud chcete zobrazit body obnovení, které jsou starší než 30 dní, klikněte na tlačítko **filtru** otevřete **body obnovení filtru** nabídky. Pokud si zvolíte bod obnovení rozdílové, Azure Backup nejprve obnoví bod odpovídající úplné obnovení a poté použije vybraný rozdílové bod obnovení.

    ![Vyberte nabídku bodu obnovení](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. V **vyberte bod obnovení** nabídce vyberte **úplné & rozdíl**.

       ![Vyberte nabídku bodu obnovení](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        Zobrazí se seznam dostupných bodů obnovení.

    2. V seznamu bodů obnovení vyberte bod obnovení a klikněte na tlačítko **OK** k dokončení tohoto postupu bod obnovení. 

        ![Zvolte bod úplné obnovení](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        **Bod obnovení** nabídky zavře a **Upřesnit konfiguraci** otevře se nabídka.

        ![upřesňující konfigurace nabídky](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Z **Upřesnit konfiguraci** nabídky:

        * Zachovat databázi nefunkční po obnovení, na **obnovit pomocí NORECOVERY** nabídce vyberte možnost **povoleno**. **Obnovení s NORECOVERY** ve výchozím nastavení vypnutá.
        * Pokud chcete změnit umístění pro obnovení na cílovém serveru, zadat novou cestu v **cíl** sloupce.
        * Klikněte na tlačítko **OK** schválit nastavení a zavřete **Upřesnit konfiguraci**.

    4. Na **obnovení** nabídky, klikněte na tlačítko **obnovení** při spuštění úlohy obnovení. Průběh můžete sledovat v oblasti oznámení. Můžete také sledovat průběh úlohy obnovení databáze.

       ![upřesňující konfigurace nabídky](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>Obnovit a přepsat databázi

Tento postup vás provede obnovení dat a přepsáním databáze. Pokud chcete obnovit do alternativního umístění, přejít k části [obnovit do alternativního umístění](backup-azure-sql-database.md#restore-to-an-alternate-location). Tento postup předpokládá máte vaší otevřený trezor služeb zotavení a jsou na **obnovení konfigurace** nabídky (viz následující obrázek). Pokud si nejste, začněte částí, [obnovení databáze SQL](backup-azure-sql-database.md#restore-a-sql-database).

![Kliknutím na tlačítko Konfigurovat a v nabídce konfigurace obnovení](./media/backup-azure-sql-database/restore-overwrite-db.png)

**Server** rozevírací nabídky se zobrazí pouze servery SQL Server, který je zaregistrován v trezoru služeb zotavení. Pokud chcete, aby server není v **Server** seznamu, najdete v části [databáze serveru SQL zjistit](backup-azure-sql-database.md#discover-sql-server-databases) najít server. Během procesu zjišťování databáze jsou registrované všechny nové servery do trezoru služeb zotavení.

1. V **obnovení konfigurace** nabídce vyberte možnost **přepsat DB** a klikněte na tlačítko **OK** k dokončení konfigurace cílové. 

   ![Klikněte na tlačítko Přepsat DB](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    **Server**, **Instance**, a **obnovit název databáze** dialogová okna nejsou nutné.

2. V **vyberte bod obnovení** nabídky, můžete protokoly (bod v čase) nebo úplné & rozdíl bod obnovení. Pokud chcete obnovit protokolu v okamžiku, pokračujte v tomto kroku. Pokud chcete obnovit bod obnovení úplné & rozdíl, přeskočte ke kroku 3.

    ![Vyberte nabídku bodu obnovení](./media/backup-azure-sql-database/recovery-point-menu.png)

    Bod v době obnovení je dostupná jenom pro zálohy protokolů pro databáze s úplným & hromadné protokolována model obnovení. Chcete-li druhý vyberte bod obnovení čas na konkrétní:

    1. Vyberte **protokoly (bod v čase)** jako možnost obnovení.

        ![Zvolte typ bodu obnovení](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. V části **obnovit data a času**, klikněte na ikonu kalendáře otevřete kalendáři. Kalendářních dat tučným písmem obsahovat body obnovení a zvýrazní aktuální datum. Vyberte datum v kalendáři se body obnovení. Data se žádné body obnovení nemůžete vybrat.

        ![Otevřete kalendáře](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Jakmile vyberete datum, časová osa grafu zobrazí dostupné body obnovení.

    3. Pomocí časové osy grafu nebo dialogovém okně čas zadejte určité doby pro bod obnovení a klikněte na tlačítko **OK** k dokončení kroku bod obnovení.
    
       ![Otevřete kalendáře](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        **Vyberte bod obnovení** nabídky zavře a **Upřesnit konfiguraci** otevře se nabídka.

       ![upřesňující konfigurace nabídky](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Z **Upřesnit konfiguraci** nabídky:

        * Zachovat databázi nefunkční po obnovení, na **obnovit pomocí NORECOVERY** nabídce vyberte možnost **povoleno**.
        * Pokud chcete změnit umístění pro obnovení na cílovém serveru, zadat novou cestu v **cíl** sloupce.
        * Klikněte na tlačítko **OK** schválit nastavení a zavřete **Upřesnit konfiguraci**.

    5. Na **obnovení** nabídky, klikněte na tlačítko **obnovení** při spuštění úlohy obnovení. Průběh můžete sledovat v oblasti oznámení. Můžete také sledovat průběh úlohy obnovení databáze.

       ![upřesňující konfigurace nabídky](./media/backup-azure-sql-database/restore-job-notification.png)

3. V **vyberte bod obnovení** nabídce vyberte bod obnovení. Můžete zvolit protokoly (bod v čase) nebo úplné & rozdíl. Pokud chcete obnovit protokolu v okamžiku, přejděte zpátky ke kroku 2. Tento krok obnoví bod konkrétní úplnou nebo rozdílovou obnovení. Pomocí této možnosti se zobrazí všechny úplné a rozdíl bodů obnovení za posledních 30 dní. Pokud chcete zobrazit body obnovení, které jsou starší než 30 dní, klikněte na tlačítko **filtru** otevřete **body obnovení filtru** nabídky. Pokud si zvolíte bod obnovení rozdílové, Azure Backup nejprve obnoví bod odpovídající úplné obnovení a poté použije vybraný rozdílové bod obnovení.

    ![Vyberte nabídku bodu obnovení](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. V **vyberte bod obnovení** nabídce vyberte **úplné & rozdíl**.

       ![Vyberte nabídku bodu obnovení](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        Zobrazí se seznam dostupných bodů obnovení.

    2. V seznamu bodů obnovení vyberte bod obnovení a klikněte na tlačítko **OK** k dokončení tohoto postupu bod obnovení. 

        ![Zvolte bod úplné obnovení](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        **Bod obnovení** nabídky zavře a **Upřesnit konfiguraci** otevře se nabídka.

        ![upřesňující konfigurace nabídky](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Z **Upřesnit konfiguraci** nabídky:

        * Zachovat databázi nefunkční po obnovení, na **obnovit pomocí NORECOVERY** nabídce vyberte možnost **povoleno**. **Obnovení s NORECOVERY** ve výchozím nastavení vypnutá.
        * Pokud chcete změnit umístění pro obnovení na cílovém serveru, zadat novou cestu v **cíl** sloupce.
        * Klikněte na tlačítko **OK** schválit nastavení a zavřete **Upřesnit konfiguraci**.

    4. Na **obnovení** nabídky, klikněte na tlačítko **obnovení** při spuštění úlohy obnovení. Průběh můžete sledovat v oblasti oznámení. Můžete také sledovat průběh úlohy obnovení databáze.

       ![upřesňující konfigurace nabídky](./media/backup-azure-sql-database/restore-job-notification.png)


## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Správa operací zálohování Azure SQL na virtuálních počítačích Azure

Tato část obsahuje informace o různých Azure Backup operacích správy k dispozici pro SQL na virtuálních počítačích Azure. Existují následující operace vysoké úrovně:

* Monitorování úloh
* Výstrahy zálohy
* Ukončete ochranu databáze SQL
* Pokračovat v ochraně pro databázi SQL.
* Spustit úlohu zálohování ad hoc
* Zrušení registrace SQL server

### <a name="monitor-jobs"></a>Monitorování úloh
Zálohování Azure je podnikové řešení třída poskytuje pokročilé zálohování výstrahy a oznámení k jeho selhání (naleznete výstrahy zálohování níže). Pokud chcete sledovat konkrétní úlohy můžete použít některý z následujících možností podle potřeby:

#### <a name="using-azure-portal---recovery-services-vault-for-all-ad-hoc-operations"></a>Pomocí portálu Azure -> trezoru služeb zotavení pro všechny operace ad-hoc
Azure Backup zobrazuje všechny ruční aktivaci ad hoc, úlohy nebo na portálu úlohy zálohování. Úlohy, které jsou k dispozici v portálu zahrnout: všechny konfiguraci operace zálohování, ručně spustí operace zálohování, operace obnovení registrace a zjistit databázových operací a zastavit operace zálohování. 
![upřesňující konfigurace nabídky](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Všechny naplánované úlohy zálohování včetně úplné, rozdíl a protokolu zálohování, nebude se zobrazovat na portálu a je možné monitorovat pomocí SQL Server Management Studio, jak je popsáno níže.
>

#### <a name="using-sql-server-management-studio-ssms-for-backup-jobs"></a>Pomocí SQL Server Management Studio (SSMS) pro úlohy zálohování
Azure Backup používá SQL nativních rozhraní API pro všechny operace zálohování. Pomocí nativních rozhraní API, může načíst všechny úlohy informace ze [tabulky SQL záloh](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) v databázi msdb. 

Můžete použít níže dotazu jako příklad načíst všechny úlohy zálohování pro konkrétní databázi s názvem "DB1". Můžete přizpůsobit níže dotazu další pokročilé monitorování.
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

### <a name="backup-alerts"></a>Výstrahy zálohy

S zálohy protokolu, ke kterým dochází každých 15 minut může být někdy monitorování úloh zálohování zdlouhavé. Zálohování Azure plánované v tomto případě potenciálně zdlouhavé zadáním e-mailové výstrahy aktivovány žádné chyby zálohování. Výstrahy se konsolidují na úrovni databáze v kódu chyby. Například pokud databáze obsahuje více selhání zálohování, místo přijetí výstrahu při každém selhání obdržíte e-mailu pro první selhání. Pak můžete přihlášení do portálu Azure monitorovat následné selhání pro tuto databázi. 

K monitorování výstrahy zálohy:

1. Přihlaste se k předplatnému Azure v [portál Azure](https://portal.azure.com).

2. Otevřený trezor služeb zotavení registrován u virtuálního počítače systému SQL.

3. V nabídce trezoru služeb zotavení vyberte **výstrahy a události**. 

   ![upřesňující konfigurace nabídky](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. V **výstrahy a události** nabídce vyberte možnost **zálohování výstrahy** zobrazíte seznam výstrah.

   ![upřesňující konfigurace nabídky](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-on-a-sql-server-database"></a>Ukončete ochranu databáze SQL serveru

Když přestanete chránit databázi systému SQL Server, požádá zálohování Azure, pokud chcete zachovat body obnovení. Zastavte ochranu databáze SQL dvěma způsoby:

* Zastavit všechny budoucí úlohy zálohování a odstranění všech bodů obnovení
* Zastavit všechny budoucí úlohy zálohování, ale ponechte bodů obnovení 

Opouštění body obnovení představuje s náklady, jako body obnovení pro SQL provádění ceny poplatků navíc úložiště využívat chráněné instance SQL. Další informace o cenách zálohování Azure SQL najdete v tématu [Azure Backup stránce s cenami](https://azure.microsoft.com/pricing/details/backup/). Ukončete ochranu pro databázi:

1. Otevřený trezor služeb zotavení registrován u virtuálního počítače systému SQL.

2. V řídícím panelu trezoru vyberte **využití** zálohování položek otevřete nabídku zálohování položek.

    ![Klikněte na tlačítko + zálohování otevřete nabídku cíle zálohování](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. V **položky zálohování** nabídce vyberte typ zálohování správu **SQL ve virtuálním počítači Azure**. 

    ![Klikněte na tlačítko + zálohování otevřete nabídku cíle zálohování](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    Zálohování položek seznamu upraví zobrazíte seznam databází SQL. 

4. Ze seznamu databází SQL vyberte databázi, kterou chcete zastavit ochranu.

    ![Vyberte ze seznamu SQL ve virtuálním počítači Azure](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Když vyberete databázi, otevře se jeho nabídky. 

5. V nabídce vybrané databáze, klikněte na tlačítko **zastavení zálohování** zastavení ochrany databáze.

    ![Vyberte možnost obnovení databáze](./media/backup-azure-sql-database/stop-db-button.png)

    **Zastavení zálohování** otevře se nabídka.

6. V **zastavení zálohování** nabídce zvolte zachovat Data zálohy, nebo odstranit Data zálohování. Volitelně můžete zadat důvod k zastavení ochrany a komentář.

    ![Vyberte možnost obnovení databáze](./media/backup-azure-sql-database/stop-backup-button.png)

7. Klikněte na tlačítko **zastavení zálohování** ukončit ochranu v databázi. 

### <a name="resume-protection-for-a-sql-database"></a>Pokračovat v ochraně pro databázi SQL.

Pokud **zachovat Data zálohování** jste vybrali možnost, při zastavení ochrany pro databáze SQL, je možné, obnovte ochranu. Pokud se zálohovaná data nebyla zachována, ochrany nelze obnovit. 

1. Chcete-li obnovit ochranu pro databázi SQL, otevřete položku Zálohování a klikněte na **pokračovat v zálohování**.

    ![Obnovte ochranu databáze](./media/backup-azure-sql-database/resume-backup-button.png)

   Otevře se v nabídce zásady zálohování.

2. Z **zásady zálohování** nabídce vyberte zásadu a klikněte na tlačítko **Uložit**.

### <a name="trigger-an-adhoc-backup"></a>Spustit zálohování v režimu ad hoc

Vždy, když chcete, můžete spustit zálohování v režimu ad hoc. Existují čtyři typy záloh ad hoc. Podrobné informace pro každý typ, najdete v článku [typy SQL zálohy](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups).

* Úplné zálohování
* Kopírovat pouze úplné zálohování
* Rozdílovou zálohu
* Záloha protokolu

### <a name="unregister-a-sql-server"></a>Zrušení registrace SQL Server

Zrušení registrace SQL server po odebrání ochrany, ale před odstraněním trezoru

1. Otevřený trezor služeb zotavení registrován u virtuálního počítače systému SQL.

2. V **spravovat** části nabídce trezoru klikněte na tlačítko **infrastruktura zálohování**.  

   ![Obnovte ochranu databáze](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. V **servery pro správu** klikněte na tlačítko **chráněné servery**.

   ![Obnovte ochranu databáze](./media/backup-azure-sql-database/protected-servers.png)

    Otevře se nabídka chráněné servery. 

4. V **chráněné servery** nabídce vyberte server, kterou chcete zrušit registraci. Pokud chcete trezor odstranit, musíte zrušit registraci všech serverů.

5. V nabídce chráněné servery, klikněte pravým tlačítkem na chráněném serveru a vyberte **odstranit**. 

   ![Obnovte ochranu databáze](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="next-steps"></a>Další postup

Další informace o službě Azure Backup najdete v ukázce PowerShellu pro zálohování šifrovaných virtuálních počítačů.

> [!div class="nextstepaction"]
> [Zálohování šifrovaného virtuálního počítače](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
