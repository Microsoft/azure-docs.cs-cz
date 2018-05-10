---
title: Vytvoření a správa vymahatelných virtuálních počítačů v testovacím prostředí v Azure DevTest Labs | Microsoft Docs
description: Informace o postupu přidání vymahatelných virtuálního počítače do testovacího prostředí v Azure DevTest Labs
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
ms.openlocfilehash: 669dfab75f34a0d1f997dc34f600402d3c10669b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Vytvoření a správa vymahatelných virtuálních počítačů v Azure DevTest Labs
Přidat vymahatelných virtuálního počítače do testovacího prostředí podobným způsobem, jak můžete [přidat standardní virtuální počítač](devtest-lab-add-vm.md) – z *základní* to znamená buď [vlastní image](devtest-lab-create-template.md), [vzorec](devtest-lab-manage-formulas.md), nebo [Marketplace image](devtest-lab-configure-marketplace-images.md). V tomto kurzu vás provede pomocí portálu Azure pro přidání do testovacího prostředí v DevTest Labs vymahatelných virtuálního počítače a zobrazuje procesy že uživatel bude postupovat pro deklarace identity a unclaim virtuálního počítače.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Postup pro přidání do testovacího prostředí v Azure DevTest Labs vymahatelných virtuálního počítače
1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby**a potom vyberte **DevTest Labs** ze seznamu.
1. Ze seznamu labs vyberte testovací prostředí, ve kterém chcete vytvořit vymahatelných virtuálního počítače.  
1. V tomto prostředí **přehled** podokně, vyberte **+ přidat**.  

    ![Virtuální počítač tlačítko Přidat](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Na **zvolte na základní** oblasti, vyberte základ pro virtuální počítač.
1. V **virtuálního počítače** podokně, zadejte název pro nový virtuální počítač **název virtuálního počítače** textového pole.

    ![Virtuální počítač podokně testovacího prostředí](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Zadejte **uživatelské jméno** , jsou udělena oprávnění správce na virtuálním počítači.  
1. Pokud chcete použít heslo uložené v vaše [tajný úložiště](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store), vyberte **použít uložené tajný klíč**a zadejte hodnotu klíče, která odpovídá váš tajný klíč (heslo). Jinak, zadejte heslo do textového pole s názvem bez přípony **zadejte hodnotu**.
1. **Typ disku virtuálního počítače** určuje disku typů úložiště, které u virtuálních počítačů v testovacím prostředí.
1. Vyberte **velikost virtuálního počítače** a vyberte jednu z předdefinovaných položky, které zadejte jader procesoru, velikosti paměti RAM a velikost pevného disku pro vytvoření virtuálního počítače.
1. Vyberte **artefakty** a ze seznamu artefaktů, vyberte a nakonfigurujte artefaktů, které chcete přidat do základní bitové kopie. Pokud nepracovali DevTest Labs nebo konfigurace artefaktů, vyhledejte [přidat existující artefaktů pro virtuální počítač](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) části a vraťte se sem po dokončení.
1. Vyberte **upřesňující nastavení** nakonfigurovat možnosti sítě a vypršení platnosti možnosti Virtuálního počítače. V části **deklarace identity možnosti**, zvolte **Ano** k zajištění vymahatelných počítače.

  ![Zvolte, aby vymahatelných virtuálního počítače.](./media/devtest-lab-add-vm/devtestlab-claim-VM-option.png)

1. Pokud chcete zobrazit nebo zkopírování šablony Azure Resource Manager, přečtěte si [šablony uložit Azure Resource Manageru](devtest-lab-add-vm.md#save-azure-resource-manager-template) části a sem vraťte po dokončení.
1. Vyberte **vytvořit** přidat zadaný virtuální počítač do testovacího prostředí.

   Stav vytvoření Virtuálního počítače se zobrazí, jako první **vytváření**, pak jako **systémem** po spuštění virtuálního počítače.

> [!NOTE]
> Pokud nasadíte virtuální počítače testovacího prostředí prostřednictvím [šablon Azure Resource Manageru](devtest-lab-create-environment-from-arm.md), můžete vytvořit virtuální počítače vymahatelných nastavením **allowClaim** vlastnost na hodnotu true v části Vlastnosti.
>
>

## <a name="using-a-claimable-vm"></a>Pomocí vymahatelných virtuálního počítače

Uživatel může deklarace identity žádné virtuální počítače ze seznamu "Vymahatelných virtuální počítače" jedním z těchto kroků:

* Ze seznamu "Vymahatelných virtuální počítače" v dolní části podokna "Přehled" v prostředí, klikněte pravým tlačítkem na jednu z virtuálních počítačů v seznamu a vyberte **deklarace identity počítače**.

 ![Požádat o konkrétní vymahatelných virtuální počítač.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* V horní části podokna "Přehled", zvolte **všechny deklarace**. Náhodné virtuální počítač přidělenou ze seznamu vymahatelných virtuálních počítačů.

 ![Požádat o žádné vymahatelných virtuální počítače.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Poté, co uživatel deklarací virtuálního počítače, je do jejich seznam "Moje virtuální počítače" Přesunout nahoru a již není vymahatelných jiným uživatelem.

## <a name="unclaim-a-vm"></a>Unclaim virtuálního počítače

Když uživatel přestane používat deklarovaný virtuálních počítačů a chce, aby byl k dispozici někomu jinému, jejich deklarovaný virtuální počítač vrátit do seznamu vymahatelných virtuálních počítačů pomocí jedné z těchto kroků:

- Ze seznamu "Moje virtuální počítače", klikněte pravým tlačítkem na jednu z virtuálních počítačů v seznamu – nebo vyberte jeho třemi tečkami (...) – a zvolte **Unclaim**.

  ![Unclaim virtuální počítač na seznam virtuálních počítačů.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- V seznamu "Moje virtuální počítače", vyberte virtuální počítač otevřete podokno jeho správu a pak vyberte **Unclaim** z hlavní nabídky panelu.

  ![Unclaim virtuálních počítačů v podokně Správa Virtuálního počítače.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Když uživatel unclaims virtuální počítač, mají už veškerá oprávnění pro tento konkrétní testovací prostředí virtuálních počítačů.

### <a name="transferring-the-data-disk"></a>Přenos dat disku
Pokud má vymahatelných virtuální počítač datový disk připojen k jeho a uživatel ho unclaims, zůstává datový disk s virtuálním Počítačem. Když jiný uživatel pak deklarací, že virtuální počítač, tento nový uživatel deklarací datový disk, jakož i virtuální počítač.

To se označuje jako "přenosu datový disk". Datový disk se pak stane k dispozici v seznamu nového uživatele **Moje datových disků** pro ně ke správě.

![Unclaim datových disků.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Další postup
* Jakmile je vytvořen, se můžete připojit k virtuálnímu počítači pomocí výběr **Connect** na jeho podokně Správa.
* Prozkoumejte [šablony galerii pro rychlý start DevTest Labs Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
