---
title: Správa záloh virtuálních počítačů nasazených Resource Managerem
description: Informace o správě a sledování záloh virtuálních počítačů nasazených Resource Managerem
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 11/28/2016
ms.author: trinadhk
ms.openlocfilehash: 0e599c95f40e62a4bb01a7ab698fe0ce1f738e49
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53635761"
---
# <a name="manage-azure-virtual-machine-backups"></a>Správa záloh virtuálních počítačů Azure

Tento článek obsahuje pokyny týkající se správy záloh virtuálních počítačů a informace jsou vysvětlené v výstrahy zálohování v řídicím panelu portálu k dispozici. Pokyny v tomto článku se vztahuje na virtuální počítače pomocí trezory služby Recovery Services. Tento článek nepopisuje vytváření virtuálních počítačů ani nemá vysvětluje postup k ochraně virtuálních počítačů. Úvod do ochrany virtuálních počítačů nasazených Azure Resource Manageru v Azure s trezorem služby Recovery Services, najdete v části [stručně a přehledně: Zálohování virtuálních počítačů do trezoru služby Recovery Services](backup-azure-vms-first-look-arm.md).

## <a name="manage-vaults-and-protected-virtual-machines"></a>Správa trezorů a chráněné virtuální počítače
Řídicím panelu trezoru služby Recovery Services na webu Azure Portal, poskytuje přístup k informacím o trezoru, včetně:

* nejnovější snímek zálohy, což je také nejnovější bod obnovení
* zásady zálohování
* Celková velikost všech snímků zálohy
* počet virtuálních počítačů, které jsou chráněné v trezoru

Mnoho úlohy správy počítačů s zálohování virtuálních počítačů začínají znakem otevírání v řídicím panelu trezoru. Ale protože trezory můžete použít k ochraně více položek (nebo více virtuálních počítačů), chcete-li zobrazit podrobnosti o konkrétního virtuálního počítače, otevřete položku řídicím panelu trezoru. Následující postup ukazuje, jak otevřít *řídicího panelu trezoru* a pokračujte *řídicího panelu trezoru položky*. U obou postupů, které upozorňují na tom, jak přidat trezor a trezor položky na řídicí panel Azure s použitím připnout na řídicí panel příkaz existují "tipy". Připnout na řídicí panel je způsob, jak vytvořit místní úložiště nebo položky. Běžné příkazy můžete spustit také pomocí zástupce.

> [!TIP]
> Pokud máte více řídicích panelů, otevřou se snímek řídicího panelu Azure vpřed a zpět pomocí tmavě modrou posuvníku v dolní části okna.
>
>

![Úplné zobrazení s posuvníkem](./media/backup-azure-manage-vms/bottom-slider.png)

