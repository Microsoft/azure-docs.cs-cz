---
title: Vytvoření a správa nárokovatelných virtuálních počítačů v testovacím prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak přidat nárokovatelný virtuální počítač do testovacího prostředí ve službě Azure DevTest Labs
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
ms.openlocfilehash: 5ff0a70d8b370655df1eef55dac68804a1db406b
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55082142"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Vytvoření a správa nárokovatelných virtuálních počítačů ve službě Azure DevTest Labs
Podobným způsobem, jak přidat nárokovatelných virtuálních počítačů do testovacího prostředí můžete [přidat virtuální počítače standard](devtest-lab-add-vm.md) – od *základní* , který je buď [vlastní image](devtest-lab-create-template.md), [vzorec](devtest-lab-manage-formulas.md) , nebo [image z Marketplace](devtest-lab-configure-marketplace-images.md). Tento kurz vás provede přidání nárokovatelných virtuálních počítačů pro testovací prostředí v DevTest Labs pomocí webu Azure portal a procesy, které ukazuje, že uživatel sleduje deklarace identity a jejich převzetí zruší virtuálního počítače.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Postup přidání nárokovatelných virtuálních počítačů do testovacího prostředí ve službě Azure DevTest Labs
1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby**a pak vyberte **DevTest Labs** v **DEVOPS** oddílu. Pokud vyberete * (hvězdičkami) vedle položky **DevTest Labs** v **DEVOPS** oddílu. Tato akce přidá **DevTest Labs** do levé navigační nabídce tak, aby k němu máte přístup snadno dalším. Pak můžete vybrat **DevTest Labs** v navigační nabídce vlevo.

    ![Vyberte všechny služby – DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. V seznamu testovacích prostředí vyberte prostředí, ve kterém chcete vytvořit virtuální počítač.  
2. Cvičení **přehled** stránce **+ přidat**.  

    ![Přidání tlačítka virtuálního počítače](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. Na **vyberte bázi** vyberte marketplace image pro virtuální počítač.
1. Na **základní nastavení** karty **virtuálního počítače** stránce, proveďte následující akce: 
    1. Zadejte název pro virtuální počítač v **název virtuálního počítače** textového pole. Do textového pole je pro vás předem vyplněný s jedinečným názvem automaticky generované. Název odpovídá uživatelské jméno v e-mailovou adresu, za nímž následuje jedinečný 3 číslice. Tato funkce vám ušetří čas přemýšlení o název počítače a zadávat pokaždé, když vytváříte počítač. Pokud budete chtít, můžete přepsat toto pole automaticky vyplněný s názvem podle vašeho výběru. Pokud chcete přepsat automaticky vyplněný název pro virtuální počítač, zadejte název do **název virtuálního počítače** textového pole.
    2. Zadejte **uživatelské jméno** , která jsou udělena oprávnění správce na virtuálním počítači. **Uživatelské jméno** pro počítač je předem vyplněná jedinečný automaticky generovaný název. Název odpovídá uživatelské jméno v e-mailovou adresu. Tato funkce vám ušetří čas při rozhodování o uživatelského jména pokaždé, když vytvoříte nový počítač. Pokud chcete znovu, můžete přepsat toto pole automaticky vyplněný s uživatelským jménem podle svého výběru. Chcete-li přepsat automaticky vyplněné hodnoty pro uživatelské jméno, zadejte hodnotu v **uživatelské jméno** textového pole. Tomuto uživateli je udělen **správce** oprávnění na virtuálním počítači.
    3. Pokud vytváříte první virtuální počítač v testovacím prostředí, zadejte **heslo** pro daného uživatele. Chcete-li uložit toto heslo jako výchozí heslo ve službě Azure key vault související s testovacím prostředí, vyberte **uložit jako výchozí heslo**. Výchozí heslo se uloží ve službě key vault s názvem: **VmPassword**. Při pokusu o vytvoření dalších virtuálních počítačů v testovacím prostředí, **VmPassword** je automaticky vybrána pro **heslo**. Chcete-li přepsat hodnotu, zrušte **použít uložený tajný kód** zaškrtněte políčko a zadejte heslo. 

        Můžete také tajné kódy ve službě key vault nejdříve uložte a pak použít při vytváření virtuálního počítače v testovacím prostředí. Další informace najdete v tématu [Store tajné kódy ve službě key vault](devtest-lab-store-secrets-in-key-vault.md). Chcete-li použít heslo uložené ve službě key vault, vyberte **použít uložený tajný kód**a zadat hodnotu klíče, který odpovídá váš tajný klíč (heslo).
    4. V **další možnosti** vyberte **změnit velikost**. Vyberte jednu z předdefinovaných položek, které určují jader procesoru, velikosti paměti RAM a velikost pevného disku virtuálního počítače k vytvoření.
    5. Vyberte **přidat nebo odebrat artefakty**. Vyberte a nakonfigurujte artefakty, které chcete přidat do základní image.
    **Poznámka:** Pokud začínáte s DevTest Labs začínáte nebo konfigurace artefakty, vyhledejte [k virtuálnímu počítači přidat existující artefakt](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) části a pak se sem vraťte po dokončení.
2. Přepněte **Upřesnit nastavení** kartě v horní části a proveďte následující akce:
    1. Chcete-li změnit virtuální síť, virtuální počítač, vyberte **změnit virtuální síť**. 
    2. Pokud chcete změnit podsíť, vyberte **změnit podsíť**. 
    3. Určete, jestli je IP adresa virtuálního počítače **veřejné, privátní nebo sdílené**. 
    4. Pokud chcete automaticky odstranit virtuální počítač, zadejte **datum a čas vypršení**. 
    5. Chcete-li virtuální počítač nárokovatelných uživatelem testovacího prostředí, vyberte **Ano** pro **nastavit tento počítač nárokovatelných** možnost. 
    6. Zadejte počet **instancí virtuálních počítačů** chcete zpřístupnit uživatelům testovacího prostředí. 
3. Vyberte **vytvořit** zadaný virtuální počítač přidat do testovacího prostředí.

   Na stránce testovacího prostředí se zobrazuje stav vytvoření Virtuálního počítače – nejprve jako **vytváření**, pak jako **systémem** po spuštění virtuálního počítače.

> [!NOTE]
> Pokud nasadíte virtuální počítače testovacího prostředí prostřednictvím [šablon Azure Resource Manageru](devtest-lab-create-environment-from-arm.md), nárokovatelných virtuálních počítačů můžete vytvořit tak, že nastavíte **allowClaim** vlastnost na hodnotu true v oddílu Vlastnosti.


## <a name="using-a-claimable-vm"></a>Pomocí nárokovatelných virtuálních počítačů

Uživatel si může nárokovat libovolný virtuální počítač ze seznamu "Nárokovatelné virtuální počítače" jedním z těchto kroků:

* Ze seznamu "Nárokovatelných virtuálních počítačů" v dolní části podokna "Přehled" testovací prostředí, klikněte pravým tlačítkem na jeden z virtuálních počítačů v seznamu a zvolte **nárokovat počítač**.

 ![Požádat o konkrétní nárokovatelných virtuálních počítačů.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* V horní části podokna "Přehled", zvolte **nárokovat libovolný**. Ze seznamu nárokovatelných virtuálních počítačů je náhodný virtuální počítač přiřazený.

 ![Požádat o žádné nárokovatelných virtuálních počítačů.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Jakmile uživatel deklarací identity virtuálního počítače, přesunuty do svého seznamu "Moje virtual machines" a není už nárokovatelných žádný uživatel.

## <a name="unclaim-a-vm"></a>Vzdát se virtuálního počítače

Když uživatel přestane používat nárokován. virtuální počítač a chce, aby byla k dispozici někomu jinému, se vrátí nárokován. virtuální počítač do seznam nárokovatelných virtuálních počítačů pomocí jedné z těchto kroků:

- Ze seznamu "Moje virtuální počítače", klikněte pravým tlačítkem na jeden z virtuálních počítačů v seznamu – nebo vyberte jeho ikonu tří teček (...) – a zvolte **Unclaim**.

  ![Vzdát se virtuálního počítače na seznam virtuálních počítačů.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- V seznamu "Moje virtuální počítače", vyberte virtuální počítač otevřete jeho panel pro správu a pak vyberte **Unclaim** v horní nabídce.

  ![Vrátit. virtuální počítač v podokně Správa Virtuálního počítače.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Když uživatel unclaims virtuálního počítače, že už nebude mít žádná oprávnění pro tento konkrétní testovací virtuální počítač.

### <a name="transferring-the-data-disk"></a>Převod datového disku
Pokud nárokovatelných virtuálních počítačů má datový disk připojený a uživatel ho unclaims, datový disk zůstává s virtuálním Počítačem. Když jiný uživatel potom deklarací identity, že virtuální počítač, tento nový uživatel deklarací datový disk, stejně jako virtuální počítač.

To se označuje jako "převod datový disk". Datový disk pak bude k dispozici v seznamu nového uživatele **Moje datové disky** spravuje.

![Vzdát datové disky.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Další postup
* Jakmile se vytvoří, můžete připojit k virtuálnímu počítači tak, že vyberete **připojit** v podokně pro správu.
* Prozkoumejte [Galerie šablon rychlý start DevTest Labs Azure Resource Manageru](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
