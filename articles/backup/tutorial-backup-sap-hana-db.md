---
title: Kurz – zálohování SAP HANA databází na virtuálních počítačích Azure
description: V tomto kurzu se naučíte zálohovat SAP HANA databáze běžící na virtuálním počítači Azure do trezoru služby Azure Backup Recovery Services.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: 435668dedc7efa33fd5fbfeea8671f05d070a385
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128647"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Kurz: zálohování SAP HANA databází ve virtuálním počítači Azure

V tomto kurzu se dozvíte, jak zálohovat SAP HANA databáze běžící na virtuálních počítačích Azure do trezoru služby Azure Backup Recovery Services. V tomto článku se dozvíte, jak:

> [!div class="checklist"]
>
> * Vytvoření a konfigurace trezoru
> * Vyhledat databáze
> * Konfigurace zálohování

[Tady](sap-hana-backup-support-matrix.md#scenario-support) jsou všechny scénáře, které momentálně podporujeme.

## <a name="prerequisites"></a>Předpoklady

Před konfigurací zálohování se ujistěte, že jste provedli následující kroky:

* Umožněte připojení z virtuálního počítače k Internetu, aby bylo možné získat přístup k Azure, jak je popsáno níže v postupu [Nastavení síťového připojení](#set-up-network-connectivity) .
* Klíč by měl existovat v **hdbuserstore** , který splňuje následující kritéria:
  * Měl by se nacházet ve výchozím **hdbuserstore**
  * V případě MDC by měl klíč ukazovat na port SQL **názvový server**. V případě SDC by měl odkazovat na port SQL **INDEXSERVER**
  * Měl by obsahovat přihlašovací údaje k přidávání a odstraňování uživatelů.
* Spusťte skript konfigurace zálohování SAP HANA (předregistrující skript) ve virtuálním počítači, kde je nainstalovaná verze HANA, jako uživatel root. [Tento skript](https://aka.ms/scriptforpermsonhana) NAČTE systém Hana, který je připravený k zálohování. Další informace o skriptu před registrací najdete v části [co je to skript](#what-the-pre-registration-script-does) pro předběžnou registraci.

## <a name="set-up-network-connectivity"></a>Nastavení připojení k síti

U všech operací vyžaduje virtuální počítač SAP HANA připojení k veřejným IP adresám Azure. Operace virtuálních počítačů (zjišťování databáze, konfigurace záloh, plánování záloh, obnovení bodů obnovení atd.) bez připojení k veřejným IP adresám Azure.

Navažte připojení pomocí jedné z následujících možností:

### <a name="allow-the-azure-datacenter-ip-ranges"></a>Povoluje rozsahy IP adres datacentra Azure.

Tato možnost povoluje [rozsahy IP adres](https://www.microsoft.com/download/details.aspx?id=41653) ve staženém souboru. Pro přístup ke skupině zabezpečení sítě (NSG) použijte rutinu Set-AzureNetworkSecurityRule. Pokud váš seznam bezpečných příjemců obsahuje jenom IP adresy specifické pro oblast, budete taky muset aktualizovat seznam bezpečných příjemců, aby se povolilo ověřování v seznamu služby Azure Active Directory (Azure AD).

### <a name="allow-access-using-nsg-tags"></a>Povolení přístupu pomocí značek NSG

Pokud k omezení připojení používáte NSG, měli byste pomocí značky služby AzureBackup povolit odchozí přístup k Azure Backup. Kromě toho byste měli také umožňovat připojení pro ověřování a přenos dat pomocí [pravidel](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) pro Azure AD a Azure Storage. To se dá udělat z Azure Portal nebo přes PowerShell.

Vytvoření pravidla pomocí portálu:

  1. Ve **všech službách**klikněte na **skupiny zabezpečení sítě** a vyberte skupinu zabezpečení sítě.
  2. V části **Nastavení**vyberte **odchozí pravidla zabezpečení** .
  3. Vyberte **Přidat**. Zadejte všechny požadované podrobnosti pro vytvoření nového pravidla, jak je popsáno v [Nastavení pravidla zabezpečení](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Ujistěte se, že možnost **cíl** je nastavená na **příznak služby** a **cílová značka služby** je nastavená na **AzureBackup**.
  4. Klikněte na tlačítko **Přidat**a uložte nově vytvořené odchozí pravidlo zabezpečení.

Vytvoření pravidla pomocí prostředí PowerShell:

 1. Přidání přihlašovacích údajů k účtu Azure a aktualizace národních cloudů<br/>
      `Add-AzureRmAccount`<br/>

 2. Výběr předplatného NSG<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. Vybrat NSG<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. Přidat odchozí pravidlo pro Azure Backup tag služby<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. Přidat odchozí pravidlo pro značku služby úložiště<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. Přidat odchozí pravidlo pro značku služby Azureactivedirectory selhala<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. Uložit NSG<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**Povolí přístup pomocí značek Azure firewall**. Pokud používáte Azure Firewall, vytvořte pravidlo aplikace pomocí [značky plně kvalifikovaného názvu domény](https://docs.microsoft.com/azure/firewall/fqdn-tags)AzureBackup. To umožňuje odchozí přístup k Azure Backup.

**Nasaďte proxy server HTTP pro směrování provozu**. Při zálohování databáze SAP HANA na virtuálním počítači Azure používá rozšíření zálohování na virtuálním počítači rozhraní API HTTPS k posílání příkazů pro správu do Azure Backup a dat do Azure Storage. Rozšíření zálohování používá pro ověřování taky službu Azure AD. Přesměrujte provoz rozšíření zálohování pro tyto tři služby prostřednictvím proxy serveru HTTP. Rozšíření jsou jedinou komponentou, která je nakonfigurovaná pro přístup k veřejnému Internetu.

Možnosti připojení zahrnují následující výhody a nevýhody:

**Možnost** | **Výhody** | **Nevýhody**
--- | --- | ---
Povoluje rozsahy IP adres. | Žádné další náklady | Složitá Správa, protože se rozsahy IP adres v průběhu času mění <br/><br/> Poskytuje přístup k celé službě Azure, ne jen Azure Storage
Použití značek služby NSG | Jednodušší Správa jako změny rozsahu se sloučí automaticky. <br/><br/> Žádné další náklady <br/><br/> | Dá se použít jenom s skupin zabezpečení sítě <br/><br/> Poskytuje přístup k celé službě.
Použití Azure Firewall značek plně kvalifikovaného názvu domény | Jednodušší Správa, protože jsou automaticky spravovány požadované plně kvalifikované názvy domén | Dá se použít jenom s Azure Firewall.
Použití proxy serveru HTTP | Podrobné řízení v proxy serveru přes adresy URL úložiště je povolené. <br/><br/> Přístup k virtuálním počítačům jediným bodem z Internetu <br/><br/> Nepodléhá změnám IP adresy Azure | Další náklady na spuštění virtuálního počítače s využitím softwaru proxy

## <a name="what-the-pre-registration-script-does"></a>Co skript pro předběžnou registraci dělá

Spuštění předregistračního skriptu provede následující funkce:

* Nainstaluje nebo aktualizuje všechny potřebné balíčky, které vyžaduje agent Azure Backup pro vaši distribuci.
* Provádí odchozí kontroly připojení k síti pomocí Azure Backup serverů a závislých služeb, jako je Azure Active Directory a Azure Storage.
* Přihlásí se do systému HANA pomocí klíče uživatele uvedeného v rámci [požadavků](#prerequisites). Uživatelský klíč se používá k vytvoření zálohovacího uživatele (AZUREWLBACKUPHANAUSER) v systému HANA a klíč uživatele lze odstranit po úspěšném spuštění skriptu před registrací.
* K AZUREWLBACKUPHANAUSER se přiřazují tyto požadované role a oprávnění:
  * Správce databáze: pro vytvoření nových databází během obnovy.
  * ČTENÍ katalogu: pro čtení katalogu záloh.
  * SAP_INTERNAL_HANA_SUPPORT: pro přístup k několika soukromým tabulkám.
* Skript přidá klíč do **hdbuserstore** pro AZUREWLBACKUPHANAUSER pro modul plug-in Hana pro zpracování všech operací (databázové dotazy, operace obnovení, konfigurace a spuštění zálohování).

Pro potvrzení vytvoření klíče spusťte na počítači HANA příkaz HDBSQL s přihlašovacími údaji SIDADM:

```hdbsql
hdbuserstore list
```

Výstup příkazu by měl zobrazovat klíč {SID} {DBNAME} s uživatelem zobrazeným jako AZUREWLBACKUPHANAUSER.

>[!NOTE]
> Ujistěte se, že v `/usr/sap/{SID}/home/.hdb/`máte jedinečnou sadu souborů SSFS. V této cestě by měla být jenom jedna složka.

## <a name="create-a-recovery-service-vault"></a>Vytvoření trezoru služby Recovery Services

Recovery Services trezor je entita, která ukládá zálohy a body obnovení vytvořené v průběhu času. Trezor Recovery Services obsahuje také zásady zálohování, které jsou přidruženy k chráněným virtuálním počítačům.

Chcete-li vytvořit trezor Služeb zotavení:

1. Přihlaste se k předplatnému v [Azure Portal](https://portal.azure.com/).

2. V nabídce vlevo vyberte **všechny služby** .

   ![Vybrat všechny služby](./media/tutorial-backup-sap-hana-db/all-services.png)

3. V dialogovém okně **všechny služby** zadejte **Recovery Services**. Seznam prostředků se filtruje podle vašeho zadání. V seznamu prostředků vyberte **Recovery Services trezory**.

   ![Vybrat Recovery Services trezory](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. Na řídicím panelu trezorů **Recovery Services** vyberte **Přidat**.

   ![Přidat Recovery Services trezor](./media/tutorial-backup-sap-hana-db/add-vault.png)

   Otevře se dialogové okno **Recovery Services trezor** . Zadejte hodnoty pro **název, předplatné, skupinu prostředků** a **umístění** .

   ![Vytvoření trezoru služby Recovery Services](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Název**: název slouží k identifikaci trezoru služby Recovery Services a musí být jedinečný pro předplatné Azure. Zadejte název, který obsahuje alespoň 2 znaky, ale ne více než 50 znaků. Název musí začínat písmenem a obsahovat jenom písmena, číslice a spojovníky. Pro tento kurz jsme použili název **SAPHanaVault**.
   * **Předplatné**: vyberte předplatné, které chcete použít. Pokud jste členem jenom jednoho předplatného, uvidíte tento název. Pokud si nejste jistí, které předplatné se má použít, použijte výchozí (navrhované) předplatné. K dispozici je více možností pouze v případě, že je váš pracovní nebo školní účet spojen s více než jedním předplatným Azure. V tomto příkladu jsme použili předplatné předplatného **řešení SAP HANA Solution Lab** .
   * **Skupina prostředků**: použijte existující skupinu prostředků nebo vytvořte novou. Tady jsme použili **SAPHANADemo**.<br>
   Pokud chcete zobrazit seznam dostupných skupin prostředků ve vašem předplatném, vyberte **použít existující**a pak v rozevíracím seznamu vyberte prostředek. Pokud chcete vytvořit novou skupinu prostředků, vyberte **vytvořit novou** a zadejte název. Úplné informace o skupinách prostředků najdete v tématu [přehled Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   * **Umístění**: vyberte zeměpisnou oblast trezoru. Trezor musí být ve stejné oblasti jako virtuální počítač se spuštěným SAP HANA. Použili jsme **východní USA 2**.

5. Vyberte **zkontrolovat + vytvořit**.

   ![Vyberte zkontrolovat & vytvořit.](./media/tutorial-backup-sap-hana-db/review-create.png)

Trezor služby Recovery Services je nyní vytvořen.

## <a name="discover-the-databases"></a>Zjištění databází

1. V trezoru v **Začínáme**klikněte na **zálohovat**. V aplikaci **kde je spuštěná vaše úloha?** vyberte **na virtuálním počítači Azure SAP HANA**.
2. Klikněte na **Spustit zjišťování**. Tím se iniciuje zjišťování nechráněných virtuálních počítačů se systémem Linux v oblasti trezoru. Zobrazí se virtuální počítač Azure, který chcete chránit.
3. V části **vybrat Virtual Machines**klikněte na odkaz pro stažení skriptu, který poskytuje oprávnění pro službu Azure Backup pro přístup k SAP HANA virtuálním počítačům pro zjištění databáze.
4. Spusťte skript na virtuálním počítači hostujícím SAP HANA databázi, kterou chcete zálohovat.
5. Po spuštění skriptu na virtuálním počítači vyberte v části **vybrat Virtual Machines**virtuální počítač. Pak klikněte na **Vyhledat databáze**.
6. Azure Backup zjistí všechny databáze SAP HANA na virtuálním počítači. Během zjišťování Azure Backup registruje virtuální počítač s trezorem a na virtuálním počítači nainstaluje rozšíření. V databázi není nainstalován žádný agent.

   ![Zjištění databází](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Konfigurace zálohování

Teď, když se hledají databáze, které chceme zálohovat, pojďme povolit zálohování.

1. Klikněte na **Konfigurovat zálohu**.

   ![Konfigurace zálohování](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. V nabídce **Vyberte položky, které chcete zálohovat**vyberte jednu nebo více databází, které chcete chránit, a poté klikněte na tlačítko **OK**.

   ![Vyberte položky, které chcete zálohovat.](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. V části **zásady zálohování > vyberte zásady zálohování**, vytvořte nové zásady zálohování pro databáze, a to v souladu s pokyny v následující části.

   ![Zvolit zásady zálohování](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. Po vytvoření zásady klikněte v **nabídce zálohování**na **Povolit zálohování**.

   ![Klikněte na povolit zálohování.](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Sledujte průběh konfigurace zálohování v oblasti **oznámení** na portálu.

## <a name="creating-a-backup-policy"></a>Vytvoření zásady zálohování

Zásady zálohování definují, kdy se zálohují zálohy a jak dlouho se uchovávají.

* Zásada se vytvoří na úrovni trezoru.
* Několik trezorů může používat stejné zásady zálohování, ale je nutné použít zásady zálohování pro každý trezor.

Nastavení zásad určete následujícím způsobem:

1. Do pole **název zásady**zadejte název nové zásady. V takovém případě zadejte **SAPHANA**.

   ![Zadat název pro nové zásady](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. V **zásadách úplného zálohování**vyberte **četnost zálohování**. Můžete zvolit **každý den** nebo **každý týden**. Pro tento kurz jsme zvolili **denní** zálohování.

   ![Výběr četnosti zálohování](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. V části **Rozsah uchování**nakonfigurujte nastavení uchovávání pro úplnou zálohu.
   * Ve výchozím nastavení jsou vybrány všechny možnosti. Vymažte všechny limity rozsahu uchování, které nechcete používat, a nastavte ty, které chcete provést.
   * Minimální doba uchování pro jakýkoli typ zálohy (úplný/rozdíl/protokol) je sedm dní.
   * Body obnovení jsou označeny pro uchování na základě jejich rozsahu uchovávání. Pokud například vyberete denní úplnou zálohu, spustí se každý den jenom jedno úplné zálohování.
   * Záloha pro určitý den je označená a zachovaná na základě rozsahu a nastavení týdenního uchování.
   * Měsíční a roční rozsah uchování se chová podobným způsobem.
4. V nabídce **zásady úplného zálohování** klikněte na **OK** , aby se nastavení přijímalo.
5. Pak vyberte **rozdílové zálohování** a přidejte rozdílovou zásadu.
6. V části **rozdílová zásada zálohování**vyberte **Povolit** a otevřete tak ovládací prvky četnost a uchování. Povolili jsme rozdílovou zálohu každé **neděle** v **2:00**. ta se uchovává po dobu **30 dnů**.

   ![Zásady rozdílového zálohování](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >Přírůstkové zálohování není aktuálně podporováno.
   >

7. Kliknutím na **OK** zásadu uložte a vraťte se do nabídky hlavní **zásady zálohování** .
8. Vyberte **zálohování protokolu** a přidejte zásady zálohování transakčního protokolu.
   * **Zálohování protokolu** je ve výchozím nastavení nastaveno na hodnotu **Povolit**. Toto nejde zakázat, protože SAP HANA spravuje všechny zálohy protokolů.
   * Nastavili jsme **2 hodiny** jako plán zálohování a **15 dní** doby uchování.

    ![Zásady zálohování protokolů](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > Zálohování protokolů začíná proudem až po úspěšném úplném úplném zálohování.
   >

9. Kliknutím na **OK** zásadu uložte a vraťte se do nabídky hlavní **zásady zálohování** .
10. Až dokončíte definování zásad zálohování, klikněte na **OK**.

Nyní jste úspěšně nakonfigurovali zálohy pro vaše SAP HANA databáze.

## <a name="next-steps"></a>Další kroky

* Naučte se [spouštět zálohy na vyžádání v SAP HANA databázích běžících na virtuálních počítačích Azure](backup-azure-sap-hana-database.md#run-an-on-demand-backup) .
* Informace o tom, jak [obnovit SAP HANA databáze běžící na virtuálních počítačích Azure](sap-hana-db-restore.md)
* Naučte se [spravovat SAP HANA databází zálohovaných pomocí Azure Backup](sap-hana-db-manage.md)
* Naučte se [řešit běžné problémy při zálohování SAP HANA databází](backup-azure-sap-hana-database-troubleshoot.md) .
