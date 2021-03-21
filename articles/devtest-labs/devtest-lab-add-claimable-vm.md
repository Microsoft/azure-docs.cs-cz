---
title: Vytváření a správa virtuálních počítačů s deklarací identity v Azure DevTest Labs | Microsoft Docs
description: Naučte se, jak pomocí Azure Portal přidat do Azure DevTest Labs nárokovat virtuální počítač s deklarací identity a zobrazit následující procesy pro nárokování nebo odmítání virtuálního počítače.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1fe949d7104fe6437087e77c403cc0d921ebd025
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88270974"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Vytváření a správa virtuálních počítačů s deklarací identity v Azure DevTest Labs
Do testovacího prostředí můžete přidat vynucené virtuální počítače podobným způsobem jako při [přidávání standardního virtuálního počítače](devtest-lab-add-vm.md) – od *základu* , který je buď [vlastní image](devtest-lab-create-template.md), [vzorec](devtest-lab-manage-formulas.md)nebo Image na [webu Marketplace](devtest-lab-configure-marketplace-images.md). Tento kurz vás provede použitím Azure Portal k přidání s nárokem na virtuální počítač do testovacího prostředí v DevTest Labs a ukazuje procesy, které uživatel sleduje, aby mohl uplatnit deklaraci virtuálního počítače a uvolnit ho.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Postup přidání vynuceného virtuálního počítače do testovacího prostředí v Azure DevTest Labs
1. Přihlaste se na [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby** a potom v části **DEVOPS** vyberte **DevTest Labs** . Pokud vyberete * (hvězdička) vedle **DevTest Labs** v části **DEVOPS** . Tato akce přidá **DevTest Labs** do levé navigační nabídky, abyste k ní mohli snadno získat přístup později. Pak můžete v levé navigační nabídce vybrat **DevTest Labs** .

    ![Všechny služby – výběr DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. V seznamu cvičení vyberte testovací prostředí, ve kterém chcete virtuální počítač vytvořit.
2. Na stránce **Přehled** testovacího prostředí vyberte **+ Přidat**.

    ![Tlačítko pro přidání virtuálního počítače](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. Na stránce **Zvolte základní** stránku vyberte Image Marketplace pro virtuální počítač.
1. Na kartě **základní nastavení** na stránce **virtuální počítač** proveďte následující akce:
    1. Do textového pole **název virtuálního počítače** zadejte název virtuálního počítače. Textové pole je předem vyplněno jedinečným automaticky generovaným názvem. Název odpovídá uživatelskému jménu v e-mailové adrese a následuje jedinečné číslo 3 číslice. Tato funkce šetří čas, po který si myslíte název počítače a zadejte ho pokaždé, když vytvoříte počítač. Pokud chcete, můžete toto automaticky vyplněné pole přepsat názvem dle vašeho výběru. Pokud chcete pro virtuální počítač přepsat automaticky vyplněný název, zadejte název do textového pole **název virtuálního počítače** .
    2. Zadejte **uživatelské jméno** , kterému je uděleno oprávnění správce na virtuálním počítači. **Uživatelské jméno** pro tento počítač je předem vyplněno jedinečným automaticky generovaným názvem. Název odpovídá uživatelskému jménu v rámci své e-mailové adresy. Tato funkce šetří čas pro rozhodování o uživatelském jménu pokaždé, když vytvoříte nový počítač. V případě potřeby můžete toto automaticky vyplněné pole přepsat vlastním uživatelským jménem dle vašeho výběru. Pokud chcete pro uživatelské jméno přepsat automaticky vyplněnou hodnotu, zadejte do textového pole **uživatelské jméno** hodnotu. Tento uživatel má na virtuálním počítači udělená oprávnění **správce** .
    3. Pokud vytváříte první virtuální počítač v testovacím prostředí, zadejte **heslo** pro tohoto uživatele. Pokud chcete toto heslo uložit jako výchozí heslo do trezoru klíčů Azure přidruženého k testovacímu prostředí, vyberte **Uložit jako výchozí heslo**. Výchozí heslo je uloženo v trezoru klíčů s názvem: **VmPassword**. Při pokusu o vytvoření dalších virtuálních počítačů v testovacím prostředí se pro **heslo** automaticky vybere **VmPassword** . Pokud chcete hodnotu přepsat, zrušte zaškrtnutí políčka **použít uložený tajný kód** a zadejte heslo.

        V trezoru klíčů můžete nejdřív také ukládat tajné klíče a pak je použít při vytváření virtuálního počítače v testovacím prostředí. Další informace najdete v tématu [uložení tajných klíčů v trezoru klíčů](devtest-lab-store-secrets-in-key-vault.md). Pokud chcete použít heslo uložené v trezoru klíčů, vyberte **použít uložený tajný kód** a zadejte hodnotu klíče, která odpovídá vašemu tajnému klíči (heslo).
    4. V části **Další možnosti** vyberte **změnit velikost**. Vyberte jednu z předdefinovaných položek, které určují jádra procesoru, velikost paměti RAM a velikost pevného disku virtuálního počítače, který se má vytvořit.
    5. Vyberte **Přidat nebo odebrat artefakty**. Vyberte a nakonfigurujte artefakty, které chcete přidat do základní image.
    **Poznámka:** Pokud s DevTest Labs začínáte a konfigurujete artefakty, přečtěte si část [Přidání existujícího artefaktu do virtuálního počítače](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) a po dokončení se vraťte sem.
2. V horní části přepněte na kartu **Upřesnit nastavení** a proveďte následující akce:
    1. Pokud chcete změnit virtuální síť, ve které je virtuální počítač, vyberte **změnit virtuální** síť.
    2. Chcete-li změnit podsíť, vyberte možnost **změnit podsíť**.
    3. Určete, jestli je IP adresa virtuálního počítače **veřejná, soukromá nebo sdílená**.
    4. Pokud chcete virtuální počítač automaticky odstranit, zadejte **Datum a čas vypršení platnosti**.
    5. Pokud chcete virtuální počítač vyžádat od uživatele testovacího prostředí, vyberte **Ano** , **aby se tato možnost vymáhat z tohoto počítače** mohla vydávat.
    6. Zadejte počet **instancí virtuálního počítače** , které mají být k dispozici pro uživatele testovacího prostředí.
3. Výběrem **vytvořit** přidejte zadaný virtuální počítač do testovacího prostředí.

   Stránka testovací prostředí zobrazuje stav vytvoření virtuálního počítače, který se nejdřív **vytvoří**, a pak se **spustí** po spuštění virtuálního počítače.

> [!NOTE]
> Pokud nasadíte virtuální počítače testovacího prostředí prostřednictvím [šablon Azure Resource Manager](devtest-lab-create-environment-from-arm.md), můžete vytvořit virtuální počítače s deklarací identity nastavením vlastnosti **allowClaim** na hodnotu true v oddílu Properties (vlastnosti).


## <a name="using-a-claimable-vm"></a>Použití vynuceného virtuálního počítače

Uživatel může uplatnit jakýkoli virtuální počítač ze seznamu "s nárokem na virtuální počítače" pomocí jednoho z následujících kroků:

* V seznamu "vynucené virtuální počítače" v dolní části podokna "Přehled" testovacího prostředí klikněte pravým tlačítkem na jeden z virtuálních počítačů v seznamu a vyberte možnost **počítač s nárokem**.

  ![Vyžádá konkrétní virtuální počítač s deklarací identity.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* V horní části podokna Přehled vyberte **deklarace identity any**. Náhodný virtuální počítač je přiřazený ze seznamu vynucených virtuálních počítačů.

  ![Vyžádejte si jakýkoli virtuální počítač s nárokem.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Jakmile uživatel uplatní virtuální počítač, DevTest Labs spustí počítač a přesune ho do seznamu "moje virtuální počítače" v testovacím prostředí. To znamená, že uživatel testovacího prostředí teď bude mít na tomto počítači oprávnění vlastníka. Čas potřebný pro tento krok se může lišit v závislosti na časech spuštění a všech dalších vlastních akcích, které se v události deklarace identity provádějí. Po jeho uplatnění už nebude počítač dostupný ve fondu s nárokem.  

## <a name="unclaim-a-vm"></a>Uvolnění virtuálního počítače

Když je uživatel dokončený pomocí deklarovaného virtuálního počítače a chce ho zpřístupnit někomu jinému, může vrátit deklarovaný virtuální počítač do seznamu deklarací, které mají nárok na virtuální počítače, pomocí jednoho z následujících kroků:

- V seznamu "moje virtuální počítače" klikněte pravým tlačítkem na jeden z virtuálních počítačů v seznamu – nebo vyberte tři tečky (...) – a zvolte **nedeklaraci**.

  ![Uvolní virtuální počítač v seznamu virtuálních počítačů.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- V seznamu Moje virtuální počítače vyberte virtuální počítač, pro který chcete otevřít podokno správy, a pak v horním řádku nabídek vyberte zrušit **deklaraci** .

  ![Oddeklarujte virtuální počítač v podokně Správa virtuálního počítače.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Když uživatel nezíská nárok na virtuální počítač, už k tomuto konkrétnímu testovacímu virtuálnímu počítači nemá oprávnění vlastníka a je dostupný pro všechny ostatní uživatele testovacího prostředí ve stavu, který se vrátil do fondu. 

### <a name="transferring-the-data-disk"></a>Přenos datového disku
Pokud má virtuální počítač s deklarací identity připojený datový disk a uživatel je netvrdí, bude tento datový disk stále s virtuálním počítačem. Když tento virtuální počítač vypíše jiný uživatel, bude tento nový uživatel přizpůsobovat datový disk i virtuálnímu počítači.

To se označuje jako "přenos datového disku". Datový disk pak bude k dispozici v seznamu pro **Moje datové disky** nového uživatele, aby je bylo možné spravovat.

![Nedeklarujte datové disky.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Další kroky
* Po vytvoření se můžete připojit k virtuálnímu počítači výběrem možnosti **připojit** v podokně Správa.
* Prozkoumejte [galerii šablon rychlého startu Azure Resource Manager DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
