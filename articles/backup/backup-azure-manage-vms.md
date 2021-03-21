---
title: Správa a sledování záloh virtuálních počítačů Azure
description: Naučte se spravovat a monitorovat zálohy virtuálních počítačů Azure pomocí služby Azure Backup.
ms.topic: conceptual
ms.date: 08/02/2020
ms.openlocfilehash: 51ce88bb67d64ce129a3479d38db9a66dfe65d0a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100635073"
---
# <a name="manage-azure-vm-backups-with-azure-backup-service"></a>Správa záloh virtuálních počítačů Azure pomocí služby Azure Backup

Tento článek popisuje, jak spravovat virtuální počítače Azure, které jsou zálohované ve [službě Azure Backup](backup-overview.md). Tento článek také shrnuje informace o zálohování, které najdete na řídicím panelu trezoru.

Řídicí panel trezoru Recovery Services v Azure Portal poskytuje přístup k informacím o trezoru, včetně těchto:

* Nejnovější záloha, což je také nejnovější bod obnovení.
* Zásady zálohování.
* Celková velikost všech záložních snímků.
* Počet virtuálních počítačů, které jsou povoleny pro zálohování.

Zálohy můžete spravovat pomocí řídicího panelu a přechodem k jednotlivým virtuálním počítačům. Pokud chcete zahájit zálohování počítačů, otevřete trezor na řídicím panelu.

![Zobrazení celého řídicího panelu s posuvníkem](./media/backup-azure-manage-vms/bottom-slider.png)

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="view-vms-on-the-dashboard"></a>Zobrazení virtuálních počítačů na řídicím panelu

Postup zobrazení virtuálních počítačů na řídicím panelu trezoru:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. V nabídce vlevo vyberte **Všechny služby**.

    ![Vyberte Všechny služby.](./media/backup-azure-manage-vms/select-all-services.png)

1. V dialogovém okně **Všechny služby** zadejte *Recovery Services*. Seznam prostředků se filtruje podle vašeho zadání. Ze seznamu prostředků vyberte **Trezory služby Recovery Services**.

    ![Zadání a volba trezorů služby Recovery Services](./media/backup-azure-manage-vms/all-services.png)

    Zobrazí se seznam trezorů služby Recovery Services v předplatném.

1. Pro snadné použití vyberte ikonu připnutí vedle názvu trezoru a vyberte **Připnout na řídicí panel**.
1. Otevřete řídicí panel trezoru.

    ![Otevření řídicího panelu trezoru a podokna nastavení](./media/backup-azure-manage-vms/full-view-rs-vault.png)

1. Na dlaždici **zálohované položky** vyberte **virtuální počítač Azure**.

    ![Otevřít dlaždici zálohované položky](./media/backup-azure-manage-vms/azure-virtual-machine.png)

1. V podokně **zálohované položky** můžete zobrazit seznam chráněných virtuálních počítačů. V tomto příkladu trezor chrání jeden virtuální počítač: *myVMR1*.  

    ![Zobrazit podokno zálohované položky](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

1. Z řídicího panelu položky trezoru můžete upravit zásady zálohování, spustit zálohování na vyžádání, zastavit nebo obnovit ochranu virtuálních počítačů, odstranit data záloh, Zobrazit body obnovení a spustit obnovení.

    ![Řídicí panel zálohované položky a podokno nastavení](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Správa zásad zálohování pro virtuální počítač

### <a name="modify-backup-policy"></a>Upravit zásady zálohování

Postup úpravy existujících zásad zálohování:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/). Otevřete řídicí panel trezoru.
2. V části **Správa zásad zálohování >** vyberte zásady zálohování pro typ **virtuálního počítače Azure**.
3. Vyberte **Upravit** a změňte nastavení.

### <a name="switch-backup-policy"></a>Přepnout zásadu zálohování

Správa zásad zálohování:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/). Otevřete řídicí panel trezoru.
2. Na dlaždici **zálohované položky** vyberte **virtuální počítač Azure**.

    ![Otevřít dlaždici zálohované položky](./media/backup-azure-manage-vms/azure-virtual-machine.png)

