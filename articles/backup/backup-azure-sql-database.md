---
title: Zálohování databází SQL serveru do Azure | Dokumentace Microsoftu
description: Tento kurz vysvětluje, jak k zálohování SQL serveru do Azure. Tento článek také popisuje obnovení SQL serveru.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: raynew
ms.openlocfilehash: 959e58a58099b71a595f995e03cc70255fd6d059
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198023"
---
# <a name="back-up-sql-server-databases-to-azure"></a>Zálohování databází systému SQL Server do Azure

Databáze systému SQL Server jsou důležité úlohy, které vyžadují plánovaného bodu s nízkou obnovení (RPO) a dlouhodobé uchovávání. Azure Backup poskytuje zálohování řešení systému SQL Server, které nevyžaduje žádnou infrastrukturu: není komplexní záložní server, žádný agent pro správu a žádné záložní úložiště pro správu. Azure Backup poskytuje centralizovanou správu pro své zálohy ve všech serverech, na kterých běží SQL Server, nebo dokonce během různých úloh.

V tomto článku se dozvíte:

> [!div class="checklist"]
> * Požadavky pro zálohování instanci SQL serveru do Azure.
> * Jak vytvořit a použít trezor služby Recovery Services.
> * Jak nakonfigurovat zálohování databáze serveru SQL Server.
> * Jak nastavit zásadu zálohování (nebo uchovávání) pro body obnovení.
> * Postup při obnovení databáze.

Než začnete postupy v tomto článku, měli byste mít instanci SQL serveru, na kterém běží v Azure. [Pomocí virtual machines SQL Marketplace můžete rychle vytvořit instanci systému SQL Server](../sql-database/sql-database-get-started-portal.md).

## <a name="public-preview-limitations"></a>Omezení veřejné verze Preview

Následující položky jsou známé omezení verze Public Preview:

