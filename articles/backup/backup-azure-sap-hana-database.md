---
title: Zálohování databáze SAP HANA do Azure pomocí Azure Backup | Microsoft Docs
description: V tomto kurzu se dozvíte, jak zálohovat databázi SAP HANA do Azure pomocí služby Azure Backup.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dacurwin
ms.openlocfilehash: a11d454feb965907f3bd4e994c0916eeb7236fa7
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034568"
---
# <a name="back-up-an-sap-hana-database-to-azure"></a>Zálohování databáze SAP HANA do Azure

[Azure Backup](backup-overview.md) podporuje zálohování databází SAP HANA do Azure.

> [!NOTE]
> Tato funkce je aktuálně ve verzi Public Preview. V současnosti není připravené pro produkční prostředí a nemá zaručenou smlouvu SLA. 

## <a name="scenario-support"></a>Podpora scénářů

**Podpora** | **Podrobnosti**
--- | ---
**Podporované zeměpisných oblastech** | Austrálie – jihovýchod, východní Austrálie <br> Brazílie – jih <br> Kanada – střed, Kanada – východ <br> Jižní Východní Asie Východní Asie <br> Východní USA, Východní USA 2, Středozápadní USA, Západní USA, Západní USA 2, Střed USA – sever, Střed USA, Střed USA – jih<br> Indie – střed, Indie – jih <br> Japonsko – západ, Japonsko – východ<br> Jižní Korea – střed, Jižní Korea – jih <br> Severní Evropa, Západní Evropa <br> Velká Británie – jih Velká Británie – západ
**Podporované operační systémy virtuálních počítačů** | SLES 12 s aktualizací SP2 nebo SP3.
**Podporované verze HANA** | SDC v HANA 1. x, MDC na HANA 2. x < = SPS03

### <a name="current-limitations"></a>Aktuální omezení

- Můžete zálohovat jenom SAP HANA databáze běžící na virtuálních počítačích Azure.
- SAP HANA Backup můžete nakonfigurovat pouze v Azure Portal. Funkci nejde nakonfigurovat pomocí PowerShellu, CLI nebo REST API.
- Databáze můžete zálohovat pouze v režimu horizontálního navýšení kapacity.
- Protokoly databáze můžete zálohovat každých 15 minut. Zálohování protokolů začíná proudem až po úspěšném úplném zálohování databáze.
- Můžete přijímat úplné a rozdílové zálohy. Přírůstkové zálohování není aktuálně podporováno.
- Zásady zálohování nemůžete po použití pro SAP HANA zálohy upravovat. Pokud chcete zálohovat s jiným nastavením, vytvořte novou zásadu nebo přiřaďte jinou zásadu.
  - Pokud chcete vytvořit novou zásadu, v trezoru klikněte na **zásady** > **zálohování** > **a přidat** > **SAP HANA na virtuálním počítači Azure**a určete nastavení zásad.
  - Pokud chcete přiřadit jinou zásadu, ve vlastnostech virtuálního počítače, na kterém je spuštěná databáze, klikněte na název aktuální zásady. Pak na stránce **zásady zálohování** můžete vybrat jinou zásadu, která se má použít pro zálohování.

## <a name="prerequisites"></a>Požadavky

Před konfigurací zálohování se ujistěte, že jste provedli následující:

