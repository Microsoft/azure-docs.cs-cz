---
title: Vytváření a správa nárokovatelných virtuálních počítačů v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Zjistěte, jak přidat nárokovatelný virtuální počítač do testovacího prostředí v Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: 13d642597fdf5d0eae6c6fd4f0cab16181f033c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270794"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Vytváření a správa nárokovatelných virtuálních počítačů v azure devtest labs
Přidáte nárokovatelný virtuální ms do testovacího prostředí podobným způsobem jako [přidání standardního virtuálního virtuálního movitého virtuálního pracovního místa](devtest-lab-add-vm.md) – ze *základny,* která je [buď vlastní image](devtest-lab-create-template.md), [vzorec](devtest-lab-manage-formulas.md)nebo [obrázek Marketplace](devtest-lab-configure-marketplace-images.md). Tento kurz vás provede pomocí portálu Azure k přidání nárokovatelného virtuálního počítače do testovacího prostředí v DevTest Labs a zobrazí procesy, které uživatel provede, aby si nárokoval a vynebral nárok na virtuální počítač.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Postup přidání nárokovatelného virtuálního počítače do testovacího prostředí v azure devtest labs
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
3. Vyberte **Vytvořit,** chcete-li přidat zadaný virtuální virtuální ms do testovacího prostředí.

   Na stránce testovacího prostředí se zobrazí stav vytvoření virtuálního soudu – nejprve jako **vytvoření**, pak jako **Spuštěno** po spuštění virtuálního soudu.

> [!NOTE]
> Pokud nasazujete virtuální počítače testovacího prostředí prostřednictvím [šablon Azure Resource Manager](devtest-lab-create-environment-from-arm.md), můžete vytvořit nárokovatelné virtuální počítače nastavením vlastnosti **allowClaim** na hodnotu true v části vlastnosti.


## <a name="using-a-claimable-vm"></a>Použití nárokovatelného virtuálního mísa

Uživatel může nárokovat libovolný virtuální počítač ze seznamu "Nárokovatelné virtuální počítače" provedením jednoho z těchto kroků:

* Ze seznamu "Nárokovatelné virtuální počítače" v dolní části podokna "Přehled" testovacího prostředí klikněte pravým tlačítkem myši na jeden z virtuálních počítačů v seznamu a zvolte **Claim machine**.

  ![Požádejte o konkrétní nárokovatelný virtuální ms.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* V horní části podokna Přehled zvolte **Nárokovat libovolné**. Náhodný virtuální počítač se přiřazuje ze seznamu nárokovatelných virtuálních počítačích.

  ![Vyžádejte si nárokovatelný virtuální ms.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Poté, co uživatel nároky virtuálního počítače, DevTest Labs spustí počítač a přesunout do seznamu uživatele testovacího prostředí "Moje virtuální počítače". To znamená, že uživatel testovacího prostředí bude mít nyní oprávnění vlastníka v tomto počítači. Doba potřebná pro tento krok se může lišit v závislosti na časech spuštění a také na všech dalších vlastních akcích prováděných během události deklarace. Jakmile je stroj nárokován, není již k dispozici v nárokovatelném fondu.  

## <a name="unclaim-a-vm"></a>Zrušení deklarace virtuálního virtuálního montova

Když uživatel dokončí používání dekonovaného virtuálního počítače a chce ho zpřístupnit někomu jinému, může dekonovaný virtuální počítač vrátit do seznamu nárokovatelných virtuálních počítačů jedním z těchto kroků:

- Ze seznamu "Moje virtuální počítače" klikněte pravým tlačítkem myši na jeden z virtuálních počítačů v seznamu – nebo vyberte jeho tři tečky (...) – a zvolte **Unclaim**.

  ![Zrušení deklarace virtuálního počítače v seznamu virtuálního počítači.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- V seznamu "Moje virtuální počítače" vyberte virtuální počítač, chcete-li otevřít podokno správy, a pak v horním řádku nabídek vyberte **Unclaim.**

  ![Zrušení deklarace virtuálního počítače v podokně správy virtuálního počítači.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Když uživatel unclaims virtuálního ms, již nemají oprávnění vlastníka pro tento konkrétní virtuální ms testovacího prostředí a je k dispozici pro nárokování jiným uživatelem testovacího prostředí ve stavu, že byl retured do fondu. 

### <a name="transferring-the-data-disk"></a>Přenos datového disku
Pokud nárokovatelný virtuální modul má datový disk připojený k němu a uživatel jej unclaims, datový disk zůstane s virtuálním počítačem. Když jiný uživatel pak tvrdí, že virtuální počítače, že nový uživatel nároky datový disk, stejně jako virtuální počítače.

To to je známé jako "přenos datového disku". Datový disk pak bude k dispozici v seznamu nových uživatelů **moje datové disky** pro jejich správu.

![Zrušit vyzvednutí datových disků.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Další kroky
* Po vytvoření se můžete připojit k virtuálnímu počítači výběrem **možnosti Připojit v** podokně správy.
* Prozkoumejte [galerii šablon Rychlého spuštění nástroje Azure Labs od DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