- Virtuální počítač SQL (VM) vyžaduje připojení k Internetu pro přístup k veřejným IP adresám Azure. Podrobnosti najdete v tématu [navázat připojení k síti](backup-azure-sql-database.md#establish-network-connectivity).
- Chraňte až 2 000 databází SQL v jeden trezor služby Recovery Services. Další databáze SQL by měla být uložena v samostatném trezoru služby Recovery Services.
- [Zálohování skupiny dostupnosti v distribuovaných](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017) mají omezení.
- Vždy na převzetí služeb při selhání instance clusteru SQL Server (instancích Fci) nejsou podporovány pro zálohování.
- Nakonfigurujte zálohování Azure pro ochranu databáze SQL serveru pomocí webu Azure portal. Prostředí Azure PowerShell, rozhraní příkazového řádku Azure a rozhraní REST API nejsou aktuálně podporované.
- Operace zálohování a obnovení pro FCI zrcadlení databáze, snímky databází a databází nejsou podporovány.
- Databáze s velkým počtem souborů nejde chránit. Maximální počet souborů, které jsou podporovány není velmi deterministické číslo, protože nejen závisí na počtu souborů, ale také závisí na délka cesty k souborům. Takové případy jsou však méně běžně se vyskytujícím. Vytváříme řešení, které se o to postarají.

Najdete [oddílu Nejčastější dotazy](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#faq) podrobné informace o podpoře nebo není podporované scénáře.

## <a name="support-for-azure-geos"></a>Podpora pro zeměpisných oblastech Azure

Azure Backup podporuje pro následujících zeměpisných oblastech:

- Austrálie – jihovýchod (ASE)
- Brazílie – jih (BRS)
- Kanada – střed (CNC)
- Kanada – východ (CE)
- USA – střed (CUS)
- Východní Asie (EA)
- Východní Austrálie (AE)
- USA – východ (EUS)
- USA – východ 2 (EUS2)
- Indie – střed (INC)
- Indie – jih (INS)
- Japonsko – východ (JPE)
- Japonsko – západ (JPW)
- Jižní Korea – střed (KRC)
- Jižní Korea – jih (KRS)
- Střed USA – sever (NCUS)
- Severní Evropa (NE)
- Střed USA – jih (SCUS)
- Jihovýchodní Asie (SEA)
- Velká Británie – jih (UKS)
- Velká Británie – západ (UKW)
- Střed USA – západ (WCUS)
- Západní Evropa (WE)
- Západní USA (WUS)
- Západní USA 2 (WUS 2)

## <a name="support-for-operating-systems-and-sql-server-versions"></a>Podpora pro operační systémy a verze systému SQL Server

Tato část popisuje podporu Azure Backup pro operační systémy a verze systému SQL Server. Jsou podporována SQL Tržiště Azure virtual machines a virtuálních počítačů mimo Marketplace (kde je nainstalován SQL Server ručně).

### <a name="supported-operating-systems"></a>Podporované operační systémy

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

Linux se momentálně nepodporuje.

### <a name="supported-sql-server-versions-and-editions"></a>Podporované edice a verze SQL serveru

- SQL Server 2012 Enterprise, Standard, Web, Developer, Express
- SQL Server 2014 Enterprise, Standard, Web, Developer, Express
- SQL Server 2016 Enterprise, Standard, Web, Developer, Express
- SQL Server 2017 Enterprise, Standard, Web, Developer, Express

## <a name="prerequisites"></a>Požadavky

Zálohujte databázi SQL serveru, zkontrolujte následující podmínky:

- Identifikovat nebo [vytvořte trezor služby Recovery Services](backup-azure-sql-database.md#create-a-recovery-services-vault) ve stejné oblasti nebo národní prostředí jako virtuální počítač, který je hostitelem vaší instanci SQL serveru.
- [Zkontrolujte oprávnění na virtuálním počítači](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms) , které jsou potřeba k zálohování databází SQL.
- Ověřte, že [SQL virtuální počítač má síťové připojení](backup-azure-sql-database.md#establish-network-connectivity).
- Zkontrolujte, zda jsou databáze SQL s názvem, jak je uvedeno [pokyny pro pojmenování](backup-azure-sql-database.md#sql-database-naming-guidelines-for-azure-backup) pro službu Azure Backup úspěšné zálohování.

> [!NOTE]
> V době zálohování databází systému SQL Server může mít pouze jeden záložní řešení. Před použitím této funkce; zakázat další zálohy SQL zálohy v opačném případě bude v konfliktu a selhání. Můžete povolit Azure Backup pro virtuální počítač IaaS spolu s zálohování SQL bez jakéhokoli konfliktu.
>

Pokud tyto podmínky existují ve vašem prostředí, i nadále [konfigurace zálohování databází systému SQL Server](backup-azure-sql-database.md#configure-backup-for-sql-server-databases). Pokud neexistují žádné požadavky, pokračujte ve čtení.


## <a name="establish-network-connectivity"></a>Vytvoření připojení k síti

Pro všechny operace musí virtuální počítač SQL připojení k veřejným IP adresám Azure. Operace virtuálního počítače SQL (jako je například zjišťování databází, nakonfigurujte zálohování, naplánovat zálohování, obnovit body obnovení a tak dále) selhání bez připojení k veřejné IP adresy. Použijte některou z následujících možností a poskytne jasný pro provoz zálohování:

- Seznam povolených adres Azure datacenter rozsahy IP adres: Na seznam povolených rozsahů IP adres datacentra Azure použít [stránce služby Stažení softwaru pro podrobnosti o rozsahy IP adres a pokyny](https://www.microsoft.com/download/details.aspx?id=41653).
- Nasazení HTTP proxy server pro směrování provozu: Při zálohování databáze SQL na virtuálním počítači, rozšíření zálohování na virtuálním počítači pomocí rozhraní API HTTPS příkazy pro správu odesílat data do služby Azure Storage a Azure Backup. Rozšíření zálohování také používá pro ověřování Azure Active Directory (Azure AD). Směrování provozu linka záložního telefonu pro tyto tři služby prostřednictvím proxy serveru HTTP. Rozšíření vaší jedinou komponentou, která je nakonfigurovaná pro přístup k veřejnému Internetu.

Kompromisy mezi možnostmi jsou možnosti správy, podrobnou kontrolu a náklady.

> [!NOTE]
> Značky služby Azure Backup by měla být podle obecné dostupnosti k dispozici.
>

| Možnost | Výhody | Nevýhody |
| ------ | ---------- | ------------- |
| Rozsahy seznamu povolených IP adres | Žádné další náklady. <br/> Pro přístup k aplikaci skupinu zabezpečení sítě, použijte **Set-AzureNetworkSecurityRule** rutiny. | Komplexní spravovat, protože ovlivněné rozsahy IP adres v průběhu času měnit. <br/>Poskytuje přístup k celé Azure, ne jenom služby Azure Storage.|
| Použít proxy server HTTP   | Detailní kontrola v proxy serveru úložiště je povolené adresy URL. <br/>Jeden bod internetový přístup k virtuálním počítačům. <br/> Není v souladu s Azure IP adresa změní. | Další náklady na provoz virtuálního počítače se softwarem proxy serveru. |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>Nastavení oprávnění pro jiné - Tržiště virtuálních počítačů SQL

Zálohování virtuálního počítače, vyžaduje Azure Backup **AzureBackupWindowsWorkload** rozšíření k instalaci. Pokud používáte virtuální počítače Azure Marketplace, i nadále [databáze SQL serveru zjistit](backup-azure-sql-database.md#discover-sql-server-databases). Pokud virtuální počítač, který je hostitelem databáze SQL není vytvořen z Azure Marketplace, proveďte následující postup k instalaci rozšíření a nastavte příslušná oprávnění. Kromě **AzureBackupWindowsWorkload** rozšíření, Azure Backup vyžaduje oprávnění správce systému SQL pro ochranu databáze SQL. Azure Backup se zjistit databáze na virtuálním počítači, vytvoří účet **NT SERVICE\AzureWLBackupPluginSvc**. Tento účet se používá pro zálohování a obnovení a musí mít oprávnění správce systému SQL. Kromě toho bude využívat Azure Backup **NT AUTHORITY\SYSTEM** účet pro zjišťování DB nebo dotaz, aby tento účet musí být veřejné přihlášení na SQL.

Ke konfiguraci oprávnění:

1. V [webu Azure portal](https://portal.azure.com), otevřete trezor služby Recovery Services, který používáte pro ochranu databáze SQL.

2. Na **trezor služby Recovery Services** řídicího panelu, vyberte **zálohování**. **Cíl zálohování** otevře se nabídka.

   ![Vybrat zálohu, otevřete nabídku cíl zálohování](./media/backup-azure-sql-database/open-backup-menu.png)

3. Na **cíl zálohování** nabídky, nastavte **ve kterém je spuštěná vaše úloha** na výchozí hodnotu: **Azure**.

4. Rozbalte **co chcete zálohovat** rozevírací seznam a vyberte **systému SQL Server na virtuálním počítači Azure**.

    ![Vyberte pro zálohování serveru SQL Server ve virtuálním počítači Azure](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    **Cíl zálohování** nabídce se zobrazí dva kroky: **Zjistit databáze ve virtuálních počítačích** a **konfiguraci zálohování**. **Zjistit databáze ve virtuálních počítačích** kroku spustit vyhledávání pro virtuální počítače Azure.

    ![Projděte si dva kroky cíl zálohování](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. V části **zjistit databáze ve virtuálních počítačích**vyberte **spustit zjišťování** vyhledat nechráněné virtuální počítače v rámci předplatného. Může trvat nějakou vyhledávání ve všech virtuálních počítačů. Doba hledání závisí na počtu nechráněných virtuálních počítačů v rámci předplatného.

    ![Zálohování čeká na vyřízení při hledání pro databáze ve virtuálních počítačích](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. V seznamu virtuálních počítačů vyberte virtuální počítač, který má SQL databázi zálohovat a pak vyberte **zjišťování databází**.

    Proces zjišťování nainstaluje **AzureBackupWindowsWorkload** rozšíření na virtuálním počítači. Rozšíření umožňuje službě Azure Backup pro komunikaci s virtuálním počítačem, můžete zálohovat databáze SQL. Po instalaci rozšíření Azure Backup vytvoří účet virtuální služby Windows **NT Service\AzureWLBackupPluginSvc** na virtuálním počítači. Účet virtuální služby vyžaduje oprávnění správce systému SQL. Během procesu účet instalace virtuální služby, pokud se zobrazí chyba `UserErrorSQLNoSysadminMembership`, naleznete v tématu [oprávnění sysadmin SQL opravit](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

    **Oznámení** oblasti se zobrazuje průběh zjišťování databází. Může trvat nějakou dobu se úloha dokončí. Čas úlohy závisí na tom, kolik databází na virtuálním počítači. Když se zjistí vybrané databáze, se zobrazí zpráva o úspěchu.

    ![Zpráva o úspěšném nasazení](./media/backup-azure-sql-database/notifications-db-discovered.png)

Poté, co přidružíte databáze v trezoru služby Recovery Services, dalším krokem je [konfigurace úlohy zálohování](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

### <a name="fix-sql-sysadmin-permissions"></a>Oprava oprávnění správce systému SQL

Během procesu instalace, pokud se zobrazí chyba `UserErrorSQLNoSysadminMembership`, přihlaste se k serveru SQL Server Management Studio (SSMS) se pomocí účtu s oprávněními správce systému SQL Server. Pokud potřebujete speciální oprávnění, by měla fungovat ověřování Windows.

1. Na serveru SQL Server otevřete složku zabezpečení/přihlášení.

    ![Otevřít složku zabezpečení/přihlášení a zobrazit účty](./media/backup-azure-sql-database/security-login-list.png)

2. Klikněte pravým tlačítkem na složku přihlášení a výběr **nového přihlašovacího jména**. V **přihlášení – nové** dialogu **hledání**.

    ![V části přihlášení – nové dialogové okno, vyberte hledání](./media/backup-azure-sql-database/new-login-search.png)

3. Účet služby Windows virtuální **NT SERVICE\AzureWLBackupPluginSvc** jste vytvořili během registrace virtuálního počítače a fázi zjišťování SQL. Zadejte název účtu, jak je znázorněno **zadejte název objektu k výběru** pole. Vyberte **Kontrola názvů** přeložit název.

    ![Vyberte Zkontrolovat jména k přeložení názvu služby neznámý](./media/backup-azure-sql-database/check-name.png)

4. Vyberte **OK** zavřete dialogové okno.

5. V **role serveru** pole, ujistěte se, že **sysadmin** je vybrána role. Vyberte **OK** zavřete dialogové okno.

    ![Ujistěte se, že je vybrána role serveru sysadmin](./media/backup-azure-sql-database/sysadmin-server-role.png)

    Teď by měla existovat požadovaná oprávnění.

6. I když jste opravili chybu oprávnění, je potřeba přidružit databázi k trezoru služby Recovery Services. Na webu Azure Portal v **chráněné servery** seznamu, klikněte pravým tlačítkem na server, který je v chybě, stavu a vyberte **opětovné zjištění databází**.

    ![Ověřte, že server má příslušná oprávnění](./media/backup-azure-sql-database/check-erroneous-server.png)

    **Oznámení** oblasti se zobrazuje průběh zjišťování databází. Může trvat nějakou dobu se úloha dokončí. Čas úlohy závisí na tom, kolik databází na virtuálním počítači. Když se nacházejí ve vybraných databázích, zobrazí se zpráva o úspěchu.

    ![Zpráva o úspěšném nasazení](./media/backup-azure-sql-database/notifications-db-discovered.png)

Poté, co přidružíte databáze v trezoru služby Recovery Services, dalším krokem je [konfigurace úlohy zálohování](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

## <a name="sql-database-naming-guidelines-for-azure-backup"></a>SQL database, pokyny pro pojmenování pro službu Azure Backup
Pro zajištění plynulého zálohování pomocí služby Azure Backup pro SQL Server na virtuálním počítači IaaS, nepoužívejte následující při pojmenování databáze:

  * Na konci/úvodní mezery
  * Na konci '!'

Máme aliasů pro Azure table nepodporované znaky, ale doporučujeme ty také. Další informace najdete v tomto [článku](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Zjistit databáze systému SQL Server

Azure Backup zjistí všechny databáze na instanci systému SQL Server. Podle požadavků na zálohování může chránit databáze. Následující postup použijte k identifikaci virtuálního počítače, který je hostitelem databáze SQL. Po identifikaci virtuálního počítače Azure Backup nainstaluje jednoduché rozšíření se zjistit databáze systému SQL Server.

1. Přihlaste se k vašemu předplatnému [webu Azure portal](https://portal.azure.com/).

2. V nabídce vlevo vyberte **všechny služby**.

    ![Vyberte všechny služby](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. V **všechny služby** dialogového okna zadejte **služby Recovery Services**. Při psaní váš vstup, vyfiltruje seznam prostředků. Vyberte **trezory služby Recovery Services** v seznamu.

  ![Zadejte a vyberte trezory služby Recovery Services](./media/backup-azure-sql-database/all-services.png) <br/>

    Zobrazí se seznam trezorů služby Recovery Services v rámci předplatného.

4. V seznamu trezorů služby Recovery Services vyberte trezor použití k ochraně vašich databází SQL.

5. Na **trezor služby Recovery Services** řídicího panelu, vyberte **zálohování**. **Cíl zálohování** otevře se nabídka.

   ![Vybrat zálohu, otevřete nabídku cíl zálohování](./media/backup-azure-sql-database/open-backup-menu.png)

6. Na **cíl zálohování** nabídky, nastavte **ve kterém je spuštěná vaše úloha** na výchozí hodnotu: **Azure**.

7. Rozbalte **co chcete zálohovat** rozevírací seznam a vyberte **systému SQL Server na virtuálním počítači Azure**.

    ![Vyberte pro zálohování serveru SQL Server ve virtuálním počítači Azure](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    **Cíl zálohování** nabídce se zobrazí dva kroky: **Zjistit databáze ve virtuálních počítačích** a **konfiguraci zálohování**.

    ![Projděte si dva kroky cíl zálohování](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. V části **zjistit databáze ve virtuálních počítačích**vyberte **spustit zjišťování** vyhledat nechráněné virtuální počítače v rámci předplatného. Může trvat nějakou dobu museli procházet všechny virtuální počítače. Doba hledání závisí na počtu nechráněných virtuálních počítačů v rámci předplatného.

    ![Zálohování čeká na vyřízení při hledání pro databáze ve virtuálních počítačích](./media/backup-azure-sql-database/discovering-sql-databases.png)

    Po zjištění nechráněného virtuálního počítače se zobrazí v seznamu. Několik virtuálních počítačů může mít stejný název. Virtuální počítače se stejným názvem, ale patří do různých skupin prostředků. Nechráněné virtuální počítače jsou uvedené v jejich virtuálních počítačů a skupinou prostředků. Pokud není uveden očekávané virtuálního počítače, naleznete v tématu Pokud tento virtuální počítač už chráněný do trezoru.

9. V seznamu virtuálních počítačů vyberte virtuální počítač, který má SQL databázi zálohovat a pak vyberte **zjišťování databází**.

    Azure Backup zjistí všechny databáze SQL na virtuálním počítači. Informace o tom, co se stane během fáze zjištění databáze najdete v tématu [operace na pozadí](backup-azure-sql-database.md#background-operations). Po zjištění databází SQL, jste připraveni [konfigurace úlohy zálohování](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

### <a name="background-operations"></a>Operace na pozadí

Při použití **zjišťování databází** nástroj Azure Backup provádí následující operace na pozadí:

- Registraci virtuálního počítače v trezoru služby Recovery Services úlohu zálohování. Všechny databáze na registrované virtuálního počítače můžete zálohovat do trezoru služby Recovery Services.

- Nainstalujte **AzureBackupWindowsWorkload** rozšíření na virtuálním počítači. Zálohování SQL database je bez agentů řešení. Rozšíření je nainstalované na virtuálním počítači a neinstaluje žádný agent pro SQL database.

- Vytvoření účtu služby **NT Service\AzureWLBackupPluginSvc** na virtuálním počítači. Všechny operace zálohování a obnovení pomocí účtu služby. **NT Service\AzureWLBackupPluginSvc** potřebuje oprávnění správce systému SQL. Všechny virtuální počítače SQL Marketplace jsou dostupné **SqlIaaSExtension** nainstalované. **AzureBackupWindowsWorkload** používá rozšíření **SQLIaaSExtension** automaticky získat požadované oprávnění. Pokud váš virtuální počítač nemá **SqlIaaSExtension** nainstalovali, se zjistit databáze nezdaří s chybovou zprávou `UserErrorSQLNoSysAdminMembership`. Chcete-li přidat oprávnění správce systému pro zálohování, postupujte podle pokynů v [nastavit oprávnění pro zálohování Azure pro jiné - Tržiště virtuálních počítačů SQL](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms).

    ![Vyberte virtuální počítač a databáze](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-databases"></a>Konfigurace zálohování databází systému SQL Server

Azure Backup poskytuje služby správy pro ochranu databáze SQL serveru a Správa úloh zálohování. Správa a monitorování funkce závisí na trezoru služby Recovery Services.

> [!NOTE]
> V době zálohování databází systému SQL Server může mít pouze jeden záložní řešení. Před použitím této funkce; zakázat další zálohy SQL zálohy v opačném případě bude v konfliktu a selhání. Můžete povolit Azure Backup pro virtuální počítač IaaS spolu s zálohování SQL bez jakéhokoli konfliktu.
>

Konfigurace ochrany pro SQL database:

1. Otevřete trezor služby Recovery Services, který je registrovaný k virtuálnímu počítači SQL.

2. Na **trezor služby Recovery Services** řídicího panelu, vyberte **zálohování**. **Cíl zálohování** otevře se nabídka.

   ![Vybrat zálohu, otevřete nabídku cíl zálohování](./media/backup-azure-sql-database/open-backup-menu.png)

3. Na **cíl zálohování** nabídky, nastavte **ve kterém je spuštěná vaše úloha** na výchozí hodnotu: **Azure**.

4. Rozbalte **co chcete zálohovat** rozevírací seznam a vyberte **systému SQL Server na virtuálním počítači Azure**.

    ![Vyberte pro zálohování serveru SQL Server ve virtuálním počítači Azure](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    **Cíl zálohování** nabídce se zobrazí dva kroky: **Zjistit databáze ve virtuálních počítačích** a **konfiguraci zálohování**.

    Pokud jste dokončili kroky v tomto článku v pořadí, jste se seznámili nechráněné virtuální počítače a tento trezor je zaregistrován s virtuálním počítačem. Nyní jste připraveni nakonfigurovat ochranu pro databáze SQL.

5. Na **cíl zálohování** nabídce vyberte možnost **konfigurace zálohování**.

    ![Vyberte konfiguraci zálohování](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Služba Azure Backup se zobrazí všechny instance systému SQL Server pomocí samostatné databáze a SQL Server vždy na skupiny dostupnosti. Chcete-li zobrazit samostatné databáze v instanci systému SQL Server, vyberte dvojitou šipku nalevo od názvu instance. Podobným způsobem vyberte dvojitou šipku nalevo od skupiny vždy na dostupnosti zobrazíte seznam databází. Na následujícím obrázku je příklad samostatná instance a vždy na dostupnosti skupiny.

      ![Zobrazení všech instancí systému SQL Server u samostatných databází](./media/backup-azure-sql-database/list-of-sql-databases.png)

6. V seznamu databází, vyberte všechny databáze, kterou chcete chránit a klikněte na tlačítko **OK**.

    ![Ochranu databáze](./media/backup-azure-sql-database/select-database-to-protect.png)

    Současně můžete vybrat až 50 databázemi. Chránit víc než 50 databázemi, proveďte několik průchodů. Po prvních 50 databází chráníte, opakujte tento krok k ochraně další sadu databází.

    > [!Note]
    > K optimalizaci zálohování zatížení, Azure Backup rozdělí rozsáhlé úlohy zálohování do několika dávek. Maximální počet databází ve jediná úloha zálohování je 50.
    >

      Alternativně můžete povolit [automatickou ochranu](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) celý instance nebo skupina vždy na dostupnosti tak, že vyberete **ON** možnost v rozevíracím seznamu příslušné v **AUTOPROTECT**  sloupce. [Automatickou ochranu](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) funkci nejen umožňuje ochranu pro všechny existující databáze najednou, ale také automaticky chrání jakýchkoli nových databází, které budou přidány do této instance nebo skupina dostupnosti v budoucnosti.  

      ![Povolit automatické ochrany na skupiny dostupnosti Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

7. K vytvoření nebo výběr zásady zálohování, na **zálohování** nabídce vyberte možnost **zásady zálohování**. **Zásady zálohování** otevře se nabídka.

    ![Vyberte zásady zálohování](./media/backup-azure-sql-database/select-backup-policy.png)

8. V **výběr zásady zálohování** rozevíracího seznamu vyberte zásadu zálohování a vyberte **OK**. Informace o tom, jak vytvořit zásadu zálohování, naleznete v tématu [Definujte zásadu zálohování](backup-azure-sql-database.md#define-a-backup-policy).

   > [!NOTE]
   > Ve verzi Preview nejde upravit zásady zálohování. Pokud chcete jiné zásady než co je k dispozici v seznamu, je nutné vytvořit tyto zásady. Informace o vytvoření nové zásady zálohování, naleznete v části, [Definujte zásadu zálohování](backup-azure-sql-database.md#define-a-backup-policy).

    ![Zásady zálohování vyberte ze seznamu](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    Na **zásady zálohování** nabídky v **výběr zásady zálohování** rozevírací seznam, můžete:
    - Vyberte výchozí zásady: **HourlyLogBackup**.
    - Vyberte existující zásadu zálohování dříve vytvořili pro SQL.
    - [Definovat novou zásadu](backup-azure-sql-database.md#define-a-backup-policy) na základě cíle bodu obnovení a uchovávání rozsahu.

    > [!Note]
    > Azure Backup podporuje dlouhodobé uchování, která je založena na schéma dědečka ze strany otce SYN zálohování. Schéma optimalizuje využití back endové úložiště během schůzky dodržování předpisů.
    >

9. Po výběru zásady zálohování, na **nabídka zálohování**vyberte **povolit zálohování**.

    ![Povolit vybrané zásady zálohování](./media/backup-azure-sql-database/enable-backup-button.png)

    Sledovat průběh na konfiguraci **oznámení** části portálu.

    ![Oznamovací oblasti](./media/backup-azure-sql-database/notifications-area.png)


## <a name="auto-protect-sql-server-in-azure-vm"></a>Automatická ochrana systému SQL Server na virtuálním počítači Azure  

Automatická ochrana umožňuje automaticky chránit všechny existující databáze a databáze, které můžete přidat v budoucnosti do samostatná instance SQL serveru nebo SQL Server vždy na skupinu dostupnosti. Zapnutí **ON** Automatická ochrana a výběr zásady zálohování bude platit pro nově chráněných databází, existující chráněných databází bude nadále používat předchozí zásady.

![Povolit automatické ochrany na skupiny dostupnosti Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

Neexistuje žádné omezení počtu databází, které získáte vybrané najednou pomocí automatické ochrany funkce. Konfigurace zálohování se aktivuje pro všechny databáze společně a lze sledovat v **úlohy zálohování**.

Pokud z nějakého důvodu potřebujete zakázat automatickou ochranu pro instanci, klikněte na název instance v rámci **konfigurace zálohování** otevřete panel informací na pravé straně, který má **zakázat Autoprotect** na horní části. Klikněte na tlačítko **zakázat Autoprotect** zakázat automatickou ochranu pro tuto instanci.

![Zakažte automatickou ochranu pro tuto instanci](./media/backup-azure-sql-database/disable-auto-protection.png)

Všechny databáze v dané instanci bude nadále chránit. Ale tato akce zakáže automatické ochrany na všechny databáze, které se přidají v budoucnu.

### <a name="define-a-backup-policy"></a>Definice zásady zálohování

Zásady zálohování definují pořizování zálohování a jak dlouho se uchovávají. Pomocí Azure Backup můžete naplánovat tři typy zálohování pro SQL databáze:

* Úplné zálohování: Úplná záloha databáze vytvoří zálohu celé databáze. Úplné zálohování obsahuje všechna data v konkrétní databázi nebo sadu skupiny souborů nebo souborů a dostatek protokoly a tato data obnovit. Maximálně můžete aktivovat jednu úplnou zálohu denně. Můžete provést úplné zálohování na denní nebo týdenní interval.
* Rozdílové zálohování: Rozdílové zálohy je založená na nejnovější, předchozí úplná záloha. Rozdílové zálohování zaznamená pouze data, která se změnila od úplného zálohování. Maximálně můžete aktivovat jeden rozdílové zálohy za den. Úplné zálohování a rozdílovou zálohu nelze konfigurovat ve stejný den.
* Zálohy transakčního protokolu: Záloha protokolu umožňuje obnovení bodu v čase až po konkrétní sekundy. Maximálně můžete nakonfigurovat zálohy transakčního protokolu každých 15 minut.

Zásady vytvořené v trezoru služby Recovery Services úrovně. Více trezorů můžete použít stejné zásady zálohování, ale musíte použít zásadu zálohování, která pro každý trezor. Při vytváření zásady zálohování denně úplné zálohování je výchozí hodnota. Rozdílové zálohování, ale pouze můžete přidat, pokud nakonfigurujete týdenních úplných záloh. Následující postup vysvětluje, jak vytvořit zásady zálohování pro instanci systému SQL Server ve virtuálním počítači Azure.

> [!NOTE]
> Ve verzi Preview nejde upravit zásady zálohování. Místo toho musíte vytvořit novou zásadu požadované podrobnosti.  

Vytvoření zásady zálohování:

1. V trezoru služby Recovery Services, která chrání databáze SQL, klikněte na tlačítko **zásady zálohování**a potom klikněte na tlačítko **přidat**.

   ![Otevřete dialogové okno vytvořit nové zásady zálohování](./media/backup-azure-sql-database/new-policy-workflow.png)

   **Přidat** zobrazí se nabídka.

2. V **přidat** nabídky, klikněte na tlačítko **systému SQL Server na virtuálním počítači Azure**.

   ![Vyberte typ zásad pro nové zásady zálohování](./media/backup-azure-sql-database/policy-type-details.png)

   Výběr systému SQL Server na virtuálním počítači Azure definuje typ zásad a otevře se nabídka zásady zálohování. **Zásady zálohování** nabídce se zobrazuje pole, které jsou nezbytné pro nové zásady zálohování serveru SQL Server.

3. V **Název_zásady**, zadejte název pro novou zásadu.

4. Úplné zálohování je povinné; nelze vypnout **úplná záloha** možnost. Klikněte na tlačítko **úplná záloha** k zobrazení a úpravám zásad. I v případě, že neměnit zásady zálohování by měl zobrazit podrobnosti zásady.

    ![Nová pole zásady zálohování](./media/backup-azure-sql-database/full-backup-policy.png)

    V **úplná záloha zásad** nabídce pro **četnost záloh**, zvolte **denní** nebo **týdenní**. Pro **denní**, vyberte hodiny a časové pásmo po zahájení úlohy zálohování. Nelze vytvořit rozdílové zálohování pro denní úplná zálohování.

   ![denní interval nastavení](./media/backup-azure-sql-database/daily-interval.png)

    Pro **týdenní**, vyberte den v týdnu, hodiny a časové pásmo po zahájení úlohy zálohování.

   ![týdenní interval nastavení](./media/backup-azure-sql-database/weekly-interval.png)

5. Ve výchozím nastavení všechny **rozsah uchování** jsou vybrané možnosti: denní, týdenní, měsíční nebo roční. Zrušte výběr žádná omezení rozsahu uchování nežádoucí. Nastavte intervaly používat. V **úplná záloha zásad** nabídce vyberte možnost **OK** potvrďte nastavení.

   ![Nastavení intervalu rozsah uchování](./media/backup-azure-sql-database/retention-range-interval.png)

    Body obnovení jsou označené pro uchovávání informací podle jejich rozsah uchování. Například pokud vyberete každodenní úplnou zálohu, se aktivuje pouze jeden úplné zálohování každý den. Zálohování pro určitý den je příznakem a uchovávají na základě týdenní rozsah uchování a nastavení týdenního uchovávání informací. Měsíční a roční rozsahy uchovávání se chovají podobně.

6. Chcete-li přidat zásadu pro rozdílové zálohování, **rozdílové zálohování**. **Rozdílovou zálohu zásad** otevře se nabídka.

   ![Otevřete nabídku zásady rozdílové zálohování](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    Na **rozdílovou zálohu zásad** nabídce vyberte možnost **povolit** otevřete ovládací prvky frekvence a uchování. Maximálně můžete aktivovat jeden rozdílové zálohy za den.

    > [!Important]
    > Rozdílové zálohy je možné uchovávat maximálně na 180 dnů. Pokud budete potřebovat delší dobu uchování, musíte použít úplné zálohy.
    >

   ![Upravit zásady rozdílové zálohování](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    Vyberte **OK** zásadu uložte a vraťte se do hlavní **zásady zálohování** nabídky.

7. Chcete-li přidat zásadu zálohování transakčního protokolu, **zálohování protokolu**. **Záloha protokolu** otevře se nabídka.

    V **záloha protokolu** nabídce vyberte možnost **povolit**a potom nastavte ovládací prvky frekvence a uchování. Zálohy protokolů může dojít, tak často, jak každých 15 minut a můžou uchovávat až 35 dnů. Vyberte **OK** zásadu uložte a vraťte se do hlavní **zásady zálohování** nabídky.

   ![Upravit zásady zálohování protokolu](./media/backup-azure-sql-database/log-backup-policy-editor.png)

8. Na **zásady zálohování** nabídce vyberte, jestli chcete povolit **kompresi zálohování SQL**. Komprese je ve výchozím nastavení zakázána.

    Na back-endu Azure Backup použije nativní kompresi záloh SQL.

9. Po dokončení úprav zásady zálohování vyberte **OK**.

   ![Přijmout nové zásady zálohování](./media/backup-azure-sql-database/backup-policy-click-ok.png)

## <a name="restore-a-sql-database"></a>Obnovení databáze SQL
Azure Backup poskytuje funkce pro obnovení jednotlivých databází na konkrétní datum nebo čas (sekundy) pomocí zálohování transakčního protokolu. Azure Backup automaticky určuje odpovídající úplné rozdílového a řetězec zálohy protokolu, které jsou nutné k obnovení dat podle vašich časů obnovení.

Můžete také vybrat konkrétní úplnou nebo rozdílovou zálohu k obnovení pro konkrétní bod obnovení, spíše než určený čas.

### <a name="pre-requisite-before-triggering-a-restore"></a>Předpoklad před aktivací obnovení

1. Databázi můžete obnovit do instance systému SQL Server ve stejné oblasti Azure. Cílový server musí být zaregistrovaná ke stejnému trezoru služby Recovery Services jako zdroj.  
2. Obnovení databáze šifrovaná transparentní šifrování dat na jiný Server SQL, nejdřív Obnovte prosím certifikát na cílový server pomocí následujících kroků popsaných [tady](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
3. Předtím, než aktivujete obnovení databázi "master", spusťte instanci systému SQL Server v režimu jednoho uživatele s možností spuštění `-m AzureWorkloadBackup`. Argument `-m` možnost je název klienta. K otevření připojení je povolen pouze tento klient. Pro všechny systémové databáze (model, hlavní, msdb) zastavte službu Agent serveru SQL před aktivaci obnovení. Zavřete všechny aplikace, které může pokusit o krádež připojení ke kterékoli z těchto databází.

### <a name="steps-to-restore-a-database"></a>Postup obnovení databáze:

1. Otevřete trezor služby Recovery Services, který je registrovaný k virtuálnímu počítači SQL.

2. Na **trezor služby Recovery Services** řídicího panelu v části **využití**vyberte **zálohování položek** otevřít **zálohování položek** nabídky.

    ![Otevřete nabídku zálohování položek](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. Na **zálohování položek** nabídky v části **typu správy zálohování**vyberte **SQL na virtuálním počítači Azure**.

    ![Vyberte SQL na virtuálním počítači Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    **Zálohování položek** nabídce se zobrazuje seznam databází SQL.

4. V seznamu databází SQL vyberte databáze, kterou chcete obnovit.

    ![Vyberte databáze, kterou chcete obnovit](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Když vyberete databázi, otevře se jeho nabídku. Nabídka poskytuje podrobnosti zálohování pro databázi, včetně:

    * Nejstarší a nejnovější body obnovení.
    * Stav zálohování protokolů za posledních 24 hodin (pro databáze v úplný a hromadně protokolovaného obnovení modelu, pokud je nakonfigurovaná pro zálohování transakčních protokolů).

5. V nabídce ke zvolené databázi, vyberte **obnovení DB**. **Obnovení** otevře se nabídka.

    ![Vyberte obnovit databázi](./media/backup-azure-sql-database/restore-db-button.png)

    Když **obnovení** otevře se nabídka, **obnovit konfiguraci** také otevře se nabídka. **Obnovit konfiguraci** nabídky je prvním krokem k obnovení konfigurace. Určete, kam mají být data obnovena pomocí této nabídky. Dostupné možnosti:
    - **Alternativní umístění**: Obnovení databáze do alternativního umístění a zachovat původní zdrojové databáze.
    - **Přepsat databázi**: Obnovení dat na stejnou instanci systému SQL Server jako původní zdroj. Účinek této možnosti je přepisovat původní databázi.

    > [!Important]
    > Pokud vybraná databáze patří do skupiny vždy na dostupnost, SQL Server nepovoluje databáze, kterou chcete přepsat. V takovém případě pouze **alternativního umístění** je povolená možnost.
    >

    ![Obnovení nabídky konfigurace](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Obnovení do alternativního umístění

Tento postup vás provede obnovení dat do alternativního umístění. Chcete-li přepsat databázi během obnovení, nadále [obnovení a přepsat databázi](backup-azure-sql-database.md#restore-and-overwrite-the-database). V této fázi je otevřený trezoru služby Recovery Services a **obnovit konfiguraci** nabídka je viditelná. Pokud nejste v této fázi, začněte tím, že [obnovení databáze SQL](backup-azure-sql-database.md#restore-a-sql-database).

> [!NOTE]
> Databázi můžete obnovit do instance systému SQL Server ve stejné oblasti Azure. Cílový server musí být zaregistrované do trezoru služby Recovery Services.
>

Na **obnovit konfiguraci** nabídky, **Server** rozevíracím seznamu zobrazí pouze instance systému SQL Server, které jsou zaregistrované do trezoru služby Recovery Services. Pokud server, který chcete, aby se v seznamu, přečtěte si téma [databáze SQL serveru zjistit](backup-azure-sql-database.md#discover-sql-server-databases) najít server. Během procesu zjišťování nových serverů zaregistrováni do trezoru služby Recovery Services.

1. V **obnovit konfiguraci** nabídky:

    * V části **kam obnovit**vyberte **alternativního umístění**.
    * Otevřít **Server** rozevíracího seznamu a vyberte instanci SQL serveru k obnovení databáze.
    * Otevřít **Instance** rozevíracího seznamu a vyberte instanci systému SQL Server.
    * V **název databáze obnovit** zadejte název cílové databáze.
    * Případně, vyberte **přepsat, pokud ve vybrané instanci SQL existuje databáze se stejným názvem**.
    * Vyberte **OK** k dokončení konfigurace cílové a pokračujte výběrem bodu obnovení.

    ![Zadejte hodnoty pro konfiguraci obnovení nabídky](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. Na **vyberte bod obnovení** nabídce zvolte **protokoly (časový okamžik)** nebo **úplné a rozdílové** jako bod obnovení. Pokud chcete obnovit do konkrétního bodu v čase protokolu, pokračujte s tímto krokem. Obnovení bodu obnovení úplné a rozdílové, pokračujte krokem 3.

    ![Vyberte nabídku se bod obnovení](./media/backup-azure-sql-database/recovery-point-menu.png)

    Obnovení bodu v čase je k dispozici pouze pro zálohování protokolů pro databáze s modelem úplný a hromadně protokolovaného obnovení. Obnovení k určitému bodu v čase:

    1. Vyberte **protokoly (časový okamžik)** jako typ obnovení.

        ![Zvolte typ bodu obnovení](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. V části **obnovit data a času**, vyberte mini kalendář, který chcete otevřít **kalendáře**. Na **kalendáře**tučně označená data mají body obnovení a je zvýrazněn aktuální datum. Vyberte datum se body obnovení. Data bez body obnovení nelze vybrat.

        ![Otevřít kalendář](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Vyberte datum a časové osy grafu zobrazuje dostupné body obnovení v průběžné rozsahu.

    3. Pomocí časové osy grafu nebo **čas** dialogové okno k zadání určitý čas bodu obnovení. Vyberte **OK** k dokončení kroku bodu obnovení.

       ![Otevřít kalendář](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        **Vyberte bod obnovení** zavře nabídky a **Upřesnit konfiguraci** otevře se nabídka.

       ![Nabídka pokročilou konfiguraci](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Na **Upřesnit konfiguraci** nabídky:

        * Chcete-li zachovat databázi nefunkční po obnovení, nastavte **obnovit s NORECOVERY** k **povoleno**.
        * Chcete-li změnit umístění pro obnovení na cílovém serveru, zadejte novou cestu v **cílové** sloupce.
        * Vyberte **OK** schválit nastavení. Zavřít **Upřesnit konfiguraci** nabídky.

    5. Na **obnovení** nabídce vyberte možnost **obnovení** spustit úlohu obnovení. Sledovat průběh obnovení v **oznámení** oblasti nebo vyberte **obnovení úlohy** v nabídce databáze.

       ![Průběh úlohy obnovení](./media/backup-azure-sql-database/restore-job-notification.png)

3. Na **vyberte bod obnovení** nabídce zvolte **protokoly (časový okamžik)** nebo **úplné a rozdílové** jako bod obnovení. Obnovení bodu v čase protokolu, vraťte se ke kroku 2. Tento krok obnoví bod konkrétní úplnou nebo rozdílovou obnovení. Zobrazí se všechny body obnovení úplné a rozdílové za posledních 30 dní. Pokud chcete zobrazit body obnovení starší než 30 dní, vyberte **filtr** otevřít **body obnovení filtr** nabídky. Pro bod obnovení rozdílové zálohování Azure nejprve obnoví odpovídající úplný bod obnovení a poté použije vybraný rozdílové bod obnovení.

    ![Vyberte nabídku se bod obnovení](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. V **vyberte bod obnovení** nabídku, vyberte **úplné a rozdílové**.

       ![Vyberte úplné a rozdílové](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        V nabídce se zobrazuje seznam dostupných bodů obnovení.

    2. Vyberte bod obnovení ze seznamu a vyberte **OK** postup bodu obnovení.

        ![Vyberte úplný bod obnovení](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        **Bod obnovení** zavře nabídky a **Upřesnit konfiguraci** otevře se nabídka.

        ![Nabídka Upřesnit konfiguraci](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Na **Upřesnit konfiguraci** nabídky:

        * Chcete-li zachovat databázi nefunkční po obnovení, nastavte **obnovit s NORECOVERY** k **povoleno**. **Obnovit s NORECOVERY** je ve výchozím nastavení zakázané.
        * Chcete-li změnit umístění pro obnovení na cílovém serveru, zadejte novou cestu v **cílové** sloupce.
        * Vyberte **OK** schválit nastavení. Zavřít **Upřesnit konfiguraci** nabídky.

    4. Na **obnovení** nabídce vyberte možnost **obnovení** spustit úlohu obnovení. Sledovat průběh obnovení v **oznámení** oblasti nebo vyberte **obnovení úlohy** v nabídce databáze.

       ![Průběh úlohy obnovení](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>Obnovení a přepsat databázi

Tento postup vás provede obnovení dat a přepsat databázi. Chcete-li obnovit do alternativního umístění, nadále [obnovení do alternativního umístění](backup-azure-sql-database.md#restore-to-an-alternate-location). V této fázi je otevřený trezoru služby Recovery Services a **obnovit konfiguraci** nabídka je viditelná (viz následující obrázek). Pokud nejste v této fázi, začněte tím, že [obnovení databáze SQL](backup-azure-sql-database.md#restore-a-sql-database).

![Obnovení nabídky konfigurace](./media/backup-azure-sql-database/restore-overwrite-db.png)

Na **obnovit konfiguraci** nabídky, **Server** rozevíracím seznamu zobrazí pouze instance systému SQL Server, které jsou zaregistrované do trezoru služby Recovery Services. Pokud server, který chcete, aby se v seznamu, přečtěte si téma [databáze SQL serveru zjistit](backup-azure-sql-database.md#discover-sql-server-databases) najít server. Během procesu zjišťování nových serverů zaregistrováni do trezoru služby Recovery Services.

1. V **obnovit konfiguraci** nabídce vyberte možnost **přepsat DB**a pak vyberte **OK** k dokončení konfigurace cíle.

   ![Vyberte možnost přepsat databázi](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    **Server**, **Instance**, a **název databáze obnovit** nastavení nejsou potřebné.

2. Na **vyberte bod obnovení** nabídce zvolte **protokoly (časový okamžik)** nebo **úplné a rozdílové** jako bod obnovení. Pokud chcete obnovit do konkrétního bodu v čase protokolu, pokračujte s tímto krokem. Obnovení bodu obnovení úplné a rozdílové, pokračujte krokem 3.

    ![Vyberte nabídku se bod obnovení](./media/backup-azure-sql-database/recovery-point-menu.png)

    Obnovení bodu v čase je k dispozici pouze pro zálohování protokolů pro databáze s modelem úplný a hromadně protokolovaného obnovení. Chcete-li obnovit konkrétní sekundy:

    1. Vyberte **protokoly (časový okamžik)** jako bod obnovení.

        ![Zvolte typ bodu obnovení](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. V části **obnovit data a času**, vyberte mini kalendář, který chcete otevřít **kalendáře**. Na **kalendáře**tučně označená data mají body obnovení a je zvýrazněn aktuální datum. Vyberte datum se body obnovení. Data bez body obnovení nelze vybrat.

        ![Otevřít kalendář](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Vyberte datum a časové osy grafu zobrazí dostupné body obnovení.

    3. Pomocí časové osy grafu nebo **čas** dialogové okno k zadání určitý čas bodu obnovení. Vyberte **OK** k dokončení kroku bodu obnovení.

       ![Otevřít kalendář](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        **Vyberte bod obnovení** zavře nabídky a **Upřesnit konfiguraci** otevře se nabídka.

       ![Nabídka pokročilou konfiguraci](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Na **Upřesnit konfiguraci** nabídky:

        * Chcete-li zachovat databázi nefunkční po obnovení, nastavte **obnovit s NORECOVERY** k **povoleno**.
        * Chcete-li změnit umístění pro obnovení na cílovém serveru, zadejte novou cestu v **cílové** sloupce.
        * Vyberte **OK** schválit nastavení. Zavřít **Upřesnit konfiguraci** nabídky.

    5. Na **obnovení** nabídce vyberte možnost **obnovení** spustit úlohu obnovení. Sledovat průběh obnovení v **oznámení** oblasti nebo vyberte **obnovení úlohy** v nabídce databáze.

       ![Průběh úlohy obnovení](./media/backup-azure-sql-database/restore-job-notification.png)

3. Na **vyberte bod obnovení** nabídce zvolte **protokoly (časový okamžik)** nebo **úplné a rozdílové** jako bod obnovení. Obnovení bodu v čase protokolu, vraťte se ke kroku 2. Tento krok obnoví bod konkrétní úplnou nebo rozdílovou obnovení. Zobrazí se všechny body obnovení úplné a rozdílové za posledních 30 dní. Pokud chcete zobrazit body obnovení starší než 30 dní, vyberte **filtr** otevřít **body obnovení filtr** nabídky. Pro bod obnovení rozdílové zálohování Azure nejprve obnoví odpovídající úplný bod obnovení a poté použije vybraný rozdílové bod obnovení.

    ![Vyberte nabídku se bod obnovení](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. V **vyberte bod obnovení** nabídku, vyberte **úplné a rozdílové**.

       ![Vyberte úplné a rozdílové](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        V nabídce se zobrazuje seznam dostupných bodů obnovení.

    2. Vyberte bod obnovení ze seznamu a vyberte **OK** postup bodu obnovení.

        ![Vyberte úplný bod obnovení](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        **Bod obnovení** zavře nabídky a **Upřesnit konfiguraci** otevře se nabídka.

        ![Nabídka Upřesnit konfiguraci](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Na **Upřesnit konfiguraci** nabídky:

        * Chcete-li zachovat databázi nefunkční po obnovení, nastavte **obnovit s NORECOVERY** k **povoleno**. **Obnovit s NORECOVERY** je ve výchozím nastavení zakázané.
        * Chcete-li změnit umístění pro obnovení na cílovém serveru, zadejte novou cestu v **cílové** sloupce.
        * Vyberte **OK** schválit nastavení. Zavřít **Upřesnit konfiguraci** nabídky.

    4. Na **obnovení** nabídce vyberte možnost **obnovení** spustit úlohu obnovení. Sledovat průběh obnovení v **oznámení** oblasti nebo tak, že vyberete **obnovení úlohy** v nabídce databáze.

       ![Průběh úlohy obnovení](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Správa operací zálohování Azure pro SQL na virtuálních počítačích Azure

Tato část obsahuje informace o různých operacích správy Azure Backup, které jsou k dispozici pro SQL na virtuálních počítačích Azure. Existují následující základní operace:

* Monitorování úloh
* Výstrahy záloh
* Zastavili ochranu databáze SQL
* Pokračovat v ochraně databáze SQL
* Spustit úlohu zálohování ad hoc
* Zrušení registrace serveru se systémem SQL Server

### <a name="monitor-backup-jobs"></a>Monitorování úlohy zálohování
Azure Backup je podnikové řešení třída, která poskytuje pokročilé zálohování výstrahy a oznámení pro všechny chyby. (Viz [zobrazení výstrahy zálohování](backup-azure-sql-database.md#view-backup-alerts).) Sledování konkrétních úloh, použijte některý z následujících možností podle vašich požadavků.

#### <a name="use-the-azure-portal-for-adhoc-operations"></a>Pomocí webu Azure portal pro ad hoc operace
Azure Backup se zobrazí všechny ručně aktivované nebo ad hoc, úlohy v **úlohy zálohování** portálu. Úlohy, které jsou k dispozici v **úlohy zálohování** portálu patří:
- Nakonfigurujte všechny operace zálohování.
- Ručně aktivované operace zálohování.
- Operace obnovení.
- Registrace a zjišťování databázových operací.
- Zastavte zálohování.

![Úlohy zálohování portálu](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Všechny naplánované úlohy zálohování, včetně úplného, rozdílového a záloha protokolu, se nezobrazují v **úlohy zálohování** portálu. Pomocí SQL Server Management Studio ke sledování naplánovaných úlohách zálohování, jak je popsáno v další části.
>

#### <a name="use-sql-server-management-studio-for-backup-jobs"></a>Pomocí SQL Server Management Studio pro úlohy zálohování
Azure Backup používá nativní rozhraní API SQL pro všechny operace zálohování. Pomocí nativních rozhraní API k načtení všech informací o úlohu z [tabulku záloh SQL](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) v databázi msdb.

V následujícím příkladu je dotaz, který načte všechny úlohy zálohování pro databázi s názvem **DB1**. Upravte dotaz pro rozšířené monitorování.

```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  

```

### <a name="view-backup-alerts"></a>Zobrazit výstrahy zálohování

Vzhledem k tomu dojít k zálohování protokolu každých 15 minut, v některých případech monitorování úlohy zálohování může být zdlouhavé. Azure Backup poskytuje nápovědu v této situaci. E-mailová upozornění se aktivuje všechny případné selhání zálohování. Upozornění konsolidované podle kódu chyby na úrovni databáze. Jenom pro první selhání zálohování pro databázi přijde e-mailové upozornění. Přihlaste se k webu Azure portal k monitorování všech chyb pro databázi.

Chcete-li monitorovat výstrahy zálohování:

1. Přihlaste se ke svému předplatnému Azure v [webu Azure portal](https://portal.azure.com).

2. Otevřete trezor služby Recovery Services, který je registrovaný k virtuálnímu počítači SQL.

3. Na **trezor služby Recovery Services** řídicího panelu, vyberte **výstrahy a události**.

   ![Vyberte výstrahy a události](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. Na **výstrahy a události** nabídce vyberte možnost **výstrahy zálohování** zobrazíte seznam výstrah.

   ![Vyberte výstrahy zálohování](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-for-a-sql-server-database"></a>Zastavte ochranu pro databázi serveru SQL Server

Pokud zastavíte ochranu pro databázi serveru SQL Server, požádá o Azure Backup, jestli se má zachovat body obnovení. Existují dva způsoby, jak zastavit ochranu pro službu SQL database:

* Zastavit všechny budoucí úlohy zálohování a odstranit všechny body obnovení.
* Zastavit všechny budoucí úlohy zálohování, ale ponechat body obnovení.

Pokud se rozhodnete zastavit zálohování při zachování dat, body obnovení se vyčistí podle zásady zálohování. Bude účtovat za chráněnou instanci SQL, ceny za poplatek, a navíc úložiště spotřebovaného před vyčištěním všech bodů obnovení. Další informace o cenách zálohování Azure pro SQL, najdete v článku [stránce s cenami za Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

Ukončit ochranu pro databázi:

1. Otevřete trezor služby Recovery Services, který je registrovaný k virtuálnímu počítači SQL.

2. Na **trezor služby Recovery Services** řídicího panelu v části **využití**vyberte **zálohování položek** otevřít **zálohování položek** nabídky.

    ![Otevřete nabídku zálohování položek](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. Na **zálohování položek** nabídky v části **typu správy zálohování**vyberte **SQL na virtuálním počítači Azure**.

    ![Vyberte SQL na virtuálním počítači Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    **Zálohování položek** nabídce se zobrazuje seznam databází SQL.

4. V seznamu databází SQL vyberte databázi pro zastavení ochrany.

    ![Vyberte databáze, kterou chcete zastavit ochranu](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Když vyberete databázi, otevře se jeho nabídku.

5. V nabídce ke zvolené databázi, vyberte **Zastavit zálohování**.

    ![Vyberte Zastavit zálohování](./media/backup-azure-sql-database/stop-db-button.png)

    **Zastavit zálohování** otevře se nabídka.

6. Na **Zastavit zálohování** nabídce zvolit **zachovat zálohovaná Data** nebo **odstranit zálohovaná Data**. Volitelně zadejte důvod pro zastavení ochrany a komentář.

    ![Zastavit zálohování nabídky](./media/backup-azure-sql-database/stop-backup-button.png)

7. Vyberte **Zastavit zálohování** ukončit ochranu databáze.

  Pamatujte, že **Zastavit zálohování** možnost nebude fungovat pro databázi v [automatickou ochranu](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) instance. Jediný způsob, jak zastavit ochranu této databáze je zakázat [automatickou ochranu](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) na instanci prozatím a klikněte na tlačítko **Zastavit zálohování** v části **zálohování položek**pro tuto databázi.<br>
  Po zakázání automatické ochrany můžete **Zastavit zálohování** pro databáze v rámci **zálohování položek**. Instance můžete znovu povolit pro automatickou ochranu nyní.


### <a name="resume-protection-for-a-sql-database"></a>Pokračovat v ochraně databáze SQL

Pokud **zachovat zálohovaná Data** byla vybrána možnost, pokud ochranu databáze SQL se zastavila, můžete obnovit ochranu. Pokud se data záloh se uchovávají, nelze obnovit ochranu.

1. Chcete-li obnovit ochranu pro službu SQL database, otevřete zálohovaná položka a vyberte **obnovit zálohu**.

    ![Vyberte obnovit zálohu k obnovení ochrany databáze](./media/backup-azure-sql-database/resume-backup-button.png)

   **Zásady zálohování** otevře se nabídka.

2. Na **zásady zálohování** nabídku, vyberte zásadu a pak vyberte **Uložit**.

### <a name="trigger-an-adhoc-backup"></a>Aktivační událost zálohování ad hoc

Aktivovat zálohování ad hoc podle potřeby. Existují čtyři typy zálohování ad hoc:

* Úplné zálohování
* Úplné zálohy
* Rozdílové zálohování
* Zálohování protokolů

Podrobnosti o jednotlivých typů naleznete v tématu [záloh typy SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups).

### <a name="unregister-a-sql-server-instance"></a>Zrušit registraci instance serveru SQL Server

Po odebrání ochrany, ale před odstranění trezoru, zrušit registraci instance systému SQL Server:

1. Otevřete trezor služby Recovery Services, který je registrovaný k virtuálnímu počítači SQL.

2. Na **trezor služby Recovery Services** řídicího panelu v části **spravovat**vyberte **infrastruktura zálohování**.  

   ![Vyberte infrastruktury zálohování](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. V části **servery pro správu**vyberte **chráněné servery**.

   ![Vyberte chráněné servery](./media/backup-azure-sql-database/protected-servers.png)

    **Chráněné servery** otevře se nabídka.

4. Na **chráněné servery** nabídku, vyberte server ke zrušení registrace. Odstranit trezor, musíte zrušit registraci všech serverů.

5. Na **chráněné servery** nabídku, klikněte pravým tlačítkem na chráněném serveru a vyberte **odstranit**.

   ![Vyberte odstranit](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="faq"></a>Nejčastější dotazy

Následující část obsahuje další informace o zálohování databáze SQL.

### <a name="can-i-throttle-the-speed-of-the-sql-server-backup-policy"></a>Je možné omezovat rychlost zásady zálohování serveru SQL Server?

Ano. Můžete omezit rychlost, jakou zásadu zálohování, která spustí k minimalizaci vlivu na instanci systému SQL Server.
Chcete-li změnit nastavení:
1. V instanci SQL serveru v *složky C:\Program Files\Azure úlohy Backup\bin*, vytvořte **ExtensionSettingsOverrides.json** souboru.
2. V **ExtensionSettingsOverrides.json** změňte **DefaultBackupTasksThreshold** nastavení na nižší hodnotu (například 5) <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. Uložte provedené změny. Zavřete soubor.
4. Na instanci serveru SQL Server otevřete **Správce úloh**. Restartujte **AzureWLBackupCoordinatorSvc** služby.

### <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Ze sekundární repliky, spusťte úplné zálohování?
Ne. Tato funkce není podporována.

### <a name="do-successful-backup-jobs-create-alerts"></a>Úspěšné úlohy zálohování vytvářejí výstrahy?

Ne. Úspěšné úlohy zálohování není Generovat výstrahy. Oznámení se odešlou jenom pro úlohy zálohování, které selžou.

### <a name="can-i-see-details-for-scheduled-backup-jobs-in-the-jobs-menu"></a>Můžete zobrazit podrobnosti pro naplánované úlohy zálohování v nabídce úlohy?

Ne. **Úlohy zálohování** nabídky zobrazí podrobnosti úlohy ad hoc, ale ne naplánovaných úloh zálohování. Pokud selžou i všechny naplánované úlohy zálohování, podrobnosti jsou dostupné ve výstrahách neúspěšnou úlohu. Můžete sledovat všechny naplánované a pomocí úlohy zálohování ad hoc [SQL Server Management Studio](backup-azure-sql-database.md#use-sql-server-management-studio-for-backup-jobs).

### <a name="when-i-select-a-sql-server-instance-are-future-databases-automatically-added"></a>Když vyberu instanci systému SQL Server se automaticky přidá budoucí databáze?

Ne. Při konfiguraci ochrany pro instanci systému SQL Server, pokud vyberete možnost úrovni serveru, se přidají všechny databáze. Pokud chcete přidat databáze na instanci systému SQL Server po dokončení konfigurace ochrany, je třeba ručně přidat nové databáze před nimi chránit. Databáze nejsou automaticky součástí nakonfigurovanou ochranu.

### <a name="if-i-change-the-recovery-model-how-do-i-restart-protection"></a>Jak se můžu změnit model obnovení,-li restartovat ochrany?

Aktivujte úplné zálohování. Protokol zálohování začne podle očekávání.

### <a name="can-i-protect-sql-always-on-availability-groups-where-the-primary-replica-is-on-premises"></a>Může chránit SQL skupin dostupnosti Always On se primární repliky v místním prostředí?

Ne. Azure Backup chrání servery SQL v Azure. Pokud se skupina dostupnosti (AG) se pak rozdělí mezi Azure a v místním počítači, skupinu dostupnosti se dají chránit jenom v případě, že primární replikou je spuštěná v Azure. Kromě toho Azure Backup chrání pouze uzly spuštěnými ve stejné oblasti Azure jako trezor služby Recovery Services.

### <a name="can-i-protect-sql-always-on-availability-groups-which-are-spread-across-azure-regions"></a>Může chránit SQL skupin dostupnosti Always On, které jsou rozděleny mezi oblastmi Azure?

Azure Backup trezoru Recovery Services můžete zjišťovat a chránit všechny uzly, které jsou ve stejné oblasti jako trezor služby Recovery Services. Pokud máte vždy ve skupině dostupnosti SQL pokrývající víc oblastí Azure, musíte nakonfigurovat zálohování z oblasti, která má primárního uzlu. Azure Backup bude moct zjišťuje a chrání všechny databáze ve skupině dostupnosti podle preference zálohování. Pokud není splněná předvolby zálohování, zálohování se nezdaří a zobrazí se výstrahy týkající se selhání.

### <a name="while-i-want-to-protect-most-of-the-databases-in-an-instance-i-would-like-to-exclude-a-few-is-it-possible-to-still-use-the-auto-protection-feature"></a>Když chci chránit většinu databáze v instanci, chci vyloučit některé. Je možné dál používat funkci automatické ochrany?

Ne, [automatickou ochranu](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) platí pro celou instanci. Nemůžete chránit selektivně databází pomocí automatické ochrany instancí.

### <a name="can-i-have-different-policies-for-different-databases-in-an-auto-protected-instance"></a>Může mít různé zásady pro různé databáze v instanci automaticky chráněná?

Pokud už máte některé chráněné databáze v instanci, budou i nadále se dají chránit pomocí jejich příslušných zásad i po zapnutí **ON** [automatickou ochranu](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) možnost. Nicméně nechráněných databází spolu s těch, které by v budoucnu přidat budou mít pouze jednu zásadu, která je definovat v rámci **konfigurace zálohování** po databáze dwrepository. Na rozdíl od jiných chráněných databází, nelze ve skutečnosti, dokonce i změnit zásady pro databáze v rámci automaticky chráněná instance.
Pokud chcete udělat, je jediným způsobem zakázat automatickou ochranu pro instanci prozatím a potom změňte zásady pro tuto databázi. Teď můžete znovu povolit automatickou ochranu pro tuto instanci.

### <a name="if-i-delete-a-database-from-an-auto-protected-instance-will-the-backups-for-that-database-also-stop"></a>Když odstraním databázi z instance automaticky chráněny, bude zálohování této databáze také zastavit?

Ne, pokud z automaticky chráněná instance je vyřadit databázi, zálohy pro tuto databázi jsou stále k pokusu o. Z toho vyplývá, že se zobrazí jako není v pořádku, v části začíná odstraněnou databázi **zálohování položek** a je stále považovány za chráněné.

Jediný způsob, jak zastavit ochranu této databáze je zakázat [automatickou ochranu](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) na instanci prozatím a klikněte na tlačítko **Zastavit zálohování** v části **zálohování položek**pro tuto databázi. Teď můžete znovu povolit automatickou ochranu pro tuto instanci.

###  <a name="why-cant-i-see-the-newly-added-database-to-an-auto-protected-instance-under-the-protected-items"></a>Proč nelze zobrazit nově přidaná databáze na instanci automaticky chráněná v rámci chráněné položky?

Nemusíte vidět nově přidaná databáze do [automaticky chráněny](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) okamžitě chráněné instance. Je to proto, že zjišťování spuštěno obvykle každých 8 hodin. Uživatel však můžete spustit ruční zjišťování pomocí **obnovení databází** možnost pro zjišťování a ochranu nové databáze hned, jak je znázorněno obrázku níže:

  ![Zobrazí nově přidaná databáze](./media/backup-azure-sql-database/view-newly-added-database.png)


## <a name="next-steps"></a>Další postup

Další informace o Azure Backup, najdete v ukázce Azure Powershellu pro zálohování šifrovaných virtuálních počítačů.

> [!div class="nextstepaction"]
> [Zálohování šifrovaného virtuálního počítače](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
