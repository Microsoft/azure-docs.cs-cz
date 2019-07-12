---
title: Proveďte zálohu databáze SAP HANA do Azure pomocí služby Azure Backup | Dokumentace Microsoftu
description: Tento kurz vysvětluje, jak zálohovat databázi SAP HANA do Azure pomocí služby Azure Backup.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: raynew
ms.openlocfilehash: 1c5c38e9af58aa9468fb0e22491327d3a35f0dda
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656650"
---
# <a name="back-up-an-sap-hana-database"></a>Zálohovat databázi SAP HANA

[Azure Backup](backup-overview.md) podporuje zálohování databáze SAP HANA do Azure.

> [!NOTE]
> Tato funkce je aktuálně ve verzi public preview. Aktuálně není připraveno na produkční a nemá zaručené smlouvy SLA. 

## <a name="scenario-support"></a>Scénáře podpory

**Podpora** | **Podrobnosti**
--- | ---
**Podporované zeměpisných oblastech** | Austrálie – jihovýchod, Austrálie – východ <br> Brazílie – jih <br> Canada Central, Canada East <br> Jihovýchodní Asie, jihovýchodní Asie <br> USA – východ, USA – východ 2, střed USA – Západ, USA – Západ, USA – západ 2, USA (střed) – sever, střed USA, USA (střed) – jih<br> India Central, India South <br> Japonsko – západ, Japonsko – východ<br> Jižní Korea – střed, Jižní Korea – jih <br> Severní Evropa, Západní Evropa <br> Velká Británie – Jih, Velká Británie – západ
**Podporované operační systémy pro virtuální počítač** | SLES 12 SP2 nebo s aktualizací SP3.
**Podporované verze HANA** | SDC on HANA 1.x MDC on HANA 2.x < = SPS03

### <a name="current-limitations"></a>Aktuální omezení

- Zálohovat můžete pouze databáze SAP HANA, které jsou spuštěné na virtuálních počítačích Azure.
- Zálohování SAP HANA můžete konfigurovat pouze na webu Azure Portal. Tuto funkci nelze konfigurovat pomocí Powershellu, rozhraní příkazového řádku nebo rozhraní REST API.
- Zálohovat můžete pouze databáze v režimu vertikálně navýšit kapacitu.
- Můžete zálohovat databázi protokolů každých 15 minut. Zálohy protokolů pouze začít tok po dokončení úspěšného úplné zálohy databáze.
- Můžete provést úplné a rozdílové zálohy. Přírůstkové zálohování se momentálně nepodporuje.
- Zásady zálohování nelze změnit po použití zálohování SAP HANA. Pokud chcete zálohování s různými nastaveními, vytvořte novou zásadu nebo přiřazení různých zásad.
  - Chcete-li vytvořit novou zásadu, v trezoru klikněte na tlačítko **zásady** > **zásady zálohování** >  **+ přidat** > **SAP HANA v Virtuální počítač Azure**a zadejte nastavení zásad.
  - Pro přiřazení různých zásad ve vlastnostech virtuálního počítače s databází, klikněte na název aktuální zásady. Pak na **zásady zálohování** stránky můžete vybrat jinou zásadu pro zálohování.

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že proveďte následující kroky konfigurace zálohování:

