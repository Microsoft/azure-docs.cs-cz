---
title: Kurz – zálohování databází SAP HANA ve virtuálních počítačích Azure
description: V tomto kurzu se dozvíte, jak zálohovat databáze SAP HANA spuštěné na virtuálním počítači Azure do trezoru služby Obnovení zálohování Azure.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: f64dd74ad0e038c5cad152e20ae2255de03114e3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79501450"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Kurz: Zálohování databází SAP HANA ve virtuálním počítači Azure

Tento kurz ukazuje, jak zálohovat databáze SAP HANA spuštěné na virtuálních počítačích Azure do trezoru služby Azure Backup Recovery Services. V tomto článku se dozvíte, jak:

> [!div class="checklist"]
>
> * Vytvoření a konfigurace trezoru
> * Zjišťování databází
> * Konfigurace zálohování

[Zde](sap-hana-backup-support-matrix.md#scenario-support) jsou všechny scénáře, které v současné době podporujeme.

## <a name="prerequisites"></a>Požadavky

Před konfigurací záloh postupujte takto:

* Povolte připojení z virtuálního počítače k internetu, aby mohl dosáhnout Azure, jak je popsáno v postupu [nastavení připojení k síti](#set-up-network-connectivity) níže.
* Klíč by měl existovat v **úložišti hdbuserstore,** který splňuje následující kritéria:
  * Měl by být přítomen ve výchozím **hdbuserstore**
  * Pro MDC by měl klíč přejděte na port SQL **NAMESERVER**. V případě SDC by měl poukázat na SQL port **INDEXSERVER**
  * Měl by mít pověření pro přidání a odstranění uživatelů
* Spusťte skript konfigurace zálohování SAP HANA (skript předběžné registrace) ve virtuálním počítači, ve kterém je nainstalován hana, jako kořenový uživatel. [Tento skript](https://aka.ms/scriptforpermsonhana) získá systém HANA připraven k zálohování. Informace o skriptu předběžné registrace naleznete v části [Co skript předběžné registrace obsahuje.](#what-the-pre-registration-script-does)

## <a name="set-up-network-connectivity"></a>Nastavení připojení k síti

Pro všechny operace vyžaduje virtuální počítač SAP HANA připojení k veřejným IP adresám Azure. Operace virtuálních počítačů (zjišťování databáze, konfigurace zálohování, plánování zálohování, body obnovení a tak dále) se nezdaří bez připojení k veřejným IP adresám Azure.

Navázání připojení pomocí jedné z následujících možností:

### <a name="allow-the-azure-datacenter-ip-ranges"></a>Povolení rozsahů IP adres datového centra Azure

Tato možnost umožňuje [rozsahy IP adres](https://www.microsoft.com/download/details.aspx?id=41653) ve staženém souboru. Chcete-li získat přístup ke skupině zabezpečení sítě (NSG), použijte rutinu Set-AzureNetworkSecurityRule. Pokud seznam bezpečných příjemců obsahuje jenom IP adresy specifické pro oblast, budete muset aktualizovat seznam bezpečných příjemců, značku služby Azure Active Directory (Azure AD), abyste povolili ověřování.

### <a name="allow-access-using-nsg-tags"></a>Povolit přístup pomocí značek NSG

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

**Nasazení serveru proxy HTTP pro směrování provozu**. Když zálohujete databázi SAP HANA na virtuálním počítači Azure, rozšíření zálohy na virtuálním počítači používá rozhraní API HTTPS k odesílání příkazů pro správu do Azure Backup a dat do Azure Storage. Rozšíření zálohování také používá Azure AD pro ověřování. Směrovat provoz rozšíření zálohy pro tyto tři služby prostřednictvím proxy serveru HTTP. Rozšíření jsou jedinou součástí, která je nakonfigurována pro přístup k veřejnému internetu.

Možnosti připojení zahrnují následující výhody a nevýhody:

**Možnost** | **Výhody** | **Nevýhody**
--- | --- | ---
Povolit rozsahy IP adres | Žádné další náklady | Komplexní správa, protože rozsahy IP adres se v průběhu času mění <br/><br/> Poskytuje přístup k celému Azure, nejen k Úložišti Azure.
Použití značek služby NSG | Snadnější správa při automatickém sloučení změn rozsahu <br/><br/> Žádné další náklady <br/><br/> | Lze použít pouze s nsgs <br/><br/> Poskytuje přístup k celé službě
Použití značek FQDN brány Azure Firewall | Snadnější správa při automatické správě požadovaných souborů FQDN | Lze použít pouze s Azure Firewall
Použití proxy serveru HTTP | Podrobné ovládání v proxy serveru přes adresy URL úložiště je povoleno <br/><br/> Jednotný bod přístupu k internetu k virtuálním operátorům <br/><br/> Nepodléhá změnám IP adres Azure | Dodatečné náklady na spuštění virtuálního počítače se softwarem proxy

## <a name="what-the-pre-registration-script-does"></a>Co dělá skript pro předběžnou registraci

Spuštění skriptu předběžné registrace provádí následující funkce:

* Nainstaluje nebo aktualizuje všechny potřebné balíčky vyžadované agentem Azure Backup na vaší distribuci.
* Provádí kontroly odchozího připojení k síti pomocí serverů Azure Backup a závislých služeb, jako je Azure Active Directory a Azure Storage.
* Přihlásí se do vašeho systému HANA pomocí uživatelského klíče uvedeného jako součást [předpokladů](#prerequisites). Uživatelský klíč se používá k vytvoření záložního uživatele (AZUREWLBACKUPHANAUSER) v systému HANA a uživatelský klíč lze odstranit po úspěšném spuštění skriptu předběžné registrace.
* AzurewlBACKUPHANAUSER je přiřazeny tyto požadované role a oprávnění:
  * DATABASE ADMIN (v případě MDC) a BACKUP ADMIN (v případě SDC): vytvořit nové databáze během obnovení.
  * KATALOG READ: chcete-li číst katalog záloh.
  * SAP_INTERNAL_HANA_SUPPORT: pro přístup k několika soukromým stolům.
* Skript přidá klíč k **hdbuserstore** pro AZUREWLBACKUPHANAUSER pro modul plug-in zálohy HANA pro zpracování všech operací (databázové dotazy, operace obnovení, konfigurace a spuštění zálohování).

>[!NOTE]
> Můžete explicitně předat uživatelský klíč uvedený jako součást [předpokladů](#prerequisites) jako parametr do skriptu předběžné registrace:`-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>Chcete-li zjistit, jaké další parametry skript přijímá, použijte příkaz`bash msawb-plugin-config-com-sap-hana.sh --help`

Chcete-li potvrdit vytvoření klíče, spusťte příkaz HDBSQL na počítači HANA s pověřeními SIDADM:

```hdbsql
hdbuserstore list
```

Výstup příkazu by měl zobrazit klíč {SID}{DBNAME} s uživatelem zobrazeným jako AZUREWLBACKUPHANAUSER.

>[!NOTE]
> Ujistěte se, že máte jedinečnou sadu `/usr/sap/{SID}/home/.hdb/`souborů SSFS v části . V této cestě by měla být pouze jedna složka.

## <a name="create-a-recovery-service-vault"></a>Vytvoření trezoru služby obnovení

Trezor služby Recovery Services je entita, která ukládá zálohy a body obnovení vytvořené v průběhu času. Trezor služby Recovery Services také obsahuje zásady zálohování, které jsou přidruženy k chráněným virtuálním počítačům.

Chcete-li vytvořit trezor Služeb zotavení:

1. Přihlaste se ke svému předplatnému na [webu Azure Portal](https://portal.azure.com/).

2. V levé nabídce vyberte **Všechny služby**

   ![Vybrat všechny služby](./media/tutorial-backup-sap-hana-db/all-services.png)

3. V dialogovém okně **Všechny služby** zadejte **služby obnovení**. Seznam zdrojů filtruje podle vašeho vstupu. V seznamu zdrojů vyberte **trezory služby Recovery Services**.

   ![Vybrat trezory služby Recovery Services](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. Na řídicím panelu trezorů **služby Recovery Services** vyberte **Přidat**.

   ![Přidat trezor služby Recovery Services](./media/tutorial-backup-sap-hana-db/add-vault.png)

   Otevře se dialogové okno **trezoru služby Recovery Services.** Zadejte hodnoty pro **název, odběr, skupinu prostředků** a **umístění**

   ![Vytvoření trezoru služby Recovery Services](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Název**: Název se používá k identifikaci trezoru služeb pro obnovení a musí být jedinečný pro předplatné Azure. Zadejte název, který má alespoň dva, ale ne více než 50 znaků. Název musí začínat písmenem a skládat pouze z písmen, čísel a spojovníků. Pro účely tohoto kurzu jsme použili název **SAPHanaVault**.
   * **Předplatné**: Zvolte předplatné, které chcete použít. Pokud jste členem pouze jednoho předplatného, uvidíte toto jméno. Pokud si nejste jistí, které předplatné použít, použijte výchozí (navrhované) předplatné. Existuje několik možností pouze v případě, že váš pracovní nebo školní účet je přidružený k více než jednomu předplatnému Azure. Zde jsme použili předplatné **testovacího prostředí řešení SAP HANA.**
   * **Skupina prostředků**: Použijte existující skupinu prostředků nebo vytvořte novou. Zde jsme použili **SAPHANADemo**.<br>
   Pokud chcete zobrazit seznam dostupných skupin prostředků v předplatném, vyberte **Použít existující**a v rozevíracím seznamu vyberte prostředek. Chcete-li vytvořit novou skupinu prostředků, vyberte **Vytvořit nový** a zadejte název. Úplné informace o skupinách prostředků najdete v [tématu Přehled Správce prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   * **Umístění**: Vyberte zeměpisnou oblast pro úschovnu. Úschovna musí být ve stejné oblasti jako virtuální počítač se systémem SAP HANA. Použili jsme **východní USA 2**.

5. Vyberte **zkontrolovat + vytvořit**.

   ![Vybrat zkontrolovat & vytvořit.](./media/tutorial-backup-sap-hana-db/review-create.png)

Trezor služeb obnovení je nyní vytvořen.

## <a name="discover-the-databases"></a>Seznamte se s databázemi

1. V trezoru klikněte v **části Začínáme**na **položku Zálohování**. V **yberte Kde běží vaše úloha?**, vyberte SAP **HANA v virtuálním počítači Azure**.
2. Klepněte na tlačítko **Spustit zjišťování**. To iniciuje zjišťování nechráněných virtuálních počítačích SIP v oblasti úschovny. Uvidíte virtuální počítač Azure, který chcete chránit.
3. V **části Vybrat virtuální počítače**klikněte na odkaz a stáhněte si skript, který poskytuje oprávnění pro službu Azure Backup pro přístup k virtuálním počítačům SAP HANA pro zjišťování databáze.
4. Spusťte skript na virtuálním počítači hostující databáze SAP HANA, které chcete zálohovat.
5. Po spuštění skriptu na virtuálním počítači vyberte v **nabídce Vybrat virtuální počítače**virtuální počítač. Potom klepněte na tlačítko **Objevit centrální soubory**.
6. Azure Backup zjišťuje všechny databáze SAP HANA na virtuálním počítači. Během zjišťování Azure Backup zaregistruje virtuální počítač s trezorem a nainstaluje rozšíření na virtuální počítač. V databázi není nainstalován žádný agent.

   ![Seznamte se s databázemi](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Konfigurace zálohování

Nyní, když jsou databáze, které chceme zálohovat, objeveny, povolte zálohování.

1. Klepněte na **tlačítko Konfigurovat zálohování**.

   ![Konfigurace zálohování](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. V **části Vybrat položky, které chcete zálohovat**, vyberte jednu nebo více databází, které chcete chránit, a klepněte na tlačítko **OK**.

   ![Výběr položek, které chcete zálohovat](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. V **části Zásady zálohování > Zvolte zásady zálohování**, vytvořte nové zásady zálohování pro databáze v souladu s pokyny v další části.

   ![Zvolte zásady zálohování](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. Po vytvoření zásady klepněte v **nabídce Zálohování**na **položku Povolit zálohování**.

   ![Klikněte na Povolit zálohování.](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Sledujte průběh konfigurace zálohování v oblasti **Oznámení** na portálu.

## <a name="creating-a-backup-policy"></a>Vytvoření zásady zálohování

Zásady zálohování definují, kdy jsou zálohy prováděny a jak dlouho jsou zachovány.

* Zásada je vytvořena na úrovni úschovny.
* Více trezorů může používat stejné zásady zálohování, ale zásady zálohování je nutné použít pro každý trezor.

Nastavení zásad zadejte následujícím způsobem:

1. V **části Název zásad**zadejte název nové zásady. V tomto případě zadejte **SAPHANA**.

   ![Zadejte název nové zásady.](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. V **zásadách úplného zálohování**vyberte **frekvenci zálohování**. Můžete si vybrat **denní** nebo **týdenní**. Pro tento kurz jsme zvolili **denní** zálohu.

   ![Výběr frekvence zálohování](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. V **rozsahu uchování**nakonfigurujte nastavení uchovávání informací pro úplnou zálohu.
   * Ve výchozím nastavení jsou vybrány všechny možnosti. Zrušte všechny limity rozsahu uchování, které nechcete používat, a nastavte ty, které nechcete používat.
   * Minimální doba uchování pro jakýkoli typ zálohy (full/differential/log) je sedm dní.
   * Body obnovení jsou označeny pro uchovávání na základě jejich rozsah uchování. Pokud například vyberete denní úplnou zálohu, bude každý den spuštěna pouze jedna úplná záloha.
   * Záloha pro konkrétní den je označena a zachována na základě týdenního rozsahu uchovávání a nastavení.
   * Měsíční a roční retenční rozsahy se chovají podobně.
4. V nabídce **zásad úplného zálohování** klepněte na tlačítko **OK,** abyste přijali nastavení.
5. Pak vyberte **rozdílové zálohování** přidat rozdílové zásady.
6. V **zásadách rozdílového zálohování**vyberte **Povolit,** chcete-li otevřít ovládací prvky četnosti a uchovávání informací. Povolili jsme rozdílovou zálohu každou **neděli** ve **2:00**, která je zachována po dobu **30 dnů**.

   ![Zásady rozdílového zálohování](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >Přírůstkové zálohy nejsou aktuálně podporovány.
   >

7. Klepnutím na **tlačítko OK** uložte zásadu a vraťte se do hlavní nabídky **zásad zálohování.**
8. Chcete-li přidat zásady zálohování transakčních protokolů, vyberte **možnost Záloha protokolů.**
   * **Program Zálohování protokolu** je ve výchozím nastavení nastaven na **povolit**. To nelze zakázat, protože SAP HANA spravuje všechny zálohy protokolů.
   * Nastavili jsme **2 hodiny** jako plán zálohování a **15 dní** retenční ho období.

    ![Protokolovat zásady zálohování](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > Zálohy protokolů začnou tok až po dokončení jedné úspěšné úplné zálohy.
   >

9. Klepnutím na **tlačítko OK** uložte zásadu a vraťte se do hlavní nabídky **zásad zálohování.**
10. Po dokončení definování zásad zálohování klepněte na tlačítko **OK**.

Nyní jste úspěšně nakonfigurovali zálohy pro databáze SAP HANA.

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak [spouštět zálohy na vyžádání v databázích SAP HANA spuštěných na virtuálních počítačích Azure](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Zjistěte, jak [obnovit databáze SAP HANA spuštěné na virtuálních počítačích Azure](sap-hana-db-restore.md)
* Zjistěte, jak [spravovat databáze SAP HANA, které jsou zálohované pomocí azure backupu](sap-hana-db-manage.md)
* Informace o [řešení běžných problémů při zálohování databází SAP HANA](backup-azure-sap-hana-database-troubleshoot.md)
