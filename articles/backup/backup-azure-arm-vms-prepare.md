---
title: Zálohování virtuálních počítačů Azure v trezoru Recovery Services
description: Popisuje, jak zálohovat virtuální počítače Azure v Recovery Services trezoru pomocí Azure Backup
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: f6fe2f629742e15e62dfc13106e92623a4b45add
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92172751"
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

* **Instalace agenta virtuálního počítače na virtuální počítač**: Azure Backup zálohuje virtuální počítače Azure tím, že nainstaluje rozšíření na agenta virtuálního počítače Azure, který běží na počítači. Pokud byl váš virtuální počítač vytvořen z bitové kopie Azure Marketplace, je agent nainstalovaný a spuštěný. Pokud vytvoříte vlastní virtuální počítač nebo migrujete místní počítač, možná budete muset [agenta nainstalovat ručně](#install-the-vm-agent).

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

### <a name="modify-storage-replication"></a>Úprava replikace úložiště

Ve výchozím nastavení trezory používají [geograficky redundantní úložiště (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage).

* Pokud je trezor vaším primárním zálohovacím mechanismem, doporučujeme použít GRS.
* Pro levnější možnost můžete použít [místně redundantní úložiště (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) .
* [Zóna – redundantní úložiště (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) replikuje vaše data do [zón dostupnosti](../availability-zones/az-overview.md#availability-zones)a zaručuje jejich započet a odolnost dat ve stejné oblasti.

Typ replikace úložiště upravte takto:

1. V novém trezoru vyberte v části **Nastavení** možnost **vlastnosti** .
2. V části **vlastnosti** v části **Konfigurace zálohování** vyberte **aktualizovat**.
3. Vyberte typ replikace úložiště a vyberte **Uložit**.

      ![Nastavení konfigurace úložiště pro nový trezor](./media/backup-azure-arm-vms-prepare/full-blade.png)

> [!NOTE]
   > Po nastavení trezoru a obsahujícího zálohované položky nemůžete upravit typ replikace úložiště. Pokud to chcete udělat, musíte znovu vytvořit trezor.

## <a name="apply-a-backup-policy"></a>Použití zásad zálohování

Nakonfigurujte zásady zálohování pro trezor.

1. V trezoru v části **Přehled** vyberte **+ Backup** .

   ![Tlačítko zálohovat](./media/backup-azure-arm-vms-prepare/backup-button.png)

1. V **cíli zálohování**  >  **, kde je spuštěná vaše úloha?** vyberte **Azure**. V **Možnosti co chcete zálohovat?** vyberte **virtuální počítač**  >   **OK**. Tím se registruje rozšíření virtuálního počítače v trezoru.

   ![Podokna zálohování a cíle zálohování](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

1. V části **zásady zálohování** vyberte zásadu, kterou chcete přidružit k trezoru.
    * Výchozí zásada zálohuje virtuální počítač jednou denně. Denní zálohy se uchovávají po dobu 30 dnů. Snímky okamžitého obnovení se uchovávají po dobu dvou dnů.

      ![Výchozí zásady zálohování](./media/backup-azure-arm-vms-prepare/default-policy.png)

    * Pokud nechcete používat výchozí zásady, vyberte **vytvořit novou** a vytvořte vlastní zásadu, jak je popsáno v následujícím postupu.

1. V části **Virtual Machines** vyberte **Přidat**.

      ![Přidat virtuální počítače](./media/backup-azure-arm-vms-prepare/add-virtual-machines.png)

1. Otevře se podokno **Výběr virtuálních počítačů** . Pomocí zásad vyberte virtuální počítače, které chcete zálohovat. Pak vyberte **OK**.

   * Vybrané virtuální počítače jsou ověřené.
   * Virtuální počítače můžete vybrat jenom ve stejné oblasti jako trezor.
   * Virtuální počítače se dají zálohovat jenom v jednom trezoru.

     ![Podokno vybrat virtuální počítače](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    >[!NOTE]
    > Pro konfiguraci zálohování jsou k dispozici všechny virtuální počítače ve stejné oblasti a předplatném, které jsou v trezoru. Při konfiguraci zálohování můžete přejít na název virtuálního počítače a jeho skupinu prostředků, i když nemáte požadovaná oprávnění k těmto virtuálním počítačům. Pokud je váš virtuální počítač v tichém odstraněném stavu, nebude se v tomto seznamu zobrazovat. Pokud potřebujete znovu nastavit ochranu virtuálního počítače, musíte počkat na vypršení platnosti nebo zrušit platnost tohoto virtuálního počítače ze seznamu odstraněných. Další informace najdete v [článku obnovitelné odstranění pro virtuální počítače](soft-delete-virtual-machines.md#soft-delete-for-vms-using-azure-portal).

1. V **zálohování** vyberte **Povolit zálohování**. Tím se tyto zásady nasadí do trezoru a do virtuálních počítačů a nainstaluje se rozšíření zálohování na agenta virtuálního počítače spuštěného na virtuálním počítači Azure.

Po povolení zálohování:

* Služba zálohování nainstaluje záložní rozšíření bez ohledu na to, jestli je virtuální počítač spuštěný.
* Počáteční zálohování se spustí podle plánu zálohování.
* Po spuštění zálohování si všimněte, že:
  * Virtuální počítač, který je spuštěný, má největší šanci na zachycení bodu obnovení konzistentního vzhledem k aplikacím.
  * Nicméně i v případě, že je virtuální počítač vypnutý, je zálohovaný. Takový virtuální počítač se označuje jako offline virtuální počítač. V takovém případě bude bod obnovení konzistentní vzhledem k selháním.
* Aby bylo možné zálohovat virtuální počítače Azure, není nutné explicitní odchozí připojení.

### <a name="create-a-custom-policy"></a>Vytvoření vlastní zásady

Pokud jste vybrali vytvoření nové zásady zálohování, vyplňte nastavení zásad.

1. Do **název zásady** zadejte smysluplný název.
2. V **plánu zálohování** určete, kdy se mají považovat zálohy. Pro virtuální počítače Azure můžete provádět denní nebo týdenní zálohy.
3. V **rychlém obnovení** zadejte, jak dlouho chcete snímky uchovávat místně pro okamžité obnovení.
    * Při obnovení se zálohované disky virtuálních počítačů zkopírují z úložiště napříč sítí do umístění úložiště pro obnovení. Při okamžitém obnovení můžete využít místně uložené snímky, které se provedou během úlohy zálohování, aniž byste čekali na přenos zálohovaných dat do trezoru.
    * Snímky pro okamžité obnovení můžete uchovávat po dobu mezi 1 a pěti dny. Výchozím nastavením je dva dny.
4. V **oblasti uchovávání** zadejte, jak dlouho chcete zachovat denní nebo týdenní body zálohování.
5. V části **uchování měsíčního bodu zálohy** a **uchování ročního bodu zálohy** určete, jestli chcete uchovávat měsíční nebo roční zálohu denních nebo týdenních záloh.
6. Kliknutím na **OK** zásadu uložte.

    ![Nové zásady zálohování](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure Backup nepodporuje automatické úpravy hodin pro změny letního času pro zálohování virtuálních počítačů Azure. V době, kdy dojde ke změnám, upravte zásady zálohování ručně podle potřeby.

## <a name="trigger-the-initial-backup"></a>Aktivovat počáteční zálohu

Počáteční zálohování se spustí podle plánu, ale můžete ho spustit hned takto:

1. V nabídce trezoru vyberte **zálohované položky**.
2. V položku **zálohované položky** vyberte **virtuální počítač Azure**.
3. V seznamu **zálohované položky** vyberte tři tečky (...).
4. Vyberte **Zálohovat nyní**.
5. V části **Zálohovat nyní** pomocí ovládacího prvku kalendáře vyberte poslední den, kdy se má bod obnovení zachovat. Pak vyberte **OK**.
6. Monitorujte oznámení na portálu. Průběh úlohy můžete monitorovat na řídicím panelu trezoru > probíhající **úlohy zálohování**  >  . V závislosti na velikosti virtuálního počítače může vytváření prvotní zálohy chvíli trvat.

## <a name="verify-backup-job-status"></a>Ověřit stav úlohy zálohování

Podrobnosti úlohy zálohování pro každou zálohu virtuálního počítače se skládají ze dvou fází, fáze **snímků** následovaná fází **přenosu dat do trezoru** .<br/>
Fáze snímkování zaručuje dostupnost bodu obnovení uloženého spolu s disky pro **okamžité obnovení** a jsou k dispozici po dobu maximálně pět dní v závislosti na uchování snímku nakonfigurovaného uživatelem. Při přenosu dat do trezoru se vytvoří bod obnovení v trezoru pro dlouhodobou dobu uchovávání. Přenos dat do trezoru se spustí až po dokončení fáze snímku.

  ![Stav úlohy zálohování](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Existují dva **dílčí úkoly** spuštěné v back-endu, jednu pro úlohu front-end zálohování, kterou lze zkontrolovat v podokně podrobností **úlohy zálohování** , jak je uvedeno níže:

  ![Dílčí úkoly stavu úlohy zálohování](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

Fáze **přenosu dat do trezoru** může trvat několik dní v závislosti na velikosti disků, počtu změn na disk a několika dalších faktorech.

Stav úlohy se může lišit v závislosti na následujících scénářích:

**Snímek** | **Přenos dat do trezoru** | **Stav úlohy**
--- | --- | ---
Dokončeno | Rozpracované | Rozpracované
Dokončeno | Přeskočeno | Dokončeno
Dokončeno | Dokončeno | Dokončeno
Dokončeno | Neúspěšný | Dokončeno s upozorněním
Neúspěšný | Neúspěšný | Neúspěšný

Díky této funkci se můžou dvě zálohy spustit paralelně, ale v obou fázích (snímky, přenos dat do trezoru) může běžet jenom jedna dílčí úloha. Takže ve scénářích, kdy skončila úloha zálohování v průběhu příštího dne, selže zálohování v příštím dni, bude tato funkce oddálení zabráněno. Zálohy v dalších dnech můžou mít hotový snímek, zatímco **přenos dat do trezoru** se přeskočí, pokud probíhá úloha zálohování staršího dne.
Přírůstkový bod obnovení vytvořený v trezoru zachytí veškerou četnost změn z posledního bodu obnovení vytvořeného v trezoru. Na uživatele není žádný vliv na náklady.

## <a name="optional-steps"></a>Volitelné kroky

### <a name="install-the-vm-agent"></a>Instalace agenta virtuálního počítače

Azure Backup zálohuje virtuální počítače Azure tím, že nainstaluje rozšíření na agenta virtuálního počítače Azure, který běží na počítači. Pokud byl váš virtuální počítač vytvořen z bitové kopie Azure Marketplace, je agent nainstalovaný a spuštěný. Pokud vytvoříte vlastní virtuální počítač nebo migrujete místní počítač, možná budete muset agenta nainstalovat ručně, jak je shrnuto v tabulce.

**Virtuální počítač** | **Podrobnosti**
--- | ---
**Windows** | 1. [Stáhněte a nainstalujte](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) soubor MSI agenta.<br/><br/> 2. Nainstalujte na počítači oprávnění správce.<br/><br/> 3. Ověřte instalaci. V *C:\WindowsAzure\Packages* na virtuálním počítači klikněte pravým tlačítkem na **WaAppAgent.exe**  >  **vlastnosti**. Na kartě **Podrobnosti** by **verze produktu** měla být 2.6.1198.718 nebo vyšší.<br/><br/> Pokud aktualizujete agenta, zajistěte, aby neběžely žádné operace zálohování, a [přeinstalujte agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Nainstalujte pomocí balíčku ot./min. nebo balíčku DEB z úložiště balíčků distribuce. Toto je upřednostňovaná metoda pro instalaci a upgrade agenta Azure Linux. Všichni [poskytovatelé schválené distribuce](../virtual-machines/linux/endorsed-distros.md) integrují balíček agenta Azure Linux do svých imagí a úložišť. Agent je k dispozici na [GitHubu](https://github.com/Azure/WALinuxAgent), ale nedoporučujeme ho instalovat.<br/><br/> Pokud aktualizujete agenta, zajistěte, aby neběžely žádné operace zálohování, a aktualizujte binární soubory.

## <a name="next-steps"></a>Další kroky

* Vyřešte všechny problémy s [agenty virtuálních počítačů Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) nebo [zálohováním virtuálních počítačů Azure](backup-azure-vms-troubleshoot.md).
* [Obnovit](backup-azure-arm-restore-vms.md) Virtuální počítače Azure.