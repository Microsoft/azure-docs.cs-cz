---
title: Zálohování databází systému SQL Server na virtuálních počítačích Azure | Dokumentace Microsoftu
description: Zjistěte, jak zálohovat databáze SQL serveru na virtuálních počítačích Azure
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: sachdevaswati
ms.openlocfilehash: 0307dc5c83782119f6c10279563b8b9f0a999d28
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236884"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Zálohování databází SQL Serveru ve virtuálních počítačích Azure

Databáze systému SQL Server jsou důležité úlohy, které vyžadují cíl s nízkou bodu obnovení (RPO) a dlouhodobé uchovávání. Můžete zálohovat databáze systému SQL Server běžící na virtuálních počítačích Azure (VM) pomocí [Azure Backup](backup-overview.md).

Tento článek ukazuje, jak zálohovat databázi systému SQL Server, na kterém běží na Virtuálním počítači Azure do trezoru služby Azure Backup Recovery Services.

V tomto článku se dozvíte jak:

> [!div class="checklist"]
> * Vytvořte a nakonfigurujte trezor.
> * Najít databáze a nastavení zálohování.
> * Nastavte si Automatická ochrana databáze.


## <a name="prerequisites"></a>Požadavky

Než budete zálohovat databázi systému SQL Server, zkontrolujte následující kritéria:

