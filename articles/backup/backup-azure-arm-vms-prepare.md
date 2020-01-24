---
title: Zálohování virtuálních počítačů Azure v trezoru Recovery Services
description: Popisuje, jak zálohovat virtuální počítače Azure v Recovery Services trezoru pomocí Azure Backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: aeadd7bc798f690c67eef38c6dc645204ff39115
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705543"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Zálohování virtuálních počítačů Azure v trezoru Recovery Services

Tento článek popisuje, jak zálohovat virtuální počítače Azure v trezoru Recovery Services pomocí služby [Azure Backup](backup-overview.md) .

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Příprava virtuálních počítačů Azure.
> * Vytvořte trezor.
> * Vyhledejte virtuální počítače a nakonfigurujte zásady zálohování.
> * Povolte zálohování virtuálních počítačů Azure.
> * Spuštění prvotního zálohování.

> [!NOTE]
> Tento článek popisuje, jak nastavit trezor a vybrat virtuální počítače k zálohování. To je užitečné, pokud chcete zálohovat víc virtuálních počítačů. Alternativně můžete [zálohovat jeden virtuální počítač Azure](backup-azure-vms-first-look-arm.md) přímo z nastavení virtuálního počítače.

## <a name="before-you-start"></a>Než začnete

* [Zkontrolujte](backup-architecture.md#architecture-built-in-azure-vm-backup) architekturu zálohování virtuálních počítačů Azure.
* [Další informace](backup-azure-vms-introduction.md) Zálohování virtuálních počítačů Azure a záložní rozšíření.
* Před konfigurací zálohování [si přečtěte tabulku podpory](backup-support-matrix-iaas.md) .

Kromě toho je možné, že v některých případech budete muset udělat několik věcí:

* **Instalace agenta virtuálního počítače na virtuální počítač**: Azure Backup zálohuje virtuální počítače Azure tím, že nainstaluje rozšíření na agenta virtuálního počítače Azure, který běží na počítači. Pokud byl váš virtuální počítač vytvořený z image Azure Marketplace, Agent se nainstaluje a spustí. Pokud vytvoříte vlastní virtuální počítač nebo migrujete místní počítač, možná budete muset [agenta nainstalovat ručně](#install-the-vm-agent).

## <a name="create-a-vault"></a>Vytvoření trezoru

 Trezor ukládá zálohy a body obnovení vytvořené v průběhu času a ukládá zásady zálohování přidružené k zálohovaným počítačům. Vytvořte Trezor následujícím způsobem:

1. Přihlaste se k [Portálu Azure](https://portal.azure.com/).
2. Do Hledat zadejte **Recovery Services**. V části **služby**klikněte na **Recovery Services trezory**.

     ![Hledat trezory Recovery Services](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png)

3. V nabídce **Recovery Services trezory** klikněte na **+ Přidat**.

     ![Vytvoření trezoru Recovery Services – krok 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. V **Recovery Services trezoru**zadejte popisný název pro identifikaci trezoru.
    * Název musí být jedinečný v rámci předplatného Azure.
    * Může obsahovat 2 až 50 znaků.
    * Musí začínat písmenem a může obsahovat jenom písmena, číslice a spojovníky.
5. Vyberte předplatné Azure, skupinu prostředků a geografickou oblast, ve které se má Trezor vytvořit. Poté klikněte na **Vytvořit**.
    * Vytvoření trezoru může chvíli trvat.
    * Sledujte oznámení o stavu v pravé horní části portálu.

Po vytvoření trezoru se zobrazí v seznamu Recovery Services trezory. Pokud váš trezor nevidíte, vyberte **aktualizovat**.

![Seznam trezorů záloh](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

>[!NOTE]
> Azure Backup teď umožňuje přizpůsobení názvu skupiny prostředků vytvořeného službou Azure Backup. Další informace najdete v tématu [Azure Backup skupiny prostředků pro Virtual Machines](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

### <a name="modify-storage-replication"></a>Úprava replikace úložiště

Ve výchozím nastavení trezory používají [geograficky redundantní úložiště (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Pokud je trezor vaším primárním zálohovacím mechanismem, doporučujeme použít GRS.
* Pro levnější možnost můžete použít [místně redundantní úložiště (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .

Typ replikace úložiště upravte takto:

1. V novém trezoru klikněte na **vlastnosti** v části **Nastavení** .
2. V části **vlastnosti**v části **Konfigurace zálohování**klikněte na **aktualizovat**.
3. Vyberte typ replikace úložiště a klikněte na **Uložit**.

      ![Nastavení konfigurace úložiště pro nový trezor](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > Po nastavení trezoru a obsahujícího zálohované položky nemůžete upravit typ replikace úložiště. Pokud to chcete udělat, musíte znovu vytvořit trezor.

## <a name="apply-a-backup-policy"></a>Použití zásad zálohování

Nakonfigurujte zásady zálohování pro trezor.

1. V trezoru klikněte v části **Přehled** na **+ zálohovat** .

   ![Tlačítko zálohovat](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. V > **cíl zálohování** **, kde je spuštěná vaše úloha?** vyberte **Azure**. V **Možnosti co chcete zálohovat?** vyberte **virtuální počítač** >  **OK**. Tím se registruje rozšíření virtuálního počítače v trezoru.

   ![Podokna zálohování a cíle zálohování](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. V části **zásady zálohování**vyberte zásadu, kterou chcete přidružit k trezoru.
    * Výchozí zásada zálohuje virtuální počítač jednou denně. Denní zálohy se uchovávají po dobu 30 dnů. Snímky okamžitého obnovení se uchovávají po dobu dvou dnů.
    * Pokud nechcete používat výchozí zásady, vyberte **vytvořit novou**a vytvořte vlastní zásadu, jak je popsáno v následujícím postupu.

      ![Výchozí zásady zálohování](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. V části **Vybrat virtuální počítače**vyberte virtuální počítače, které chcete pomocí zásad zálohovat. Pak klikněte na **OK**.

   * Vybrané virtuální počítače jsou ověřené.
   * Virtuální počítače můžete vybrat jenom ve stejné oblasti jako trezor.
   * Virtuální počítače se dají zálohovat jenom v jednom trezoru.

     ![Podokno vybrat virtuální počítače](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. V **zálohování**klikněte na **Povolit zálohování**. Tím se tyto zásady nasadí do trezoru a do virtuálních počítačů a nainstaluje se rozšíření zálohování na agenta virtuálního počítače spuštěného na virtuálním počítači Azure.

     ![Tlačítko Povolit zálohování](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Po povolení zálohování:

* Služba zálohování nainstaluje záložní rozšíření bez ohledu na to, jestli je virtuální počítač spuštěný.
* Počáteční zálohování se spustí podle plánu zálohování.
* Po spuštění zálohování si všimněte, že:
  * Virtuální počítač, který je spuštěný, má největší šanci na zachycení bodu obnovení konzistentního vzhledem k aplikacím.
  * Ale i v případě, že je virtuální počítač vypnutý, je zálohovaný. Takový virtuální počítač se označuje jako offline virtuální počítač. V takovém případě bude bod obnovení konzistentní vzhledem k selháním.
* K povolení zálohování virtuálních počítačů Azure se nevyžaduje explicitní odchozí připojení.

### <a name="create-a-custom-policy"></a>Vytvoření vlastní zásady

Pokud jste vybrali vytvoření nové zásady zálohování, vyplňte nastavení zásad.

1. Do **název zásady**zadejte smysluplný název.
2. V **plánu zálohování**určete, kdy se mají považovat zálohy. Pro virtuální počítače Azure můžete provádět denní nebo týdenní zálohy.
3. V **rychlém obnovení**zadejte, jak dlouho chcete snímky uchovávat místně pro okamžité obnovení.
    * Při obnovení se zálohované disky virtuálních počítačů zkopírují z úložiště napříč sítí do umístění úložiště pro obnovení. Při okamžitém obnovení můžete využít místně uložené snímky, které se provedou během úlohy zálohování, aniž byste čekali na přenos zálohovaných dat do trezoru.
    * Snímky pro okamžité obnovení můžete uchovávat po dobu mezi 1 a pěti dny. Výchozím nastavením je dva dny.
4. V **oblasti uchovávání**zadejte, jak dlouho chcete zachovat denní nebo týdenní body zálohování.
5. V části **uchování měsíčního bodu zálohy**určete, jestli chcete zachovat měsíční zálohu denních nebo týdenních záloh.
6. Zásadu uložíte kliknutím na **OK** .

    ![Nové zásady zálohování](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure Backup nepodporuje automatické úpravy hodin pro změny letního času pro zálohování virtuálních počítačů Azure. V době, kdy dojde ke změnám, upravte zásady zálohování ručně podle potřeby.

## <a name="trigger-the-initial-backup"></a>Aktivovat počáteční zálohu

Počáteční zálohování se spustí podle plánu, ale můžete ho spustit hned takto:

1. V nabídce trezoru klikněte na položku **zálohované položky**.
2. V nabídce **zálohované položky**klikněte na **virtuální počítač Azure**.
3. V seznamu **zálohované položky** klikněte na tři tečky (...).
4. Klikněte na **Zálohovat nyní**.
5. V části **Zálohovat nyní**pomocí ovládacího prvku kalendáře vyberte poslední den, kdy se má bod obnovení zachovat. Pak klikněte na **OK**.
6. Monitorujte oznámení na portálu. Průběh úlohy můžete monitorovat na řídicím panelu trezoru > **úlohy zálohování** ** > probíhá**. V závislosti na velikosti virtuálního počítače může vytváření prvotní zálohy chvíli trvat.

## <a name="verify-backup-job-status"></a>Ověřit stav úlohy zálohování

Podrobnosti úlohy zálohování pro každou zálohu virtuálního počítače se skládají ze dvou fází, fáze **snímků** následovaná fází **přenosu dat do trezoru** .<br/>
Fáze snímkování zaručuje dostupnost bodu obnovení uloženého spolu s disky pro **okamžité obnovení** a jsou k dispozici po dobu maximálně pět dní v závislosti na uchování snímku nakonfigurovaného uživatelem. Při přenosu dat do trezoru se vytvoří bod obnovení v trezoru pro dlouhodobou dobu uchovávání. Přenos dat do trezoru se spustí až po dokončení fáze snímku.

  ![Stav úlohy zálohování](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Existují dva **dílčí úkoly** spuštěné v back-endu, jednu pro úlohu front-end zálohování, kterou je možné zkontrolovat v okně Podrobnosti **úlohy zálohování** , jak je uvedeno níže:

  ![Stav úlohy zálohování](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

Fáze **přenosu dat do trezoru** může trvat několik dní v závislosti na velikosti disků, počtu změn na disk a několika dalších faktorech.

Stav úlohy se může lišit v závislosti na následujících scénářích:

**Snímek** | **Přenos dat do trezoru** | **Stav úlohy**
--- | --- | ---
Dokončeno | Probíhá | Probíhá
Dokončeno | Přeskočeno | Dokončeno
Dokončeno | Dokončeno | Dokončeno
Dokončeno | Selhalo | Dokončeno s upozorněním
Selhalo | Selhalo | Selhalo

Díky této funkci se můžou dvě zálohy spustit paralelně, ale v obou fázích (snímky, přenos dat do trezoru) může běžet jenom jedna dílčí úloha. Takže v rámci scénářů skončila úloha zálohování v průběhu příštího dne neúspěšného zálohování v této funkci odpojuje se. V dalších dnech můžou být snímky dokončené **, zatímco přenos dat do trezoru** se přeskočil, pokud probíhá úloha zálohování staršího dne.
Přírůstkový bod obnovení vytvořený v trezoru bude zachytit všechny změny z posledního bodu obnovení vytvořeného v trezoru. Na uživatele není žádný vliv na náklady.

## <a name="optional-steps"></a>Volitelné kroky

### <a name="install-the-vm-agent"></a>Instalace agenta virtuálního počítače

Azure Backup zálohuje virtuální počítače Azure tím, že nainstaluje rozšíření na agenta virtuálního počítače Azure, který běží na počítači. Pokud byl váš virtuální počítač vytvořen z bitové kopie Azure Marketplace, je agent nainstalovaný a spuštěný. Pokud vytvoříte vlastní virtuální počítač nebo migrujete místní počítač, možná budete muset agenta nainstalovat ručně, jak je shrnuto v tabulce.

**VM** | **Podrobnosti**
--- | ---
**Windows** | 1. [Stáhněte a nainstalujte](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) soubor MSI agenta.<br/><br/> 2. Nainstalujte na počítači oprávnění správce.<br/><br/> 3. Ověřte instalaci. V *C:\WindowsAzure\Packages* na virtuálním počítači klikněte pravým tlačítkem na **WaAppAgent. exe** > **vlastnosti**. Na kartě **Podrobnosti** by **verze produktu** měla být 2.6.1198.718 nebo vyšší.<br/><br/> Pokud aktualizujete agenta, zajistěte, aby neběžely žádné operace zálohování, a [přeinstalujte agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Nainstalujte pomocí balíčku ot./min. nebo balíčku DEB z úložiště balíčků distribuce. Toto je upřednostňovaná metoda pro instalaci a upgrade agenta Azure Linux. Všichni [poskytovatelé schválené distribuce](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrují balíček agenta Azure Linux do svých imagí a úložišť. Agent je k dispozici na [GitHubu](https://github.com/Azure/WALinuxAgent), ale nedoporučujeme ho instalovat.<br/><br/> Pokud aktualizujete agenta, zajistěte, aby neběžely žádné operace zálohování, a aktualizujte binární soubory.

>[!NOTE]
> **Azure Backup teď podporuje zálohování a obnovení selektivního disku pomocí řešení zálohování virtuálních počítačů Azure.**
>
>V současné době Azure Backup podporuje zálohování všech disků (operačního systému a dat) na virtuálním počítači společně s využitím řešení zálohování virtuálních počítačů. Díky funkci vyloučení disku získáte možnost zálohovat jeden nebo několik datových disků ve virtuálním počítači. To poskytuje efektivní a nákladově efektivní řešení pro potřeby zálohování a obnovení. Každý bod obnovení obsahuje data disků zahrnutých v operaci zálohování, která dále umožňuje mít v průběhu operace obnovení podmnožinu disků obnovených z daného bodu obnovení. To platí pro obnovení ze snímku i z trezoru.
>
>**Pokud si chcete zaregistrovat verzi Preview, napište nám na AskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>Další kroky

* Vyřešte všechny problémy s [agenty virtuálních počítačů Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) nebo [zálohováním virtuálních počítačů Azure](backup-azure-vms-troubleshoot.md).
* [Obnovit](backup-azure-arm-restore-vms.md) Virtuální počítače Azure.
