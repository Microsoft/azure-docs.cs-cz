---
title: Zálohování databází SQL Serveru ve virtuálních počítačích Azure
description: V tomto článku se dozvíte, jak zálohovat databáze SQL Serveru na virtuálních počítačích Azure pomocí Azure Backup.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 5b10907738feeecbec06669175e82578f2915f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273329"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Zálohování databází SQL Serveru ve virtuálních počítačích Azure

Sql Server databáze jsou kritické úlohy, které vyžadují nízký cíl bodu obnovení (RPO) a dlouhodobé uchovávání. Databáze SQL Serveru spuštěné na virtuálních počítačích Azure (VM) můžete zálohovat pomocí [Azure Backup](backup-overview.md).

Tento článek ukazuje, jak zálohovat databázi SQL Serveru, která běží na virtuálním počítači Azure do trezoru služby Obnovení zálohování Azure.

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
>
> * Vytvořte a nakonfigurujte úschovnu.
> * Objevte databáze a nastavte zálohy.
> * Nastavte automatickou ochranu databází.

>[!NOTE]
>**Obnovitelné odstranění pro SQL server v Azure VM a obnovitelné odstranění pro SAP HANA v úlohách virtuálních virtuálních počítače Azure** je teď dostupné ve verzi Preview.<br>
>Chcete-li se zaregistrovat do náhledu, napište nám naAskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Požadavky

Před zálohováním databáze serveru SQL Server zkontrolujte následující kritéria:

