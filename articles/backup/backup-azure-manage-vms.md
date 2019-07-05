---
title: Správa a sledování záloh virtuálních počítačů Azure pomocí služby Azure Backup
description: Zjistěte, jak ke správě a sledování záloh virtuálních počítačů Azure pomocí služby Azure Backup.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: sogup
ms.openlocfilehash: add2c72535b5be0edcbc00c077dfe20a6deaa3e0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434195"
---
# <a name="manage-azure-vm-backups"></a>Správa záloh virtuálních počítačů Azure

Tento článek popisuje, jak spravovat virtuální počítače Azure (VM), které jsou zálohovány pomocí [služby Azure Backup](backup-overview.md). Tento článek shrnuje také zálohování informace, které najdete na řídicím panelu trezoru.


Na webu Azure Portal, řídicího panelu trezoru služby Recovery Services poskytuje přístup k trezoru informace, včetně:

* Poslední zálohy, což je také nejnovější bod obnovení.
* Zásady zálohování.
* Celková velikost všech snímků zálohy.
* Počet virtuálních počítačů, které jsou povolené pro zálohování.

Zálohování můžete spravovat pomocí řídicího panelu a podrobnostem jednotlivým virtuálním počítačům. Zahajte zálohování počítačů, otevřete na řídicím panelu trezoru.

