---
title: Zálohování databází SQL serveru do Azure | Dokumentace Microsoftu
description: Tento kurz vysvětluje, jak k zálohování SQL serveru do Azure. Tento článek také popisuje obnovení SQL serveru.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: b08d4e55deac4c9dd6a11fc9a278bdcfaa739627
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224307"
---
# <a name="back-up-sql-server-databases-on-azure-vms"></a>Zálohování databází SQL Serveru na virtuálních počítačích Azure 

Databáze systému SQL Server jsou důležité úlohy, které vyžadují plánovaného bodu s nízkou obnovení (RPO) a dlouhodobé uchovávání. Můžete zálohovat databáze SQL serveru, na kterých běží na virtuálních počítačích Azure s použitím [Azure Backup](backup-overview.md). 

V tomto kurzu se dozvíte, jak zálohovat databázi systému SQL Server, na kterém běží na Virtuálním počítači Azure do trezoru služby Azure Backup Recovery Services. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Ověřte požadavky pro zálohování do instance systému SQL Server.
> * Vytvořte a nakonfigurujte trezor.
> * Najít databáze a nastavení zálohování.
> * Nastavte si Automatická ochrana databáze.

> [!NOTE]
> Tato funkce je aktuálně ve verzi public preview.

## <a name="before-you-start"></a>Než začnete


- Ujistěte se, že máte instanci systému SQL Server běžící v Azure. Můžete rychle [vytvořit instanci systému SQL Server](../sql-database/sql-database-get-started-portal.md) na webu Azure Marketplace.
- Přečtěte si omezení ve verzi public preview.
- Zkontrolujte podporu scénář.
- [Projděte si nejčastější dotazy](faq-backup-sql-server.md) o tomto scénáři.

## <a name="preview-limitations"></a>Omezení verze Preview

Tato veřejná Předběžná verze má několik omezení.

- Virtuální počítač, na kterém běží SQL Server vyžaduje připojení k Internetu pro přístup k veřejným IP adresám Azure. 
- Můžete zálohovat 2 000 databází systému SQL Server v trezoru. Pokud máte více, vytvořte nový trezor. 
- Zálohy [distribuované skupiny dostupnosti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017) plně nefungují.
- Vždy na převzetí služeb při selhání instance clusteru SQL Server (instancích Fci) nejsou podporovány pro zálohování.
- Konfigurace zálohování SQL serveru na portálu. Momentálně nelze nakonfigurovat zálohování pomocí Azure Powershellu, rozhraní příkazového řádku nebo rozhraní REST API.
- Operace zálohování a obnovení pro FCI zrcadlení databáze, snímky databází a databází nejsou podporovány.
- Databáze s velkým množstvím souborů nelze chránit. Maximální počet souborů, které jsou podporovány není deterministický. Závisí na počtu souborů a také závisí na délka cesty k souborům. 

Kontrola [– nejčastější dotazy](faq-backup-sql-server.md) o zálohování databází systému SQL Server.
## <a name="scenario-support"></a>Scénáře podpory

**Podpora** | **Podrobnosti**
--- | ---
**Podporované nasazení** | Virtuální počítače Azure Marketplace a mimo Marketplace (ručně nainstalovat SQL Server) se podporují virtuální počítače.
**Podporované zeměpisných oblastech** | Austrálie – jihovýchod (ASE); Brazílie – Jih (BRS); Kanada – střed (CNC); Kanada – východ (CE); Střed USA (CUS); Východní Asie (EA); Východní Austrálie (AE); USA – východ (EUS); USA – východ 2 (EUS2); Indie – střed (INC); Indie – Jih (INS); Japonsko – východ (JPE); Japonsko – západ (JPW); Korea – střed (KRC); Korea – Jih (KRS); Střed USA – sever (NCUS); Severní Evropa (NE); Střed USA – Jih (SCUS); Jihovýchodní Asie (SEA); Velká Británie – Jih (UKS); Velká Británie – západ (UKW); Střed USA – západ (WCUS); Západní Evropa (WE); USA – západ (WUS); Západní USA 2 (WUS 2)
**Podporované operační systémy** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> Linux se momentálně nepodporuje.
**Podporované verze systému SQL Server** | SQL Server 2017, SQL Server 2016, SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.

