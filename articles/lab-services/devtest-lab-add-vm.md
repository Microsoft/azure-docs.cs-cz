---
title: Přidání virtuálního počítače do testovacího prostředí v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Zjistěte, jak přidat virtuální počítač do testovacího prostředí v Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: 22060cc6dd5eb15e81a0c397a7b0255f16780d74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284249"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Přidání virtuálního počítače do testovacího prostředí v azure devtest labs
Pokud jste už [vytvořili první virtuální počítač](tutorial-create-custom-lab.md#add-a-vm-to-the-lab), pravděpodobně jste tak učinili z předinstalované [image marketplace](devtest-lab-configure-marketplace-images.md). Teď, pokud chcete přidat následné virtuální počítače do testovacího prostředí, můžete také zvolit *základnu,* která je buď [vlastní obrázek](devtest-lab-create-template.md) nebo [vzorec](devtest-lab-manage-formulas.md). Tento kurz vás provede pomocí portálu Azure k přidání virtuálního počítače do testovacího prostředí v DevTest Labs.

Tento článek také ukazuje, jak spravovat artefakty pro virtuální počítač ve vaší laboratoři.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Postup přidání virtuálního počítače do testovacího prostředí v azure devtest labs
1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **Všechny služby**a v části **DEVOPS** vyberte **DevTest Labs.** Pokud vyberete * (hvězdička) vedle **DevTest Labs** v sekci **DEVOPS.** Tato akce přidá **DevTest Labs** do levé navigační nabídky, takže k ní budete mít příště snadný přístup. Potom můžete vybrat **DevTest Labs** v levé navigační nabídce.

    ![Všechny služby - vyberte DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. Ze seznamu testovacích prostředí vyberte testovací prostředí, ve kterém chcete vytvořit virtuální hod.
2. Na stránce **Přehled** testovacího prostředí vyberte **+ Přidat**.

    ![Tlačítko Přidat virtuální ho disponiál](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. Na **stránce Zvolit základní** vyberte image marketplace pro virtuální počítač.
1. Na kartě **Základní nastavení** na stránce **Virtuální počítač** proveďte následující akce:
    1. Do textového pole Název **virtuálního počítače** zadejte název virtuálního počítače. Textové pole je pro vás předvyplněno jedinečným automaticky generovaným názvem. Jméno odpovídá uživatelskému jménu ve vaší e-mailové adrese následované jedinečným 3místným číslem. Tato funkce vám ušetří čas přemýšlet o názvu počítače a zadejte jej při každém vytvoření počítače. Toto automaticky vyplněné pole můžete přepsat názvem, který si vyberete, pokud chcete. Pokud chcete přepsat název automatického vyplnění virtuálního počítače, zadejte název do textového pole **Název virtuálního počítače.**
    2. Zadejte **uživatelské jméno,** kterému jsou udělena oprávnění správce ve virtuálním počítači. **Uživatelské jméno** počítače je předvyplněno jedinečným automaticky generovaným názvem. Jméno odpovídá uživatelskému jménu ve vaší e-mailové adrese. Tato funkce vám ušetří čas rozhodnout o uživatelské jméno při každém vytvoření nového stroje. Opět můžete přepsat toto automaticky vyplněné pole uživatelským jménem dle vašeho výběru, pokud chcete. Chcete-li přepsat hodnotu automatického vyplnění uživatelského jména, zadejte hodnotu do textového pole **Uživatelské jméno.** Tomuto uživateli jsou udělena oprávnění **správce** ve virtuálním počítači.
    3. Pokud vytváříte první virtuální virtuální počítače v testovacím prostředí, zadejte **heslo** pro uživatele. Chcete-li toto heslo uložit jako výchozí heslo do trezoru klíčů Azure přidruženého k testovacímu prostředí, vyberte **uložit jako výchozí heslo**. Výchozí heslo je uloženo v trezoru klíčů s **názvem: VmPassword**. Při pokusu o vytvoření následných virtuálních počítače v testovacím prostředí je pro **heslo**automaticky **vybráno vmPassword** . Chcete-li hodnotu přepsat, zrušte zaškrtnutí **políčka Použít uložený tajný** klíč a zadejte heslo.

        ![Výběr základní image](./media/tutorial-create-custom-lab/new-virtual-machine.png)

        Můžete také uložit tajné klíče v trezoru klíčů první a potom použít při vytváření virtuálního virtuálního provozu v testovacím prostředí. Další informace naleznete [v tématu Ukládání tajných klíčů v trezoru klíčů](devtest-lab-store-secrets-in-key-vault.md). Chcete-li použít heslo uložené v trezoru klíčů, vyberte **použít uložený tajný klíč**a zadejte hodnotu klíče, která odpovídá vašemu tajnému klíči (heslu).
    4. V části **Další možnosti** vyberte **Změnit velikost**. Vyberte jednu z předdefinovaných položek, které určují jádra procesoru, velikost paměti RAM a velikost pevného disku virtuálního počítače, který chcete vytvořit.
    5. Vyberte **Přidat nebo odebrat artefakty**. Vyberte a nakonfigurujte artefakty, které chcete přidat do základní bitové kopie.
    **Poznámka:** Pokud jste s DevTest Labs teče nebo konfigurací artefaktů, přečtěte si část [Přidat existující artefakt do oddílu virtuálního](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) zařízení a po dokončení se sem vraťte.
2. Přepněte na kartu **Upřesnit nastavení** v horní části a proveďte následující akce:
    1. Pokud chcete změnit virtuální síť, ve které se virtuální počítač nachází, vyberte **Změnit virtuální síť**.
    2. Chcete-li změnit podsíť, vyberte **Změnit podsíť**.
    3. Určete, jestli je IP adresa virtuálního počítačů **veřejná, soukromá nebo sdílená**.
    4. Chcete-li virtuální ho střebané automaticky odstranit, zadejte **datum a čas vypršení platnosti**.
    5. Chcete-li, aby byl virtuální počítač nárokovatelný uživatelem testovacího prostředí, vyberte **Ano,** **chcete-li, aby byl tento počítač nárokovatelný.**
    6. Zadejte počet **instancí virtuálního počítače,** které chcete zpřístupnit uživatelům testovacího prostředí.

        ![Výběr základní image](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
1. Vyberte **Vytvořit,** chcete-li přidat zadaný virtuální virtuální ms do testovacího prostředí.

   Na stránce testovacího prostředí se zobrazí stav vytvoření virtuálního soudu – nejprve jako **vytvoření**, pak jako **Spuštěno** po spuštění virtuálního soudu.

    ![Stav vytváření virtuálního počítače](./media/tutorial-create-custom-lab/vm-creation-status.png)

## <a name="add-an-existing-artifact-to-a-vm"></a>Přidání existujícího artefaktu do virtuálního mísy
Při vytváření virtuálního virtuálního ms můžete přidat existující artefakty. Každá testovací prostředí obsahuje artefakty z veřejného úložiště artefaktů DevTest Labs a artefakty, které jste vytvořili a přidali do vlastního úložiště artefaktů.

* *Artefakty* Azure DevTest Labs umožňují určit *akce,* které se provádějí při zřízení virtuálního počítače, jako je například spuštění skriptů prostředí Windows PowerShell, spuštění příkazů Bash a instalace softwaru.
* *Parametry artefaktu* umožňují přizpůsobit artefakt pro konkrétní scénář

Chcete-li zjistit, jak vytvářet artefakty, naleznete v článku [Naučte se vytvářet vlastní artefakty pro použití s DevTest Labs](devtest-lab-artifact-author.md).

1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**
1. Ze seznamu testovacích prostředí vyberte testovací prostředí obsahující virtuální hod, se kterým chcete pracovat.
1. Vyberte **Moje virtuální počítače**.
1. Vyberte požadovaný virtuální virtuální ms.
1. Vyberte **Spravovat artefakty**.
1. Vyberte **Použít artefakty**.
1. V podokně **Použít artefakty** vyberte artefakt, který chcete přidat do virtuálního počítače.
1. V podokně **Přidat artefakt** zadejte požadované hodnoty parametrů a všechny volitelné parametry, které potřebujete.
1. Vyberte **Přidat,** chcete-li přidat artefakt a vrátit se do podokna **Použít artefakty.**
1. Pokračujte v přidávání artefaktů podle potřeby pro váš virtuální počítač.
1. Po přidání artefaktů můžete změnit [pořadí, ve kterém jsou artefakty spuštěny](#change-the-order-in-which-artifacts-are-run). Můžete se také vrátit k [zobrazení nebo úpravě artefaktu](#view-or-modify-an-artifact).
1. Až budete přidávat artefakty, vyberte **Použít**

## <a name="change-the-order-in-which-artifacts-are-run"></a>Změna pořadí, ve kterém jsou artefakty spuštěny
Ve výchozím nastavení jsou akce artefaktů provedeny v pořadí, ve kterém jsou přidány do virtuálního soudu.
Následující kroky ilustrují, jak změnit pořadí, ve kterém jsou spuštěny artefakty.

1. V horní části podokna **Použít artefakty** vyberte odkaz označující počet artefaktů, které byly přidány do virtuálního účtu.

    ![Počet artefaktů přidaných do virtuálního účtu](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. V podokně **Vybrané artefakty** přetáhněte artefakty do požadovaného pořadí. **Poznámka:** Pokud máte potíže s přetažením artefaktu, ujistěte se, že přetahujete z levé strany artefaktu.
1. Až to budete mít, vyberte **OK**.

## <a name="view-or-modify-an-artifact"></a>Zobrazení nebo úprava artefaktu
Následující kroky ilustrují, jak zobrazit nebo upravit parametry artefaktu:

1. V horní části podokna **Použít artefakty** vyberte odkaz označující počet artefaktů, které byly přidány do virtuálního účtu.

    ![Počet artefaktů přidaných do virtuálního účtu](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. V podokně **Vybrané artefakty** vyberte artefakt, který chcete zobrazit nebo upravit.
1. V podokně **Přidat artefakt** proveďte potřebné změny a výběrem možnosti **OK** zavřete podokno **Přidat artefakt.**
1. Výběrem **možnosti OK** zavřete podokno **Vybrané artefakty.**

## <a name="save-azure-resource-manager-template"></a>Uložení šablony Azure Resource Manager
Šablona Azure Resource Manager poskytuje deklarativní způsob, jak definovat opakovatelné nasazení.
Následující kroky vysvětlují, jak uložit šablonu Azure Resource Manager pro vytvářený virtuální počítač.
Po uložení můžete použít šablonu Azure Resource Manager k [nasazení nových virtuálních počítačů s Azure PowerShellem](../azure-resource-manager/templates/overview.md).

1. V podokně **Virtuální počítač** vyberte **Zobrazit šablonu Správce prostředků Azure**.
2. V podokně **předlohy Zobrazit správce prostředků Azure** vyberte text šablony.
3. Zkopírujte vybraný text do schránky.
4. Výběrem **možnosti OK** zavřete **podokno Zobrazit podokno šablony Správce prostředků Azure**.
5. Otevřete textový editor.
6. Vložte text šablony ze schránky.
7. Uložte soubor pro pozdější použití.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další kroky
* Po vytvoření virtuálního počítače se můžete k virtuálnímu počítači připojit výběrem **možnosti Připojit** v podokně virtuálního počítače.
* Přečtěte si, jak [vytvořit vlastní artefakty pro váš virtuální počítač DevTest Labs](devtest-lab-artifact-author.md).
* Prozkoumejte [galerii šablon rychlého startu nástroje DevTest Labs Azure .](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