3. V podokně **zálohované položky** můžete zobrazit seznam chráněných virtuálních počítačů a stav posledního zálohování s časem poslední body obnovení.

    ![Zobrazit podokno zálohované položky](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. Z řídicího panelu položky trezoru můžete vybrat zásadu zálohování.

   * Pokud chcete přepnout zásady, vyberte jinou zásadu a pak vyberte **Uložit**. Nové zásady se okamžitě použijí na trezor.

     ![Vybrat zásady zálohování](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Spuštění zálohování na vyžádání

Po nastavení ochrany můžete spustit zálohování virtuálního počítače na vyžádání. Pamatujte na tyto informace:

* Pokud je počáteční záloha nevyřízená, zálohování na vyžádání vytvoří úplnou kopii virtuálního počítače v trezoru Recovery Services.
* Pokud je prvotní zálohování hotové, zálohování na vyžádání pošle do trezoru Recovery Services jenom změny z předchozího snímku. To znamená, že pozdější zálohy jsou vždycky přírůstkové.
* Rozsah uchování zálohy na vyžádání je hodnota uchování, kterou zadáte při aktivaci zálohy.

> [!NOTE]
> Služba Azure Backup podporuje až tři zálohy na vyžádání za den a jedno další naplánované zálohování.

Aktivace zálohování na vyžádání:

1. Na [řídicím panelu položky trezoru](#view-vms-on-the-dashboard)v části **chráněná položka** vyberte **zálohovaná položka**.

    ![Možnost zálohovat nyní](./media/backup-azure-manage-vms/backup-now-button.png)

2. Z **typu správy zálohování** vyberte **virtuální počítač Azure**. Zobrazí se podokno **zálohovaná položka (virtuální počítač Azure)** .
3. Vyberte virtuální počítač a výběrem **Zálohovat nyní** vytvořte zálohu na vyžádání. Zobrazí se podokno **Zálohovat nyní** .
4. Do pole **uchovat zálohu** zadejte datum, kdy se má záloha zachovat.

    ![Kalendář Backup Now](./media/backup-azure-manage-vms/backup-now-check.png)

5. Výběrem **OK** spusťte úlohu zálohování.

Pokud chcete sledovat průběh úlohy, klikněte na řídicím panelu trezoru na dlaždici **úlohy zálohování** .

## <a name="stop-protecting-a-vm"></a>Zastavení ochrany virtuálního počítače

Existují dva způsoby, jak zastavit ochranu virtuálního počítače:

* **Zastavte ochranu a zachovejte data záloh**. Tato možnost zastaví všechny budoucí úlohy zálohování z ochrany virtuálního počítače. Služba Azure Backup však uchová body obnovení, které byly zálohovány.  Abyste zachovali body obnovení v trezoru, musíte platit, abyste si ponechali body obnovení v trezoru (podrobnosti najdete [Azure Backup ceny](https://azure.microsoft.com/pricing/details/backup/) ). V případě potřeby budete moct virtuální počítač obnovit. Pokud se rozhodnete obnovit ochranu virtuálního počítače, můžete použít možnost *obnovit zálohování* .
* **Zastavte ochranu a odstraňte zálohovaná data**. Tato možnost zastaví všechny budoucí úlohy zálohování z ochrany virtuálního počítače a odstraní všechny body obnovení. Nebudete moct obnovit virtuální počítač ani použít možnost *obnovit zálohování* .

>[!NOTE]
>Pokud odstraníte zdroj dat bez zastavení zálohování, nové zálohování se nezdaří. V souladu se zásadami vyprší platnost starých bodů obnovení, ale nejnovější bod obnovení bude vždycky uložený, dokud nezastavíte zálohování a data odstraníte.
>

### <a name="stop-protection-and-retain-backup-data"></a>Zastavení ochrany a uchování zálohovaných dat

Zastavení ochrany a zachování dat virtuálního počítače:

1. Na [řídicím panelu položky trezoru](#view-vms-on-the-dashboard)vyberte **Zastavit zálohování**.
2. Vyberte možnost **zachovat zálohovaná data** a podle potřeby potvrďte výběr. Pokud chcete, přidejte komentář. Pokud si nejste jisti názvem položky, najeďte na značku vykřičníkem a zobrazte název.

    ![Uchování zálohovaných dat](./media/backup-azure-manage-vms/retain-backup-data.png)

Oznámení vás upozorní, že se zastavily úlohy zálohování.

### <a name="stop-protection-and-delete-backup-data"></a>Zastavení ochrany a odstranění zálohovaných dat

Zastavení ochrany a odstranění dat virtuálního počítače:

1. Na [řídicím panelu položky trezoru](#view-vms-on-the-dashboard)vyberte **Zastavit zálohování**.
2. Vyberte možnost **Odstranit zálohovaná data** a podle potřeby potvrďte výběr. Zadejte název zálohované položky a přidejte komentář, pokud chcete.

    ![Odstranění zálohovaných dat](./media/backup-azure-manage-vms/delete-backup-data.png)

> [!NOTE]
> Po dokončení operace odstranění se zálohovaná data uchovávají po dobu 14 dnů ve [stavu tichého odstranění](./soft-delete-virtual-machines.md). <br>Kromě toho můžete také [Povolit nebo zakázat obnovitelné odstranění](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete).

## <a name="resume-protection-of-a-vm"></a>Obnovení ochrany virtuálního počítače

Pokud jste při zastavování ochrany virtuálního počítače zvolili možnost [Zastavit ochranu a zachovat data záloh](#stop-protection-and-retain-backup-data) , můžete použít možnost **pokračovat v zálohování**. Tato možnost není k dispozici, pokud zvolíte možnost [Zastavit ochranu a odstranit data zálohy](#stop-protection-and-delete-backup-data) nebo [Odstranit zálohovaná data](#delete-backup-data).

Obnovení ochrany virtuálního počítače:

1. Na [řídicím panelu položky trezoru](#view-vms-on-the-dashboard)vyberte **pokračovat v zálohování**.

2. Podle pokynů v části [Správa zásad zálohování](#manage-backup-policy-for-a-vm) přiřaďte zásady pro virtuální počítač. Nemusíte vybírat zásadu prvotní ochrany virtuálního počítače.
3. Po použití zásad zálohování u virtuálního počítače se zobrazí následující zpráva:

    ![Zpráva indikující úspěšnému chráněnému virtuálnímu počítači](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Odstranění zálohovaných dat

Existují dva způsoby, jak odstranit data zálohy virtuálního počítače:

* Z řídicího panelu položky trezoru vyberte Zastavit zálohování a postupujte podle pokynů pro možnost [Zastavit ochranu a odstranit data zálohy](#stop-protection-and-delete-backup-data) .

  ![Vyberte Zastavit zálohování.](./media/backup-azure-manage-vms/stop-backup-button.png)

* Z řídicího panelu položky trezoru vyberte Odstranit zálohovaná data. Tato možnost je povolená, pokud jste se rozhodli při zastavování ochrany virtuálního počítače [zastavit možnost Ochrana a zachovat data záloh](#stop-protection-and-retain-backup-data) .

  ![Vyberte odstranit zálohu.](./media/backup-azure-manage-vms/delete-backup-button.png)

  * Na [řídicím panelu položky trezoru](#view-vms-on-the-dashboard)vyberte **Odstranit zálohovaná data**.
  * Zadejte název zálohované položky, abyste potvrdili, že chcete body obnovení odstranit.

    ![Odstranění zálohovaných dat](./media/backup-azure-manage-vms/delete-backup-data.png)

  * Chcete-li odstranit data zálohy pro položku, vyberte možnost **Odstranit**. Zpráva s oznámením vám poskytne informace o odstranění zálohovaných dat.

K ochraně dat Azure Backup zahrnuje funkci obnovitelného odstranění. Díky obnovitelnému odstranění i po odstranění zálohy (všechny body obnovení) virtuálního počítače se zálohovaná data uchovávají po dobu 14 dalších dnů. Další informace najdete v [dokumentaci k obnovitelnému odstranění](./backup-azure-security-feature-cloud.md).

  > [!NOTE]
  > Při odstraňování zálohovaných dat odstraníte všechny přidružené body obnovení. Nemůžete zvolit konkrétní body obnovení, které se mají odstranit.

### <a name="backup-item-where-primary-data-source-no-longer-exists"></a>Zálohovaná položka, ve které už primární zdroj dat neexistuje

* Pokud se virtuální počítače Azure nakonfigurované pro Azure Backup buď odstranily, nebo se přesunuly bez zastavení ochrany, pak úlohy zálohování naplánované zálohovací úlohy i na vyžádání (ad-hoc) selžou s chybou UserErrorVmNotFoundV2. Předběžná kontroly zálohování se budou zobrazovat jako kritická jenom pro neúspěšné úlohy zálohování na vyžádání (naplánované úlohy, které selhaly, se nezobrazují).
* Tyto zálohované položky zůstávají aktivní v systému, který dodržuje zásady zálohování a uchovávání dat nastavené uživatelem. Zálohovaná data pro tyto virtuální počítače Azure se budou uchovávat podle zásad uchovávání informací. Body obnovení s vypršenou platností (s výjimkou nejaktuálnějšího bodu obnovení) se vyčistí podle rozsahu uchování nastaveného v zásadách zálohování.
* Abyste se vyhnuli dalším poplatkům, doporučujeme odstranit zálohované položky, kde primární zdroj dat už neexistuje. To je v situaci, kdy už není potřeba zálohovat položku nebo data pro odstraněné prostředky, protože nejnovější bod obnovení je trvale zachované a účtují se podle příslušných cen za zálohování.

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [zálohovat virtuální počítače Azure z nastavení virtuálního počítače](backup-azure-vms-first-look-arm.md).
* Přečtěte si, jak [obnovit virtuální počítače](backup-azure-arm-restore-vms.md).
* Naučte se [monitorovat zálohy virtuálních počítačů Azure](./backup-azure-monitoring-built-in-monitor.md).