## <a name="prerequisites"></a>Požadavky

Zálohujte databázi SQL serveru, zkontrolujte následující podmínky:

- Identifikovat nebo [vytvořte trezor služby Recovery Services](backup-azure-sql-database.md#create-a-recovery-services-vault) ve stejné oblasti nebo národní prostředí jako virtuální počítač, který je hostitelem instance serveru SQL Server.
- [Zkontrolujte oprávnění VM](#fix-sql-sysadmin-permissions) , které jsou potřeba k zálohování databází SQL.
- Ověřte, zda má virtuální počítač [připojení k síti](backup-azure-sql-database.md#establish-network-connectivity).
- Zkontrolujte, zda databáze systému SQL Server jsou pojmenovány v souladu s maticí [pokyny pro pojmenování pro službu Azure Backup](backup-azure-sql-database.md).
- Ověřte, že není nutné záložní řešení pro databázi povoleno. Zakažte další zálohy systému SQL Server před instalací tohoto scénáře. Azure Backup můžete povolit pro virtuální počítač Azure, společně s Azure Backup pro databáze SQL serveru, na kterém běží na virtuálním počítači bez jakéhokoli konfliktu.

### <a name="establish-network-connectivity"></a>Vytvoření připojení k síti

Pro všechny operace virtuálního počítače s SQL serverem virtuální počítač vyžaduje připojení k veřejným IP adresám Azure. Operace virtuálního počítače (databáze zjišťování, konfigurace zálohování, plánovat zálohy, obnovit body obnovení) selhání bez připojení k veřejné IP adresy. Navázat spojení s některou z těchto možností:

- **Povolit rozsahy IP adres Azure datacenter**: Povolit [rozsahy IP adres](https://www.microsoft.com/download/details.aspx?id=41653) v souboru pro stažení. Pro přístup do skupiny zabezpečení sítě (NSG), použijte `Set-AzureNetworkSecurityRule` rutiny.
- **Nasazení HTTP proxy server pro směrování provozu**: Při zálohování databáze serveru SQL Server na Virtuálním počítači Azure, rozšíření zálohování na virtuálním počítači používá rozhraní API pro protokol HTTPS, odesílat příkazy pro správu do služby Azure Backup a odesílat data do služby Azure Storage. Rozšíření zálohování také používá pro ověřování Azure Active Directory (Azure AD). Směrování provozu linka záložního telefonu pro tyto tři služby prostřednictvím proxy serveru HTTP. Rozšíření je jedinou komponentou, která je nakonfigurovaná pro přístup k veřejnému Internetu.

Každá možnost má výhody a nevýhody.

**Možnost** | **Výhody** | **Nevýhody**
--- | --- | ---
Povolit rozsahy IP adres | Žádné další náklady. | Komplexní spravovat, protože rozsahy IP adres v průběhu času měnit. <br/><br/> Poskytuje přístup k celé Azure, ne jenom do služby Azure Storage.
Použít proxy server HTTP   | Detailní kontrola v proxy serveru úložiště je povolené adresy URL. <br/><br/> Jeden bod internetový přístup k virtuálním počítačům. <br/><br/> Není v souladu s Azure IP adresa změní. | Další náklady na provoz virtuálního počítače se softwarem proxy serveru. 

### <a name="set-vm-permissions"></a>Nastavit oprávnění pro virtuální počítač

Při konfiguraci zálohování pro databázi serveru SQL Server, provede Azure Backup několik věcí:

- Azure Backup přidá **AzureBackupWindowsWorkload** rozšíření.
- Azure Backup vytváří účet **NT SERVICE\AzureWLBackupPluginSvc** se zjistit databáze na virtuálním počítači. Tento účet se používá pro zálohování a obnovení a vyžaduje oprávnění správce systému SQL.
- Využívá službu Azure Backup **NT AUTHORITY\SYSTEM** účet pro zjišťování databází a dotaz. Tento účet musí být veřejné přihlášení na SQL serveru.

Pokud jste nevytvořili virtuální počítač SQL Server na Azure Marketplace, může se zobrazit **UserErrorSQLNoSysadminMembership** chyby. Pokud k této chybě dochází, [postupujte podle těchto pokynů](#fix-sql-sysadmin-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Zkontrolujte pokyny pro pojmenování databáze pro službu Azure Backup

Nepoužívejte následující názvy databází:

* Na konci/úvodní mezery
* Koncové vykřičník (!)
* Pravá hranatá závorka (])

Máme aliasů pro Azure table nepodporované znaky, ale doporučujeme, abyste jim vyhnout. [Další informace](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Zjistit databáze systému SQL Server

Zjišťování databází, které jsou spuštěny na virtuálním počítači. 

1. V [webu Azure portal](https://portal.azure.com), otevřete trezor služby Recovery Services, který používáte k zálohování databáze.

1. Na **trezor služby Recovery Services** řídicího panelu, vyberte **zálohování**. 

   ![Vybrat zálohu, otevřete nabídku cíl zálohování](./media/backup-azure-sql-database/open-backup-menu.png)

1. V **cíl zálohování**, nastavte **ve kterém je spuštěná vaše úloha** k **Azure** (výchozí).

1. V **co chcete zálohovat**vyberte **systému SQL Server na virtuálním počítači Azure**.

    ![Vyberte pro zálohování serveru SQL Server ve virtuálním počítači Azure](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)


1. V **cíl zálohování** > **zjistit databáze ve virtuálních počítačích**vyberte **spustit zjišťování** vyhledat nechráněné virtuální počítače v rámci předplatného. To může nějakou dobu trvat, v závislosti na počtu nechráněných virtuálních počítačů v rámci předplatného.

    ![Zálohování čeká na vyřízení při hledání pro databáze ve virtuálních počítačích](./media/backup-azure-sql-database/discovering-sql-databases.png)

1. V seznamu virtuálních počítačů vyberte virtuální počítač, na kterém běží databáze systému SQL Server > **zjišťování databází**.

1. Sledování zjišťování databází v **oznámení** oblasti. Může trvat nějakou dobu se úloha dokončí, v závislosti na tom, kolik databází jsou na virtuálním počítači. Když se zjistí vybrané databáze, se zobrazí zpráva o úspěchu.

    ![Zpráva o úspěšném nasazení](./media/backup-azure-sql-database/notifications-db-discovered.png)

    - Nechráněné virtuální počítače by se zobrazit v seznamu po zjištění, seřazené podle názvu a skupinu prostředků.
    - Pokud virtuální počítač není uvedená podle očekávání, zkontrolujte, jestli je už zálohovaná do trezoru.
    - Několik virtuálních počítačů může mít stejný název, ale bude patřit do různých skupin prostředků. 

1. Vyberte virtuální počítač, na kterém běží databáze systému SQL Server > **zjišťování databází**. 
1. Azure Backup zjistí všechny databáze SQL serveru na virtuálním počítači. Během zjišťování dojde k následující aktivitu na pozadí:

    - Azure Backup zaregistruje virtuální počítač s úložištěm pro úlohy zálohování. Všechny databáze na registrované virtuálních počítačů můžete zálohovat pouze k tomuto trezoru.
    - Azure Backup nainstaluje **AzureBackupWindowsWorkload** rozšíření ve virtuálním počítači. V SQL database se neinstaluje žádný agent.
    - Vytvoří účet služby Azure Backup **NT Service\AzureWLBackupPluginSvc** na virtuálním počítači.
      - Všechny operace zálohování a obnovení pomocí účtu služby.
      - **NT Service\AzureWLBackupPluginSvc** potřebuje oprávnění správce systému SQL. Všechny virtuální počítače SQL serveru, které jsou vytvořeny na webu Azure Marketplace jsou dostupné **SqlIaaSExtension** nainstalované. **AzureBackupWindowsWorkload** používá rozšíření **SQLIaaSExtension** automaticky získat požadované oprávnění.
    - Pokud jste nevytvořili virtuální počítač z Marketplace, virtuální počítač nemá **SqlIaaSExtension** nainstalované, a operace zjišťování se nezdaří s **UserErrorSQLNoSysAdminMembership** chybovou zprávu. Postupujte podle pokynů v [#fix-sql--oprávnění sysadmin] Pokud chcete tento problém vyřešit.

        ![Vyberte virtuální počítač a databáze](./media/backup-azure-sql-database/registration-errors.png)



## <a name="configure-a-backup-policy"></a>Nakonfigurujte zásady zálohování 

K optimalizaci zálohování zatížení, Azure Backup Nastaví maximální počet databází ve jediná úloha zálohování na 50.

- K ochraně více než 50 databází, nakonfigurujte více záloh.
- Alternativně můžete povolit automatickou ochranu. Automatická ochrana chrání existujících databází najednou a automaticky chrání nové databáze, které jsou přidány do instance skupiny dostupnosti.


Konfigurace zálohování následujícím způsobem:

1. Na řídicím panelu trezoru vyberte **zálohování**. 

   ![Vybrat zálohu, otevřete nabídku cíl zálohování](./media/backup-azure-sql-database/open-backup-menu.png)

1. V **cíl zálohování**, nastavte **ve kterém je spuštěná vaše úloha?** k **Azure**.

1. V **co chcete zálohovat?** vyberte **systému SQL Server na virtuálním počítači Azure**.

    ![Vyberte pro zálohování serveru SQL Server ve virtuálním počítači Azure](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    
1. V **cíl zálohování**vyberte **konfigurace zálohování**.

    ![Vyberte konfiguraci zálohování](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    ![Zobrazení všech instancí systému SQL Server u samostatných databází](./media/backup-azure-sql-database/list-of-sql-databases.png)

1. Vybrat všechny databáze, které chcete chránit > **OK**.

    ![Ochranu databáze](./media/backup-azure-sql-database/select-database-to-protect.png)

    - Všechny instance systému SQL Server jsou uvedeny (samostatné a dostupnosti skupiny).
    - Vyberte šipku dolů nalevo od instance název nebo dostupnost skupinu, kterou chcete filtrovat.

1. V **zálohování** nabídce vyberte možnost **zásady zálohování**. 

    ![Vyberte zásady zálohování](./media/backup-azure-sql-database/select-backup-policy.png)

1. V **výběr zásady zálohování**, vyberte zásadu a pak vyberte **OK**.

    - Vyberte výchozí zásady: **HourlyLogBackup**.
    - Vyberte existující zásadu zálohování, který byl dříve vytvořen pro SQL.
    - [Definovat novou zásadu](backup-azure-sql-database.md#configure-a-backup-policy) na základě cíle bodu obnovení a uchovávání rozsahu.
    - Ve verzi Preview nejde upravit existující zásadu zálohování.
    
1. V **zálohování** nabídce vyberte možnost **povolit zálohování**.

    ![Povolit vybrané zásady zálohování](./media/backup-azure-sql-database/enable-backup-button.png)

1. Sledovat průběh na konfiguraci **oznámení** části portálu.

    ![Oznamovací oblasti](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Vytvoření zásady zálohování

Zásady zálohování Určuje, kdy zálohy jsou prováděny a jak dlouho se uchovávají. 

- Zásady se vytvářejí na úrovni trezoru.
- Více trezorů můžete použít stejné zásady zálohování, ale musíte použít zásadu zálohování, která pro každý trezor.
- Při vytváření zásady zálohování denně úplné zálohování je výchozí hodnota.
- Rozdílové zálohování, ale pouze můžete přidat, pokud nakonfigurujete týdenních úplných záloh.
- [Další informace o](backup-architecture.md#sql-server-backup-types) různé druhy zásad zálohování.

Chcete-li vytvořit zásadu zálohování, postupujte takto:

1. V trezoru, vyberte **zásady zálohování** > **přidat**.
1. V **přidat** nabídce vyberte možnost **systému SQL Server na virtuálním počítači Azure**. Definuje typ zásad.

   ![Vyberte typ zásad pro nové zásady zálohování](./media/backup-azure-sql-database/policy-type-details.png)

1. V **Název_zásady**, zadejte název pro novou zásadu. 
1. V **úplná záloha zásad**, vyberte **četnost záloh**. Zvolte **denní** nebo **týdenní**.

    - Pro **denní**, vyberte hodiny a časové pásmo pro zálohování začít.
    - Je nutné spustit úplné zálohování. Nelze vypnout **úplná záloha** možnost.
    - Vyberte **úplná záloha** zásadách. 
    - Nelze vytvořit rozdílové zálohování pro denní úplná zálohování.
    - Pro **týdenní**, vyberte den v týdnu, hodiny a časové pásmo pro zálohování začít.

     ![Nová pole zásady zálohování](./media/backup-azure-sql-database/full-backup-policy.png)  

1. V **rozsah uchování**, ve výchozím nastavení jsou vybrané všechny možnosti. Vymažte všechny omezení rozsahu uchování nežádoucí, které nechcete používat, a nastavit intervaly, které chcete použít. 

    - Body obnovení jsou označené pro uchovávání informací podle jejich rozsah uchování. Například pokud vyberete každodenní úplnou zálohu, se aktivuje pouze jeden úplné zálohování každý den.
    - Zálohování pro určitý den je příznakem a uchovávají na základě týdenní rozsah uchování a nastavení týdenního uchovávání informací.
    - Měsíční a roční rozsahy uchovávání se chovají podobně.

   ![Nastavení intervalu rozsah uchování](./media/backup-azure-sql-database/retention-range-interval.png)

    

1. V **úplná záloha zásad** nabídce vyberte možnost **OK** potvrďte nastavení.
1. Chcete-li přidat zásadu pro rozdílové zálohování, **rozdílové zálohování**. 

   ![Nastavení intervalu rozsah uchování](./media/backup-azure-sql-database/retention-range-interval.png)
   ![otevřete nabídku zásady rozdílové zálohování](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

1. V **rozdílovou zálohu zásad**vyberte **povolit** otevřete ovládací prvky frekvence a uchování. 

    - Maximálně můžete aktivovat jeden rozdílové zálohy za den.
    - Rozdílové zálohy je možné uchovávat maximálně na 180 dnů. Pokud budete potřebovat delší dobu uchování, musíte použít úplné zálohy.

1. Vyberte **OK** zásadu uložte a vraťte se do hlavní **zásady zálohování** nabídky.

1. Chcete-li přidat zásadu zálohování transakčního protokolu, **zálohování protokolu**.
1. V **záloha protokolu**vyberte **povolit**a potom nastavte ovládací prvky frekvence a uchování. Zálohy protokolů může dojít, tak často, jak každých 15 minut a můžou uchovávat až 35 dnů.
1. Vyberte **OK** zásadu uložte a vraťte se do hlavní **zásady zálohování** nabídky.

    ![Upravit zásady zálohování protokolu](./media/backup-azure-sql-database/log-backup-policy-editor.png)

1. V **zásady zálohování** nabídce vyberte, jestli chcete povolit **kompresi zálohování SQL**.
    - Komprese je ve výchozím nastavení zakázána.
    - Na back-endu Azure Backup použije nativní kompresi záloh SQL.

1. Po dokončení úprav zásady zálohování vyberte **OK**.



## <a name="enable-auto-protection"></a>Povolit automatickou ochranu  

Povolte automatickou ochranu automaticky zálohovat všechny existující databáze a databáze, které se přidají v budoucnu, samostatná instance SQL serveru nebo do skupiny dostupnosti AlwaysOn SQL serveru. 

- Když zapnete automatickou ochranu a vyberte zásadu, existující chráněných databází bude nadále používat předchozí zásady.
- Neexistuje žádné omezení počtu databází, které můžete vybrat pro automatickou ochranu.

Použijte následující postup povolení automatické ochrany:

1. V **položky k zálohování**, vyberte instanci, pro kterou chcete povolit automatickou ochranu.
1. Vyberte rozevírací seznam v části **Autoprotect**a nastavte **na**. Pak vyberte **OK**.

    ![Povolit automatické ochrany na skupiny dostupnosti Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

1. Zálohování je nakonfigurovaný pro všechny databáze společně a lze sledovat v **úlohy zálohování**.


Pokud je potřeba zakázat automatickou ochranu, vyberte název instance v rámci **konfigurace zálohování**a vyberte **zakázat Autoprotect** pro instanci. Všechny databáze bude nadále zálohovat. Budoucí databáze však nebudou chráněné automaticky.

![Zakažte automatickou ochranu pro tuto instanci](./media/backup-azure-sql-database/disable-auto-protection.png)


## <a name="fix-sql-sysadmin-permissions"></a>Oprava oprávnění správce systému SQL

Pokud je potřeba opravit oprávnění z důvodu **UserErrorSQLNoSysadminMembership** chyby, postupujte podle těchto kroků:

1. Použijte účet s oprávněními správce systému SQL Server k přihlášení k serveru SQL Server Management Studio (SSMS). Pokud potřebujete speciální oprávnění, by měla fungovat ověřování Windows.
1. Na instanci serveru SQL Server, otevřete **zabezpečení/přihlášení** složky.

    ![Otevřít složku zabezpečení/přihlášení a zobrazit účty](./media/backup-azure-sql-database/security-login-list.png)

1. Klikněte pravým tlačítkem myši **přihlášení** a pak zvolte položku **nového přihlašovacího jména**. V **přihlášení – nové**vyberte **hledání**.

    ![V části přihlášení – nové dialogové okno, vyberte hledání](./media/backup-azure-sql-database/new-login-search.png)

1. Účet služby Windows virtuální **NT SERVICE\AzureWLBackupPluginSvc** jste vytvořili během registrace virtuálního počítače a fázi zjišťování SQL. Zadejte název účtu, jak je znázorněno v **zadejte název objektu k výběru**. Vyberte **Kontrola názvů** přeložit název. Vyberte **OK**.

    ![Vyberte Zkontrolovat jména k přeložení názvu služby neznámý](./media/backup-azure-sql-database/check-name.png)


1. V **role serveru**, ujistěte se, že **sysadmin** je vybrána role. Vyberte **OK**. Teď by měla existovat požadovaná oprávnění.

    ![Ujistěte se, že je vybrána role serveru sysadmin](./media/backup-azure-sql-database/sysadmin-server-role.png)

1. Přidružte databázi k trezoru služby Recovery Services. Na webu Azure Portal v **chráněné servery** seznamu, klikněte pravým tlačítkem na server, který je v chybovém stavu > **opětovné zjištění databází**.

    ![Ověřte, že server má příslušná oprávnění](./media/backup-azure-sql-database/check-erroneous-server.png)

1. Kontrola průběhu **oznámení** oblasti. Když se nacházejí ve vybraných databázích, zobrazí se zpráva o úspěchu.

    ![Zpráva o úspěšném nasazení](./media/backup-azure-sql-database/notifications-db-discovered.png)

Můžete také povolit [automatickou ochranu](backup-azure-sql-database.md#enable-auto-protection) celý instance nebo skupiny dostupnosti Always On. Ho Pokud chcete povolit, vyberte **ON** možnost v rozevíracím seznamu příslušné v **AUTOPROTECT** sloupce. [Automatickou ochranu](backup-azure-sql-database.md#enable-auto-protection) funkce umožňuje ochranu pro všechny existující databáze. Také automaticky chrání všechny nové databáze, které budou přidány do této instance nebo skupina dostupnosti v budoucnu.  

   ![Povolit automatické ochrany na skupiny dostupnosti Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

## <a name="next-steps"></a>Další postup

- [Další informace o](restore-sql-database-azure-vm.md) obnovení zálohovaných databáze systému SQL Server.
- [Další informace o](manage-monitor-sql-database-backup.md) Správa zálohování databází systému SQL Server.

