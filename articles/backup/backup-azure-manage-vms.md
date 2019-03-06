---
title: Správa a sledování záloh virtuálních počítačů Azure pomocí služby Azure Backup
description: Zjistěte, jak ke správě a sledování záloh virtuálních počítačů Azure pomocí služby Azure Backup.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: sogup
ms.openlocfilehash: 0fa221721471772b066990ec2d33f0cedb960239
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453537"
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

4. Na **zálohování položek** dlaždice, vyberte **Azure Virtual Machines**.

    ![Otevřete dlaždici zálohování položek](./media/backup-azure-manage-vms/contoso-vault-1606.png)

5. Na **zálohované položky** okně zobrazí poslední úloha zálohování pro každou položku. V tomto příkladu trezor chrání jeden virtuální počítač: demovm markgal.  

    ![Zobrazit okno zálohování položek](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)


6. Z řídicího panelu trezoru položky můžete vytvořit nebo upravit zásady zálohování, Zobrazit body obnovení, spustit zálohování, zastavit na vyžádání nebo obnovit ochranu virtuálních počítačů, odstranit body obnovení a spusťte obnovení.

    ![Zálohování položek řídicího panelu a v okně Nastavení](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policies"></a>Správa zásad zálohování

Správa zásad zálohování:

1. Na [řídicího panelu trezoru položky](#view-vms-in-the-dashboard)vyberte **všechna nastavení**.

    ![Všechny možnosti nastavení](./media/backup-azure-manage-vms/all-settings-button.png)
2. V **nastavení**vyberte **zásady zálohování**.
3. Na **výběr zásady zálohování** nabídky:

   * K přepnutí zásad, vyberte jinou zásadu a pak vyberte **Uložit**. Nové zásady se okamžitě použijí na trezor.
   * Chcete-li vytvořit zásadu, vyberte **vytvořit nový**. Další informace najdete v tématu [konfiguraci zásad zálohování](backup-azure-arm-vms-prepare.md#configure-a-backup-policy).

     ![Vybrat zásady zálohování](./media/backup-azure-manage-vms/backup-policy-create-new.png)


## <a name="run-an-on-demand-backup"></a>Spustit zálohu na vyžádání
Po nastavení ochrany, můžete spustit zálohování na vyžádání z virtuálního počítače. Mějte tyto podrobnosti: 
- Pokud čeká na vyřízení prvotní zálohování, zálohování na vyžádání vytvoří úplná kopie virtuálního počítače v trezoru služby Recovery Services.
- Pokud dokončení prvotní zálohy se zálohu na vyžádání odešle pouze změny z předchozího snímku do trezoru služby Recovery Services. To znamená jsou novější zálohy vždy přírůstkové.
- Hodnotu uchování, určete, kdy aktivovat zálohování je rozsah uchování pro zálohu na vyžádání.

Spustit zálohu na vyžádání:

1. Na [řídicího panelu trezoru položky](#view-vms-in-the-dashboard)v části **chráněné položky**vyberte **zálohovaná položka**.

    ![Možnost zálohování nyní](./media/backup-azure-manage-vms/backup-now-button.png)

2. Z **typu správy zálohování**vyberte **virtuálního počítače Azure**. **Zálohované položky (virtuální počítač Azure)** otevře se okno.
3. Vyberte virtuální počítač a vyberte **zazálohovat** vytvořit zálohu na vyžádání. **Zálohovat nyní** otevře se okno.
4. V **záloha zachována** uveďte datum zálohy uchovávat.

    ![Kalendář zálohovat nyní](./media/backup-azure-manage-vms/backup-now-check.png)

5. Vyberte **OK** spuštění úlohy zálohování.

Chcete-li sledovat průběh úlohy na řídicím panelu trezoru, vyberte **úlohy zálohování** dlaždici.

## <a name="stop-protecting-a-vm"></a>Ukončit ochranu virtuálního počítače

Ochranu virtuálního počítače můžete zastavit dvěma způsoby:

- Zastavit všechny budoucí úlohy zálohování a odstranit všechny body obnovení. V takovém případě nebudete schopni obnovit virtuální počítač.
- Zastavit všechny budoucí úlohy zálohování a zachovat body obnovení. I když se budete muset zaplatit zachovat body obnovení v trezoru, budete moct v případě potřeby obnovte virtuální počítač. Další informace najdete v tématu [ceny služby Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

>[!NOTE]
>Při odstranění zdroje dat bez zastavení zálohování, se nezdaří nových záloh. Podle zásad vyprší platnost starých bodů obnovení, ale jeden poslední bod obnovení bude vždy zachovaná, dokud Zastavit zálohování a odstraňte data.
>

Zastavení ochrany pro virtuální počítač:

1. Na [položky řídícím panelu trezoru](#view-vms-in-the-dashboard)vyberte **Zastavit zálohování**.
2. Zvolte, jestli se má uchovat nebo odstranit data zálohy a potvrďte výběr podle potřeby. Pokud chcete přidáte komentář. Pokud si nejste jisti názvem položky, najeďte myší vykřičník, abyste zobrazili jméno.

    ![Zastavení ochrany](./media/backup-azure-manage-vms/retain-or-delete-option.png)

     Oznámení vám umožňuje vědět, že byly zastaveny úlohy zálohování.

## <a name="resume-protection-of-a-vm"></a>Pokračovat v ochraně virtuálního počítače

Pokud při zastavení virtuálního počítače se zachovat zálohovaná data, můžete později obnovit ochranu. Při odstranění zálohovaných dat, nelze obnovit ochranu.

Pokud chcete obnovit ochranu pro virtuální počítač:

1. Na [položky řídícím panelu trezoru](#view-vms-in-the-dashboard)vyberte **obnovit zálohu**.

2. Postupujte podle kroků v [Správa zásad zálohování](#manage-backup-policies) přiřazení zásady pro virtuální počítač. Není nutné vyberte zásadu pro počáteční ochranu Virtuálního počítače.
3. Po použití zásady zálohování pro virtuální počítač, zobrazí se následující zpráva:

    ![Zprávu s upozorněním úspěšně chráněného virtuálního počítače](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Odstranit data zálohy

Můžete odstranit zálohovaná data Virtuálního počítače během **Zastavit zálohování** úlohy nebo po dokončení úlohy zálohování. Než odstraníte data záloh, mějte tyto podrobnosti:

- Může být vhodné čekat dny nebo týdny před odstraněním bodů obnovení.
- Na rozdíl od procesu pro obnovování bodů obnovení, při odstranit data zálohy, nelze zvolit konkrétní body obnovení odstranit. Pokud jste odstranit zálohovaná data, odstraníte všechny body obnovení přidružené.

Po zastavení nebo zakázání úloha zálohování Virtuálního počítače, můžete odstranit zálohovaná data:


1. Na [řídicího panelu trezoru položky](#view-vms-in-the-dashboard)vyberte **odstranit zálohy**.

    ![Vybrat zálohu, která Delete](./media/backup-azure-manage-vms/delete-backup-buttom.png)

1. Zadejte název zálohované položky potvrďte, že chcete odstranit body obnovení.

    ![Potvrďte, že chcete odstranit body obnovení](./media/backup-azure-manage-vms/item-verification-box.png)

1. Chcete-li odstranit zálohovaná data pro položku, vyberte **odstranit**. Zprávu s oznámením poznáte, že záložní data byla odstraněna.

## <a name="next-steps"></a>Další postup
- Zjistěte, jak [zálohování virtuálních počítačů Azure z nastavení Virtuálního počítače](backup-azure-vms-first-look-arm.md).
- Zjistěte, jak [obnovení virtuálních počítačů](backup-azure-arm-restore-vms.md).
- Zjistěte, jak [sledování záloh virtuálních počítačů Azure](backup-azure-monitor-vms.md).
