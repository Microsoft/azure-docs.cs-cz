---
title: Zálohování databáze SAP HANA do Azure s využitím Azure Backup
description: V tomto článku se dozvíte, jak zálohovat databázi SAP HANA do virtuálních počítačů Azure pomocí služby Azure Backup.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 3246f6cf8046e0a0c5795059ad3448b70130e7e1
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/26/2019
ms.locfileid: "75496951"
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

## <a name="prerequisites"></a>Požadavky

Informace o nastavení databáze pro zálohování najdete v oddílech [požadavků](tutorial-backup-sap-hana-db.md#prerequisites) a [Nastavení oprávnění](tutorial-backup-sap-hana-db.md#setting-up-permissions) .

### <a name="set-up-network-connectivity"></a>Nastavení připojení k síti

Pro všechny operace potřebuje virtuální počítač SAP HANA připojení k veřejným IP adresám Azure. Operace virtuálních počítačů (zjišťování databáze, konfigurace zálohování, plánování záloh, obnovení bodů obnovení atd.) nemůžou pracovat bez připojení. Navažte připojení tak, že povolíte přístup k rozsahům IP adres datacentra Azure:

* Můžete si stáhnout [rozsahy IP adres](https://www.microsoft.com/download/details.aspx?id=41653) pro datacentra Azure a pak přístup k těmto IP adresám.
* Pokud používáte skupiny zabezpečení sítě (skupin zabezpečení sítě), můžete pomocí [značky služby](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) AzureCloud povolíte všechny veřejné IP adresy Azure. K úpravě pravidel NSG můžete použít [rutinu Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) .
* Port 443 by měl být přidán do seznamu povolených, protože přenos prostřednictvím protokolu HTTPS.

## <a name="onboard-to-the-public-preview"></a>Připojit se k veřejné verzi Preview

Připojte se k veřejné verzi Preview následujícím způsobem:

* Na portálu Zaregistrujte ID předplatného pro poskytovatele služby Recovery Services podle [tohoto článku](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal).
* Pro modul AZ v PowerShellu spusťte tuto rutinu. Měla by být dokončena jako zaregistrovaná.

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```
* Pokud používáte modul "AzureRM" v prostředí PowerShell, spusťte tuto rutinu. Měla by být dokončena jako zaregistrovaná.

    ```powershell
    Register-AzureRmProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```
    

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

    ![Backup – konfigurace](./media/backup-azure-sap-hana-database/configure-backup.png)
2. V části **Vyberte položky, které chcete zálohovat**vyberte všechny databáze, které chcete chránit > **OK**.

    ![Vyberte položky, které chcete zálohovat.](./media/backup-azure-sap-hana-database/select-items.png)
3. V části **zásady zálohování** > **vyberte zásady zálohování**, vytvořte nové zásady zálohování pro databáze v souladu s pokyny uvedenými níže.

    ![Vybrat zásady zálohování](./media/backup-azure-sap-hana-database/backup-policy.png)
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
