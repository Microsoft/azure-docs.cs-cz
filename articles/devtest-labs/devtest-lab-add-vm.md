---
title: Přidání virtuálního počítače do testovacího prostředí v Azure DevTest Labs | Microsoft Docs
description: Naučte se, jak pomocí Azure Portal přidat virtuální počítač do testovacího prostředí v Azure DevTest Labs. Můžete zvolit základ, který je buď vlastní obrázek, nebo vzorec.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 07efcf93b963009494021b90e6c730c4615301ef
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91297619"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Přidání virtuálního počítače do testovacího prostředí v Azure DevTest Labs
Pokud jste už [svůj první virtuální počítač vytvořili](tutorial-create-custom-lab.md#add-a-vm-to-the-lab), pravděpodobně jste to učinili z předem načtené [image na webu Marketplace](devtest-lab-configure-marketplace-images.md). Pokud teď chcete do testovacího prostředí přidat další virtuální počítače, můžete také zvolit *základ* , který je buď [vlastní obrázek](devtest-lab-create-template.md) , nebo [vzorec](devtest-lab-manage-formulas.md). Tento kurz vás provede použitím Azure Portal k přidání virtuálního počítače do testovacího prostředí v DevTest Labs.

Tento článek také ukazuje, jak spravovat artefakty pro virtuální počítač v testovacím prostředí.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Postup přidání virtuálního počítače do testovacího prostředí v Azure DevTest Labs
1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby**a potom v části **DEVOPS** vyberte **DevTest Labs** . Pokud vyberete * (hvězdička) vedle **DevTest Labs** v části **DEVOPS** . Tato akce přidá **DevTest Labs** do levé navigační nabídky, abyste k ní mohli snadno získat přístup později. Pak můžete v levé navigační nabídce vybrat **DevTest Labs** .

    ![Všechny služby – výběr DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. V seznamu cvičení vyberte testovací prostředí, ve kterém chcete virtuální počítač vytvořit.
2. Na stránce **Přehled** testovacího prostředí vyberte **+ Přidat**.

    ![Tlačítko pro přidání virtuálního počítače](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. Na stránce **Zvolte základní** stránku vyberte Image Marketplace pro virtuální počítač.
1. Na kartě **základní nastavení** na stránce **virtuální počítač** proveďte následující akce:
    1. Do textového pole **název virtuálního počítače** zadejte název virtuálního počítače. Textové pole je předem vyplněno jedinečným automaticky generovaným názvem. Název odpovídá uživatelskému jménu v e-mailové adrese a následuje jedinečné číslo 3 číslice. Tato funkce šetří čas, po který si myslíte název počítače a zadejte ho pokaždé, když vytvoříte počítač. Pokud chcete, můžete toto automaticky vyplněné pole přepsat názvem dle vašeho výběru. Pokud chcete pro virtuální počítač přepsat automaticky vyplněný název, zadejte název do textového pole **název virtuálního počítače** .
    2. Zadejte **uživatelské jméno** , kterému je uděleno oprávnění správce na virtuálním počítači. **Uživatelské jméno** pro tento počítač je předem vyplněno jedinečným automaticky generovaným názvem. Název odpovídá uživatelskému jménu v rámci své e-mailové adresy. Tato funkce šetří čas pro rozhodování o uživatelském jménu pokaždé, když vytvoříte nový počítač. V případě potřeby můžete toto automaticky vyplněné pole přepsat vlastním uživatelským jménem dle vašeho výběru. Pokud chcete pro uživatelské jméno přepsat automaticky vyplněnou hodnotu, zadejte do textového pole **uživatelské jméno** hodnotu. Tento uživatel má na virtuálním počítači udělená oprávnění **správce** .
    3. Pokud vytváříte první virtuální počítač v testovacím prostředí, zadejte **heslo** pro tohoto uživatele. Pokud chcete toto heslo uložit jako výchozí heslo do trezoru klíčů Azure přidruženého k testovacímu prostředí, vyberte **Uložit jako výchozí heslo**. Výchozí heslo je uloženo v trezoru klíčů s názvem: **VmPassword**. Při pokusu o vytvoření dalších virtuálních počítačů v testovacím prostředí se pro **heslo**automaticky vybere **VmPassword** . Pokud chcete hodnotu přepsat, zrušte zaškrtnutí políčka **použít uložený tajný kód** a zadejte heslo.

        ![Snímek obrazovky zobrazující stránku vytvořit prostředek testovacího prostředí s vybraným základním nastavením a zadané hodnoty "uživatelské nastavení".](./media/tutorial-create-custom-lab/new-virtual-machine.png)

        V trezoru klíčů můžete nejdřív také ukládat tajné klíče a pak je použít při vytváření virtuálního počítače v testovacím prostředí. Další informace najdete v tématu [uložení tajných klíčů v trezoru klíčů](devtest-lab-store-secrets-in-key-vault.md). Pokud chcete použít heslo uložené v trezoru klíčů, vyberte **použít uložený tajný kód**a zadejte hodnotu klíče, která odpovídá vašemu tajnému klíči (heslo).
    4. V části **Další možnosti** vyberte **změnit velikost**. Vyberte jednu z předdefinovaných položek, které určují jádra procesoru, velikost paměti RAM a velikost pevného disku virtuálního počítače, který se má vytvořit.
    5. Vyberte **Přidat nebo odebrat artefakty**. Vyberte a nakonfigurujte artefakty, které chcete přidat do základní image.
    **Poznámka:** Pokud s DevTest Labs začínáte a konfigurujete artefakty, přečtěte si část [Přidání existujícího artefaktu do virtuálního počítače](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) a po dokončení se vraťte sem.
2. V horní části přepněte na kartu **Upřesnit nastavení** a proveďte následující akce:
    1. Pokud chcete změnit virtuální síť, ve které je virtuální počítač, vyberte **změnit virtuální**síť.
    2. Chcete-li změnit podsíť, vyberte možnost **změnit podsíť**.
    3. Určete, jestli je IP adresa virtuálního počítače **veřejná, soukromá nebo sdílená**.
    4. Pokud chcete virtuální počítač automaticky odstranit, zadejte **Datum a čas vypršení platnosti**.
    5. Pokud chcete virtuální počítač vyžádat od uživatele testovacího prostředí, vyberte **Ano** , **aby se tato možnost vymáhat z tohoto počítače** mohla vydávat.
    6. Zadejte počet **instancí virtuálního počítače** , které mají být k dispozici pro uživatele testovacího prostředí.

        ![Výběr základní image](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
1. Výběrem **vytvořit** přidejte zadaný virtuální počítač do testovacího prostředí.

   Stránka testovací prostředí zobrazuje stav vytvoření virtuálního počítače, který se nejdřív **vytvoří**, a pak se **spustí** po spuštění virtuálního počítače.

    ![Stav vytváření virtuálního počítače](./media/tutorial-create-custom-lab/vm-creation-status.png)

## <a name="add-an-existing-artifact-to-a-vm"></a>Přidat existující artefakt do virtuálního počítače
Při vytváření virtuálního počítače můžete přidat existující artefakty. Každé testovací prostředí zahrnuje artefakty z úložiště artefaktů veřejných DevTest Labs i artefakty, které jste vytvořili a přidali do vlastního úložiště artefaktů.

* Azure DevTest Labs *artefakty* umožňují určit *Akce* , které se provedou při zřizování virtuálního počítače, například spouštění skriptů prostředí Windows PowerShell, spouštění příkazů bash a instalace softwaru.
* *Parametry* artefaktu umožňují přizpůsobit artefakt pro konkrétní scénář.

Chcete-li zjistit, jak vytvořit artefakty, přečtěte si článek, [jak vytvářet vlastní artefakty pro použití s DevTest Labs](devtest-lab-artifact-author.md).

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby**a v seznamu vyberte **DevTest Labs** .
1. V seznamu cvičení vyberte testovací prostředí obsahující virtuální počítač, se kterým chcete pracovat.
1. Vyberte **moje virtuální počítače**.
1. Vyberte požadovaný virtuální počítač.
1. Vyberte **Spravovat artefakty**.
1. Vyberte **použít artefakty**.
1. V podokně **použít artefakty** vyberte artefakt, který chcete přidat do virtuálního počítače.
1. V podokně **Přidat artefakt** zadejte požadované hodnoty parametrů a všechny volitelné parametry, které potřebujete.
1. Vyberte **Přidat** a přidejte artefakt a vraťte se do podokna **použít artefakty** .
1. Pokračujte v přidávání artefaktů podle potřeby pro váš virtuální počítač.
1. Po přidání artefaktů můžete [změnit pořadí, ve kterém jsou artefakty spuštěny](#change-the-order-in-which-artifacts-are-run). Můžete také přejít zpátky a [Zobrazit nebo upravit artefakt](#view-or-modify-an-artifact).
1. Až skončíte s přidáváním artefaktů, vyberte **použít** .

## <a name="change-the-order-in-which-artifacts-are-run"></a>Změna pořadí, ve kterém jsou artefakty spuštěny
Ve výchozím nastavení jsou akce artefaktů spouštěny v pořadí, ve kterém jsou přidány do virtuálního počítače.
Následující postup ukazuje, jak změnit pořadí, ve kterém jsou artefakty spuštěny.

1. V horní části podokna **použít artefakty** vyberte odkaz označující počet artefaktů přidaných do virtuálního počítače.

    ![Počet artefaktů přidaných do virtuálního počítače](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. V podokně **vybrané artefakty** přetáhněte artefakty do požadovaného pořadí. **Poznámka:** Pokud máte potíže při přetahování artefaktem, ujistěte se, že přetahujete z levé strany artefaktu.
1. Až to budete mít, vyberte **OK**.

## <a name="view-or-modify-an-artifact"></a>Zobrazení nebo úprava artefaktu
Následující postup ukazuje, jak zobrazit nebo upravit parametry artefaktu:

1. V horní části podokna **použít artefakty** vyberte odkaz označující počet artefaktů přidaných do virtuálního počítače.

    ![Počet artefaktů přidaných do virtuálního počítače](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. V podokně **vybrané artefakty** vyberte artefakt, který chcete zobrazit nebo upravit.
1. V podokně **Přidat artefakt** proveďte potřebné změny a kliknutím na **tlačítko OK** zavřete podokno **Přidat artefakt** .
1. Výběrem **OK** zavřete podokno **vybrané artefakty** .

## <a name="save-azure-resource-manager-template"></a>Uložit šablonu Azure Resource Manager
Šablona Azure Resource Manager poskytuje deklarativní způsob, jak definovat opakované nasazení.
Následující postup vysvětluje, jak uložit šablonu Azure Resource Manager pro vytvářený virtuální počítač.
Po uložení můžete použít šablonu Azure Resource Manager k [nasazení nových virtuálních počítačů s Azure PowerShell](../azure-resource-manager/templates/overview.md).

1. V podokně **virtuální počítač** vyberte **Zobrazit Azure Resource Manager šablonu**.
2. V podokně **zobrazit Azure Resource Manager šablonu** vyberte text šablony.
3. Zkopíruje vybraný text do schránky.
4. Výběrem **OK** zavřete **podokno Azure Resource Manager šablon zobrazení**.
5. Otevřete textový editor.
6. Vloží text šablony ze schránky.
7. Uložte soubor pro pozdější použití.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další kroky
* Po vytvoření virtuálního počítače se můžete připojit k virtuálnímu počítači tak, že v podokně virtuálního počítače vyberete **připojit** .
* Naučte se [vytvářet vlastní artefakty pro virtuální počítač DevTest Labs](devtest-lab-artifact-author.md).
* Prozkoumejte [galerii šablon rychlého startu Azure Resource Manager DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
