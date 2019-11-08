---
title: Kurz – zálohování databází SQL Server do Azure
description: V tomto kurzu se naučíte zálohovat SQL Server databázi běžící na virtuálním počítači Azure do trezoru služby Azure Backup Recovery Services.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: dacurwin
ms.openlocfilehash: 28a804a57a4113b22efd5274ad00b3a216b700aa
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747028"
---
# <a name="back-up-a-sql-server-database-in-an-azure-vm"></a>Zálohování databáze SQL Server na virtuálním počítači Azure

V tomto kurzu se dozvíte, jak zálohovat databázi SQL Server běžící na virtuálním počítači Azure do trezoru služby Azure Backup Recovery Services. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Vytvořte a nakonfigurujte trezor.
> * Objevte databáze a nastavte zálohy.
> * Nastavte automatickou ochranu pro databáze.
> * Spusťte zálohování ad hoc.

## <a name="prerequisites"></a>Požadavky

Před zálohováním SQL Server databáze ověřte následující podmínky:

1. Identifikujte nebo [vytvořte](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) Recovery Services trezor ve stejné oblasti nebo národním prostředí jako virtuální počítač, který je hostitelem instance SQL Server.
2. [Ověřte oprávnění virtuálních počítačů](backup-azure-sql-database.md#set-vm-permissions) potřebných k zálohování databází SQL.
3. Ověřte, že virtuální počítač má [připojení k síti](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
4. Ověřte, zda jsou databáze SQL Server pojmenovány v souladu s [pokyny pro pojmenování](#verify-database-naming-guidelines-for-azure-backup) pro Azure Backup.
5. Ověřte, že nemáte pro databázi povolená žádná další zálohovací řešení. Před nastavením tohoto scénáře zakažte všechny ostatní SQL Server zálohy. Můžete povolit Azure Backup pro virtuální počítač Azure spolu s Azure Backup pro SQL Server databázi běžící na virtuálním počítači bez jakéhokoli konfliktu.

### <a name="establish-network-connectivity"></a>Navázat připojení k síti

Pro všechny operace je potřeba, aby virtuální počítač s SQL Server virtuálním počítačem byl připojen k veřejným IP adresám Azure. Operace virtuálních počítačů (zjišťování databáze, konfigurace záloh, plánování záloh, obnovení bodů obnovení atd.) bez připojení k veřejným IP adresám. Navažte spojení pomocí jedné z následujících možností:

* **Povolte rozsahy IP adres datacentra Azure**: Povolte [rozsahy IP](https://www.microsoft.com/download/details.aspx?id=41653) adres ve stahování. Pro přístup ke skupině zabezpečení sítě (NSG) použijte rutinu **set-AzureNetworkSecurityRule** .
* **Nasazení proxy server HTTP pro směrování provozu**: při zálohování databáze SQL Server na virtuálním počítači Azure používá rozšíření zálohování na virtuálním počítači rozhraní API protokolu HTTPS k posílání příkazů pro správu do Azure Backup a data, která se mají Azure Storage. Rozšíření zálohování používá pro ověřování taky Azure Active Directory (Azure AD). Přesměrujte provoz rozšíření zálohování pro tyto tři služby prostřednictvím proxy serveru HTTP. Rozšíření jsou jedinou komponentou, která je nakonfigurovaná pro přístup k veřejnému Internetu.

Každá z možností má výhody a nevýhody.

**Možnost** | **Výhody** | **Nevýhody**
--- | --- | ---
Povoluje rozsahy IP adres. | Žádné další náklady. | Správa je složitá, protože rozsahy IP adres se v průběhu času mění. <br/><br/> Poskytuje přístup k celému systému Azure, ne jen Azure Storage.
Použití proxy serveru HTTP   | Podrobné řízení v proxy serveru přes adresy URL úložiště je povoleno. <br/><br/> Přístup k virtuálním počítačům jediným bodem z Internetu. <br/><br/> Nepodléhá změnám IP adresy Azure. | Další náklady na spuštění virtuálního počítače se softwarem proxy serveru.

### <a name="set-vm-permissions"></a>Nastavení oprávnění virtuálních počítačů

Při konfiguraci zálohování pro databázi SQL Server Azure Backup provede několik věcí:

* Přidá rozšíření **AzureBackupWindowsWorkload** .
* Pokud chcete zjišťovat databáze na virtuálním počítači, Azure Backup vytvoří účet **NT SERVICE\AzureWLBackupPluginSvc**. Tento účet se používá pro zálohování a obnovení a vyžaduje oprávnění správce systému SQL.
* Azure Backup využívá účet **NT AUTHORITY\SYSTEM** pro zjišťování nebo dotaz databáze, takže tento účet musí být veřejným přihlášením na SQL.

Pokud jste virtuální počítač SQL Server z Azure Marketplace nevytvořili, může se zobrazit chyba **UserErrorSQLNoSysadminMembership**. Pokud k tomu dojde, [postupujte podle těchto pokynů](backup-azure-sql-database.md#set-vm-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Ověření pokynů pro pojmenovávání databází pro Azure Backup

Nepoužívejte pro názvy databází následující:

* Koncové/úvodní mezery
* Koncová '! '
* Zavřít hranatou závorku '] '
* Názvy databází začínají řetězcem ' F:\ '

Pro nepodporované znaky tabulky Azure máme aliasy, ale doporučujeme je vyhnout. [Další informace](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN)

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Zjišťování SQL Serverch databází

Zjišťovat databáze běžící na virtuálním počítači.

1. V [Azure Portal](https://portal.azure.com)otevřete Recovery Services trezor, který používáte k zálohování databáze.

2. Na řídicím panelu **trezoru Recovery Services** vyberte **zálohování**.

   ![Výběrem zálohovat otevřete nabídku cíl zálohování.](./media/backup-azure-sql-database/open-backup-menu.png)

3. V části **cíl zálohování**nastavte, **kde se vaše zatížení spouští** do **Azure** (výchozí nastavení).

4. V **Možnosti co chcete zálohovat**vyberte **SQL Server na virtuálním počítači Azure**.

    ![Vyberte SQL Server na virtuálním počítači Azure pro zálohování.](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. V části **cíl zálohování** > na **virtuálním počítači vyhledat databáze**, vyberte **Spustit zjišťování** a vyhledejte nechráněné virtuální počítače v předplatném. Může to chvíli trvat v závislosti na počtu nechráněných virtuálních počítačů v rámci předplatného.

   * Nechráněné virtuální počítače by se měly zobrazit v seznamu po zjištění, které jsou uvedené podle názvu a skupiny prostředků.
   * Pokud virtuální počítač není uvedený podle očekávání, ověřte, jestli je už zálohovaný v trezoru.
   * Stejný název může mít víc virtuálních počítačů, ale patří do různých skupin prostředků.

     ![Při hledání databáze na virtuálních počítačích čeká na zálohování.](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. V seznamu virtuálních počítačů vyberte virtuální počítač, na kterém běží SQL Server databáze > **zjistit databáze**.

7. Sledujte zjišťování databáze v oblasti **oznámení** . Může chvíli trvat, než se úloha dokončí, v závislosti na tom, kolik databází je na virtuálním počítači. Když jsou vybrané databáze zjištěny, zobrazí se zpráva o úspěchu.

    ![Zpráva o úspěšném nasazení](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup zjistí všechny databáze SQL Server na virtuálním počítači. Při zjišťování níže dochází na pozadí:

    * Azure Backup Zaregistrujte virtuální počítač s trezorem pro zálohování úloh. Všechny databáze na zaregistrovaném virtuálním počítači se dají zálohovat jenom do tohoto trezoru.
    * Azure Backup nainstaluje na virtuální počítač rozšíření **AzureBackupWindowsWorkload** . V databázi SQL není nainstalován žádný agent.
    * Azure Backup vytvoří účet služby **NT Service\AzureWLBackupPluginSvc** ve virtuálním počítači.
      * Všechny operace zálohování a obnovení používají účet služby.
      * **NT Service\AzureWLBackupPluginSvc** potřebuje oprávnění správce systému SQL. Všechny SQL Server virtuální počítače vytvořené v Azure Marketplace se dodávají s nainstalovaným **SqlIaaSExtension** . Rozšíření **AzureBackupWindowsWorkload** používá k automatickému získání požadovaných oprávnění **SQLIaaSExtension** .
    * Pokud jste virtuální počítač nevytvořili z webu Marketplace, pak na virtuálním počítači není nainstalovaný **SqlIaaSExtension** a operace zjišťování se nezdařila s chybovou zprávou **UserErrorSQLNoSysAdminMembership**. Pokud chcete tento problém vyřešit, postupujte podle [pokynů](backup-azure-sql-database.md#set-vm-permissions) .

        ![Vyberte virtuální počítač a databázi.](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Konfigurace zálohování  

Proveďte konfiguraci zálohování následujícím způsobem:

1. V nastavení **cíl zálohování**vyberte **Konfigurovat zálohování**.

   ![Vyberte konfigurovat zálohu.](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Klikněte na **Konfigurovat zálohu**. zobrazí se okno **Vybrat položky k zálohování** . Zobrazí se seznam všech registrovaných skupin dostupnosti a samostatných serverů SQL. Rozbalením dvojitou šipku nalevo od řádku zobrazíte všechny nechráněné databáze v této instanci nebo Always On AG.  

    ![Zobrazení všech instancí SQL Server se samostatnými databázemi](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Vyberte všechny databáze, které chcete chránit > **OK**.

   ![Ochrana databáze](./media/backup-azure-sql-database/select-database-to-protect.png)

   Pro optimalizaci zátěže zálohování Azure Backup nastaví maximální počet databází v jedné úloze zálohování na 50.

     * Případně můžete zapnout automatickou ochranu pro celou instanci nebo skupinu dostupnosti Always On výběrem možnosti **on** v odpovídajícím rozevíracím seznamu ve SLOUPCI automaticky **chránit** . Funkce automatické ochrany nepovoluje ochranu jenom na všech stávajících databázích na jednom cestách, ale také automaticky chrání všechny nové databáze, které se do této instance nebo skupiny dostupnosti přidají v budoucnu.  

4. Kliknutím na **OK** otevřete okno **zásady zálohování** .

    ![Povolení automatické ochrany ve skupině dostupnosti Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. V **nabídce zvolte zásady zálohování**vyberte zásadu a pak klikněte na **OK**.

   * Vyberte výchozí zásadu: HourlyLogBackup.
   * Vyberte existující zásadu zálohování, která byla dříve vytvořena pro SQL.
   * Definujte novou zásadu na základě bodu RPO a rozsahu uchování.

     ![Vybrat zásady zálohování](./media/backup-azure-sql-database/select-backup-policy.png)

6. V nabídce **zálohování** vyberte **Povolit zálohování**.

    ![Povolit zvolenou zásadu zálohování](./media/backup-azure-sql-database/enable-backup-button.png)

7. Sledujte průběh konfigurace v oblasti **oznámení** na portálu.

    ![Oznamovací oblast](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Vytvoření zásady zálohování

Zásady zálohování definují, kdy se zálohují zálohy a jak dlouho se uchovávají.

* Zásada se vytvoří na úrovni trezoru.
* Několik trezorů může používat stejné zásady zálohování, ale je nutné použít zásady zálohování pro každý trezor.
* Při vytváření zásad zálohování je výchozí denní úplné zálohování.
* Rozdílové zálohování můžete přidat, ale jenom v případě, že nakonfigurujete úplné zálohování na týden.
* [Seznamte](backup-architecture.md#sql-server-backup-types) se s různými typy zásad zálohování.

Vytvoření zásady zálohování:

1. V trezoru klikněte na **zásady zálohování** > **Přidat**.
2. V nabídce **Přidat** klikněte na **SQL Server na virtuálním počítači Azure** a definujte typ zásad.

   ![Vyberte typ zásad pro nové zásady zálohování.](./media/backup-azure-sql-database/policy-type-details.png)

3. Do pole **název zásady**zadejte název nové zásady.
4. V **zásadách úplného zálohování**vyberte **četnost záloh**, zvolte **denně** nebo **týdně**.

   * V **denní**době vyberte hodiny a časové pásmo, kdy se úloha zálohování spustí.
   * Musíte spustit úplnou zálohu, protože nemůžete vypnout možnost **úplného zálohování** .
   * Zásadu zobrazíte kliknutím na **úplné zálohování** .
   * Pro každodenní úplné zálohování nemůžete vytvořit rozdílové zálohy.
   * V poli **týdně**vyberte den v týdnu, hodinu a časové pásmo při zahájení úlohy zálohování.

     ![Nová pole zásad zálohování](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Pro **Rozsah uchování**je ve výchozím nastavení vybraná možnost všechny možnosti. Vymažte všechny nepožadované limity rozsahu uchovávání, které nechcete používat, a nastavte intervaly, které se mají použít.

    * Minimální doba uchování pro jakýkoli typ zálohy (úplný/rozdíl/protokol) je sedm dní.
    * Body obnovení jsou označeny pro uchování na základě jejich rozsahu uchovávání. Pokud například vyberete denní úplnou zálohu, spustí se každý den jenom jedno úplné zálohování.
    * Záloha pro určitý den je označená a uchovávaná na základě rozsahu týdenního uchování a nastavení týdenního uchovávání.
    * Měsíční a roční rozsah uchování se chová podobným způsobem.

   ![Nastavení intervalu rozsahu uchování](./media/backup-azure-sql-database/retention-range-interval.png)

6. V nabídce **zásady úplného zálohování** vyberte **OK** a přijměte nastavení.
7. Chcete-li přidat zásady rozdílového zálohování, vyberte **rozdílové zálohování**.

   nastavení intervalu rozsahu uchování ![](./media/backup-azure-sql-database/retention-range-interval.png)
   ![otevřete nabídku rozdílových zásad zálohování](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. V části **rozdílová zásada zálohování**vyberte **Povolit** a otevřete tak ovládací prvky četnost a uchování.

    * Ve většině případů můžete aktivovat jednu rozdílovou zálohu za den.
    * Rozdílové zálohy je možné uchovávat maximálně po dobu 180 dnů. Pokud budete potřebovat delší dobu uchovávání, je nutné použít úplné zálohování.

9. Výběrem **OK** zásadu uložíte a vrátíte se do nabídky hlavní **zásady zálohování** .

10. Chcete-li přidat zásady zálohování transakčního protokolu, vyberte možnost **zálohování protokolu**.
11. V části **zálohování protokolu**vyberte **Povolit**a pak nastavte četnost a ovládací prvky uchování. Zálohy protokolů se můžou nacházet jako každých 15 minut a můžou se uchovávat až 35 dní.
12. Výběrem **OK** zásadu uložíte a vrátíte se do nabídky hlavní **zásady zálohování** .

    ![Upravit zásady zálohování protokolu](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. V nabídce **zásady zálohování** vyberte, jestli se má povolit **Komprese záloh SQL**.
    * Komprese je ve výchozím nastavení zakázaná.
    * Na back-endu Azure Backup používá kompresi nativního zálohování SQL.

14. Po dokončení úprav zásad zálohování vyberte **OK**.

## <a name="run-an-ad-hoc-backup"></a>Spuštění zálohování ad hoc

1. V Recovery Services trezoru vyberte zálohované položky.
2. Klikněte na SQL na virtuálním počítači Azure.
3. Klikněte pravým tlačítkem na databázi a vyberte zálohovat nyní.
4. Vyberte typ zálohování (úplný/rozdíl/protokol/kopírovat pouze úplné) a kompresi (Povolit/zakázat).
5. Kliknutím na OK zahajte zálohování.
6. Sledujte úlohu zálohování tak, že do svého trezoru Recovery Services kliknete a zvolíte "úlohy zálohování".

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste pomocí webu Azure Portal provedli následující kroky:

> [!div class="checklist"]
>
> * Vytvořte a nakonfigurujte trezor.
> * Objevte databáze a nastavte zálohy.
> * Nastavte automatickou ochranu pro databáze.
> * Spusťte zálohování ad hoc.

Přejděte k dalšímu kurzu, kde obnovíte virtuální počítač Azure z disku.

> [!div class="nextstepaction"]
> [Obnovení databází SQL Server na virtuálních počítačích Azure](./restore-sql-database-azure-vm.md)
