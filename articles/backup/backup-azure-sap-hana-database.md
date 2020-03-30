---
title: Zálohování databáze SAP HANA do Azure pomocí Azure Backup
description: V tomto článku se dozvíte, jak zálohovat databázi SAP HANA do virtuálních počítačů Azure pomocí služby Azure Backup.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: deedd4d2553b3b06f76f698fdb2425a8d3878d23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248057"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Zálohování databází SAP HANA na virtuálních počítačích Azure

Databáze SAP HANA jsou kritické úlohy, které vyžadují cíl nízkého bodu obnovení (RPO) a dlouhodobé uchovávání. Databáze SAP HANA spuštěné na virtuálních počítačích Azure můžete zálohovat pomocí [Azure Backup](backup-overview.md).

Tento článek ukazuje, jak zálohovat databáze SAP HANA, které běží na virtuálních počítačích Azure do trezoru služby Azure Backup Recovery Services.

V tomto článku se dozvíte, jak:
> [!div class="checklist"]
>
> * Vytvoření a konfigurace trezoru
> * Zjišťování databází
> * Konfigurace zálohování
> * Spuštění úlohy zálohování na vyžádání

>[!NOTE]
>**Obnovitelné odstranění pro SQL server v Azure VM a obnovitelné odstranění pro SAP HANA v úlohách virtuálních virtuálních počítače Azure** je teď dostupné ve verzi Preview.<br>
>Chcete-li se zaregistrovat do náhledu, napište nám naAskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Požadavky

