---
title: Správa a sledování záloh virtuálních počítačů Azure pomocí služby Azure Backup
description: Zjistěte, jak se správou a monitorováním zálohování virtuálních počítačů Azure pomocí služby Azure Backup.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: sogup
ms.openlocfilehash: f5c0373e2ef094a7fc5be64f4aeb8c0bb132e683
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430059"
---
# <a name="manage-azure-vm-backups"></a>Správa záloh virtuálních počítačů Azure

Tento článek popisuje, jak spravovat virtuální počítače Azure zálohovat pomocí [služba Azure Backup](backup-overview.md) zálohování a shrnuje informace výstrahy zálohování v řídicím panelu portálu k dispozici.


Řídicím panelu trezoru služby Recovery Services na webu Azure Portal, poskytuje přístup k informacím o trezoru, včetně:

* Poslední zálohy, což je také nejnovější bod obnovení.
* zásady zálohování
* Celková velikost všech snímků zálohy
* Počet virtuálních počítačů, které jsou povolené pro zálohování

Zálohování z řídicího panelu, můžete spravovat a podrobnostem jednotlivým virtuálním počítačům. vzdálený zálohování začínat otevírání v řídicím panelu trezoru. 

![Úplné zobrazení s posuvníkem](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-in-the-dashboard"></a>Zobrazení virtuálních počítačů na řídicím panelu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V nabídce centra klikněte na **Procházet** a v seznamu prostředků zadejte **Recovery Services**. Seznam se průběžně filtruje podle zadávaného textu. Klikněte na **Trezor Recovery Services**. 
    ![Vytvoření trezoru služby Recovery Services – krok 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Snadné použití, klikněte pravým tlačítkem na trezor v seznamu trezorů > **připnout na řídicí panel**.
3. Otevření řídicího panelu trezoru. 
    ![Otevření řídicího panelu trezoru a okna nastavení](./media/backup-azure-manage-vms/full-view-rs-vault.png)

4. Na **zálohování položek** dlaždici, klikněte na tlačítko **Azure Virtual Machines**.

    ![Dlaždice otevřít zálohované položky](./media/backup-azure-manage-vms/contoso-vault-1606.png)

5. V **zálohované položky** okno, zobrazí se poslední úloha zálohování pro každou položku. V tomto příkladu je jeden chráněný virtuální počítač, demovm markgal, v tomto trezoru.  

    ![Zálohované položky dlaždice](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

 
6. Z řídicího panelu trezoru položky můžete vytvořit nebo upravit zásady zálohování, Zobrazit body obnovení, spustit zálohování, zastavit na vyžádání a obnovit ochranu virtuálních počítačů, odstranit body obnovení a spusťte obnovení.

    ![Řídicí panel zálohované položky se okno nastavení](./media/backup-azure-manage-vms/item-dashboard-settings.png)



## <a name="manage-backup-policies"></a>Správa zásad zálohování
1. Na [řídicího panelu trezoru položky](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klikněte na tlačítko **všechna nastavení** .

    ![Okno zásady zálohování](./media/backup-azure-manage-vms/all-settings-button.png)
2. V **nastavení**, klikněte na tlačítko**zásady zálohování**e.
3. Z **výběr zásady zálohování** nabídky:

   * Chcete-li změnit zásady, vyberte jiné zásady a klikněte na tlačítko **Uložit**. Nové zásady se okamžitě použijí na trezor.
   * Chcete-li vytvořit zásadu, vyberte **vytvořit nový**. [Další informace](backup-azure-arm-vms-prepare.md#configure-a-backup-policy)

     ![Záloha virtuálního počítače](./media/backup-azure-manage-vms/backup-policy-create-new.png)


## <a name="run-an-on-demand-backup"></a>Spustit zálohu na vyžádání
Podle potřeby můžete využít zálohování virtuálního počítače, jakmile je nakonfigurován pro ochranu.
- Pokud čeká na vyřízení prvotní zálohování, zálohování na vyžádání vytvoří úplná kopie virtuálního počítače v trezoru služby Recovery Services.
- Po dokončení prvotní zálohy zálohu na vyžádání odešle pouze změny z předchozího snímku do trezoru služby Recovery Services. To znamená jsou následné zálohy vždy přírůstkové.
- uchování hodnota zadaná pro denního bodu zálohy v zásadě je rozsah uchování používaný pro zálohu na vyžádání. Pokud je vybrána žádná denního bodu zálohy, použije se týdenního bodu zálohy.


Spustit zálohu na vyžádání:

1. Na [řídicího panelu trezoru položky](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klikněte na tlačítko **zálohovat nyní**.

    ![Zálohování teď tlačítko.](./media/backup-azure-manage-vms/backup-now-button.png)

 2. Klikněte na tlačítko **Ano** spustit úlohu zálohování.

    ![Zálohování teď tlačítko.](./media/backup-azure-manage-vms/backup-now-check.png)

 
 Úloha zálohování vytvoří bod obnovení. Rozsah uchování bodu obnovení je stejný jako rozsah uchování uveden v zásadách, které jsou spojené s virtuálním počítačem. Chcete-li sledovat průběh úlohy v řídicím panelu trezoru klikněte na tlačítko **úlohy zálohování** dlaždici.  

## <a name="stop-protecting-a-vm"></a>Ukončit ochranu virtuálního počítače

Existují dva způsoby, jak zastavit ochranu virtuálních počítačů:

- Zastavit všechny budoucí úlohy zálohování a odstranit všechny body obnovení. Nebudete schopni obnovit virtuální počítač v tomto případě.
- Zastavit všechny budoucí úlohy zálohování, ale ponechat body obnovení. Existuje náklady spojené s ponecháním bodů obnovení v úložišti. Výhodou ponechání bodů obnovení však je, že v případě potřeby můžete obnovit virtuální počítač. [Další informace](https://azure.microsoft.com/pricing/details/backup/) o podrobnosti o cenách.

Zastavení ochrany pro virtuální počítač:

1. Na [řídicího panelu trezoru položky](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klikněte na tlačítko **Zastavit zálohování**.

    ! [Zálohování tlačítko Zastavit] (./media/backup-azure-manage-vms/stop-backup-button.png
2. Zvolte, jestli se má uchovat nebo odstranit data zálohy a potvrďte podle potřeby. Potvrďte podle potřeby a volitelně zadejte komentář. Pokud si nejste jisti názvem položky, najeďte myší vykřičník, abyste zobrazili jméno.

    ![Zastavení ochrany](./media/backup-azure-manage-vms/retain-or-delete-option.png)

 Oznámení vám umožňuje vědět, že byly zastaveny úlohy zálohování.


## <a name="resume-protection-of-a-vm"></a>Pokračovat v ochraně virtuálního počítače

Pokud se data záloh uchovávají, když byl virtuální počítač zastavený, můžete obnovit ochranu. Pokud se zálohovaná data odstraněna, pak nelze obnovit.

te

1. Na [řídicího panelu trezoru položky](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klikněte na tlačítko **obnovit zálohu**.

2. Postupujte podle kroků v [Správa zásad zálohování](backup-azure-manage-vms.md#manage-backup-policies) přiřazení zásad pro virtuální počítač. můžete použít jiné zásady než zásady, pomocí kterého byl virtuální počítač původně chráněný.
3. Po zásadu zálohování, která se použije k virtuálnímu počítači, zobrazí se následující zpráva.

    ![Úspěšně chráněného virtuálního počítače](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Odstranění zálohovaných dat

Můžete odstranit zálohovaná data přidružená k virtuálnímu počítači během **Zastavit zálohování** úlohy, nebo kdykoli po zálohování dokončení úlohy.

- Před odstraněním bodů obnovení dokonce může být užitečné několik dnů nebo týdnů počkat.
- Na rozdíl od obnovování bodů obnovení nemůžete při odstraňování zálohovaných dat zvolit konkrétní body obnovení, které se mají odstranit. Pokud se rozhodnete odstranit zálohovaná data, odstraníte všechny body obnovení přidružené k příslušné položce.

Tento postup předpokládá úloha zálohování pro virtuální počítač byla zastavena nebo zakázána.


1. Na [řídicího panelu trezoru položky](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klikněte na tlačítko **odstranit zálohy**.

    ![Typ virtuálního počítače](./media/backup-azure-manage-vms/delete-backup-buttom.png)

2. Zadejte název položky, abychom potvrdili, že chcete odstranit body obnovení.

    ![Zastavit ověření](./media/backup-azure-manage-vms/item-verification-box.png)

4. Chcete-li odstranit zálohovaná data pro aktuální položku, klikněte na tlačítko ![tlačítko Zastavit zálohování](./media/backup-azure-manage-vms/delete-button.png)

    Oznámení vám umožňuje vědět, že záložní data byla odstraněna.

## <a name="next-steps"></a>Další postup
- [Další informace o](backup-azure-vms-first-look-arm.md) zálohování virtuálních počítačů Azure z nastavení virtuálního počítače.
- [Další informace o](backup-azure-arm-restore-vms.md) obnovení virtuálních počítačů. 
- [Další informace o](backup-azure-monitor-vms.md) sledování záloh virtuálních počítačů Azure.
 
