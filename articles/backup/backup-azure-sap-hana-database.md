---
title: Zálohování databáze SAP HANA do Azure s využitím Azure Backup
description: V tomto článku se dozvíte, jak zálohovat databázi SAP HANA do virtuálních počítačů Azure pomocí služby Azure Backup.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: deedd4d2553b3b06f76f698fdb2425a8d3878d23
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248057"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Zálohování databází SAP HANA na virtuálních počítačích Azure

SAP HANA databáze jsou kritické úlohy, které vyžadují nízký cíl bodu obnovení (RPO) a dlouhodobé uchovávání. SAP HANA databáze běžící na virtuálních počítačích Azure můžete zálohovat pomocí [Azure Backup](backup-overview.md).

V tomto článku se dozvíte, jak zálohovat SAP HANA databází, které běží na virtuálních počítačích Azure, do trezoru služby Azure Backup Recovery Services.

V tomto článku se dozvíte, jak:
> [!div class="checklist"]
>
> * Vytvoření a konfigurace trezoru
> * Vyhledat databáze
> * Konfigurace zálohování
> * Spuštění úlohy zálohování na vyžádání

>[!NOTE]
>**Obnovitelné odstranění pro SQL Server na virtuálním počítači Azure a obnovitelné odstranění pro SAP HANA v úlohách virtuálních počítačů Azure** je teď dostupné ve verzi Preview.<br>
>Pokud si chcete zaregistrovat verzi Preview, napište nám na AskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Požadavky