1. Na virtuálním počítači s databází SAP HANA, nainstalovat agenta Microsoft official [.NET Core Runtime 2.1](https://dotnet.microsoft.com/download/linux-package-manager/sles/runtime-current) balíčku. Všimněte si, že:
    - Potřebujete jenom **dotnet. modul runtime 2.1** balíčku. Není nutné **aspnetcore. modul runtime 2.1**.
    - Pokud virtuální počítač nemá Internetu přístup, zrcadlení nebo poskytovat offline mezipaměť pro dotnet. modul runtime 2.1 (a všechny závislé RPMs) z balíčku Microsoft informačního kanálu zadané stránky.
    - Během instalace balíčku může být výzva k zadání možností. Pokud ano, zadejte **řešení 2**.

        ![Možnost instalace balíčku](./media/backup-azure-sap-hana-database/hana-package.png)

2. Na virtuálním počítači instalace a povolení balíčky ovladačů ODBC z oficiální SLES balíčku/média pomocí zypperu, následujícím způsobem:

    ```unix
    sudo zypper update
    sudo zypper install unixODBC
    ```

3. Povolit připojení z virtuálního počítače k Internetu, takže ho můžete navázat komunikaci s Azure, jak je popsáno v postupu [níže](#set-up-network-connectivity).

4. Spusťte skript před registrací ve virtuálním počítači nainstalovanou HANA jako uživatel root. Skript k dispozici [na portálu](#discover-the-databases) v toku a je potřeba nastavit [klikněte pravým tlačítkem myši oprávnění](backup-azure-sap-hana-database-troubleshoot.md#setting-up-permissions).

### <a name="set-up-network-connectivity"></a>Nastavit připojení k síti

Virtuální počítač SAP HANA pro všechny operace, potřebuje připojení k veřejným IP adresám Azure. Operace virtuálního počítače (zjišťování databází, konfigurace zálohování, naplánovat zálohování, obnovení body obnovení a tak dále) nemůže pracovat bez připojení. Navázat připojení tím, že přístup k rozsahy IP adres datacentra Azure: 

- Můžete stáhnout [rozsahy IP adres](https://www.microsoft.com/download/details.aspx?id=41653) pro datová centra Azure a pak umožnit přístup k tyto IP adresy.
- Pokud používáte skupiny zabezpečení sítě (Nsg), můžete AzureCloud [značka služby](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) chcete povolit všechny Azure veřejných IP adres. Můžete použít [rutiny Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) upravit pravidla skupiny zabezpečení sítě.

## <a name="onboard-to-the-public-preview"></a>Připojit k verzi public preview

Připojit k verzi public preview následujícím způsobem:

- Na portálu pro registraci k poskytovateli služby Recovery Services pomocí ID vašeho předplatného [článku](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal). 
- Pro prostředí PowerShell spusťte tuto rutinu. By se měla dokončit jako "Registrováno".

    ```powershell
    PS C:>  Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```



[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Zjistit databáze

1. V trezoru v **Začínáme**, klikněte na tlačítko **zálohování**. V **ve kterém je spuštěná vaše úloha?** vyberte **SAP HANA ve virtuálním počítači Azure**.
2. Klikněte na tlačítko **spustit vyhledávání**. Tím se zahájí zjišťování nechráněné virtuální počítače s Linuxem v oblasti trezoru.

   - Po zjištění, nechráněné virtuální počítače se na portálu, seřazené podle názvu a skupinu prostředků.
   - Pokud virtuální počítač není uvedená podle očekávání, zkontrolujte, jestli je už zálohovaná do trezoru.
   - Několik virtuálních počítačů může mít stejný název, ale patří do různých skupin prostředků.

3. V **výběr virtuálních počítačů**, klikněte na odkaz ke stažení skriptu, který poskytuje oprávnění pro přístup k virtuálním počítačům SAP HANA pro zjišťování databází služby Azure Backup
4. Spusťte skript na každém virtuálním počítači, který je hostitelem databáze SAP HANA, které chcete zálohovat.
5. Po spuštění skriptu na virtuálních počítačích, v **výběr virtuálních počítačů**, vyberte virtuální počítače. Pak klikněte na tlačítko **zjišťování databází**.
6. Azure Backup zjistí všechny databáze SAP HANA ve virtuálním počítači. Během zjišťování Azure Backup v trezoru zaregistruje virtuální počítač a nainstaluje rozšíření ve virtuálním počítači. V databázi se neinstaluje žádný agent.

    ![Zjistit databáze SAP HANA](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Konfigurace zálohování  

Nyní povolte zálohování.

1. V kroku 2, klikněte na tlačítko **konfigurace zálohování**.
2. V **vyberte položky, které chcete zálohovat**, vyberte všechny databáze, které chcete chránit > **OK**.
3. V **zásady zálohování** > **výběr zásady zálohování**, vytvořte nové zásady zálohování pro databáze, v souladu s podle následujících pokynů.
4. Po vytvoření zásady, na **zálohování** nabídky, klikněte na tlačítko **povolit zálohování**.
5. Sledovat průběh na konfiguraci zálohování **oznámení** části portálu.

### <a name="create-a-backup-policy"></a>Vytvoření zásady zálohování

Zásady zálohování definují, kdy zálohy jsou prováděny, a jak dlouho se uchovávají.

- Zásady se vytvářejí na úrovni trezoru.
- Více trezorů můžete použít stejné zásady zálohování, ale musíte použít zásadu zálohování, která pro každý trezor.

Zadejte nastavení zásad takto:

1. V **Název_zásady**, zadejte název pro novou zásadu.
2. V **úplná záloha zásad**, vyberte **četnost záloh**, zvolte **denní** nebo **týdenní**.
   - **Denní**: Vyberte hodiny a časové pásmo, ve kterém se spustí úlohy zálohování.
   
       - Je nutné spustit úplné zálohování. Tuto možnost nelze vypnout.
       - Klikněte na tlačítko **úplná záloha** zásadách.
       - Nelze vytvořit rozdílové zálohování pro denní úplná zálohování.
       
   - **Týdenní**: Vyberte den v týdnu, hodiny a časové pásmo, ve kterém se spouští úloha zálohování.
3. V **rozsah uchování**, nakonfigurujte nastavení uchovávání pro úplné zálohování.
    - Ve výchozím nastavení jsou vybrané všechny možnosti. Vymažte všechny omezení rozsahu uchování, které nechcete používat a nastavte ty, které provedete.
    - Období minimální doby uchování pro jakýkoli typ zálohování (úplné a rozdílové/protokolu) je sedm dní.
    - Body obnovení jsou označené pro uchovávání informací podle jejich rozsah uchování. Například pokud vyberete každodenní úplnou zálohu, se aktivuje pouze jeden úplné zálohování každý den.
    - Zálohování pro určitý den je příznakem a uchovávají na základě týdně rozsah uchování a nastavení.
    - Měsíční a roční rozsahy uchovávání se chovají podobně.

4. V **úplná záloha zásad** nabídky, klikněte na tlačítko **OK** potvrďte nastavení.
5. Vyberte **rozdílovou zálohu** přidání rozdílové zásady.
6. V **rozdílovou zálohu zásad**vyberte **povolit** otevřete ovládací prvky frekvence a uchování.
    - Maximálně můžete aktivovat jeden rozdílové zálohy za den.
    - Rozdílové zálohy je možné uchovávat maximálně na 180 dnů. Pokud budete potřebovat delší dobu uchování, musíte použít úplné zálohy.

    > [!NOTE]
    > Přírůstkové zálohování se momentálně nepodporují. 

7. Klikněte na tlačítko **OK** zásadu uložte a vraťte se do hlavní **zásady zálohování** nabídky.
8. Vyberte **zálohování protokolu** přidat zásadu zálohování transakčního protokolu
    - V **záloha protokolu**vyberte **povolit**.
    - Nastavte četnost a uchování ovládací prvky.

    > [!NOTE]
    > Zálohy protokolů pouze začít tok po dokončení úspěšného úplné zálohování.

9. Klikněte na tlačítko **OK** zásadu uložte a vraťte se do hlavní **zásady zálohování** nabídky.
10. Po dokončení definování zásad zálohování, klikněte na tlačítko **OK**.


## <a name="run-an-on-demand-backup"></a>Spustit zálohu na vyžádání

Spouštět podle plánu zásady zálohování. Zálohování na vyžádání můžete spustit následovně:


1. V nabídce trezoru klikněte na tlačítko **zálohování položek**.
2. V **zálohování položek**, vyberte virtuální počítač, na kterém je spuštěna databáze SAP HANA a potom klikněte na tlačítko **zálohovat nyní**.
3. V **zálohovat nyní**, pomocí ovládacího prvku kalendáře vyberte poslední den uchování bodu obnovení. Pak klikněte na **OK**.
4. Sledujte oznámení o portálu. Můžete sledovat průběh úlohy v řídicím panelu trezoru > **úlohy zálohování** > probíhá. V závislosti na velikosti databáze vytváření prvotní zálohy může chvíli trvat.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Spustit zálohování SAP HANA Studio na databázi pomocí služby Azure Backup povoleno

Pokud chcete vytvořit zálohu místní (pomocí nástroje Studio HANA), který se zálohuje databáze pomocí služby Azure Backup, postupujte takto:

1. Vyčkat, než všechny úplné nebo zálohy protokolu pro databázi na dokončení. Zkontrolujte stav v SAP HANA Studio.
2. Zakázat zálohování protokolů a nastavit katalog záloh do systému souborů pro příslušnou databázi.
3. Chcete-li to provést, dvakrát klikněte na **systemdb** > **konfigurace** > **vybrat databázi** > **filtru (protokolu)** .
4. Nastavte **enable_auto_log_backup** k **ne**.
5. Nastavte **log_backup_using_backint** k **False**.
6. Vytvořit ad hoc úplnou zálohu databáze.
7. Vyčkat, než úplné zálohování a katalog zálohování na dokončení.
8. Vrácení předchozí nastavení zpět na hodnoty pro Azure:
    - Nastavte **enable_auto_log_backup** k **Ano**.
    - Nastavte **log_backup_using_backint** k **True**.



## <a name="next-steps"></a>Další postup

[Další informace o](backup-azure-sap-hana-database-troubleshoot.md) řešení potíží s běžnými chybami při používání zálohování SAP HANA ve virtuálních počítačích Azure.
[Další informace o](backup-azure-arm-vms-prepare.md) zálohování virtuálních počítačů Azure.