1. Na virtuálním počítači, na kterém běží databáze SAP HANA, nainstalujte oficiální balíček Microsoft [.NET Core Runtime 2,1](https://dotnet.microsoft.com/download/linux-package-manager/sles/runtime-current) . Všimněte si, že:
    - Potřebujete jenom balíček **dotnet-runtime-2,1** . Nepotřebujete **aspnetcore-runtime-2,1**.
    - Pokud virtuální počítač nemá přístup k Internetu, zrcadlení nebo poskytne offline mezipaměť pro dotnet-runtime-2,1 (a všechny závislé rpm) z informačního kanálu balíčku od společnosti Microsoft, který je uvedený na stránce.
    - Při instalaci balíčku se může zobrazit výzva k zadání možnosti. Pokud ano, zadejte **Řešení 2**.

        ![Možnost instalace balíčku](./media/backup-azure-sap-hana-database/hana-package.png)

2. Na virtuálním počítači nainstalujte a povolte balíčky ovladačů ODBC z oficiálního balíčku nebo média SLES pomocí zypperu následujícím způsobem:

    ```unix
    sudo zypper update
    sudo zypper install unixODBC
    ```

3. Umožněte připojení z virtuálního počítače k Internetu, aby bylo možné dosáhnout Azure, jak je popsáno v [následujícím](#set-up-network-connectivity)postupu.

4. Spusťte předregistrační skript ve virtuálním počítači, kde je nainstalovaná verze HANA jako uživatel root. Skript je k dispozici na [portálu](#discover-the-databases) v toku a je vyžadován pro nastavení [správných oprávnění](backup-azure-sap-hana-database-troubleshoot.md#setting-up-permissions).

### <a name="set-up-network-connectivity"></a>Nastavení připojení k síti

Pro všechny operace potřebuje virtuální počítač SAP HANA připojení k veřejným IP adresám Azure. Operace virtuálních počítačů (zjišťování databáze, konfigurace zálohování, plánování záloh, obnovení bodů obnovení atd.) nemůžou pracovat bez připojení. Navažte připojení tak, že povolíte přístup k rozsahům IP adres datacentra Azure: 

- Můžete si stáhnout [rozsahy IP adres](https://www.microsoft.com/download/details.aspx?id=41653) pro datacentra Azure a pak přístup k těmto IP adresám.
- Pokud používáte skupiny zabezpečení sítě (skupin zabezpečení sítě), můžete pomocí [značky služby](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) AzureCloud povolíte všechny veřejné IP adresy Azure. K úpravě pravidel NSG můžete použít [rutinu Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) .

## <a name="onboard-to-the-public-preview"></a>Připojit se k veřejné verzi Preview

Připojte se k veřejné verzi Preview následujícím způsobem:

- Na portálu Zaregistrujte ID předplatného pro poskytovatele služby Recovery Services podle [tohoto článku](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal). 
- Spusťte tuto rutinu pro PowerShell. Měla by být dokončena jako zaregistrovaná.

    ```powershell
    PS C:>  Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```



[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Zjištění databází

1. V trezoru v **Začínáme**klikněte na **zálohovat**. V aplikaci **kde je spuštěná vaše úloha?** vyberte **na virtuálním počítači Azure SAP HANA**.
2. Klikněte na **Spustit zjišťování**. Tím se iniciuje zjišťování nechráněných virtuálních počítačů se systémem Linux v oblasti trezoru.

   - Po zjištění se na portálu zobrazí nechráněné virtuální počítače uvedené podle názvu a skupiny prostředků.
   - Pokud virtuální počítač není uvedený podle očekávání, ověřte, jestli je už zálohovaný v trezoru.
   - Několik virtuálních počítačů může mít stejný název, ale patří do různých skupin prostředků.

3. V části **vybrat Virtual Machines**klikněte na odkaz pro stažení skriptu, který poskytuje oprávnění pro službu Azure Backup pro přístup k SAP HANA virtuálním počítačům pro zjišťování databáze.
4. Spusťte skript na každém virtuálním počítači, který je hostitelem SAP HANA databází, které chcete zálohovat.
5. Po spuštění skriptu na virtuálních počítačích vyberte v části **vybrat Virtual Machines**virtuální počítače. Pak klikněte na **Vyhledat databáze**.
6. Azure Backup zjistí všechny databáze SAP HANA na virtuálním počítači. Během zjišťování Azure Backup registruje virtuální počítač s trezorem a na virtuálním počítači nainstaluje rozšíření. V databázi není nainstalován žádný agent.

    ![Zjišťování SAP HANAch databází](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Konfigurace zálohování  

Nyní povolte zálohování.

1. V kroku 2 klikněte na **Konfigurovat zálohu**.
2. V části **Vyberte položky, které chcete zálohovat**vyberte všechny databáze, které chcete chránit > **OK**.
3. V části **zásady** > zálohování**vyberte zásady zálohování**, vytvořte nové zásady zálohování pro databáze, a to v souladu s pokyny uvedenými níže.
4. Po vytvoření zásady klikněte v nabídce **zálohování** na **Povolit zálohování**.
5. Sledujte průběh konfigurace zálohování v oblasti **oznámení** na portálu.

### <a name="create-a-backup-policy"></a>Vytvoření zásady zálohování

Zásady zálohování definují, kdy se zálohují zálohy a jak dlouho se uchovávají.

- Zásada se vytvoří na úrovni trezoru.
- Několik trezorů může používat stejné zásady zálohování, ale je nutné použít zásady zálohování pro každý trezor.

Nastavení zásad určete následujícím způsobem:

1. Do pole **název zásady**zadejte název nové zásady.
2. V **zásadách úplného zálohování**vyberte **četnost záloh**, zvolte **denně** nebo **týdně**.
   - **Denně**: Vyberte hodinu a časové pásmo, ve kterém se spustí úloha zálohování.
   
       - Musíte spustit úplnou zálohu. Tuto možnost nemůžete vypnout.
       - Zásadu zobrazíte kliknutím na **úplné zálohování** .
       - Pro každodenní úplné zálohování nemůžete vytvořit rozdílové zálohy.
       
   - **Týdenní**: Vyberte den v týdnu, hodinu a časové pásmo, ve kterém se úloha zálohování spustí.
3. V části **Rozsah uchování**nakonfigurujte nastavení uchovávání pro úplnou zálohu.
    - Ve výchozím nastavení jsou vybrány všechny možnosti. Vymažte všechny limity rozsahu uchování, které nechcete používat, a nastavte ty, které chcete provést.
    - Minimální doba uchování pro jakýkoli typ zálohy (úplný/rozdíl/protokol) je sedm dní.
    - Body obnovení jsou označeny pro uchování na základě jejich rozsahu uchovávání. Pokud například vyberete denní úplnou zálohu, spustí se každý den jenom jedno úplné zálohování.
    - Záloha pro určitý den je označená a zachovaná na základě rozsahu a nastavení týdenního uchování.
    - Měsíční a roční rozsah uchování se chová podobným způsobem.

4. V nabídce **zásady úplného zálohování** klikněte na **OK** , aby se nastavení přijímalo.
5. Pokud chcete přidat rozdílové zásady, vyberte **rozdílové zálohování** .
6. V části **rozdílová zásada zálohování**vyberte **Povolit** a otevřete tak ovládací prvky četnost a uchování.
    - Ve většině případů můžete aktivovat jednu rozdílovou zálohu za den.
    - Rozdílové zálohy je možné uchovávat maximálně po dobu 180 dnů. Pokud budete potřebovat delší dobu uchovávání, je nutné použít úplné zálohování.

    > [!NOTE]
    > Přírůstkové zálohování není aktuálně podporováno. 

7. Kliknutím na **OK** zásadu uložte a vraťte se do nabídky hlavní **zásady zálohování** .
8. Vyberte **zálohování protokolu** a přidejte zásady zálohování transakčního protokolu.
    - V případě **zálohování protokolu**vyberte **Povolit**.
    - Nastavte četnost a ovládací prvky uchování.

    > [!NOTE]
    > Zálohy protokolu se začnou přesměrovat až po úspěšném úplném zálohování.

9. Kliknutím na **OK** zásadu uložte a vraťte se do nabídky hlavní **zásady zálohování** .
10. Až dokončíte definování zásad zálohování, klikněte na **OK**.


## <a name="run-an-on-demand-backup"></a>Spuštění zálohování na vyžádání

Zálohy se spouštějí v souladu s plánem zásad. Zálohu můžete spustit na vyžádání následujícím způsobem:


1. V nabídce trezoru klikněte na položku **zálohované položky**.
2. V části **zálohované položky**vyberte virtuální počítač, na kterém běží databáze SAP HANA, a pak klikněte na **Zálohovat nyní**.
3. V části **Zálohovat nyní**pomocí ovládacího prvku kalendáře vyberte poslední den, kdy se má bod obnovení zachovat. Pak klikněte na **OK**.
4. Monitorujte oznámení na portálu. Průběh úlohy můžete monitorovat na řídicím panelu trezoru > **úlohy zálohování** > probíhá. V závislosti na velikosti databáze může vytváření prvotní zálohy chvíli trvat.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Spustit zálohování SAP HANA studia na databázi s povoleným Azure Backup

Pokud chcete použít místní zálohu (pomocí HANA studia) databáze, která se zálohuje pomocí Azure Backup, udělejte toto:

1. Počkejte na dokončení všech úplných záloh nebo zálohování protokolů databáze. Podívejte se na stav v SAP HANA studiu.
2. Zakažte zálohování protokolů a nastavte Katalog zálohování na systém souborů pro příslušnou databázi.
3. Provedete to tak, že dvakrát kliknete na**Konfigurace** >  **SystemDB** > **vybrat databázový** > **filtr (protokol)** .
4. Nastavte **enable_auto_log_backup** na **ne**.
5. Nastavte **log_backup_using_backint** na **false**.
6. Proveďte úplnou zálohu databáze ad hoc.
7. Počkejte na dokončení úplného zálohování a zálohování katalogu.
8. Vrátí předchozí nastavení zpátky do těch pro Azure:
    - Nastavte **enable_auto_log_backup** na **Ano**.
    - Nastavte **log_backup_using_backint** na **true**.



## <a name="next-steps"></a>Další postup

[Přečtěte si](backup-azure-sap-hana-database-troubleshoot.md) , jak řešit běžné chyby při používání služby SAP HANA Backup na virtuálních počítačích Azure.
[Přečtěte si informace o](backup-azure-arm-vms-prepare.md) zálohování virtuálních počítačů Azure.