### <a name="open-a-recovery-services-vault-in-the-dashboard"></a>Otevřete trezor služby Recovery Services na řídicím panelu:
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V nabídce centra klikněte na **Procházet** a v seznamu prostředků zadejte **Recovery Services**. Seznam se průběžně filtruje podle zadávaného textu. Klikněte na **Trezor Recovery Services**.

    ![Vytvoření trezoru Služeb zotavení – krok 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

    Zobrazí se seznam trezorů Služeb zotavení.

    ![Trezory služby Recovery Services seznam ](./media/backup-azure-manage-vms/list-o-vaults.png)

   > [!TIP]
   > Pokud připnete trezor na řídicí panel Azure, tento trezor je okamžitě dostupné po otevření webu Azure portal. Pro Připnutí trezor na řídicí panel, v seznamu trezoru, klikněte pravým tlačítkem na trezor a vyberte **připnout na řídicí panel**.
   >
   >
3. V seznamu trezorů vyberte trezor otevřete jeho řídicí panel. Po výběru trezoru řídicím panelu trezoru a **nastavení** okno otevřené. Na následujícím obrázku **Contoso-vault** řídicí panel je zvýrazněn.

    ![Otevření řídicího panelu trezoru a okna nastavení](./media/backup-azure-manage-vms/full-view-rs-vault.png)

### <a name="open-a-vault-item-dashboard"></a>Otevření řídicího panelu trezoru položky
V předchozím postupu jste otevřeli řídicím panelu trezoru. Otevření řídicího panelu trezoru položky:

1. Na řídicím panelu trezoru na **zálohování položek** dlaždici, klikněte na tlačítko **Azure Virtual Machines**.

    ![Dlaždice otevřít zálohované položky](./media/backup-azure-manage-vms/contoso-vault-1606.png)

    **Zálohované položky** okno uvádí poslední úloha zálohování pro každou položku. V tomto příkladu je jeden chráněný virtuální počítač, demovm markgal, v tomto trezoru.  

    ![Zálohované položky dlaždice](./media/backup-azure-manage-vms/backup-items-blade.png)

   > [!TIP]
   > Pro usnadnění přístupu můžete připnout položku trezoru na řídicí panel Azure. Připnout položku trezoru v seznamu položek trezoru, klikněte pravým tlačítkem na položku a vyberte **připnout na řídicí panel**.
   >
   >
2. V **zálohování položek** okna, klikněte na položku a otevřete položku řídicím panelu trezoru.

    ![Zálohované položky dlaždice](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

    Položka řídicím panelu trezoru a jeho **nastavení** okno otevřené.

    ![Řídicí panel zálohované položky se okno nastavení](./media/backup-azure-manage-vms/item-dashboard-settings.png)

    Z řídicího panelu trezoru položky lze provádět mnoho klíčových úloh, jako například:

   * změnit zásady nebo vytvořit nové zásady zálohování
   * Zobrazit body obnovení a zobrazit jejich stav konzistence
   * zálohu virtuálního počítače
   * zastavení ochrany virtuálních počítačů
   * obnovení ochrany virtuálního počítače
   * odstranění zálohovaných dat (nebo bod obnovení)
   * [obnovit záložní disky](backup-azure-arm-restore-vms.md#create-new-restore-disks)

Počátečním bodem pro následující postupy, je položka řídicím panelu trezoru.

## <a name="manage-backup-policies"></a>Správa zásad zálohování
1. Na [řídicího panelu trezoru položky](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klikněte na tlačítko **všechna nastavení** otevřít **nastavení** okno.

    ![Okno zásady zálohování](./media/backup-azure-manage-vms/all-settings-button.png)
2. Na **nastavení** okna, klikněte na tlačítko **zásady zálohování** otevřete toto okno.

    V okně jsou uvedeny podrobnosti rozsah zálohování četnost a uchovávání.

    ![Okno zásady zálohování](./media/backup-azure-manage-vms/backup-policy-blade.png)
3. Z **výběr zásady zálohování** nabídky:

   * Chcete-li změnit zásady, vyberte jiné zásady a klikněte na tlačítko **Uložit**. Nové zásady se okamžitě použijí na trezor.
   * Chcete-li vytvořit zásadu, vyberte **vytvořit nový**.

     ![Záloha virtuálního počítače](./media/backup-azure-manage-vms/backup-policy-create-new.png)

     Pokyny k vytvoření zásady zálohování, naleznete v tématu [definování zásad zálohování](backup-azure-manage-vms.md#defining-a-backup-policy).

[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

> [!NOTE]
> Při správě zásad zálohování, nezapomeňte postupovat podle [osvědčené postupy](backup-azure-vms-introduction.md#best-practices) pro zajištění optimálního výkonu zálohování
>
>

## <a name="on-demand-backup-of-a-virtual-machine"></a>zálohu virtuálního počítače
Podle potřeby můžete využít zálohování virtuálního počítače, jakmile je nakonfigurován pro ochranu. Pokud čeká na vyřízení prvotní zálohování, zálohování na vyžádání vytvoří úplná kopie virtuálního počítače v trezoru služby Recovery Services. Po dokončení prvotní zálohy zálohu na vyžádání odešle pouze změny z předchozího snímku do trezoru služby Recovery Services. To znamená jsou následné zálohy vždy přírůstkové.

> [!NOTE]
> Uchování hodnota zadaná pro denního bodu zálohy v zásadě je rozsah uchování pro zálohu na vyžádání. Pokud je vybrána žádná denního bodu zálohy, použije se týdenního bodu zálohy.
>
>

K aktivaci virtuálního počítače zálohu na vyžádání:

* Na [řídicího panelu trezoru položky](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klikněte na tlačítko **zálohovat nyní**.

    ![Zálohování teď tlačítko.](./media/backup-azure-manage-vms/backup-now-button.png)

    Na portálu zajišťuje, že chcete spustit úlohu zálohování na vyžádání. Klikněte na tlačítko **Ano** spustit úlohu zálohování.

    ![Zálohování teď tlačítko.](./media/backup-azure-manage-vms/backup-now-check.png)

    Úloha zálohování vytvoří bod obnovení. Rozsah uchování bodu obnovení je stejný jako rozsah uchování uveden v zásadách, které jsou spojené s virtuálním počítačem. Chcete-li sledovat průběh úlohy v řídicím panelu trezoru klikněte na tlačítko **úlohy zálohování** dlaždici.  

## <a name="stop-protecting-virtual-machines"></a>zastavení ochrany virtuálních počítačů
Pokud se rozhodnete ukončit ochranu virtuálního počítače, zobrazí se výzva, jestli chcete zachovat body obnovení. Existují dva způsoby, jak zastavit ochranu virtuálních počítačů:

* Zastavit všechny budoucí úlohy zálohování a odstranit všechny body obnovení, nebo
* Zastavit všechny budoucí úlohy zálohování, ale ponechat body obnovení.

Existuje náklady spojené s ponecháním bodů obnovení v úložišti. Výhodou ponechání bodů obnovení však je, že později, můžete obnovit virtuální počítač, v případě potřeby. Informace o nákladech na ponechání bodů obnovení, najdete v článku [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/backup/). Pokud se rozhodnete odstranit všechny body obnovení, nelze obnovit virtuální počítač.

Zastavení ochrany pro virtuální počítač:

1. Na [řídicího panelu trezoru položky](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klikněte na tlačítko **Zastavit zálohování**.

    ![Zastavit zálohování tlačítko](./media/backup-azure-manage-vms/stop-backup-button.png)

    Otevře se okno Zastavit zálohování.

    ![Zastavit okno zálohování](./media/backup-azure-manage-vms/stop-backup-blade.png)
2. Na **Zastavit zálohování** okno, vyberte, jestli chcete zachovat nebo odstranit zálohovaná data. Informační okno obsahuje podrobné informace o podle vašeho výběru.

    ![Zastavení ochrany](./media/backup-azure-manage-vms/retain-or-delete-option.png)
3. Pokud jste se rozhodli zachovat zálohovaná data, přejděte ke kroku 4. Pokud jste se rozhodli odstranit data zálohy, potvrďte, že chcete zastavit úlohy zálohování a odstraňte body obnovení – zadejte název položky.

    ![Zastavit ověření](./media/backup-azure-manage-vms/item-verification-box.png)

    Pokud si nejste jisti názvem položky, najeďte myší vykřičník, abyste zobrazili jméno. Název položky je také v rámci **Zastavit zálohování** v horní části okna.
4. Volitelně můžete zadat **důvod** nebo **komentář**.
5. Pokud chcete zastavit úlohu zálohování pro aktuální položku, klikněte na tlačítko ![tlačítko Zastavit zálohování](./media/backup-azure-manage-vms/stop-backup-button-blue.png)

    Oznámení vám umožňuje vědět, že byly zastaveny úlohy zálohování.

    ![Potvrdit zastavení ochrany](./media/backup-azure-manage-vms/stop-message.png)

## <a name="resume-protection-of-a-virtual-machine"></a>obnovení ochrany virtuálního počítače
Pokud **zachovat zálohovaná Data** jste zvolili možnost při ochranu pro virtuální počítač se zastavila, pak je možné ochranu obnovit. Pokud **odstranit zálohovaná Data** jste zvolili možnost a pak ochranu pro virtuální počítač nelze obnovit.

Pokud chcete obnovit ochranu pro virtuální počítač

1. Na [řídicího panelu trezoru položky](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klikněte na tlačítko **obnovit zálohu**.

    ![Pokračovat v ochraně](./media/backup-azure-manage-vms/resume-backup-button.png)

    Otevře se okno zásady zálohování.

   > [!NOTE]
   > Po znovunastavení ochrany virtuálního počítače, můžete jiné zásady než zásady, pomocí kterého byl virtuální počítač původně chráněný.
   >
   >
2. Postupujte podle kroků v [Správa zásad zálohování](backup-azure-manage-vms.md#manage-backup-policies) přiřazení zásad pro virtuální počítač.

    Jakmile zásadu zálohování, která se použije k virtuálnímu počítači, zobrazí se následující zpráva.

    ![Úspěšně chráněného virtuálního počítače](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Odstranění zálohovaných dat
Můžete odstranit zálohovaná data spojená s virtuálním počítačem během **Zastavit zálohování** úlohy, nebo kdykoli po zálohování dokončení úlohy. Před odstraněním bodů obnovení dokonce může být užitečné několik dnů nebo týdnů počkat. Na rozdíl od obnovování bodů obnovení nemůžete při odstraňování zálohovaných dat zvolit konkrétní body obnovení, které se mají odstranit. Pokud se rozhodnete odstranit zálohovaná data, odstraníte všechny body obnovení přidružené k příslušné položce.

Následující postup předpokládá úloha zálohování pro virtuální počítač byla zastavena nebo zakázána. Jakmile se úloha zálohování je zakázána, **obnovit zálohu** a **odstranit zálohy** možnosti jsou k dispozici v řídicím panelu trezoru položky.

![Tlačítka pro pokračování a odstranění](./media/backup-azure-manage-vms/resume-delete-buttons.png)

Chcete-li odstranit zálohovaná data na virtuálním počítači s *zálohování zakázáno*:

1. Na [řídicího panelu trezoru položky](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klikněte na tlačítko **odstranit zálohy**.

    ![Typ virtuálního počítače](./media/backup-azure-manage-vms/delete-backup-buttom.png)

    **Odstranit zálohovaná Data** se otevře okno.

    ![Typ virtuálního počítače](./media/backup-azure-manage-vms/delete-backup-blade.png)
2. Zadejte název položky, abychom potvrdili, že chcete odstranit body obnovení.

    ![Zastavit ověření](./media/backup-azure-manage-vms/item-verification-box.png)

    Pokud si nejste jisti názvem položky, najeďte myší vykřičník, abyste zobrazili jméno. Název položky je také v rámci **odstranit zálohovaná Data** v horní části okna.
3. Volitelně můžete zadat **důvod** nebo **komentář**.
4. Chcete-li odstranit zálohovaná data pro aktuální položku, klikněte na tlačítko ![tlačítko Zastavit zálohování](./media/backup-azure-manage-vms/delete-button.png)

    Oznámení vám umožňuje vědět, že záložní data byla odstraněna.

## <a name="next-steps"></a>Další postup
Informace o opětovné vytvoření virtuálního počítače z bodu obnovení, projděte si [obnovení virtuálních počítačů Azure](backup-azure-arm-restore-vms.md). Pokud potřebujete informace o ochraně virtuálních počítačů, přečtěte si [stručně a přehledně: Zálohování virtuálních počítačů do trezoru služby Recovery Services](backup-azure-vms-first-look-arm.md). Informace o sledování událostí, naleznete v tématu [monitorování výstrah pro zálohy virtuálních počítačů Azure](backup-azure-monitor-vms.md).
