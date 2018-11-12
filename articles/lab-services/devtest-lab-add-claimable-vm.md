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
ms.date: 04/01/2018
ms.author: spelluru
ms.openlocfilehash: 3bfb674fa66f0701a099d237f4e760453c7b6a6e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232123"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Vytvoření a správa nárokovatelných virtuálních počítačů ve službě Azure DevTest Labs
Podobným způsobem, jak přidat nárokovatelných virtuálních počítačů do testovacího prostředí můžete [přidat virtuální počítače standard](devtest-lab-add-vm.md) – od *základní* , který je buď [vlastní image](devtest-lab-create-template.md), [vzorec](devtest-lab-manage-formulas.md) , nebo [image z Marketplace](devtest-lab-configure-marketplace-images.md). Tento kurz vás provede přidání nárokovatelných virtuálních počítačů pro testovací prostředí v DevTest Labs pomocí webu Azure portal a procesy, které ukazuje, že uživatel sleduje deklarace identity a jejich převzetí zruší virtuálního počítače.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Postup přidání nárokovatelných virtuálních počítačů do testovacího prostředí ve službě Azure DevTest Labs
1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
1. V seznamu testovacích prostředí vyberte prostředí, ve kterém chcete vytvořit nárokovatelných virtuálních počítačů.  
1. Cvičení **přehled** vyberte **+ přidat**.  

    ![Přidání tlačítka virtuálního počítače](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Na **vyberte bázi** vyberte základ pro virtuální počítač.
1. V **virtuálního počítače** podokně, zadejte název pro nový virtuální počítač v **název virtuálního počítače** textového pole.

    ![Podokno virtuálního počítače testovacího prostředí](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Zadejte **uživatelské jméno** , která jsou udělena oprávnění správce na virtuálním počítači.  
1. Pokud chcete použít heslo uložené v vaše [úložiště tajných kódů](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store)vyberte **použít uložený tajný kód**a zadat hodnotu klíče, který odpovídá váš tajný klíč (heslo). V opačném případě zadejte heslo do textového pole s popiskem **zadejte hodnotu**.
1. **Typ disku virtuálního počítače** Určuje, jaký typ disku úložiště je povolený pro virtuální počítače v testovacím prostředí.
1. Vyberte **velikost virtuálního počítače** a vyberte jednu z předdefinovaných položek, které určují jader procesoru, velikosti paměti RAM a velikost pevného disku virtuálního počítače k vytvoření.
1. Vyberte **artefakty** a ze seznamu artefakty, vyberte a nakonfigurujte artefakty, které chcete přidat do základní image. Pokud začínáte s DevTest Labs začínáte nebo konfigurace artefakty, vyhledejte [k virtuálnímu počítači přidat existující artefakt](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) části a pak se sem vraťte po dokončení.
1. Vyberte **upřesňující nastavení** konfigurovat možnosti sítě Virtuálního počítače a možností vypršení platnosti. V části **možnosti Nárokování**, zvolte **Ano** nárokovatelných aby na počítači.

  ![Zvolte, jestli nárokovatelných virtuálního počítače.](./media/devtest-lab-add-vm/devtestlab-claim-VM-option.png)

1. Pokud chcete zobrazit nebo zkopírujte šablony Azure Resource Manageru, přečtěte si [šablonu uložte Azure Resource Manageru](devtest-lab-add-vm.md#save-azure-resource-manager-template) části a vraťte se sem po dokončení.
1. Vyberte **vytvořit** zadaný virtuální počítač přidat do testovacího prostředí.

   Se zobrazí stav vytváření Virtuálního počítače jako první **vytváření**, pak jako **systémem** po spuštění virtuálního počítače.

> [!NOTE]
> Pokud nasadíte virtuální počítače testovacího prostředí prostřednictvím [šablon Azure Resource Manageru](devtest-lab-create-environment-from-arm.md), nárokovatelných virtuálních počítačů můžete vytvořit tak, že nastavíte **allowClaim** vlastnost na hodnotu true v oddílu Vlastnosti.
>
>

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