1. Identifikujte nebo vytvořte [trezor služby Recovery Services](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) ve stejné oblasti a předplatné jako virtuální modul hostující instanci serveru SQL Server.
2. Ověřte, zda má virtuální [hotel připojení k síti](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
3. Ujistěte se, že databáze serveru SQL Server postupujte podle [pokynů pro pojmenování databáze pro zálohování Azure](#database-naming-guidelines-for-azure-backup).
4. Zkontrolujte, zda pro databázi není povolena žádná jiná řešení zálohování. Před zálohováním databáze zakažte všechny ostatní zálohy serveru SQL Server.

> [!NOTE]
> Azure Backup můžete povolit pro virtuální počítač Azure a také pro databázi SQL Serveru spuštěnou na virtuálním počítači bez konfliktu.

### <a name="establish-network-connectivity"></a>Navázání připojení k síti

Pro všechny operace vyžaduje virtuální počítač SQL Server připojení k veřejným IP adresám Azure. Operace virtuálních počítačů (zjišťování databáze, konfigurace zálohování, plánování zálohování, body obnovení a tak dále) se nezdaří bez připojení k veřejným IP adresám Azure.

Navázání připojení pomocí jedné z následujících možností:

#### <a name="allow-the-azure-datacenter-ip-ranges"></a>Povolení rozsahů IP adres datového centra Azure

Tato možnost umožňuje [rozsahy IP adres](https://www.microsoft.com/download/details.aspx?id=41653) ve staženém souboru. Chcete-li získat přístup ke skupině zabezpečení sítě (NSG), použijte rutinu Set-AzureNetworkSecurityRule. Pokud seznam bezpečných příjemců obsahuje jenom IP adresy specifické pro oblast, budete muset aktualizovat seznam bezpečných příjemců, značku služby Azure Active Directory (Azure AD), abyste povolili ověřování.

#### <a name="allow-access-using-nsg-tags"></a>Povolit přístup pomocí značek NSG

Pokud používáte nsg k omezení připojení, pak byste měli použít azurebackup značku služby umožňuje odchozí přístup k Azure Backup. Kromě toho byste měli také povolit připojení pro ověřování a přenos dat pomocí [pravidel](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) pro Azure AD a Azure Storage. To lze provést z portálu Azure nebo přes PowerShell.

Vytvoření pravidla pomocí portálu:

  1. Ve **všech službách**přejděte na **skupiny zabezpečení sítě** a vyberte skupinu zabezpečení sítě.
  2. V části **Nastavení**vyberte **odchozí pravidla zabezpečení** .
  3. Vyberte **Přidat**. Zadejte všechny požadované podrobnosti pro vytvoření nového pravidla, jak je popsáno v [nastavení pravidel zabezpečení](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Ujistěte se, že možnost **Cíl** je nastavena na **výrobní číslo** a **cílová značka je** nastavena na **AzureBackup**.
  4. Klepnutím na tlačítko **Přidat**uložte nově vytvořené pravidlo odchozího zabezpečení.

Vytvoření pravidla pomocí PowerShellu:

 1. Přidání přihlašovacích údajů k účtu Azure a aktualizace národních cloudů<br/>
      `Add-AzureRmAccount`<br/>

 2. Vyberte předplatné nsg<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. Vyberte skupina o národní nenasytní.<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. Přidání pravidla povolení odchozího pro značku služby Azure Backup<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. Přidání pravidla povolení pro značku služby Úložiště<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. Přidání pravidla povolení odchozího pro značku služby AzureActiveDirectory<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. Uložit nsg<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**Povolit přístup pomocí značek Azure Firewall**. Pokud používáte Azure Firewall, vytvořte pravidlo aplikace pomocí značky AzureBackup [FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags). To umožňuje odchozí přístup k Azure Backup.

**Nasazení serveru proxy HTTP pro směrování provozu**. Když zálohujete databázi SQL Serveru na virtuálním počítači Azure, rozšíření pro zálohování na virtuálním počítači používá rozhraní API HTTPS k odesílání příkazů pro správu do Azure Backup a dat do Azure Storage. Rozšíření zálohování také používá Azure AD pro ověřování. Směrovat provoz rozšíření zálohy pro tyto tři služby prostřednictvím proxy serveru HTTP. Neexistují žádné domény se zástupnými symboly, které by se používaly s Azure Backup, které by bylo možné přidat do seznamu povolených pro pravidla serveru proxy. Budete muset použít rozsahy veřejných IP adres pro tyto služby poskytované Azure. Rozšíření jsou jedinou součástí, která je nakonfigurována pro přístup k veřejnému internetu.

Možnosti připojení zahrnují následující výhody a nevýhody:

**Možnost** | **Výhody** | **Nevýhody**
--- | --- | ---
Povolit rozsahy IP adres | Žádné další náklady | Komplexní správa, protože rozsahy IP adres se v průběhu času mění <br/><br/> Poskytuje přístup k celému Azure, nejen k Úložišti Azure.
Použití značek služby NSG | Snadnější správa při automatickém sloučení změn rozsahu <br/><br/> Žádné další náklady <br/><br/> | Lze použít pouze s nsgs <br/><br/> Poskytuje přístup k celé službě
Použití značek FQDN brány Azure Firewall | Snadnější správa při automatické správě požadovaných souborů FQDN | Lze použít pouze s Azure Firewall
Použití proxy serveru HTTP | Jednotný bod přístupu k internetu k virtuálním operátorům <br/> | Dodatečné náklady na spuštění virtuálního počítače se softwarem proxy <br/> Žádné publikované adresy VPN, pravidla povolení budou podléhat změnám IP adres Azure

#### <a name="private-endpoints"></a>Soukromé koncové body

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

### <a name="database-naming-guidelines-for-azure-backup"></a>Pokyny pro pojmenování databáze pro Azure Backup

Vyhněte se použití následujících prvků v názvech databází:

* Koncové a vedoucí prostory
* Koncové vykřičníky (!)
* Uzavírací hranaté závorky (])
* Středník ';'
* Lomítko přemítka '/'

Aliasing je k dispozici pro nepodporované znaky, ale doporučujeme jim vyhnout. Další informace najdete v tématu [Vysvětlení datového modelu služby Table Storage](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

>[!NOTE]
>Operace **Konfigurovat ochranu** pro databáze se speciálními znaky, jako je "+" nebo "&" v jejich názvu není podporována. Můžete změnit název databáze nebo povolit **automatickou ochranu**, která může tyto databáze úspěšně chránit.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Zjišťování databází serveru SQL Server

Jak zjistit databáze spuštěné na virtuálním počítači:

1. Na [webu Azure Portal](https://portal.azure.com)otevřete trezor služby Recovery Services, který používáte k zálohování databáze.

2. Na řídicím panelu **trezoru služby Recovery Services** vyberte **možnost Zálohovat**.

   ![Výběrem možnosti Zálohování otevřete nabídku Cíl zálohování.](./media/backup-azure-sql-database/open-backup-menu.png)

3. V **cíli zálohování** **Azure** **nastavte, kde běží vaše úloha?**

4. V **článku Co chcete zálohovat**vyberte SQL Server v **virtuálním počítači Azure**.

    ![Vyberte SQL Server v virtuálním počítači Azure pro zálohování](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. V **části Cíl** > zálohování**Objevte centrální paměti ve virtuálních počítačích**vyberte **Spustit zjišťování** a vyhledejte nechráněné virtuální počítače v předplatném. Toto hledání může chvíli trvat, v závislosti na počtu nechráněných virtuálních počítačích v předplatném.

   * Nechráněné virtuální počítače by se měly zobrazit v seznamu po zjišťování, uvedené podle názvu a skupiny prostředků.
   * Pokud virtuální může být vypsáno podle očekávání, zjistěte, jestli už je zálohovaný v trezoru.
   * Více virtuálních počítačů může mít stejný název, ale budou patřit do různých skupin prostředků.

     ![Zálohování čeká na vyřízení při hledání DBs ve virtuálních počítačech](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. V seznamu virtuálních společností vyberte virtuální ms se spuštěnou databází serveru SQL Server > **zjišťování dbs**.

7. Sledování zjišťování databáze v **oznámeních**. Čas potřebný pro tuto akci závisí na počtu databází virtuálních počítačích. Po zjištění vybraných databází se zobrazí zpráva o úspěchu.

    ![Zpráva o úspěchu nasazení](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup zjišťuje všechny databáze SQL Serveru na virtuálním počítači. Během zjišťování se na pozadí vyskytují následující prvky:

    * Azure Backup registruje virtuální počítač s trezorem pro zálohování úloh. Všechny databáze na registrovaném virtuálním počítači lze zálohovat pouze do tohoto trezoru.
    * Azure Backup nainstaluje rozšíření AzureBackupWindowsWorkload na virtuálním počítači. V databázi SQL není nainstalován žádný agent.
    * Azure Backup vytvoří účet služby NT Service\AzureWLBackupPluginSvc na virtuálním počítači.
      * Všechny operace zálohování a obnovení používají účet služby.
      * Nt Service\AzureWLBackupPluginSvc vyžaduje oprávnění sql sysadmin. Všechny virtuální servery SQL Server vytvořené na marketplace jsou dodávány s nainstalovaným rozšířením SqlIaaSExtension. Rozšíření AzureBackupWindowsWorkload používá sqliaasextension automaticky získat požadovaná oprávnění.
    * Pokud jste virtuální počítač nevytvořili z webu Marketplace nebo pokud jste na SQL 2008 a 2008 R2, virtuální počítač nemusí mít nainstalovánsqlIaaSExtension a operace zjišťování se nezdaří s chybovou zprávou UserErrorSQLNoSysAdminMembership. Chcete-li tento problém vyřešit, postupujte podle pokynů v části [Nastavit oprávnění virtuálního virtuálního soudu](backup-azure-sql-database.md#set-vm-permissions).

        ![Výběr virtuálního virtuálního připojení a databáze](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Konfigurace zálohování  

1. V **kroku 2 cíle zálohování:** > Konfigurace**zálohování**vyberte **Konfigurovat zálohování**.

   ![Vyberte Konfigurovat zálohování.](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. V **části Vybrat položky k zálohování**se zobrazí všechny registrované skupiny dostupnosti a samostatné instance serveru SQL Server. Výběrem šipky nalevo od řádku rozbalíte seznam všech nechráněných databází v této instanci nebo skupiny dostupnosti Always On.  

    ![Zobrazení všech instancí serveru SQL Server se samostatnými databázemi](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Vyberte všechny databáze, které chcete chránit, a pak vyberte **OK**.

   ![Ochrana databáze](./media/backup-azure-sql-database/select-database-to-protect.png)

   Chcete-li optimalizovat načtení zálohy, Azure Backup nastaví maximální počet databází v jedné úloze zálohování na 50.

     * Chcete-li chránit více než 50 databází, nakonfigurujte více záloh.
     * Chcete-li [povolit](#enable-auto-protection) celou instanci nebo skupinu dostupnosti vždy **zapnuto,** vyberte v rozevíracím seznamu AUTOPROTECT **možnost ZAPNUTO**a pak vyberte **OK**.

    > [!NOTE]
    > Funkce [automatické ochrany](#enable-auto-protection) umožňuje nejen ochranu ve všech existujících databázích najednou, ale také automaticky chrání všechny nové databáze přidané do této instance nebo skupiny dostupnosti.  

4. Chcete-li otevřít **zásadu zálohování,** vyberte **možnost OK** .

    ![Povolit automatickou ochranu pro skupinu dostupnosti always on](./media/backup-azure-sql-database/enable-auto-protection.png)

5. V **zásadách zálohování**zvolte zásadu a pak vyberte **OK**.

   * Vyberte výchozí zásadu jako HourlyLogBackup.
   * Zvolte existující zásady zálohování, které byly dříve vytvořeny pro SQL.
   * Definujte novou zásadu na základě rpo a rozsahu uchovávání informací.

     ![Vybrat zásadu zálohování](./media/backup-azure-sql-database/select-backup-policy.png)

6. V **programu Zálohování**vyberte možnost **Povolit zálohování**.

    ![Povolení zvolené zásady zálohování](./media/backup-azure-sql-database/enable-backup-button.png)

7. Sledujte průběh konfigurace v oblasti **Oznámení** na portálu.

    ![Oznamovací oblast](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Vytvoření zásad zálohování

Zásady zálohování definují, kdy jsou zálohy přijata a jak dlouho jsou zachovány.

* Zásada je vytvořena na úrovni úschovny.
* Více trezorů může používat stejné zásady zálohování, ale zásady zálohování je nutné použít pro každý trezor.
* Při vytváření zásad zálohování je výchozí denní úplná záloha.
* Můžete přidat rozdílové zálohy, ale pouze v případě, že nakonfigurujete úplné zálohy dojít týdně.
* Informace o [různých typech zásad zálohování](backup-architecture.md#sql-server-backup-types).

Vytvoření zásadzálohování:

1. V trezoru vyberte **Zásady** > zálohování**Přidat**.
2. V **poli Přidat**vyberte SQL Server v **virtuálním počítači Azure** a definujte typ zásad.

   ![Výběr typu zásad pro novou zásadu zálohování](./media/backup-azure-sql-database/policy-type-details.png)

3. V **části Název zásad**zadejte název nové zásady.
4. V **zásadách úplného zálohování**vyberte **frekvenci zálohování**. Zvolte **buď denně nebo** **týdně**.

   * V **části Denně**vyberte hodinu a časové pásmo při zahájení úlohy zálohování.
   * V **části Týdně**vyberte den v týdnu, hodinu a časové pásmo při zahájení úlohy zálohování.
   * Spusťte úplnou zálohu, protože možnost **Úplné zálohování** nelze vypnout.
   * Chcete-li zobrazit zásadu, vyberte možnost **Úplné zálohování.**
   * Nelze vytvořit rozdílové zálohy pro denní úplné zálohy.

     ![Nová pole zásad zálohování](./media/backup-azure-sql-database/full-backup-policy.png)  

5. V **rozsahu retentionu**jsou ve výchozím nastavení vybrány všechny možnosti. Zrušte všechny limity rozsahu uchování, které nechcete, a nastavte intervaly, které chcete použít.

    * Minimální doba uchování pro jakýkoli typ zálohy (úplné, rozdílové a protokolu) je sedm dní.
    * Body obnovení jsou označeny pro uchovávání na základě jejich rozsah uchování. Pokud například vyberete denní úplnou zálohu, bude každý den spuštěna pouze jedna úplná záloha.
    * Záloha pro konkrétní den je označena a zachována na základě týdenního rozsahu uchovávání a týdenního nastavení uchovávání.
    * Měsíční a roční retenční rozsahy se chovají podobně.

       ![Nastavení intervalu rozsahu retencí](./media/backup-azure-sql-database/retention-range-interval.png)

6. V nabídce **zásad Úplné zálohování** vyberte **OK,** chcete-li přijmout nastavení.
7. Chcete-li přidat zásady rozdílového zálohování, vyberte **možnost Rozdílová záloha**.

   ![Nastavení](./media/backup-azure-sql-database/retention-range-interval.png)
   ![intervalu rozsahu uchování Otevřete nabídku zásad rozdílového zálohování](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. V **zásadách rozdílového zálohování**vyberte **Povolit,** chcete-li otevřít ovládací prvky četnosti a uchovávání informací.

    * Můžete spustit pouze jednu rozdílovou zálohu za den.
    * Rozdílové zálohy lze uchovávat maximálně 180 dní. Pro delší uchovávání informací použijte úplné zálohy.

9. Výběrem **možnosti OK** uložte zásadu a vraťte se do hlavní nabídky **zásad zálohování.**

10. Chcete-li přidat zásadu zálohování transakčního protokolu, vyberte **možnost Protokolovat zálohování**.
11. V **části Zálohování protokolu**vyberte **Povolit**a nastavte ovládací prvky frekvence a uchovávání informací. Zálohy protokolů se mohou vyskytovat tak často, jak každých 15 minut a mohou být uchovávány po dobu až 35 dnů.
12. Výběrem **možnosti OK** uložte zásadu a vraťte se do hlavní nabídky **zásad zálohování.**

    ![Úprava zásad zálohování protokolu](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. V nabídce **zásad zálohování** zvolte, zda chcete povolit **kompresi zálohování SQL** nebo ne. Tato možnost je ve výchozím nastavení zakázána. Pokud je tato možnost povolena, sql server odešle komprimovaný záložní datový proud do VDI.  Upozorňujeme, že Azure Backup přepíše výchozí úroveň instance klauzulí KOMPRESNÍ / NO_COMPRESSION v závislosti na hodnotě tohoto ovládacího prvku.

14. Po dokončení úprav zásad zálohování vyberte **ok**.

> [!NOTE]
> Každá záloha protokolu je zřetězena k předchozí úplné zálohě a vytvoří řetězec obnovení. Tato úplná záloha bude zachována, dokud nevyprší platnost uchovávání poslední zálohy protokolu. To může znamenat, že úplné zálohování je zachována po dobu další a ujistěte se, že všechny protokoly lze obnovit. Předpokládejme, že uživatel má týdenní úplné zálohování, denní rozdíl a 2 hodiny protokoly. Všechny jsou uchovávány po dobu 30 dnů. Ale týdenní plný může být opravdu vyčištěn / smazán až po další úplné zálohování je k dispozici, tj., po 30 + 7 dnů. Řekněme, že týdenní plná záloha se stane na 16.listopadu. Podle zásad uchovávání informací by měla být zachována až do 16.prosince. Poslední záloha protokolu pro tento plný se stane před další naplánované plné, na 22.listopadu. Dokud tento protokol je k dispozici až do 22.prosince, 16.listopadu plné nelze odstranit. Takže 16.listopadu plné je zachována až do 22.prosince.

## <a name="enable-auto-protection"></a>Povolit automatickou ochranu  

Můžete povolit automatickou ochranu automaticky zálohovat všechny existující a budoucí databáze na samostatnou instanci serveru SQL Server nebo do skupiny dostupnosti Always On.

* Počet databází, které můžete vybrat pro automatickou ochranu najednou, není nijak omezen.
* V době, kdy povolíte automatickou ochranu, nelze selektivně chránit nebo vyloučit databáze z ochrany v instanci.
* Pokud vaše instance již obsahuje některé chráněné databáze, zůstanou chráněné podle svých zásad i po zapnutí automatické ochrany. Všechny nechráněné databáze přidané později budou mít pouze jednu zásadu, kterou definujete v době povolení automatické ochrany, uvedené v části **Konfigurovat zálohování**. Zásady přidružené k automaticky chráněné databázi však můžete později změnit.  

Povolení automatické ochrany:

  1. V **části Položky k zálohování**vyberte instanci, pro kterou chcete povolit automatickou ochranu.
  2. Vyberte rozevírací seznam v části **AUTOPROTECT**, zvolte **ZAPNUTO**a pak vyberte **OK**.

      ![Povolit automatickou ochranu ve skupině dostupnosti](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Zálohování je nakonfigurováno pro všechny databáze dohromady a lze je sledovat v **programu Zálohování úloh**.

Pokud potřebujete zakázat automatickou ochranu, vyberte název instance v části **Konfigurovat zálohování**a pak vyberte **Zakázat automatické připojištění** instance. Všechny databáze budou i nadále zálohovány, ale budoucí databáze nebudou automaticky chráněny.

![Zakázat automatickou ochranu této instance](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Další kroky

Naučte se:

* [Obnovení zálohovaných databází serveru SQL Server](restore-sql-database-azure-vm.md)
* [Správa zálohovaných databází serveru SQL Server](manage-monitor-sql-database-backup.md)
