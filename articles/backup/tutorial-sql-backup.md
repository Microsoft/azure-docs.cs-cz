---
title: Kurz – zálohování databází SERVERU SQL Server do Azure
description: V tomto kurzu se dozvíte, jak zálohovat databázi SQL Serveru spuštěnou na virtuálním počítači Azure do trezoru služby Obnovení zálohování Azure.
ms.topic: tutorial
ms.date: 06/18/2019
ms.openlocfilehash: 3216404b04647e2fb5f15beb09b9a4ccd6648bba
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74171885"
---
# <a name="back-up-a-sql-server-database-in-an-azure-vm"></a>Zálohování databáze SERVERU SQL Server ve virtuálním počítači Azure

Tento kurz ukazuje, jak zálohovat databázi SQL Serveru spuštěnou na virtuálním počítači Azure do trezoru služby Obnovení zálohování Azure. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Vytvořte a nakonfigurujte úschovnu.
> * Objevte databáze a nastavte zálohy.
> * Nastavte automatickou ochranu databází.
> * Spusťte zálohu na vyžádání.

## <a name="prerequisites"></a>Požadavky

Před zálohováním databáze serveru SQL Server zkontrolujte následující podmínky:

1. Identifikujte nebo [vytvořte](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) trezor služby Recovery Services ve stejné oblasti nebo národním prostředí jako virtuální modul hostující instanci serveru SQL Server.
2. [Zkontrolujte oprávnění virtuálního připojení](backup-azure-sql-database.md#set-vm-permissions) potřebná k zálohování databází SQL.
3. Ověřte, zda má virtuální [hotel připojení k síti](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
4. Zkontrolujte, zda jsou databáze serveru SQL Server pojmenovány v souladu s pokyny pro [pojmenování](#verify-database-naming-guidelines-for-azure-backup) pro azure backup.
5. Ověřte, zda pro databázi nejsou povolena žádná jiná řešení zálohování. Před nastavením tohoto scénáře zakažte všechny ostatní zálohy serveru SQL Server. Azure Backup můžete povolit pro virtuální počítač Azure spolu s Azure Backup pro databázi SQL Serveru spuštěnou na virtuálním počítači bez jakéhokoli konfliktu.

### <a name="establish-network-connectivity"></a>Navázání připojení k síti

Pro všechny operace virtuální počítač SQL Server potřebuje připojení k veřejným IP adresám Azure. Operace virtuálních počítačů (zjišťování databáze, konfigurace zálohování, plánování zálohování, body obnovení a tak dále) se nezdaří bez připojení k veřejným IP adresám. Navázání připojení pomocí jedné z těchto možností:

* **Povolit rozsahy IP adres datového centra Azure:** Povolit [rozsahy IP adres](https://www.microsoft.com/download/details.aspx?id=41653) při stahování. Chcete-li získat přístup ke skupině zabezpečení sítě (NSG), použijte rutinu **Set-AzureNetworkSecurityRule.**
* **Nasazení proxy serveru HTTP pro směrování provozu**: Při zálohování databáze SERVERU SQL Server na virtuálním počítači Azure, rozšíření pro zálohování na virtuálním počítači používá rozhraní API HTTPS k odesílání příkazů pro správu do Azure Backup a data do Azure Storage. Rozšíření zálohování také používá Azure Active Directory (Azure AD) pro ověřování. Směrovat provoz rozšíření zálohy pro tyto tři služby prostřednictvím proxy serveru HTTP. Rozšíření jsou jedinou součástí, která je nakonfigurována pro přístup k veřejnému internetu.

Každá možnost má své výhody a nevýhody

**Možnost** | **Výhody** | **Nevýhody**
--- | --- | ---
Povolit rozsahy IP adres | Žádné další náklady. | Komplexní správa, protože rozsahy IP adres se v průběhu času mění. <br/><br/> Poskytuje přístup k celému Azure, nejen k azure storage.
Použití proxy serveru HTTP   | Podrobný ovládací prvek v proxy serveru přes adresy URL úložiště je povolen. <br/><br/> Jednotný bod přístupu k internetu k virtuálním kvitům. <br/><br/> Nepodléhá změnám IP adres Azure. | Dodatečné náklady na spuštění virtuálního počítače se softwarem proxy.

### <a name="set-vm-permissions"></a>Nastavení oprávnění virtuálních aplikací

Azure Backup provádí řadu věcí při konfiguraci zálohování pro databázi SERVERU SQL:

* Přidá rozšíření **AzureBackupWindowsWorkload.**
* Chcete-li zjistit databáze na virtuálním počítači, Azure Backup vytvoří účet **NT SERVICE\AzureWLBackupPluginSvc**. Tento účet se používá pro zálohování a obnovení a vyžaduje oprávnění sql sysadmin.
* Azure Backup využívá účet **NT AUTHORITY\SYSTEM** pro zjišťování/dotazování databáze, takže tento účet musí být veřejné přihlášení v SQL.

Pokud jste nevytvořili virtuální počítač SQL Server z Azure Marketplace, může se zobrazit chyba **UserErrorSQLNoSysadminMembership**. Pokud k tomu [dojde, postupujte podle těchto pokynů](backup-azure-sql-database.md#set-vm-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Ověření pokynů pro pojmenování databáze pro azure backup

Vyhněte se následujícím názvům databází:

* Koncové/prokladné prostory
* Koncové '!'
* Zavřít hranatá závorka ']'
* Názvy databází začínající na 'F:\'

Máme aliasing pro azure tabulka nepodporované znaky, ale doporučujeme jim vyhnout. [Další informace](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Zjišťování databází serveru SQL Server

Zjišťujte databáze spuštěné na virtuálním počítači.

1. Na [webu Azure Portal](https://portal.azure.com)otevřete trezor služby Recovery Services, který používáte k zálohování databáze.

2. Na řídicím panelu **trezoru služby Recovery Services** vyberte **možnost Zálohování**.

   ![Výběrem možnosti Zálohování otevřete nabídku Cíl zálohování.](./media/backup-azure-sql-database/open-backup-menu.png)

3. V **cíli zálohování** **nastavte, kde je vaše úloha spuštěná** do **Azure** (výchozí).

4. V **článku Co chcete zálohovat**vyberte SQL Server v **virtuálním počítači Azure**.

    ![Vyberte SQL Server v virtuálním počítači Azure pro zálohování](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. V **části Cíl** > zálohování**Objevte centrální paměti ve virtuálních počítačích**vyberte **Spustit zjišťování** a vyhledejte nechráněné virtuální počítače v předplatném. Může chvíli trvat, v závislosti na počtu nechráněných virtuálních počítačů v předplatném.

   * Nechráněné virtuální počítače by se měly zobrazit v seznamu po zjišťování, uvedené podle názvu a skupiny prostředků.
   * Pokud virtuální virtuální může být v yp., co očekáváte, zkontrolujte, jestli už je zálohovaný v trezoru.
   * Více virtuálních počítačů může mít stejný název, ale budou patřit do různých skupin prostředků.

     ![Zálohování čeká na vyřízení při hledání DBs ve virtuálních počítačech](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. V seznamu virtuálních společností vyberte virtuální ms se spuštěnou databází serveru SQL Server > **zjišťování dbs**.

7. Sledování zjišťování databáze v oblasti **Oznámení.** Může chvíli trvat, než se úloha dokončí, v závislosti na tom, kolik databází je na virtuálním počítači. Po zjištění vybraných databází se zobrazí zpráva o úspěchu.

    ![Zpráva o úspěchu nasazení](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup zjišťuje všechny databáze SQL Serveru na virtuálním počítači. Během zjišťování dochází na pozadí k následujícímu:

    * Azure Backup zaregistrovat virtuální počítač s trezoru pro zálohování úloh. Všechny databáze na registrovaném virtuálním počítači lze zálohovat jenom do tohoto trezoru.
    * Azure Backup nainstaluje rozšíření **AzureBackupWindowsWorkload** na virtuálním počítači. V databázi SQL není nainstalován žádný agent.
    * Azure Backup vytvoří účet služby **NT Service\AzureWLBackupPluginSvc** na virtuálním počítači.
      * Všechny operace zálohování a obnovení používají účet služby.
      * **NT Service\AzureWLBackupPluginSvc** potřebuje oprávnění sql sysadmin. Všechny virtuální počítače SQL Serveru vytvořené na Azure Marketplace jsou dodávány s nainstalovaným **rozšířením SqlIaaSExtension.** Rozšíření **AzureBackupWindowsWorkload** používá **sqliaasextension** automaticky získat požadovaná oprávnění.
    * Pokud jste virtuální ho mohli vytvořit z tržiště, nemá virtuální ho virtuální ho nainstalováno **rozšíření SqlIaaSExtension** a operace zjišťování se nezdaří s chybovou zprávou **UserErrorSQLNoSysAdminMembership**. Tento problém opravte [podle pokynů.](backup-azure-sql-database.md#set-vm-permissions)

        ![Výběr virtuálního virtuálního připojení a databáze](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Konfigurace zálohování  

Konfigurace zálohování následujícím způsobem:

1. V **seznamu Cíl zálohování**vyberte Konfigurovat **zálohování**.

   ![Vyberte Konfigurovat zálohování.](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Klepněte na **tlačítko Konfigurovat zálohování**, zobrazí se okno Vybrat **položky k zálohování.** V tomto seznamu jsou uvedeny všechny registrované skupiny dostupnosti a samostatné servery SQL. Rozbalte dvojitou šipku vlevo od řádku, abyste viděli všechny nechráněné databáze v této instanci nebo Vždy v AG.  

    ![Zobrazení všech instancí serveru SQL Server se samostatnými databázemi](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Vyberte všechny databáze, které chcete chránit > **OK**.

   ![Ochrana databáze](./media/backup-azure-sql-database/select-database-to-protect.png)

   Chcete-li optimalizovat načtení zálohy, Azure Backup nastaví maximální počet databází v jedné úloze zálohování na 50.

     * Případně můžete povolit automatickou ochranu u celé instance nebo vždy na dostupnosti výběrem **možnosti ON** v příslušném rozevíracím seznamu ve sloupci **AUTOPROTECT.** Funkce automatické ochrany umožňuje nejen ochranu všech existujících databází najednou, ale také automaticky chrání všechny nové databáze, které budou přidány do této instance nebo skupiny dostupnosti v budoucnu.  

4. Klepnutím na **tlačítko OK** otevřete okno **zásad zálohování.**

    ![Povolit automatickou ochranu ve skupině dostupnosti always on](./media/backup-azure-sql-database/enable-auto-protection.png)

5. V **seznamu Zvolit zásady zálohování**vyberte zásadu a klepněte na tlačítko **OK**.

   * Vyberte výchozí zásadu: HourlyLogBackup.
   * Zvolte existující zásady zálohování, které byly dříve vytvořeny pro SQL.
   * Definujte novou zásadu na základě rpo a rozsahu uchovávání informací.

     ![Vybrat zásadu zálohování](./media/backup-azure-sql-database/select-backup-policy.png)

6. V nabídce **Zálohování** vyberte **Povolit zálohování**.

    ![Povolení zvolené zásady zálohování](./media/backup-azure-sql-database/enable-backup-button.png)

7. Sledujte průběh konfigurace v oblasti **Oznámení** na portálu.

    ![Oznamovací oblast](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Vytvoření zásad zálohování

Zásady zálohování definují, kdy jsou zálohy přijata a jak dlouho jsou zachovány.

* Zásada je vytvořena na úrovni úschovny.
* Více trezorů může používat stejné zásady zálohování, ale zásady zálohování je nutné použít pro každý trezor.
* Při vytváření zásad zálohování je výchozí denní úplná záloha.
* Můžete přidat rozdílové zálohy, ale pouze v případě, že nakonfigurujete úplné zálohy dojít týdně.
* [Seznamte se s](backup-architecture.md#sql-server-backup-types) různými typy zásad zálohování.

Vytvoření zásadzálohování:

1. V trezoru klikněte na **Možnost Přidat zásady** > **Add**zálohování .
2. V nabídce **Přidat** klikněte na **SQL Server ve virtuálním počítači Azure** a definujte typ zásad.

   ![Výběr typu zásad pro novou zásadu zálohování](./media/backup-azure-sql-database/policy-type-details.png)

3. V **části Název zásad**zadejte název nové zásady.
4. V **zásadách úplného zálohování**vyberte **frekvenci zálohování**, zvolte **Denně** nebo **Týdně**.

   * V **části Denně**vyberte hodinu a časové pásmo při zahájení úlohy zálohování.
   * Je nutné spustit úplnou zálohu, protože nelze vypnout možnost **Úplné zálohování.**
   * Chcete-li zobrazit zásadu, klepněte na tlačítko **Úplné zálohování.**
   * Nelze vytvořit rozdílové zálohy pro denní úplné zálohy.
   * V **části Týdně**vyberte den v týdnu, hodinu a časové pásmo při zahájení úlohy zálohování.

     ![Nová pole zásad zálohování](./media/backup-azure-sql-database/full-backup-policy.png)  

5. V **případě rozsahu uchování**jsou ve výchozím nastavení vybrány všechny možnosti. Zrušte všechny nežádoucí limity rozsahu uchování, které nechcete používat, a nastavte intervaly, které chcete použít.

    * Minimální doba uchování pro jakýkoli typ zálohy (full/differential/log) je sedm dní.
    * Body obnovení jsou označeny pro uchovávání na základě jejich rozsah uchování. Pokud například vyberete denní úplnou zálohu, bude každý den spuštěna pouze jedna úplná záloha.
    * Záloha pro konkrétní den je označena a zachována na základě týdenního rozsahu uchovávání a týdenního nastavení uchovávání.
    * Měsíční a roční retenční rozsahy se chovají podobně.

   ![Nastavení intervalu rozsahu retencí](./media/backup-azure-sql-database/retention-range-interval.png)

6. V nabídce **zásad Úplné zálohování** vyberte **OK,** chcete-li přijmout nastavení.
7. Chcete-li přidat zásady rozdílového zálohování, vyberte **možnost Rozdílová záloha**.

   ![Nastavení](./media/backup-azure-sql-database/retention-range-interval.png)
   ![intervalu rozsahu uchování Otevřete nabídku zásad rozdílového zálohování](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. V **zásadách rozdílového zálohování**vyberte **Povolit,** chcete-li otevřít ovládací prvky četnosti a uchovávání informací.

    * Maximálně můžete spustit jednu rozdílovou zálohu za den.
    * Rozdílové zálohy lze uchovávat maximálně 180 dní. Pokud potřebujete delší uchovávání informací, musíte použít úplné zálohy.

9. Výběrem **možnosti OK** uložte zásadu a vraťte se do hlavní nabídky **zásad zálohování.**

10. Chcete-li přidat zásadu zálohování transakčního protokolu, vyberte **možnost Protokolovat zálohování**.
11. V **části Zálohování protokolu**vyberte **Povolit**a nastavte ovládací prvky frekvence a uchovávání informací. Zálohy protokolů se mohou vyskytovat tak často, jak každých 15 minut a mohou být uchovávány po dobu až 35 dnů.
12. Výběrem **možnosti OK** uložte zásadu a vraťte se do hlavní nabídky **zásad zálohování.**

    ![Úprava zásad zálohování protokolu](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. V nabídce **zásad zálohování** zvolte, zda chcete povolit **kompresi zálohování SQL**.
    * Komprese je ve výchozím nastavení zakázána.
    * V back-endu azure backup používá nativní kompresi zálohování SQL.

14. Po dokončení úprav zásad zálohování vyberte **ok**.

## <a name="run-an-on-demand-backup"></a>Spuštění zálohy na vyžádání

1. V trezoru služby Recovery Services zvolte Zálohovat položky.
2. Klikněte na "SQL v virtuálním počítači Azure".
3. Klikněte pravým tlačítkem myši na databázi a zvolte "Zálohování nyní".
4. Zvolte typ zálohy (úplný/diferenciální/log/kopírovat pouze plný) a komprese (povolit/zakázat)
5. Chcete-li začít zálohování, vyberte možnost OK.
6. Sledování úlohy zálohování přejdete do trezoru služby Recovery Services a zvolíte možnost "Úlohy zálohování".

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste pomocí webu Azure Portal provedli následující kroky:

> [!div class="checklist"]
>
> * Vytvořte a nakonfigurujte úschovnu.
> * Objevte databáze a nastavte zálohy.
> * Nastavte automatickou ochranu databází.
> * Spusťte zálohu na vyžádání.

Přejděte k dalšímu kurzu, kde obnovíte virtuální počítač Azure z disku.

> [!div class="nextstepaction"]
> [Obnovení databází SQL Serveru na virtuálních počítačích Azure](./restore-sql-database-azure-vm.md)
