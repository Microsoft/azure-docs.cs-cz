---
title: Zálohování virtuálních počítačů Azure v trezoru služby Recovery Services
description: Popisuje, jak zálohovat virtuální počítače Azure v trezoru služby Recovery Services pomocí azure backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: aeadd7bc798f690c67eef38c6dc645204ff39115
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273511"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Zálohování virtuálních počítačů Azure v trezoru služby Recovery Services

Tento článek popisuje, jak zálohovat virtuální počítače Azure v trezoru služby Recovery Services pomocí služby [Azure Backup.](backup-overview.md)

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Připravte virtuální počítače Azure.
> * Vytvořte trezor.
> * Seznamte se s virtuálními počítačemi a nakonfigurujte zásady zálohování.
> * Povolte zálohování pro virtuální počítače Azure.
> * Spuštění prvotního zálohování.

> [!NOTE]
> Tento článek popisuje, jak nastavit trezor a vyberte virtuální chod, které chcete zálohovat. Je to užitečné, pokud chcete zálohovat více virtuálních počítačů. Případně můžete [zálohovat jeden virtuální počítač Azure](backup-azure-vms-first-look-arm.md) přímo z nastavení virtuálních počítače.

## <a name="before-you-start"></a>Než začnete

* [Zkontrolujte](backup-architecture.md#architecture-built-in-azure-vm-backup) architekturu zálohování virtuálních počítačových virtuálních počítače Azure.
* [Další informace o](backup-azure-vms-introduction.md) Zálohování virtuálních počítačových virtuálních počítačových společností Azure a rozšíření zálohování.
* [Před konfigurací zálohování zkontrolujte matici podpory.](backup-support-matrix-iaas.md)

Kromě toho existuje několik věcí, které budete muset udělat za určitých okolností:

* **Nainstalujte agenta virtuálního počítače na virtuální počítač:** Azure Backup zálohuje virtuální počítače Azure instalací rozšíření pro agenta virtuálního počítače Azure spuštěného v počítači. Pokud váš virtuální počítač byl vytvořen z image Azure Marketplace, agent je nainstalovaný a spuštěný. Pokud vytvoříte vlastní virtuální počítač nebo migrujete místní počítač, možná budete muset [nainstalovat agenta ručně](#install-the-vm-agent).

## <a name="create-a-vault"></a>Vytvoření trezoru

 Trezor ukládá zálohy a body obnovení vytvořené v průběhu času a ukládá zásady zálohování přidružené k zálohovaným počítačům. Vytvořte úschovnu takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Ve vyhledávání zadejte **služby obnovení**. V části **Služby**klepněte na **položku Trezory služby Recovery Services**.

     ![Hledání trezorů služby Recovery Services](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png)

3. V nabídce **trezory služby Recovery Services** klepněte na tlačítko **+Přidat**.

     ![Vytvoření trezoru Recovery Services – krok 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. Do **trezoru služby Recovery Services**zadejte popisný název pro identifikaci trezoru.
    * Název musí být jedinečný v rámci předplatného Azure.
    * Může obsahovat 2 až 50 znaků.
    * Musí začínat písmenem a může obsahovat pouze písmena, číslice a pomlčky.
5. Vyberte předplatné Azure, skupinu prostředků a geografickou oblast, ve které má být úložiště vytvořeno. Potom klepněte na tlačítko **Vytvořit**.
    * Vytvoření trezoru může chvíli trvat.
    * Sledujte oznámení o stavu v pravé horní části portálu.

Po vytvoření se trezor zobrazí v seznamu trezorů služby Recovery Services. Pokud trezor nevidíte, vyberte **Aktualizovat**.

![Seznam trezorů záloh](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

>[!NOTE]
> Azure Backup teď umožňuje přizpůsobení názvu skupiny prostředků vytvořeného službou Azure Backup. Další informace najdete v tématu [Azure Backup skupiny prostředků pro virtuální počítače](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

### <a name="modify-storage-replication"></a>Úprava replikace úložiště

Ve výchozím nastavení používají úložiště [geograficky redundantní úložiště (GRS).](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)

* Pokud je trezor primárním mechanismem zálohování, doporučujeme použít GRS.
* Pro levnější variantu můžete použít [místně redundantní úložiště (LRS).](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Upravte typ replikace úložiště následujícím způsobem:

1. V novém trezoru klikněte v části **Nastavení** na **Vlastnosti.**
2. V **části Vlastnosti**klepněte v části **Konfigurace zálohování**na tlačítko **Aktualizovat**.
3. Vyberte typ replikace úložiště a klepněte na **tlačítko Uložit**.

      ![Nastavení konfigurace úložiště pro nový trezor](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > Po nastavení úložiště a obsahujícím položky zálohy nelze změnit typ replikace úložiště. Chcete-li to provést, musíte trezor znovu vytvořit.

## <a name="apply-a-backup-policy"></a>Použití zásad zálohování

Nakonfigurujte zásady zálohování pro trezor.

1. V úschovně klikněte v části Přehled na **+Zálohovat.** **Overview**

   ![Tlačítko Zálohování](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. V **seznamu Cíl** > zálohování Kde **Azure**běží**vaše úloha?** V **části Co chcete zálohovat?** vyberte Virtuální **počítač** >  **OK**. Tím se zaregistruje rozšíření virtuálního virtuálního aplikace v trezoru.

   ![Podokna cílů zálohování a zálohování](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. V **zásadách zálohování**vyberte zásadu, kterou chcete přidružit k úschovně.
    * Výchozí zásady zálohovat virtuální ho virtuálního montovny jednou denně. Denní zálohy jsou uchovávány po dobu 30 dnů. Okamžité obnovení snímky jsou zachovány po dobu dvou dnů.
    * Pokud nechcete používat výchozí zásady, vyberte **Vytvořit nový**a vytvořte vlastní zásadu, jak je popsáno v dalším postupu.

      ![Výchozí zásady zálohování](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. V **části Vybrat virtuální počítače**vyberte virtuální počítače, které chcete zálohovat pomocí zásad. Pak klikněte na **OK**.

   * Vybrané virtuální ms jsou ověřené.
   * Virtuální aplikace můžete vybrat jenom ve stejné oblasti jako trezor.
   * Virtuální aplikace lze zálohovat jenom v jednom trezoru.

     ![Podokno Vybrat virtuální počítače](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. V **programu Zálohování**klepněte na tlačítko **Povolit zálohování**. To nasadí zásady do trezoru a na virtuální počítače a nainstaluje rozšíření zálohování na agenta virtuálního počítače spuštěného na virtuálním počítači Azure.

     ![Tlačítko "Povolit zálohování"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Po povolení zálohování:

* Služba Zálohování nainstaluje rozšíření zálohy bez ohledu na to, zda je virtuální počítače spuštěn.
* Počáteční zálohování bude spuštěno v souladu s plánem zálohování.
* Při spuštění záloh mějte na vědomí, že:
  * Spuštěný virtuální virtuální soud má největší šanci zachytit bod obnovení konzistentní s aplikací.
  * I když je virtuální virtuální měnový virtuální mísa vypnutý, je zálohovaný. Takový virtuální virtuální ms se označuje jako offline virtuální hod. V takovém případě bude bod obnovení konzistentní při selhání.
* Explicitní odchozí připojení není nutné povolit zálohování virtuálních počítačích Azure.

### <a name="create-a-custom-policy"></a>Vytvoření vlastní zásady

Pokud jste zvolili vytvoření nové zásady zálohování, vyplňte nastavení zásad.

1. V **poli Název zásady**zadejte smysluplný název.
2. V **plánu zálohování**určete, kdy mají být zálohy prováděny. Můžete si vzít denní nebo týdenní zálohy pro virtuální počítače Azure.
3. V **poli Okamžité obnovení**určete, jak dlouho chcete snímky zachovat místně pro okamžité obnovení.
    * Při obnovení se zálohované disky virtuálních počítačů zkopírují z úložiště přes síť do umístění úložiště pro obnovení. S okamžitým obnovením můžete využít místně uložené snímky pořízené během úlohy zálohování, aniž byste museli čekat na přenos záložních dat do trezoru.
    * Snímky můžete uchovávat pro okamžité obnovení po dobu jednoho až pěti dnů. Výchozí nastavení je dva dny.
4. V **rozsahu uchování**určete, jak dlouho chcete uchovávat denní nebo týdenní body zálohování.
5. V **poli Uchovávání bodů měsíčního zálohování**určete, zda chcete mít měsíční zálohu denních nebo týdenních záloh.
6. Chcete-li zásadu uložit, klepněte na tlačítko **OK.**

    ![Nové zásady zálohování](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure Backup nepodporuje automatické nastavení hodin pro letní změny pro zálohování virtuálních her Azure. Při provádění změn času upravte zásady zálohování ručně podle potřeby.

## <a name="trigger-the-initial-backup"></a>Aktivace počáteční zálohy

Počáteční záloha bude spuštěna v souladu s plánem, ale můžete ji spustit okamžitě následujícím způsobem:

1. V nabídce úschovny klikněte na **Zálohovat položky**.
2. V **položkách zálohování**klikněte na **položku Virtuální počítač Azure**.
3. V seznamu **Položky zálohování** klikněte na tři tečky (...).
4. Klepněte na tlačítko **Zálohovat .**
5. V **aplikaci Backup Now**vyberte pomocí ovládacího prvku kalendář poslední den, kdy má být bod obnovení zachován. Pak klikněte na **OK**.
6. Sledujte oznámení portálu. Průběh úlohy můžete sledovat na řídicím panelu trezoru >**probíhající** **úlohy** > zálohování . V závislosti na velikosti virtuálního počítače může vytváření prvotní zálohy chvíli trvat.

## <a name="verify-backup-job-status"></a>Ověřit stav úlohy zálohování

Podrobnosti úlohy zálohování pro každou zálohu virtuálního počítače se skládají ze dvou fází, fáze **snímek** následovaný **fáze přenosu dat do úložiště.**<br/>
Fáze snímku zaručuje dostupnost bodu obnovení uloženého spolu s disky pro **okamžité obnovení** a jsou k dispozici maximálně pět dní v závislosti na uchovávání snímků nakonfigurované uživatelem. Přenos dat do úložiště vytvoří bod obnovení v úschovně pro dlouhodobé uchovávání. Přenos dat do úložiště se spustí až po dokončení fáze snímku.

  ![Stav úlohy zálohování](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

V back-endu jsou spuštěny dvě **dílčí úlohy,** jedna pro úlohu zálohování front-endu, kterou lze zkontrolovat z okna podrobnosti **úlohy zálohování,** jak je uvedeno níže:

  ![Stav úlohy zálohování](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

Fáze **přenosu dat do úložiště** může trvat několik dní v závislosti na velikosti disků, konve na disk a několika dalších faktorech.

Stav úlohy se může lišit v závislosti na následujících scénářích:

**Snímek** | **Přenos dat do trezoru** | **Stav úlohy**
--- | --- | ---
Dokončeno | Probíhá | Probíhá
Dokončeno | Přeskočen | Dokončeno
Dokončeno | Dokončeno | Dokončeno
Dokončeno | Failed | Dokončeno s varováním
Failed | Failed | Failed

Nyní s touto funkcí pro stejný virtuální počítače, dvě zálohy můžete spustit paralelně, ale v obou fázích (snímek, přenos dat do trezoru) lze spustit pouze jednu dílčí úlohu. Takže ve scénářích byly zálohovací úlohy v průběhu za následek další den zálohování nezdaří se vyhnout s touto funkcí oddělení oddělení. Následující den zálohy může mít snímek dokončena **při přenosu dat do trezoru** přeskočeno, pokud předchozí den zálohovací úlohy probíhá stavu.
Přírůstkový bod obnovení vytvořený v úschovně zachytí všechny změny z posledního bodu obnovení vytvořeného v úschovně. Neexistuje žádný dopad na náklady na uživatele.

## <a name="optional-steps"></a>Volitelné kroky

### <a name="install-the-vm-agent"></a>Instalace agenta virtuálního počítače

Azure Backup zálohuje virtuální počítače Azure instalací rozšíření pro agenta virtuálního počítače Azure spuštěného na počítači. Pokud váš virtuální počítač byl vytvořen z image Azure Marketplace, agent je nainstalovaný a spuštěný. Pokud vytvoříte vlastní virtuální počítač nebo migrujete místní počítač, možná budete muset nainstalovat agenta ručně, jak je shrnuto v tabulce.

**Vm** | **Podrobnosti**
--- | ---
**Windows** | 1. [Stáhněte a nainstalujte](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) soubor agenta MSI.<br/><br/> 2. Nainstalujte s oprávněními správce na počítači.<br/><br/> 3. Ověřte instalaci. V *c:\WindowsAzure\Balíčky* na virtuálním počítači klikněte pravým tlačítkem myši na **waappagent.exe vlastnosti** > **Properties**. Na kartě **Podrobnosti** by měla být **verze produktu** 2.6.1198.718 nebo vyšší.<br/><br/> Pokud aktualizujete agenta, ujistěte se, že nejsou spuštěny žádné operace zálohování, a [přeinstalujte agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Nainstalujte pomocí RPM nebo balíčku DEB z úložiště balíčků vaší distribuce. Toto je upřednostňovaná metoda pro instalaci a upgrade agenta Azure Linuxu. Všichni [schválení poskytovatelé distribuce](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrují balíček agenta Azure Linuxu do svých ibiaa a úložišť. Agent je k dispozici na [GitHubu](https://github.com/Azure/WALinuxAgent), ale nedoporučujeme instalaci odtud.<br/><br/> Pokud aktualizujete agenta, ujistěte se, že nejsou spuštěny žádné operace zálohování a aktualizujte binární soubory.

>[!NOTE]
> **Azure Backup teď podporuje selektivní zálohování a obnovení disku pomocí řešení zálohování virtuálního počítače Azure.**
>
>Azure Backup dnes podporuje zálohování všech disků (operačního systému a dat) ve virtuálním počítači společně pomocí řešení zálohování virtuálního počítače. S funkcí vyloučit disk, získáte možnost zálohovat jeden nebo několik z mnoha datových disků ve virtuálním počítače. To poskytuje efektivní a nákladově efektivní řešení pro potřeby zálohování a obnovení. Každý bod obnovení obsahuje data disků zahrnutých do operace zálohování, což dále umožňuje obnovit podmnožinu disků z daného bodu obnovení během operace obnovení. To platí pro obnovení ze snímku a úložiště.
>
>**Chcete-li se zaregistrovat do náhledu, napište nám naAskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>Další kroky

* Řešení problémů s [agenty virtuálních počítačů Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) nebo [zálohováním virtuálních počítačů Azure](backup-azure-vms-troubleshoot.md).
* [Obnovit](backup-azure-arm-restore-vms.md) Virtuální počítače Azure.
