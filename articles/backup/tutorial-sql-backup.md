---
title: Zálohování databází SQL serveru do Azure | Dokumentace Microsoftu
description: Tento kurz vysvětluje, jak k zálohování SQL serveru do Azure.
services: backup
author: dcurwin
manager: ''
ms.service: backup
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: dacurwin
ms.openlocfilehash: 5fbbd2cf999ab8ba3183879bd9b417353aa5edd0
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203480"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Zálohování databází SQL Serveru ve virtuálních počítačích Azure



V tomto článku se dozvíte, jak zálohovat databázi serveru SQL Server běží na Virtuálním počítači Azure trezoru služeb zotavení Azure Backup. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte a nakonfigurujte trezor.
> * Najít databáze a nastavení zálohování.
> * Nastavte si Automatická ochrana databáze.
> * Spusťte zálohování ad hoc.


## <a name="prerequisites"></a>Požadavky

Zálohujte databázi SQL serveru, zkontrolujte následující podmínky:

1. Identifikovat nebo [vytvořit](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) trezor služby Recovery Services ve stejné oblasti nebo národní prostředí jako virtuální počítač, který je hostitelem instance serveru SQL Server.
2. [Zkontrolujte oprávnění VM](backup-azure-sql-database.md#set-vm-permissions) potřebné k zálohování databází SQL.
3. Ověřte, zda má virtuální počítač [připojení k síti](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
4. Zkontrolujte, zda databáze systému SQL Server jsou pojmenovány v souladu s maticí [pokyny pro pojmenování](#verify-database-naming-guidelines-for-azure-backup) pro službu Azure Backup.
5. Ověřte, že není nutné záložní řešení pro databázi povoleno. Zakažte další zálohy systému SQL Server před instalací tohoto scénáře. Azure Backup pro virtuální počítač Azure společně s Azure Backup můžete povolit pro databázi serveru SQL Server spuštěnou na virtuálním počítači bez jakéhokoli konfliktu.


### <a name="establish-network-connectivity"></a>Vytvoření připojení k síti

Pro všechny operace virtuálního počítače s SQL serverem virtuální počítač vyžaduje připojení k veřejným IP adresám Azure. Operace virtuálního počítače (zjišťování databází, konfigurace zálohování, naplánovat zálohování, obnovení body obnovení a tak dále) nezdaří bez připojení k veřejné IP adresy. Navázat spojení s některou z těchto možností:

- **Povolit rozsahy IP adres Azure datacenter**: Povolit [rozsahy IP adres](https://www.microsoft.com/download/details.aspx?id=41653) v souboru pro stažení. Chcete-li získat přístup k skupina zabezpečení sítě (NSG), použijte **Set-AzureNetworkSecurityRule** rutiny.
- **Nasazení HTTP proxy server pro směrování provozu**: Při zálohování databáze serveru SQL Server na Virtuálním počítači Azure, rozšíření zálohování na virtuálním počítači pomocí protokolu HTTPS rozhraní API odesílat příkazy pro správu Azure Backup a dat do služby Azure Storage. Rozšíření zálohování také používá pro ověřování Azure Active Directory (Azure AD). Směrování provozu linka záložního telefonu pro tyto tři služby prostřednictvím proxy serveru HTTP. Rozšíření jsou jedinou komponentou, která je nakonfigurovaná pro přístup k veřejnému Internetu.

Každá možnost má výhody a nevýhody

**Možnost** | **Výhody** | **Nevýhody**
--- | --- | ---
Povolit rozsahy IP adres | Žádné další náklady. | Komplexní spravovat, protože rozsahy IP adres v průběhu času měnit. <br/><br/> Poskytuje přístup k celé Azure, ne jenom služby Azure Storage.
Použít proxy server HTTP   | Detailní kontrola v proxy serveru úložiště je povolené adresy URL. <br/><br/> Jeden bod internetový přístup k virtuálním počítačům. <br/><br/> Není v souladu s Azure IP adresa změní. | Další náklady na provoz virtuálního počítače se softwarem proxy serveru.

### <a name="set-vm-permissions"></a>Nastavit oprávnění pro virtuální počítač

Azure Backup nemá řada věcí, při konfiguraci zálohování pro databázi serveru SQL Server:

- Přidá **AzureBackupWindowsWorkload** rozšíření.
- Azure Backup se zjistit databáze na virtuálním počítači, vytvoří účet **NT SERVICE\AzureWLBackupPluginSvc**. Tento účet se používá pro zálohování a obnovení a vyžaduje oprávnění správce systému SQL.
- Využívá službu Azure Backup **NT AUTHORITY\SYSTEM** účet za databázi zjišťování nebo dotaz, aby tento účet musí být veřejné přihlášení na SQL.

Pokud jste nevytvořili virtuální počítač SQL Server na Azure Marketplace, k chybě může dojít **UserErrorSQLNoSysadminMembership**. V tomto případě [postupujte podle těchto pokynů](backup-azure-sql-database.md#set-vm-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Zkontrolujte pokyny pro pojmenování databáze pro službu Azure Backup

Vyhněte se níže pro názvy databází:

  * Na konci/úvodní mezery
  * Na konci '!'
  * Zavřít hranatá závorka "]"
  * Databáze s názvem začínajícím "F:\.

Máme aliasů pro Azure table nepodporované znaky, ale doporučujeme vám, že se jim vyhnout. [Další informace](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).


[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Zjistit databáze systému SQL Server

Zjistěte databáze spuštěné na virtuálním počítači.

1. V [webu Azure portal](https://portal.azure.com), otevřete trezor služby Recovery Services můžete použít k zálohování databáze.

2. Na **trezor služby Recovery Services** řídicího panelu, vyberte **zálohování**.

   ![Vybrat zálohu, otevřete nabídku cíl zálohování](./media/backup-azure-sql-database/open-backup-menu.png)

3. V **cíl zálohování**, nastavte **ve kterém je spuštěná vaše úloha** k **Azure** (výchozí).

4. V **co chcete zálohovat**vyberte **systému SQL Server na virtuálním počítači Azure**.

    ![Vyberte pro zálohování serveru SQL Server ve virtuálním počítači Azure](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. V **cíl zálohování** > **zjistit databáze ve virtuálních počítačích**vyberte **spustit zjišťování** vyhledat nechráněné virtuální počítače v rámci předplatného. To může nějakou dobu trvat, v závislosti na počtu nechráněných virtuálních počítačů v rámci předplatného.

   - Nechráněné virtuální počítače by se zobrazit v seznamu po zjištění, seřazené podle názvu a skupinu prostředků.
   - Pokud virtuální počítač není uvedená podle očekávání, zkontrolujte, jestli je už zálohovaná do trezoru.
   - Několik virtuálních počítačů může mít stejný název, ale bude patřit do různých skupin prostředků.

     ![Zálohování čeká na vyřízení při hledání pro databáze ve virtuálních počítačích](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. V seznamu virtuálních počítačů vyberte virtuální počítač s databází serveru SQL Server > **zjišťování databází**.

7. Sledování zjišťování databází v **oznámení** oblasti. Může trvat nějakou dobu se úloha dokončí, v závislosti na tom, kolik databází jsou na virtuálním počítači. Když se zjistí vybrané databáze, se zobrazí zpráva o úspěchu.

    ![Zpráva o úspěšném nasazení](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup zjistí všechny databáze SQL serveru na virtuálním počítači. Během zjišťování dál probíhá na pozadí:

    - Azure Backup zaregistrujete pomocí úložiště pro úlohy zálohování virtuálního počítače. Všechny databáze na registrované virtuálních počítačů můžete zálohovat jenom k tomuto trezoru.
    - Azure Backup nainstaluje **AzureBackupWindowsWorkload** rozšíření ve virtuálním počítači. V SQL database se neinstaluje žádný agent.
    - Vytvoří účet služby Azure Backup **NT Service\AzureWLBackupPluginSvc** na virtuálním počítači.
      - Všechny operace zálohování a obnovení pomocí účtu služby.
      - **NT Service\AzureWLBackupPluginSvc** potřebuje oprávnění správce systému SQL. Všechny virtuální počítače SQL serveru, vytvořené na webu Azure Marketplace jsou dostupné **SqlIaaSExtension** nainstalované. **AzureBackupWindowsWorkload** používá rozšíření **SQLIaaSExtension** automaticky získat požadované oprávnění.
    - Pokud jste nevytvořili virtuální počítač z marketplace, pak virtuální počítač nemá **SqlIaaSExtension** nainstalované, a operace zjišťování se nezdaří s chybovou zprávou **UserErrorSQLNoSysAdminMembership**. Postupujte podle [pokyny](backup-azure-sql-database.md#set-vm-permissions) chcete tento problém vyřešit.

        ![Vyberte virtuální počítač a databáze](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Konfigurace zálohování  

Konfigurace zálohování následujícím způsobem:

1. V **cíl zálohování** vyberte **konfigurace zálohování**.

   ![Vyberte konfiguraci zálohování](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Klikněte na tlačítko **konfigurace zálohování**, **vybrat položky k zálohování** otevře se okno. Tady se uvádí všechny registrované dostupnosti skupiny a samostatné servery SQL. Rozbalte na dvojitou šipku vlevo od řádku, který má v této instanci nebo vždy v AG naleznete v tématu nechráněných databází.  

    ![Zobrazení všech instancí systému SQL Server u samostatných databází](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Vyberte všechny databáze, které chcete chránit > **OK**.

   ![Ochranu databáze](./media/backup-azure-sql-database/select-database-to-protect.png)

   K optimalizaci zálohování zatížení, Azure Backup Nastaví maximální počet databází ve jediná úloha zálohování na 50.

    
     * Alternativně můžete povolit automatickou ochranu celého instance nebo skupina vždy na dostupnosti tak, že vyberete **ON** možnost v rozevíracím seznamu příslušné v **AUTOPROTECT** sloupce. Funkce Automatické ochrany nejen povolí ochranu pro všechny existující databáze najednou, ale také automaticky chrání jakýchkoli nových databází, které budou přidány do této instance nebo skupina dostupnosti v budoucnosti.  

4. Klikněte na tlačítko **OK** otevřít **zásady zálohování** okno.

    ![Povolit automatické ochrany na skupiny dostupnosti Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. V **výběr zásady zálohování**, vyberte zásadu a pak klikněte na tlačítko **OK**.

   - Vyberte výchozí zásady: HourlyLogBackup.
   - Vyberte existující zásadu zálohování dříve vytvořili pro SQL.
   - Definujte novou zásadu na základě cíle bodu obnovení a uchovávání rozsahu.

     ![Vyberte zásady zálohování](./media/backup-azure-sql-database/select-backup-policy.png)

6. Na **zálohování** nabídce vyberte možnost **povolit zálohování**.

    ![Povolit vybrané zásady zálohování](./media/backup-azure-sql-database/enable-backup-button.png)

7. Sledovat průběh na konfiguraci **oznámení** části portálu.

    ![Oznamovací oblasti](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Vytvoření zásady zálohování

Zásady zálohování definují, kdy zálohy jsou prováděny a jak dlouho se uchovávají.

- Zásady se vytvářejí na úrovni trezoru.
- Více trezorů můžete použít stejné zásady zálohování, ale musíte použít zásadu zálohování, která pro každý trezor.
- Při vytváření zásady zálohování denně úplné zálohování je výchozí hodnota.
- Rozdílové zálohování, ale pouze můžete přidat, pokud nakonfigurujete týdenních úplných záloh.
- [Další informace o](backup-architecture.md#sql-server-backup-types) různé druhy zásad zálohování.

Vytvoření zásady zálohování:

1. V trezoru, klikněte na tlačítko **zásady zálohování** > **přidat**.
2. V **přidat** nabídky, klikněte na tlačítko **systému SQL Server na virtuálním počítači Azure**. Definuje typ zásad.

   ![Vyberte typ zásad pro nové zásady zálohování](./media/backup-azure-sql-database/policy-type-details.png)

3. V **Název_zásady**, zadejte název pro novou zásadu.
4. V **úplná záloha zásad**, vyberte **četnost záloh**, zvolte **denní** nebo **týdenní**.

   - Pro **denní**, vyberte hodiny a časové pásmo po zahájení úlohy zálohování.
   - Jak se nedá vypnout, je nutné spustit úplné zálohování **úplné zálohování** možnost.
   - Klikněte na tlačítko **úplná záloha** zásadách.
   - Nelze vytvořit rozdílové zálohování pro denní úplná zálohování.
   - Pro **týdenní**, vyberte den v týdnu, hodiny a časové pásmo po zahájení úlohy zálohování.

     ![Nová pole zásady zálohování](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Pro **rozsah uchování**, ve výchozím nastavení jsou vybrané všechny možnosti. Vymažte všechny omezení rozsahu nežádoucí uchovávání, které nechcete používat a nastavených intervalech používat.

    - Období minimální doby uchování pro jakýkoli typ zálohování (úplné a rozdílové/protokolu) je 7 dní.
    - Body obnovení jsou označené pro uchovávání informací podle jejich rozsah uchování. Například pokud vyberete každodenní úplnou zálohu, se aktivuje pouze jeden úplné zálohování každý den.
    - Zálohování pro určitý den je příznakem a uchovávají na základě týdenní rozsah uchování a nastavení týdenního uchovávání informací.
    - Měsíční a roční rozsahy uchovávání se chovají podobně.

   ![Nastavení intervalu rozsah uchování](./media/backup-azure-sql-database/retention-range-interval.png)

6. V **úplná záloha zásad** nabídce vyberte možnost **OK** potvrďte nastavení.
7. Chcete-li přidat zásadu pro rozdílové zálohování, **rozdílové zálohování**.

   ![Nastavení intervalu rozsah uchování](./media/backup-azure-sql-database/retention-range-interval.png)
   ![otevřete nabídku zásady rozdílové zálohování](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. V **rozdílovou zálohu zásad**vyberte **povolit** otevřete ovládací prvky frekvence a uchování.

    - Maximálně můžete aktivovat jeden rozdílové zálohy za den.
    - Rozdílové zálohy je možné uchovávat maximálně na 180 dnů. Pokud budete potřebovat delší dobu uchování, musíte použít úplné zálohy.

9. Vyberte **OK** zásadu uložte a vraťte se do hlavní **zásady zálohování** nabídky.

10. Chcete-li přidat zásadu zálohování transakčního protokolu, **zálohování protokolu**.
11. V **záloha protokolu**vyberte **povolit**a potom nastavte ovládací prvky frekvence a uchování. Zálohy protokolů může dojít, tak často, jak každých 15 minut a můžou uchovávat až 35 dnů.
12. Vyberte **OK** zásadu uložte a vraťte se do hlavní **zásady zálohování** nabídky.

    ![Upravit zásady zálohování protokolu](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. Na **zásady zálohování** nabídce vyberte, jestli chcete povolit **kompresi zálohování SQL**.
    - Komprese je ve výchozím nastavení zakázána.
    - Na back-endu Azure Backup použije nativní kompresi záloh SQL.

14. Po dokončení úprav zásady zálohování vyberte **OK**.

## <a name="run-an-ad-hoc-backup"></a>Spustit zálohování ad hoc

1. V trezoru služby Recovery Services vyberte zálohování položek.
2. Klikněte na "SQL na virtuálním počítači Azure".
3. Klikněte pravým tlačítkem na databázi a zvolte možnost "Zálohovat nyní".
4. Zvolte typ zálohování (pouze úplné úplné nebo rozdílové/Log/kopírování) a komprese (povolit nebo zakázat)
5. Klepněte na tlačítko OK zahájíte zálohování.
6. Monitorování úlohy zálohování tak, že přejdete do vašeho trezoru služby Recovery Services a zvolíte "Úlohy zálohování".


## <a name="next-steps"></a>Další postup

V tomto kurzu jste pomocí webu Azure Portal provedli následující kroky:

> [!div class="checklist"]
> * Vytvořte a nakonfigurujte trezor.
> * Najít databáze a nastavení zálohování.
> * Nastavte si Automatická ochrana databáze.
> * Spusťte zálohování ad hoc.

Přejděte k dalšímu kurzu, kde obnovíte virtuální počítač Azure z disku.

> [!div class="nextstepaction"]
> [Obnovení databází serveru SQL Server na virtuálních počítačích Azure](./restore-sql-database-azure-vm.md)
 