Informace o [požadavcích](tutorial-backup-sap-hana-db.md#prerequisites) a o [tom, co skript](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) pro předběžnou registraci zahrnuje oddíly k nastavení databáze pro zálohování.

### <a name="set-up-network-connectivity"></a>Nastavení připojení k síti

U všech operací vyžaduje virtuální počítač SAP HANA připojení k veřejným IP adresám Azure. Operace virtuálních počítačů (zjišťování databáze, konfigurace záloh, plánování záloh, obnovení bodů obnovení atd.) bez připojení k veřejným IP adresám Azure.

Navažte připojení pomocí jedné z následujících možností:

#### <a name="allow-the-azure-datacenter-ip-ranges"></a>Povoluje rozsahy IP adres datacentra Azure.

Tato možnost povoluje [rozsahy IP adres](https://www.microsoft.com/download/details.aspx?id=41653) ve staženém souboru. Pro přístup ke skupině zabezpečení sítě (NSG) použijte rutinu Set-AzureNetworkSecurityRule. Pokud váš seznam bezpečných příjemců obsahuje jenom IP adresy specifické pro oblast, budete taky muset aktualizovat seznam bezpečných příjemců, aby se povolilo ověřování v seznamu služby Azure Active Directory (Azure AD).

#### <a name="allow-access-using-nsg-tags"></a>Povolení přístupu pomocí značek NSG

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

#### <a name="private-endpoints"></a>Soukromé koncové body

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Zjištění databází

1. V trezoru v **Začínáme**klikněte na **zálohovat**. V aplikaci **kde je spuštěná vaše úloha?** vyberte **na virtuálním počítači Azure SAP HANA**.
2. Klikněte na **Spustit zjišťování**. Tím se iniciuje zjišťování nechráněných virtuálních počítačů se systémem Linux v oblasti trezoru.

   * Po zjištění se na portálu zobrazí nechráněné virtuální počítače uvedené podle názvu a skupiny prostředků.
   * Pokud virtuální počítač není uvedený podle očekávání, ověřte, jestli je už zálohovaný v trezoru.
   * Několik virtuálních počítačů může mít stejný název, ale patří do různých skupin prostředků.

3. V části **vybrat Virtual Machines**klikněte na odkaz pro stažení skriptu, který poskytuje oprávnění pro službu Azure Backup pro přístup k SAP HANA virtuálním počítačům pro zjištění databáze.
4. Spusťte skript na každém virtuálním počítači, který je hostitelem SAP HANA databází, které chcete zálohovat.
5. Po spuštění skriptu na virtuálních počítačích vyberte v části **vybrat Virtual Machines**virtuální počítače. Pak klikněte na **Vyhledat databáze**.
6. Azure Backup zjistí všechny databáze SAP HANA na virtuálním počítači. Během zjišťování Azure Backup registruje virtuální počítač s trezorem a na virtuálním počítači nainstaluje rozšíření. V databázi není nainstalován žádný agent.

    ![Zjišťování SAP HANAch databází](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Konfigurace zálohování  

Nyní povolte zálohování.

1. V kroku 2 klikněte na **Konfigurovat zálohu**.

    ![Konfigurace zálohování](./media/backup-azure-sap-hana-database/configure-backup.png)
2. V části **Vyberte položky, které chcete zálohovat**vyberte všechny databáze, které chcete chránit > **OK**.

    ![Vyberte položky, které chcete zálohovat.](./media/backup-azure-sap-hana-database/select-items.png)
3. V části **zásady zálohování** > **vyberte zásady zálohování**, vytvořte nové zásady zálohování pro databáze v souladu s pokyny uvedenými níže.

    ![Zvolit zásady zálohování](./media/backup-azure-sap-hana-database/backup-policy.png)
4. Po vytvoření zásady klikněte v nabídce **zálohování** na **Povolit zálohování**.

    ![Povolit zálohování](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Sledujte průběh konfigurace zálohování v oblasti **oznámení** na portálu.

### <a name="create-a-backup-policy"></a>Vytvoření zásady zálohování

Zásady zálohování definují, kdy se zálohují zálohy a jak dlouho se uchovávají.

* Zásada se vytvoří na úrovni trezoru.
* Několik trezorů může používat stejné zásady zálohování, ale je nutné použít zásady zálohování pro každý trezor.

Nastavení zásad určete následujícím způsobem:

1. Do pole **název zásady**zadejte název nové zásady.

   ![Zadejte název zásady.](./media/backup-azure-sap-hana-database/policy-name.png)
2. V **zásadách úplného zálohování**vyberte **četnost záloh**, zvolte **denně** nebo **týdně**.
   * **Denně**: vyberte hodinu a časové pásmo, ve kterém se spustí úloha zálohování.
       * Musíte spustit úplnou zálohu. Tuto možnost nemůžete vypnout.
       * Zásadu zobrazíte kliknutím na **úplné zálohování** .
       * Pro každodenní úplné zálohování nemůžete vytvořit rozdílové zálohy.
   * **Týdně**: vyberte den v týdnu, hodinu a časové pásmo, ve kterém se úloha zálohování spustí.

   ![Vybrat četnost zálohování](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. V části **Rozsah uchování**nakonfigurujte nastavení uchovávání pro úplnou zálohu.
    * Ve výchozím nastavení jsou vybrány všechny možnosti. Vymažte všechny limity rozsahu uchování, které nechcete používat, a nastavte ty, které chcete provést.
    * Minimální doba uchování pro jakýkoli typ zálohy (úplný/rozdíl/protokol) je sedm dní.
    * Body obnovení jsou označeny pro uchování na základě jejich rozsahu uchovávání. Pokud například vyberete denní úplnou zálohu, spustí se každý den jenom jedno úplné zálohování.
    * Záloha pro určitý den je označená a zachovaná na základě rozsahu a nastavení týdenního uchování.
    * Měsíční a roční rozsah uchování se chová podobným způsobem.

4. V nabídce **zásady úplného zálohování** klikněte na **OK** , aby se nastavení přijímalo.
5. Pokud chcete přidat rozdílové zásady, vyberte **rozdílové zálohování** .
6. V části **rozdílová zásada zálohování**vyberte **Povolit** a otevřete tak ovládací prvky četnost a uchování.
    * Ve většině případů můžete aktivovat jednu rozdílovou zálohu za den.
    * Rozdílové zálohy je možné uchovávat maximálně po dobu 180 dnů. Pokud budete potřebovat delší dobu uchovávání, je nutné použít úplné zálohování.

    ![Zásady rozdílového zálohování](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > Přírůstkové zálohování není aktuálně podporováno.

7. Kliknutím na **OK** zásadu uložte a vraťte se do nabídky hlavní **zásady zálohování** .
8. Vyberte **zálohování protokolu** a přidejte zásady zálohování transakčního protokolu.
    * V případě **zálohování protokolu**vyberte **Povolit**.  Toto nejde zakázat, protože SAP HANA spravuje všechny zálohy protokolů.
    * Nastavte četnost a ovládací prvky uchování.

    > [!NOTE]
    > Zálohy protokolu se začnou přesměrovat až po úspěšném úplném zálohování.

9. Kliknutím na **OK** zásadu uložte a vraťte se do nabídky hlavní **zásady zálohování** .
10. Až dokončíte definování zásad zálohování, klikněte na **OK**.

> [!NOTE]
> Každá záloha protokolu je zřetězena k předchozí úplné záloze, aby mohla tvořit řetěz obnovení. Tato úplná záloha se zachová, dokud neuplyne doba uchovávání poslední zálohy protokolu. To může znamenat, že úplná záloha se uchovává po dobu dalších let, aby se zajistilo, že se všechny protokoly mají obnovit. Předpokládejme, že uživatel má týdenní úplnou zálohu, denní rozdílovou a 2 hodinový protokol. Všechny z nich se uchovávají po dobu 30 dnů. Ale každý týden v plném rozsahu může být skutečně vyčištěný nebo odstraněný až po dokončení dalších úplných záloh, tj. po 30 až 7 dnech. Řekněme, že týdenní úplné zálohování probíhá na 16. listopadu. Podle zásad uchovávání informací by se měla uchovávat až do prosince 16. Poslední záloha protokolu pro tuto úplnou zálohu proběhne před dalším naplánovaným úplným 22. listopadu. Dokud nebude tento protokol k dispozici do prosince 22, nelze odstranit jeho plný 16. To znamená, že do prosince 22 se zachovají až do 16. listopadu.

## <a name="run-an-on-demand-backup"></a>Spuštění zálohování na vyžádání

Zálohy se spouštějí v souladu s plánem zásad. Zálohu můžete spustit na vyžádání následujícím způsobem:

1. V nabídce trezoru klikněte na položku **zálohované položky**.
2. V části **zálohované položky**vyberte virtuální počítač, na kterém běží databáze SAP HANA, a pak klikněte na **Zálohovat nyní**.
3. V části **Zálohovat nyní**pomocí ovládacího prvku kalendáře vyberte poslední den, kdy se má bod obnovení zachovat. Pak klikněte na **OK**.
4. Monitorujte oznámení na portálu. Průběh úlohy můžete monitorovat na řídicím panelu trezoru > **úlohy zálohování** ** > probíhá**. V závislosti na velikosti databáze může vytváření prvotní zálohy chvíli trvat.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Spustit zálohování SAP HANA studia na databázi s povoleným Azure Backup

Pokud chcete použít místní zálohu (pomocí HANA studia) databáze, která se zálohuje pomocí Azure Backup, udělejte toto:

1. Počkejte na dokončení všech úplných záloh nebo zálohování protokolů databáze. Podívejte se na stav v SAP HANA studiu nebo řídicím panelu.
2. Zakažte zálohování protokolů a nastavte Katalog zálohování na systém souborů pro příslušnou databázi.
3. Provedete to tak, že dvakrát kliknete na **systemdb** > **Konfigurace** > **Vybrat databázi** > **filtr (protokol)** .
4. Nastavte **enable_auto_log_backup** na **ne**.
5. Nastavte **log_backup_using_backint** na **false**.
6. Proveďte úplné zálohování databáze na vyžádání.
7. Počkejte na dokončení úplného zálohování a zálohování katalogu.
8. Vrátí předchozí nastavení zpátky do těch pro Azure:
    * Nastavte **enable_auto_log_backup** na **Ano**.
    * Nastavte **log_backup_using_backint** na **hodnotu true**.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak [obnovit SAP HANA databáze běžící na virtuálních počítačích Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Naučte se [spravovat SAP HANA databází zálohovaných pomocí Azure Backup](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