Naleznete [požadavky](tutorial-backup-sap-hana-db.md#prerequisites) a co skript [předběžné registrace dělá](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) oddíly pro nastavení databáze pro zálohování.

### <a name="set-up-network-connectivity"></a>Nastavení připojení k síti

Pro všechny operace vyžaduje virtuální počítač SAP HANA připojení k veřejným IP adresám Azure. Operace virtuálních počítačů (zjišťování databáze, konfigurace zálohování, plánování zálohování, body obnovení a tak dále) se nezdaří bez připojení k veřejným IP adresám Azure.

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

**Nasazení serveru proxy HTTP pro směrování provozu**. Když zálohujete databázi SAP HANA na virtuálním počítači Azure, rozšíření zálohy na virtuálním počítači používá rozhraní API HTTPS k odesílání příkazů pro správu do Azure Backup a dat do Azure Storage. Rozšíření zálohování také používá Azure AD pro ověřování. Směrovat provoz rozšíření zálohy pro tyto tři služby prostřednictvím proxy serveru HTTP. Rozšíření jsou jedinou součástí, která je nakonfigurována pro přístup k veřejnému internetu.

Možnosti připojení zahrnují následující výhody a nevýhody:

**Možnost** | **Výhody** | **Nevýhody**
--- | --- | ---
Povolit rozsahy IP adres | Žádné další náklady | Komplexní správa, protože rozsahy IP adres se v průběhu času mění <br/><br/> Poskytuje přístup k celému Azure, nejen k Úložišti Azure.
Použití značek služby NSG | Snadnější správa při automatickém sloučení změn rozsahu <br/><br/> Žádné další náklady <br/><br/> | Lze použít pouze s nsgs <br/><br/> Poskytuje přístup k celé službě
Použití značek FQDN brány Azure Firewall | Snadnější správa při automatické správě požadovaných souborů FQDN | Lze použít pouze s Azure Firewall
Použití proxy serveru HTTP | Podrobné ovládání v proxy serveru přes adresy URL úložiště je povoleno <br/><br/> Jednotný bod přístupu k internetu k virtuálním operátorům <br/><br/> Nepodléhá změnám IP adres Azure | Dodatečné náklady na spuštění virtuálního počítače se softwarem proxy

#### <a name="private-endpoints"></a>Soukromé koncové body

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Seznamte se s databázemi

1. V trezoru klikněte v **části Začínáme**na **položku Zálohování**. V **yberte Kde běží vaše úloha?**, vyberte SAP **HANA v virtuálním počítači Azure**.
2. Klepněte na tlačítko **Spustit zjišťování**. To iniciuje zjišťování nechráněných virtuálních počítačích SIP v oblasti úschovny.

   * Po zjišťování se na portálu zobrazí nechráněné virtuální počítače uvedené podle názvu a skupiny prostředků.
   * Pokud virtuální virtuální může být vypsánpodle očekávaných očekávání, zkontrolujte, jestli už je zálohovaný v trezoru.
   * Více virtuálních počítačů může mít stejný název, ale patří do různých skupin prostředků.

3. V **části Vybrat virtuální počítače**klikněte na odkaz a stáhněte si skript, který poskytuje oprávnění pro službu Azure Backup pro přístup k virtuálním počítačům SAP HANA pro zjišťování databáze.
4. Spusťte skript na každém virtuálním počítači hostující mašsko databáze SAP HANA, které chcete zálohovat.
5. Po spuštění skriptu na virtuálních počítačích vyberte v **nabídce Virtuální počítače**virtuální počítače. Potom klepněte na tlačítko **Objevit centrální soubory**.
6. Azure Backup zjišťuje všechny databáze SAP HANA na virtuálním počítači. Během zjišťování Azure Backup zaregistruje virtuální počítač s trezorem a nainstaluje rozšíření na virtuální počítač. V databázi není nainstalován žádný agent.

    ![Seznamte se s databázemi SAP HANA](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Konfigurace zálohování  

Nyní povolte zálohování.

1. V kroku 2 klepněte na **tlačítko Konfigurovat zálohování**.

    ![Konfigurace zálohování](./media/backup-azure-sap-hana-database/configure-backup.png)
2. V **části Vybrat položky, které chcete zálohovat**, vyberte všechny databáze, které chcete chránit, > **OK**.

    ![Výběr položek, které chcete zálohovat](./media/backup-azure-sap-hana-database/select-items.png)
3. V **části Zásady** > zálohování**Zvolte zásady zálohování**, vytvořte nové zásady zálohování pro databáze v souladu s níže uvedenými pokyny.

    ![Zvolte zásady zálohování](./media/backup-azure-sap-hana-database/backup-policy.png)
4. Po vytvoření zásady klepněte v nabídce **Zálohování** na **položku Povolit zálohování**.

    ![Povolit zálohování](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Sledujte průběh konfigurace zálohování v oblasti **Oznámení** na portálu.

### <a name="create-a-backup-policy"></a>Vytvoření zásad zálohování

Zásady zálohování definují, kdy jsou zálohy prováděny a jak dlouho jsou zachovány.

* Zásada je vytvořena na úrovni úschovny.
* Více trezorů může používat stejné zásady zálohování, ale zásady zálohování je nutné použít pro každý trezor.

Nastavení zásad zadejte následujícím způsobem:

1. V **části Název zásad**zadejte název nové zásady.

   ![Zadejte název zásady.](./media/backup-azure-sap-hana-database/policy-name.png)
2. V **zásadách úplného zálohování**vyberte **frekvenci zálohování**, zvolte **Denně** nebo **Týdně**.
   * **Denně**: Vyberte hodinu a časové pásmo, ve kterém začíná úloha zálohování.
       * Je nutné spustit úplnou zálohu. Tuto možnost nelze vypnout.
       * Chcete-li zobrazit zásadu, klepněte na tlačítko **Úplné zálohování.**
       * Nelze vytvořit rozdílové zálohy pro denní úplné zálohy.
   * **Týdně**: Vyberte den v týdnu, hodinu a časové pásmo, ve kterém je úloha zálohy spuštěna.

   ![Vybrat frekvenci zálohování](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. V **rozsahu uchování**nakonfigurujte nastavení uchovávání informací pro úplnou zálohu.
    * Ve výchozím nastavení jsou vybrány všechny možnosti. Zrušte všechny limity rozsahu uchování, které nechcete používat, a nastavte ty, které nechcete používat.
    * Minimální doba uchování pro jakýkoli typ zálohy (full/differential/log) je sedm dní.
    * Body obnovení jsou označeny pro uchovávání na základě jejich rozsah uchování. Pokud například vyberete denní úplnou zálohu, bude každý den spuštěna pouze jedna úplná záloha.
    * Záloha pro konkrétní den je označena a zachována na základě týdenního rozsahu uchovávání a nastavení.
    * Měsíční a roční retenční rozsahy se chovají podobně.

4. V nabídce **zásad úplného zálohování** klepněte na tlačítko **OK,** abyste přijali nastavení.
5. Chcete-li přidat rozdílovou zásadu, vyberte **rozdílovou zálohu.**
6. V **zásadách rozdílového zálohování**vyberte **Povolit,** chcete-li otevřít ovládací prvky četnosti a uchovávání informací.
    * Maximálně můžete spustit jednu rozdílovou zálohu za den.
    * Rozdílové zálohy lze uchovávat maximálně 180 dní. Pokud potřebujete delší uchovávání informací, musíte použít úplné zálohy.

    ![Zásady rozdílového zálohování](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > Přírůstkové zálohy nejsou aktuálně podporovány.

7. Klepnutím na **tlačítko OK** uložte zásadu a vraťte se do hlavní nabídky **zásad zálohování.**
8. Chcete-li přidat zásady zálohování transakčních protokolů, vyberte **možnost Záloha protokolů.**
    * V **seznamu Zálohování protokolů**vyberte **povolit**.  To nelze zakázat, protože SAP HANA spravuje všechny zálohy protokolů.
    * Nastavte kontroly frekvence a retence.

    > [!NOTE]
    > Zálohy protokolů začnou tok až po dokončení úspěšné úplné zálohy.

9. Klepnutím na **tlačítko OK** uložte zásadu a vraťte se do hlavní nabídky **zásad zálohování.**
10. Po dokončení definování zásad zálohování klepněte na tlačítko **OK**.

> [!NOTE]
> Každá záloha protokolu je zřetězena k předchozí úplné zálohě a vytvoří řetězec obnovení. Tato úplná záloha bude zachována, dokud nevyprší platnost uchovávání poslední zálohy protokolu. To může znamenat, že úplné zálohování je zachována po dobu další a ujistěte se, že všechny protokoly lze obnovit. Předpokládejme, že uživatel má týdenní úplné zálohování, denní rozdíl a 2 hodiny protokoly. Všechny jsou uchovávány po dobu 30 dnů. Ale týdenní plný může být opravdu vyčištěn / smazán až po další úplné zálohování je k dispozici, tj., po 30 + 7 dnů. Řekněme, že týdenní plná záloha se stane na 16.listopadu. Podle zásad uchovávání informací by měla být zachována až do 16.prosince. Poslední záloha protokolu pro tento plný se stane před další naplánované plné, na 22.listopadu. Dokud tento protokol je k dispozici až do 22.prosince, 16.listopadu plné nelze odstranit. Takže 16.listopadu plné je zachována až do 22.prosince.

## <a name="run-an-on-demand-backup"></a>Spuštění zálohy na vyžádání

Zálohy spustit v souladu s plánem zásad. Zálohu můžete spustit na vyžádání následujícím způsobem:

1. V nabídce úschovny klikněte na **Zálohovat položky**.
2. V **části Položky zálohování**vyberte virtuální ms se spuštěnou databází SAP HANA a klikněte na **Zálohovat .**
3. V **aplikaci Backup Now**vyberte pomocí ovládacího prvku kalendář poslední den, kdy má být bod obnovení zachován. Pak klikněte na **OK**.
4. Sledujte oznámení portálu. Průběh úlohy můžete sledovat na řídicím panelu trezoru >**probíhající** **úlohy** > zálohování . V závislosti na velikosti databáze může chvíli trvat vytvoření počáteční zálohy.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Spuštění zálohy SAP HANA Studio v databázi s povoleným zálohováním Azure

Pokud chcete provést místní zálohu (pomocí HANA Studio) databáze, která je zálohována pomocí služby Azure Backup, postupujte takto:

1. Počkejte na všechny úplné nebo protokol zálohy pro dokončení databáze. Zkontrolujte stav v SAP HANA Studio / Cockpit.
2. Zakažte zálohování protokolů a nastavte katalog záloh do systému souborů pro příslušnou databázi.
3. Chcete-li to provést, poklepejte na filtr**databáze** > **(Protokolu)** konfigurace **systemdb** > **.** > 
4. Nastavit **enable_auto_log_backup** na **ne**.
5. Nastavte **log_backup_using_backint** na **hodnotu False**.
6. Provést úplnou zálohu databáze na vyžádání.
7. Počkejte na dokončení úplné zálohy a zálohy katalogu.
8. Vrátit předchozí nastavení zpět na nastavení pro Azure:
    * Nastavte **enable_auto_log_backup** na **Ano**.
    * Nastavte **log_backup_using_backint** na **hodnotu True**.

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak [obnovit databáze SAP HANA spuštěné na virtuálních počítačích Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Zjistěte, jak [spravovat databáze SAP HANA, které jsou zálohované pomocí azure backupu](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
