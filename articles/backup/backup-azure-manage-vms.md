---
title: Správa záloh virtuálních počítačů nasazených Resource Managerem
description: Zjistěte, jak spravovat a monitorovat záloh virtuálních počítačů nasazených Resource Managerem
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 11/28/2016
ms.author: trinadhk
ms.openlocfilehash: 4d45db6ba6354f85c3ed67561751720b6f6f4b77
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606337"
---
# <a name="manage-azure-virtual-machine-backups"></a>Správa záloh virtuálních počítačů Azure

Tento článek obsahuje pokyny týkající se správy zálohování virtuálních počítačů a vysvětluje výstrahy zálohy informace k dispozici v řídicím panelu portálu. Pokyny v tomto článku se vztahuje na virtuální počítače pomocí trezory služeb zotavení. Tento článek nepopisuje vytváření virtuálních počítačů, ani nemá vysvětlují postup ochrana virtuálních počítačů. Úvod do o ochraně nasazení Azure Resource Manager virtuálních počítačů v Azure k trezoru služeb zotavení, najdete v části [první pohled: zálohování virtuálních počítačů do trezoru služeb zotavení](backup-azure-vms-first-look-arm.md).

## <a name="manage-vaults-and-protected-virtual-machines"></a>Správa úložišť a chráněné virtuální počítače
Na portálu Azure řídícím panelu trezoru služeb zotavení poskytuje přístup k informacím o trezoru, včetně:

* poslední snímek zálohy, který je také do nejnovějšího bodu obnovení
* zásady zálohování
* Celková velikost všech snímků zálohy
* počet virtuálních počítačů, které jsou chráněné pomocí trezoru

Mnoho úlohy správy počítačů s zálohování virtuálních počítačů začínat otevření v řídicím panelu trezoru. Ale protože trezory je možné použít k ochraně více položek (nebo víc virtuálních počítačů), chcete-li zobrazit podrobnosti o konkrétní virtuální počítač, otevřete položku panelu trezoru. Následující postup ukazuje, jak otevřít *panelu trezoru* a pak pokračujte *položky panelu trezoru*. V obou postupů, které ukazují na to, jak přidat trezoru a trezoru položky na řídicí panel Azure pomocí připnout na řídicí panel příkaz nejsou "tipy". Připnout na řídicí panel je způsob vytvoření zástupce trezoru nebo položky. Běžné příkazy můžete spustit také pomocí zástupce.

> [!TIP]
> Pokud máte více řídicí panely a otevřete oken, posuvníkem světlý blue v dolní části okna Vysuňte řídicí panel Azure a zpět.
>
>

![Úplné zobrazení s posuvníku](./media/backup-azure-manage-vms/bottom-slider.png)