![Zobrazení řídicího panelu úplné s posuvníkem](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>Zobrazení virtuálních počítačů na řídicím panelu

Chcete-li zobrazit virtuálních počítačů na řídicím panelu trezoru:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V nabídce centra vyberte **Procházet**. V seznamu prostředků zadejte **Recovery Services**. Při psaní v seznamu vyfiltrují podle vašeho zadání. Vyberte **trezory služby Recovery Services**.

    ![Vytvoření trezoru Služeb zotavení](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Snadné použití, klikněte pravým tlačítkem na trezor a vyberte **připnout na řídicí panel**.
4. Otevření řídicího panelu trezoru.

    ![Otevření řídicího panelu trezoru a okna nastavení](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. Na **zálohování položek** dlaždice, vyberte **Azure Virtual Machines**.

    ![Otevřete dlaždici zálohování položek](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. Na **zálohování položek** okně můžete zobrazit seznam chráněných virtuálních počítačů. V tomto příkladu trezor chrání jeden virtuální počítač: demobackup.  

    ![Zobrazit okno zálohování položek](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. Z řídicího panelu trezoru položky upravte zásady zálohování, spustit zálohování, zastavit na vyžádání nebo obnovit ochranu virtuálních počítačů, odstranit zálohovaná data, Zobrazit body obnovení a spusťte obnovení.

    ![Zálohování položek řídicího panelu a v okně Nastavení](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Správa zásad zálohování pro virtuální počítač

Správa zásad zálohování:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/). Otevření řídicího panelu trezoru.
2. Na **zálohování položek** dlaždice, vyberte **Azure Virtual Machines**.

    ![Otevřete dlaždici zálohování položek](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. Na **zálohované položky** okně můžete zobrazit seznam chráněných virtuálních počítačů a stav poslední zálohy s nejpozdější čas body obnovení.

    ![Zobrazit okno zálohování položek](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. Z řídicího panelu trezoru položky můžete vybrat zásady zálohování.

   * K přepnutí zásad, vyberte jinou zásadu a pak vyberte **Uložit**. Nové zásady se okamžitě použijí na trezor.

     ![Vybrat zásady zálohování](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Spustit zálohu na vyžádání
Po nastavení ochrany, můžete spustit zálohování na vyžádání z virtuálního počítače. Mějte tyto podrobnosti:

- Pokud čeká na vyřízení prvotní zálohování, zálohování na vyžádání vytvoří úplná kopie virtuálního počítače v trezoru služby Recovery Services.
- Pokud dokončení prvotní zálohy se zálohu na vyžádání odešle pouze změny z předchozího snímku do trezoru služby Recovery Services. To znamená jsou novější zálohy vždy přírůstkové.
- Hodnotu uchování, určete, kdy aktivovat zálohování je rozsah uchování pro zálohu na vyžádání.

Spustit zálohu na vyžádání:

1. Na [řídicího panelu trezoru položky](#view-vms-on-the-dashboard)v části **chráněné položky**vyberte **zálohovaná položka**.

    ![Možnost zálohování nyní](./media/backup-azure-manage-vms/backup-now-button.png)

2. Z **typu správy zálohování**vyberte **virtuálního počítače Azure**. **Zálohované položky (virtuální počítač Azure)** otevře se okno.
3. Vyberte virtuální počítač a vyberte **zazálohovat** vytvořit zálohu na vyžádání. **Zálohovat nyní** otevře se okno.
4. V **záloha zachována** uveďte datum zálohy uchovávat.

    ![Kalendář zálohovat nyní](./media/backup-azure-manage-vms/backup-now-check.png)

5. Vyberte **OK** spuštění úlohy zálohování.

Chcete-li sledovat průběh úlohy na řídicím panelu trezoru, vyberte **úlohy zálohování** dlaždici.

## <a name="stop-protecting-a-vm"></a>Ukončit ochranu virtuálního počítače

Ochranu virtuálního počítače můžete zastavit dvěma způsoby:

* **Zastavení ochrany a zachovat data záloh**. Tato možnost zastaví všechny budoucí úlohy zálohování z chránit svůj virtuální počítač; Služba Azure Backup se však zachovat body obnovení, které se mají zálohovat.  Budete muset zaplatit zachovat body obnovení v trezoru (viz [ceny služby Azure Backup](https://azure.microsoft.com/pricing/details/backup/) podrobnosti). Budete moct v případě potřeby obnovte virtuální počítač. Pokud se rozhodnete pokračovat v ochraně virtuálního počítače, pak můžete použít *obnovit zálohu* možnost.
* **Zastavení ochrany a odstraňte zálohovaná data**. Tato možnost zastaví všechny budoucí úlohy zálohování z chránit svůj virtuální počítač a odstranit všechny body obnovení. Nebude možné virtuální počítač obnovit ani použít *obnovit zálohu* možnost.

>[!NOTE]
>Při odstranění zdroje dat bez zastavení zálohování, se nezdaří nových záloh. Podle zásad vyprší platnost starých bodů obnovení, ale jeden poslední bod obnovení bude vždy zachovaná, dokud Zastavit zálohování a odstraňte data.
>

### <a name="stop-protection-and-retain-backup-data"></a>Zastavení ochrany a zachovat data záloh

Zastavení ochrany a zachovat data virtuálního počítače:

1. Na [položky řídícím panelu trezoru](#view-vms-on-the-dashboard)vyberte **Zastavit zálohování**.
2. Zvolte **zachovat zálohovaná Data**a potvrďte výběr podle potřeby. Pokud chcete přidáte komentář. Pokud si nejste jisti názvem položky, najeďte myší vykřičník, abyste zobrazili jméno.

    ![Zachovat zálohování – uložená data](./media/backup-azure-manage-vms/retain-backup-data.png)

Oznámení vám umožňuje vědět, že byly zastaveny úlohy zálohování.

### <a name="stop-protection-and-delete-backup-data"></a>Zastavení ochrany a odstranění zálohovaných dat

K zastavení ochrany a odstranění dat virtuálního počítače:

1. Na [položky řídícím panelu trezoru](#view-vms-on-the-dashboard)vyberte **Zastavit zálohování**.
2. Zvolte **odstranit zálohovaná Data**a potvrďte výběr podle potřeby. Zadejte název zálohované položky a v případě potřeby přidat komentář.

    ![Odstranění zálohovaných dat](./media/backup-azure-manage-vms/delete-backup-data1.png)

## <a name="resume-protection-of-a-vm"></a>Pokračovat v ochraně virtuálního počítače

Pokud jste vybrali [zastavení ochrany a zachovat data záloh](#stop-protection-and-retain-backup-data) možnost při zastavení ochrany virtuálního počítače, můžete použít **obnovit zálohu**. Tato možnost není k dispozici, pokud se rozhodnete [zastavení ochrany a odstraňte zálohovaná data](#stop-protection-and-delete-backup-data) možnost nebo [odstranit data zálohy](#delete-backup-data).

Pokud chcete obnovit ochranu pro virtuální počítač:

1. Na [položky řídícím panelu trezoru](#view-vms-on-the-dashboard)vyberte **obnovit zálohu**.

2. Postupujte podle kroků v [Správa zásad zálohování](#manage-backup-policy-for-a-vm) přiřazení zásady pro virtuální počítač. Není nutné vyberte zásadu pro počáteční ochranu Virtuálního počítače.
3. Po použití zásady zálohování pro virtuální počítač, zobrazí se následující zpráva:

    ![Zprávu s upozorněním úspěšně chráněného virtuálního počítače](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Odstranění zálohovaných dat

Existují dva způsoby, jak odstranit data zálohy Virtuálního počítače:

- Z řídicího panelu trezoru položky, vyberte Zastavit zálohování a postupujte podle pokynů pro [zastavení ochrany a odstraňte zálohovaná data](#stop-protection-and-delete-backup-data) možnost.

  ![Vyberte Zastavit zálohování](./media/backup-azure-manage-vms/stop-backup-buttom.png)

- Z řídicího panelu trezoru položky vyberte možnost odstranit zálohovaná data. Tato možnost je povolená, pokud jste vybrali k [zastavení ochrany a zachovat data záloh](#stop-protection-and-retain-backup-data) možnost při zastavení ochrany virtuálního počítače

  ![Vybrat zálohu, která Delete](./media/backup-azure-manage-vms/delete-backup-buttom.png)

  - Na [řídicího panelu trezoru položky](#view-vms-on-the-dashboard)vyberte **odstranit data zálohy**.
  - Zadejte název zálohované položky potvrďte, že chcete odstranit body obnovení.

    ![Odstranění zálohovaných dat](./media/backup-azure-manage-vms/delete-backup-data1.png)

  - Chcete-li odstranit zálohovaná data pro položku, vyberte **odstranit**. Zprávu s oznámením poznáte, že záložní data byla odstraněna.

  > [!NOTE]
  > Odstranit zálohovaná data odstraníte všechny body obnovení přidružené. Nemůžete zvolit konkrétní body obnovení odstranit.

## <a name="next-steps"></a>Další postup
- Zjistěte, jak [zálohování virtuálních počítačů Azure z nastavení Virtuálního počítače](backup-azure-vms-first-look-arm.md).
- Zjistěte, jak [obnovení virtuálních počítačů](backup-azure-arm-restore-vms.md).
- Zjistěte, jak [sledování záloh virtuálních počítačů Azure](backup-azure-monitor-vms.md).