1. Určete nebo vytvořte [trezor služby Recovery Services](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) ve stejné oblasti nebo národní prostředí jako virtuální počítač, který je hostitelem instance serveru SQL Server.
2. Zkontrolujte [potřebná oprávnění pro virtuální počítač](backup-azure-sql-database.md#fix-sql-sysadmin-permissions) zálohování databází SQL.
3. Ověřte, zda má virtuální počítač [připojení k síti](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
4. Ujistěte se, že databáze systému SQL Server, postupujte [pokyny pro pojmenování databáze pro službu Azure Backup](#database-naming-guidelines-for-azure-backup).
5. Zkontrolujte, že není nutné záložní řešení pro databázi povoleno. Zakažte další zálohy systému SQL Server před zálohováním databáze.

> [!NOTE]
> Azure Backup můžete povolit pro virtuální počítač Azure a také pro databázi serveru SQL Server spuštěnou na virtuálním počítači bez konfliktu.


### <a name="establish-network-connectivity"></a>Vytvoření připojení k síti

Pro všechny operace virtuálního počítače s SQL serverem vyžaduje připojení k veřejným IP adresám Azure. Operace virtuálního počítače (zjišťování databází, konfigurace zálohování, naplánovat zálohování, obnovení body obnovení a tak dále) nezdaří bez připojení k veřejným IP adresám Azure.

Navázat připojení pomocí jedné z následujících možností:

- **Povolit rozsahy IP adres Azure datacenter**. Tato možnost umožňuje [rozsahy IP adres](https://www.microsoft.com/download/details.aspx?id=41653) v souboru pro stažení. Pro přístup k skupinu zabezpečení sítě (NSG), použijte rutinu Set-AzureNetworkSecurityRule. Pokud jste bezpečné příjemci seznam IP adres pouze konkrétní oblasti, budete také potřebovat k aktualizaci seznamu bezpečných příjemců značka služby Azure Active Directory (Azure AD) Pokud chcete povolit ověřování.

- **Povolit přístup pomocí značek NSG**. Pokud používáte skupiny zabezpečení sítě k omezení připojení, tato možnost přidá do vaší skupiny NSG, která umožňuje odchozí přístup ke službě Azure Backup pomocí značky AzureBackup pravidlo. Kromě této značky, budete také potřebovat odpovídající [pravidla](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) pro službu Azure AD a Azure Storage umožňující připojení k ověřování a dat přenosu. Značka AzureBackup je pouze aktuálně dostupné v prostředí PowerShell. Chcete-li vytvořit pravidlo s použitím AzureBackup značky:

    - Přidat přihlašovací údaje pro účet Azure a aktualizujte národních cloudů<br/>
    `Add-AzureRmAccount`

    - Vyberte předplatné, skupina zabezpečení sítě<br/>
    `Select-AzureRmSubscription "<Subscription Id>"`

     - Vyberte skupiny zabezpečení sítě<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

    - Přidat odchozí pravidlo pro značku služby Azure Backup povolit<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

  - Uložit skupinu zabezpečení sítě<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`
- **Povolit přístup s použitím značky Azure bránu Firewall**. Pokud používáte Firewall služby Azure, vytvořte pravidlo aplikace pomocí AzureBackup [plně kvalifikovaný název domény značka](https://docs.microsoft.com/azure/firewall/fqdn-tags). To umožňuje odchozí přístup ke službě Azure Backup.
- **Nasazení HTTP proxy server pro směrování provozu**. Při zálohování databáze serveru SQL Server na Virtuálním počítači Azure, rozšíření zálohování na virtuálním počítači pomocí rozhraní API HTTPS příkazy pro správu odesílat data do služby Azure Storage a Azure Backup. Rozšíření zálohování také používá Azure AD pro ověřování. Směrování provozu linka záložního telefonu pro tyto tři služby prostřednictvím proxy serveru HTTP. Rozšíření jsou jedinou komponentou, která je nakonfigurovaná pro přístup k veřejnému Internetu.

Možnosti připojení patří následující výhody a nevýhody:

**Možnost** | **Výhody** | **Nevýhody**
--- | --- | ---
Povolit rozsahy IP adres | Žádné další náklady | Komplexní spravovat, protože rozsahy IP adres v průběhu času měnit <br/><br/> Poskytuje přístup k celé Azure, ne jenom služby Azure Storage
Použití značek služby skupiny zabezpečení sítě | Jednodušší spravovat stejným způsobem jako rozsah změny sloučit automaticky <br/><br/> Žádné další náklady <br/><br/> | Lze použít pomocí skupin zabezpečení sítě pouze <br/><br/> Poskytuje přístup k celé služby
Použití značek plně kvalifikovaný název domény Azure bránu Firewall | Usnadňují správu, jako jsou automaticky spravované vyžaduje plně kvalifikované názvy domén | Lze použít s bránou Firewall služby Azure pouze
Použít proxy server HTTP | Detailní kontrola v proxy serveru úložiště adresy URL je povolený. <br/><br/> Jeden bod internetový přístup k virtuálním počítačům <br/><br/> Není v souladu s změny Azure IP adresy | Další náklady na provoz virtuálního počítače se softwarem proxy

### <a name="set-vm-permissions"></a>Nastavit oprávnění pro virtuální počítač

Při konfiguraci zálohování pro databázi serveru SQL Server, Azure Backup provede následující akce:

- Přidá AzureBackupWindowsWorkload rozšíření.
- Vytvoří účet NT SERVICE\AzureWLBackupPluginSvc se zjistit databáze na virtuálním počítači. Tento účet se používá pro účely zálohování a obnovení a vyžaduje oprávnění správce systému SQL.
- Zjistí databáze, které jsou spuštěny na virtuálním počítači s Azure Backup používá účet NT AUTHORITY\SYSTEM. Tento účet musí být u veřejných přihlášení na SQL.

Pokud jste nevytvořili virtuální počítač SQL serverem na webu Azure Marketplace, UserErrorSQLNoSysadminMembership chybě může dojít. Další informace najdete v části aspektů a omezení funkce najdete v [o zálohování SQL serveru na virtuálních počítačích Azure](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

### <a name="database-naming-guidelines-for-azure-backup"></a>Databáze pokyny pro pojmenování pro službu Azure Backup

Nepoužívejte následující prvky v názvy databází:

  * Na konci a úvodní mezery
  * Na konci značky vykřičník (!)
  * Koncovou hranatou závorku (])
  * Středník (;)
  * Dopředné lomítko "/"

Vytváření aliasů je k dispozici pro nepodporované znaky, ale doporučujeme vám, že se jim vyhnout. Další informace najdete v tématu [Vysvětlení datového modelu služby Table Storage](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).


[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Zjistit databáze systému SQL Server

Jak zjistit databáze spuštěné na virtuálním počítači:

1. V [webu Azure portal](https://portal.azure.com), otevřete trezor služby Recovery Services můžete použít k zálohování databáze.

2. V **trezor služby Recovery Services** řídicího panelu, vyberte **zálohování**.

   ![Vybrat zálohu, otevřete nabídku cíl zálohování](./media/backup-azure-sql-database/open-backup-menu.png)

3. V **cíl zálohování**, nastavte **ve kterém je spuštěná vaše úloha?** k **Azure**.

4. V **co chcete zálohovat**vyberte **systému SQL Server na virtuálním počítači Azure**.

    ![Vyberte pro zálohování serveru SQL Server ve virtuálním počítači Azure](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. V **cíl zálohování** > **zjistit databáze ve virtuálních počítačích**vyberte **spustit zjišťování** vyhledat nechráněné virtuální počítače v rámci předplatného. Toto vyhledávání může chvíli trvat, v závislosti na počtu nechráněné virtuální počítače v rámci předplatného.

   - Nechráněné virtuální počítače by se zobrazit v seznamu po zjištění, seřazené podle názvu a skupinu prostředků.
   - Pokud virtuální počítač není uvedená podle očekávání, podívejte se, jestli je už zálohovaná do trezoru.
   - Několik virtuálních počítačů může mít stejný název, ale bude patřit do různých skupin prostředků.

     ![Zálohování čeká na vyřízení při hledání pro databáze ve virtuálních počítačích](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. V seznamu virtuálních počítačů vyberte virtuální počítač s databází serveru SQL Server > **zjišťování databází**.

7. Sledování zjišťování databází v **oznámení**. Čas potřebný pro tuto akci závisí na počtu databází virtuálního počítače. Když se zjistí vybrané databáze, se zobrazí zpráva o úspěchu.

    ![Zpráva o úspěšném nasazení](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup zjistí všechny databáze SQL serveru na virtuálním počítači. Během zjišťování dojde k následující elementy na pozadí:

    - Azure Backup zaregistruje virtuální počítač s úložištěm pro úlohy zálohování. K tomuto trezoru pouze dají zálohovat všechny databáze na registrované virtuálního počítače.
    - Azure Backup nainstaluje rozšíření AzureBackupWindowsWorkload na virtuálním počítači. SQL Database se neinstaluje žádný agent.
    - Azure Backup vytvoří účet služby NT Service\AzureWLBackupPluginSvc na virtuálním počítači.
      - Všechny operace zálohování a obnovení pomocí účtu služby.
      - NT Service\AzureWLBackupPluginSvc vyžaduje oprávnění správce systému SQL. Všechny virtuální počítače SQL serveru, vytvořené na webu Marketplace součástí SqlIaaSExtension nainstalované. Rozšíření AzureBackupWindowsWorkload používá SQLIaaSExtension automaticky získat požadované oprávnění.
    - Pokud jste nevytvořili virtuální počítač z Marketplace, virtuální počítač nebude mít SqlIaaSExtension nainstalovaný a operace zjišťování se nezdaří s chybovou zprávou UserErrorSQLNoSysAdminMembership. Pokud chcete tento problém vyřešit, postupujte [pokyny](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

        ![Vyberte virtuální počítač a databáze](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Konfigurace zálohování  

1. V **cíl zálohování** > **krok 2: Konfigurace služby Backup**vyberte **konfigurace zálohování**.

   ![Vyberte konfiguraci zálohování](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. V **vybrat položky k zálohování**, uvidíte všechny registrované dostupnosti skupiny a samostatné instance serveru SQL Server. Vyberte šipku vlevo od řádku, který má rozšířit seznam nechráněných databází v této instance nebo skupiny dostupnosti Always On.  

    ![Zobrazení všech instancí systému SQL Server u samostatných databází](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Zvolte všechny databáze, které chcete chránit a pak vyberte **OK**.

   ![Ochranu databáze](./media/backup-azure-sql-database/select-database-to-protect.png)

   K optimalizaci zálohování zatížení, Azure Backup Nastaví maximální počet databází ve jediná úloha zálohování na 50.

     * K ochraně více než 50 databází, nakonfigurujte více záloh.
     * Chcete-li povolit [ ](#enable-auto-protection) celý instance nebo skupiny dostupnosti Always On. V **AUTOPROTECT** rozevíracího seznamu vyberte **ON**a pak vyberte **OK**.

    > [!NOTE]
    > [Automatickou ochranu](#enable-auto-protection) funkci nejen umožňuje ochranu pro všechny existující databáze najednou, ale také automaticky chrání všechny nové databáze, přidat do této instance nebo skupiny dostupnosti.  

4. Vyberte **OK** otevřete **zásady zálohování**.

    ![Povolit automatickou ochranu pro skupiny dostupnosti Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. V **zásady zálohování**, zvolte zásadu a pak vyberte **OK**.

   - Vyberte výchozí zásady jako HourlyLogBackup.
   - Vyberte existující zásadu zálohování dříve vytvořili pro SQL.
   - Definujte novou zásadu na základě cíle bodu obnovení a uchovávání rozsahu.

     ![Vyberte zásady zálohování](./media/backup-azure-sql-database/select-backup-policy.png)

6. V **zálohování**vyberte **povolit zálohování**.

    ![Povolit vybrané zásady zálohování](./media/backup-azure-sql-database/enable-backup-button.png)

7. Sledovat průběh na konfiguraci **oznámení** části portálu.

    ![Oznamovací oblasti](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Vytvoření zásady zálohování

Zásady zálohování definují, kdy zálohy jsou prováděny a jak dlouho se uchovávají.

- Zásady se vytvářejí na úrovni trezoru.
- Více trezorů můžete použít stejné zásady zálohování, ale musíte použít zásadu zálohování, která pro každý trezor.
- Při vytváření zásady zálohování denně úplné zálohování je výchozí hodnota.
- Rozdílové zálohování, ale pouze můžete přidat, pokud nakonfigurujete týdenních úplných záloh.
- Další informace o [různé druhy zásad zálohování](backup-architecture.md#sql-server-backup-types).

Vytvoření zásady zálohování:

1. V trezoru, vyberte **zásady zálohování** > **přidat**.
2. V **přidat**vyberte **systému SQL Server na virtuálním počítači Azure** definovat typ zásad.

   ![Vyberte typ zásad pro nové zásady zálohování](./media/backup-azure-sql-database/policy-type-details.png)

3. V **Název_zásady**, zadejte název pro novou zásadu.
4. V **úplná záloha zásad**, vyberte **četnost záloh**. Zvolte buď **denní** nebo **týdenní**.

   - Pro **denní**, vyberte hodiny a časové pásmo po zahájení úlohy zálohování.
   - Pro **týdenní**, vyberte den v týdnu, hodiny a časové pásmo po zahájení úlohy zálohování.
   - Spustit úplnou zálohu, protože se nedá vypnout **úplné zálohování** možnost.
   - Vyberte **úplná záloha** zásadách.
   - Nelze vytvořit rozdílové zálohování pro denní úplná zálohování.

     ![Nová pole zásady zálohování](./media/backup-azure-sql-database/full-backup-policy.png)  

5. V **rozsah uchování**, ve výchozím nastavení jsou vybrané všechny možnosti. Vymazat všechny rozsah uchování omezení, že není vhodné a nastavte intervalech použijte.

    - Období minimální doby uchování pro jakýkoli typ zálohování (úplného, rozdílového a protokol) je sedm dní.
    - Body obnovení jsou označené pro uchovávání informací podle jejich rozsah uchování. Například pokud vyberete každodenní úplnou zálohu, se aktivuje pouze jeden úplné zálohování každý den.
    - Zálohování pro určitý den je příznakem a uchovávají na základě týdenní rozsah uchování a týdenní nastavení uchování.
    - Měsíční a roční rozsahy uchovávání se chovají podobně.

       ![Nastavení intervalu rozsah uchování](./media/backup-azure-sql-database/retention-range-interval.png)

6. V **úplná záloha zásad** nabídce vyberte možnost **OK** potvrďte nastavení.
7. Chcete-li přidat zásadu pro rozdílové zálohování, **rozdílové zálohování**.

   ![Nastavení intervalu rozsah uchování](./media/backup-azure-sql-database/retention-range-interval.png)
   ![otevřete nabídku zásady rozdílové zálohování](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. V **rozdílovou zálohu zásad**vyberte **povolit** otevřete ovládací prvky frekvence a uchování.

    - Můžete aktivovat pouze jeden rozdílové zálohy za den.
    - Rozdílové zálohy je možné uchovávat maximálně na 180 dnů. Pro delší dobu uchování pomocí úplné zálohy.

9. Vyberte **OK** zásadu uložte a vraťte se do hlavní **zásady zálohování** nabídky.

10. Chcete-li přidat zásadu zálohování transakčního protokolu, **zálohování protokolu**.
11. V **záloha protokolu**vyberte **povolit**a potom nastavte ovládací prvky frekvence a uchování. Zálohy protokolů může dojít, tak často, jak každých 15 minut a můžou uchovávat až 35 dnů.
12. Vyberte **OK** zásadu uložte a vraťte se do hlavní **zásady zálohování** nabídky.

    ![Upravit zásady zálohování protokolu](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. Na **zásady zálohování** nabídce vyberte, jestli chcete povolit **kompresi zálohování SQL**.
    - Komprese je ve výchozím nastavení zakázána.
    - Na back-endu Azure Backup použije nativní kompresi záloh SQL.

14. Po dokončení úprav zásady zálohování vyberte **OK**.


### <a name="modify-policy"></a>Upravit zásady
Upravte zásady změnit frekvenci nebo uchování rozsah zálohování.

> [!NOTE]
> Žádné změny v období uchovávání informací se použijí zpětně na všech starších bodů obnovení kromě nové značky.

V řídicím panelu trezoru přejděte na **spravovat** > **zásady zálohování** a vyberte zásadu, kterou chcete upravit.

  ![Správa zásad zálohování](./media/backup-azure-sql-database/modify-backup-policy.png)


## <a name="enable-auto-protection"></a>Povolit automatickou ochranu  

Můžete povolit automatickou ochranu automaticky zálohovat všechny existující a budoucí databáze samostatná instance SQL serveru nebo do skupiny dostupnosti Always On.

- Neexistuje žádné omezení počtu databází, které můžete vybrat pro automatickou ochranu najednou.
- Selektivně nemůže chránit nebo vyloučit databáze z ochrany v instanci v době povolíte automatické ochrany.
- Pokud vaše instance již obsahuje některé chráněné databáze, budete zůstanou chráněné v rámci svých odpovídajících zásad i po zapnout automatickou ochranu. Všechny nechráněných databází přidat později budou mít jenom jednu zásadu, která definujete v době povolení automatické ochrany, uvedený v části **konfigurace zálohování**. Můžete však změnit zásady přidružené k databázi automaticky chráněny později.  

Pokud chcete povolit automatickou ochranu:

  1. V **položky k zálohování**, vyberte instanci, pro kterou chcete povolit automatickou ochranu.
  2. Vyberte rozevírací seznam v části **AUTOPROTECT**, zvolte **ON**a pak vyberte **OK**.

      ![Povolit automatické ochrany na skupině dostupnosti](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Zálohování je nakonfigurovaný pro všechny databáze společně a lze sledovat v **úlohy zálohování**.

Pokud je potřeba zakázat automatickou ochranu, vyberte název instance v rámci **konfigurace zálohování**a pak vyberte **zakázat Autoprotect** pro instanci. Všechny databáze budou i nadále zálohovat, ale budoucí databáze nebudou chráněné automaticky.

![Zakažte automatickou ochranu pro tuto instanci](./media/backup-azure-sql-database/disable-auto-protection.png)

 
## <a name="next-steps"></a>Další postup

Naučte se:

- [Obnovit zálohování databází systému SQL Server](restore-sql-database-azure-vm.md)
- [Správa zálohování databází systému SQL Server](manage-monitor-sql-database-backup.md)