### <a name="open-a-recovery-services-vault-in-the-dashboard"></a>Otevřete v řídicím panelu trezoru služeb zotavení:
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V nabídce centra klikněte na **Procházet** a v seznamu prostředků zadejte **Recovery Services**. Seznam se průběžně filtruje podle zadávaného textu. Klikněte na **Trezor Recovery Services**.

    ![Vytvoření trezoru Služeb zotavení – krok 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

    Zobrazí se seznam trezorů Služeb zotavení.

    ![Trezory seznam služeb zotavení ](./media/backup-azure-manage-vms/list-o-vaults.png)

   > [!TIP]
   > Pokud připnete trezoru na řídicí panel Azure, je tento trezor okamžitě dostupné při otevření portálu Azure. Připnutí trezoru k řídicímu panelu, v seznamu trezoru, klikněte pravým tlačítkem na úložiště a vyberte **připnout na řídicí panel**.
   >
   >
3. Ze seznamu trezorů vyberte trezor pro otevření jeho řídicího panelu. Když vyberete je trezor, řídícím panelu trezoru a **nastavení** okno otevřít. Na následujícím obrázku **Contoso trezoru** zvýrazní řídicího panelu.

    ![Otevření panelu trezoru a okna nastavení](./media/backup-azure-manage-vms/full-view-rs-vault.png)

### <a name="open-a-vault-item-dashboard"></a>Otevřete položku řídicího panelu trezoru
V předchozím postupu otevřít řídícím panelu trezoru. Chcete-li spustit položku panelu trezoru:

1. Na řídicím panelu trezoru na **zálohování položek** dlaždici, klikněte na tlačítko **virtuální počítače Azure**.

    ![Otevřete položky zálohování dlaždice](./media/backup-azure-manage-vms/contoso-vault-1606.png)

    **Položky zálohování** okno uvádí poslední úloha zálohování pro každou položku. V tomto příkladu je jeden virtuální počítač, demovm-markgal chráněn tento trezor.  

    ![Dlaždice položky zálohování](./media/backup-azure-manage-vms/backup-items-blade.png)

   > [!TIP]
   > Pro usnadnění přístupu budete moct připnout položku trezoru na řídicí panel Azure. Chcete-li připnout položku trezoru v seznamu položek trezoru, klikněte pravým tlačítkem položku a vyberte **připnout na řídicí panel**.
   >
   >
2. V **zálohování položek** okně klikněte na položku otevřete položku panelu trezoru.

    ![Dlaždice položky zálohování](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

    Položka panelu trezoru a jeho **nastavení** okno otevřít.

    ![Řídicí panel položky zálohování se okno nastavení](./media/backup-azure-manage-vms/item-dashboard-settings.png)

    Na řídicím panelu trezoru položky můžete provést celou řadu úloh správy klíčů, jako například:

   * změnit zásady nebo vytvořte nové zásady zálohování
   * Zobrazit body obnovení a zobrazí jejich konzistence stavu
   * Zálohování na vyžádání virtuálního počítače
   * Zastavte ochranu virtuálních počítačů
   * Pokračovat v ochraně virtuálního počítače
   * odstraníte záložní data (nebo bodu obnovení)
   * [obnovit záložní disky](backup-azure-arm-restore-vms.md#restore-backed-up-disks)

Následující postupy je výchozí bod položky panelu trezoru.

## <a name="manage-backup-policies"></a>Správa zásad zálohování
1. Na [položky panelu trezoru](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klikněte na tlačítko **všechna nastavení** otevřete **nastavení** okno.

    ![Okno zásady zálohování](./media/backup-azure-manage-vms/all-settings-button.png)
2. Na **nastavení** okně klikněte na tlačítko **zálohování zásad** k otevření tohoto okna.

    V okně se zobrazí podrobnosti rozsah zálohování četnost a jejich uchovávání.

    ![Okno zásady zálohování](./media/backup-azure-manage-vms/backup-policy-blade.png)
3. Z **vyberte zásady zálohování** nabídky:

   * Chcete-li změnit zásady, vyberte jinou zásadu a klikněte na **Uložit**. Nové zásady se okamžitě použijí na trezor.
   * Chcete-li vytvořit zásadu, vyberte **vytvořit nový**.

     ![Záloha virtuálního počítače](./media/backup-azure-manage-vms/backup-policy-create-new.png)

     Pokyny pro vytvoření zásady zálohování, naleznete v části [definování zásad zálohování](backup-azure-manage-vms.md#defining-a-backup-policy).

[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

> [!NOTE]
> Při správě zásady zálohování, nezapomeňte postupujte podle [osvědčené postupy](backup-azure-vms-introduction.md#best-practices) pro optimální výkon zálohování
>
>

## <a name="on-demand-backup-of-a-virtual-machine"></a>Zálohování na vyžádání virtuálního počítače
Na vyžádání můžete provést zálohování virtuálního počítače, jakmile je nakonfigurován pro ochranu. Pokud prvotní zálohování čeká na vyřízení, zálohování na vyžádání vytvoří úplnou kopii virtuálního počítače v trezoru služeb zotavení. Pokud dokončení prvotní zálohování zálohování na vyžádání bude odesílat pouze změny z předchozího snímku, trezor služeb zotavení. To znamená jsou následné zálohy vždy přírůstkové.

> [!NOTE]
> Rozsah uchování pro zálohu na vyžádání je uchování hodnota zadaná pro denního bodu zálohy v zásadách. Pokud je vybrána žádná denního bodu zálohy, se používá týdenního bodu zálohy.
>
>

Chcete-li spustit zálohování na vyžádání virtuálního počítače:

* Na [položky panelu trezoru](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klikněte na tlačítko **zálohovat nyní**.

    ![Zálohování teď tlačítko.](./media/backup-azure-manage-vms/backup-now-button.png)

    Na portálu zajišťuje, že chcete spustit úlohu zálohování na vyžádání. Klikněte na tlačítko **Ano** spustit úlohu zálohování.

    ![Zálohování teď tlačítko.](./media/backup-azure-manage-vms/backup-now-check.png)

    Úloha zálohování vytvoří bod obnovení. Rozsah uchování bodu obnovení je stejný jako rozsah uchování určená v zásadách, které jsou spojené s virtuálním počítačem. Chcete-li sledovat průběh úlohy v řídícím panelu trezoru, klikněte na tlačítko **úlohy zálohování** dlaždici.  

## <a name="stop-protecting-virtual-machines"></a>Zastavte ochranu virtuálních počítačů
Pokud si zvolíte zastavení ochrany virtuálního počítače, zobrazí se výzva, pokud chcete zachovat body obnovení. Existují dva způsoby, jak zastavit ochranu virtuálních počítačů:

* Zastavit všechny budoucí úlohy zálohování a odstranění všech bodů obnovení, nebo
* Zastavit všechny budoucí úlohy zálohování, ale ponechte bodů obnovení

Není k dispozici s náklady spojené s ponechat bodů obnovení v úložišti. Výhodou a bodů obnovení je však, že později, můžete obnovit virtuální počítač v případě potřeby. Informace o náklady a body obnovení, najdete v článku [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/backup/). Pokud zvolíte možnost odstraní všechny body obnovení, nelze obnovit virtuální počítač.

Ukončete ochranu pro virtuální počítač:

1. Na [položky panelu trezoru](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klikněte na tlačítko **zastavení zálohování**.

    ![Zastavení zálohování tlačítko](./media/backup-azure-manage-vms/stop-backup-button.png)

    Otevře se okno zastavení zálohování.

    ![Zastavení zálohování okno](./media/backup-azure-manage-vms/stop-backup-blade.png)
2. Na **zastavení zálohování** okně vyberte, zda chcete uchovat nebo odstranit data zálohování. Pole informace poskytuje podrobnosti o svého výběru.

    ![Zastavení ochrany](./media/backup-azure-manage-vms/retain-or-delete-option.png)
3. Pokud jste vybrali možnost zachování zálohovaná data, přejděte ke kroku 4. Pokud jste se rozhodli odstranit záložní data, potvrďte, že chcete zastavit úlohy zálohování a odstraní body obnovení – zadejte název položky.

    ![Zastavit ověření](./media/backup-azure-manage-vms/item-verification-box.png)

    Pokud nevíte, název položky, pozastavte ukazatel myši nad vykřičník zobrazíte název. Název položky je taky v části **zastavení zálohování** v horní části okna.
4. Volitelně můžete zadat **důvod** nebo **komentář**.
5. Úloha zálohování pro aktuální položku, klikněte na tlačítko ![tlačítko zastavení zálohování](./media/backup-azure-manage-vms/stop-backup-button-blue.png)

    Oznámení umožňuje vědět, že byly zastaveny úlohy zálohování.

    ![Potvrďte zastavení ochrany](./media/backup-azure-manage-vms/stop-message.png)

## <a name="resume-protection-of-a-virtual-machine"></a>Pokračovat v ochraně virtuálního počítače
Pokud **zachovat Data zálohování** možnost jste vybrali při byla ukončena ochrana pro virtuální počítač, pak je možné, obnovte ochranu. Pokud **odstranit Data zálohování** jste vybrali možnost a pak ochranu pro virtuální počítač nelze obnovit.

Chcete-li obnovit ochranu pro virtuální počítač

1. Na [položky panelu trezoru](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klikněte na tlačítko **obnovit zálohu**.

    ![Pokračovat v ochraně](./media/backup-azure-manage-vms/resume-backup-button.png)

    Otevře se okno zásady zálohování.

   > [!NOTE]
   > Při znovu ochranu virtuálního počítače, můžete zvolit jinou zásadu než zásada, ke které byl virtuální počítač původně chráněný.
   >
   >
2. Postupujte podle kroků v [spravovat zásady zálohování](backup-azure-manage-vms.md#manage-backup-policies) přiřazení zásad pro virtuální počítač.

    Jakmile zásady zálohování se použije k virtuálnímu počítači, zobrazí se následující zpráva.

    ![Úspěšně chráněných virtuálních počítačů](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Odstranění zálohovaných dat
Můžete odstranit záložní data související s virtuálním počítačem během **zastavení zálohování** úlohy, nebo kdykoli po zálohování dokončení úlohy. Před odstraněním bodů obnovení dokonce může být užitečné několik dnů nebo týdnů počkat. Na rozdíl od obnovování bodů obnovení nemůžete při odstraňování zálohovaných dat zvolit konkrétní body obnovení, které se mají odstranit. Pokud se rozhodnete odstranit zálohovaná data, odstraníte všechny body obnovení přidružené k příslušné položce.

Následující postup předpokládá, úlohy zálohování pro virtuální počítač byla zastavena nebo zakázána. Jakmile úloha zálohování je zakázána, **obnovit zálohu** a **odstranění zálohování** jsou k dispozici v řídícím panelu trezoru položku Možnosti.

![Opětovné spuštění a odstranění tlačítka](./media/backup-azure-manage-vms/resume-delete-buttons.png)

Odstranit záložní data na virtuálním počítači s *zálohy zakázané*:

1. Na [položky panelu trezoru](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klikněte na tlačítko **zálohování odstranit**.

    ![Typ virtuálního počítače](./media/backup-azure-manage-vms/delete-backup-buttom.png)

    **Odstranit Data zálohování** otevře se okno.

    ![Typ virtuálního počítače](./media/backup-azure-manage-vms/delete-backup-blade.png)
2. Zadejte název položky potvrďte, že chcete odstranit body obnovení.

    ![Zastavit ověření](./media/backup-azure-manage-vms/item-verification-box.png)

    Pokud nevíte, název položky, pozastavte ukazatel myši nad vykřičník zobrazíte název. Název položky je taky v části **odstranit Data zálohování** v horní části okna.
3. Volitelně můžete zadat **důvod** nebo **komentář**.
4. Chcete-li odstranit záložní data pro aktuální položku, klikněte na tlačítko ![tlačítko zastavení zálohování](./media/backup-azure-manage-vms/delete-button.png)

    Oznámení umožňuje vědět, že byl odstraněn zálohovaná data.

## <a name="next-steps"></a>Další postup
Informace o opětovné vytvoření virtuálního počítače z bodu obnovení, podívejte se na [obnovení virtuálních počítačů Azure](backup-azure-arm-restore-vms.md). Pokud potřebujete informace o ochraně virtuálních počítačů, přečtěte si [první pohled: zálohování virtuálních počítačů do trezoru služeb zotavení](backup-azure-vms-first-look-arm.md). Informace o sledování událostí najdete v tématu [monitorování výstrahy pro virtuální počítač Azure zálohy](backup-azure-monitor-vms.md).
