---
title: Vytvoření vaší první virtuální počítač v testovacím prostředí v Azure DevTest Labs | Microsoft Docs
description: Naučte se vytvořit svůj první virtuální počítač v testovacím prostředí v Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: fbc5a438-6e02-4952-b654-b8fa7322ae5f
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 0240bbdf2055ac44f8836adfaad3bf6c44893b77
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="create-your-first-vm-in-a-lab-in-azure-devtest-labs"></a>Vytvoření vaší první virtuální počítač v testovacím prostředí v Azure DevTest Labs

Pokud jste původně přístup DevTest Labs a chcete k vytvoření vašeho prvního virtuálního počítače, bude pravděpodobně uděláte pomocí předem načtený [image základní marketplace](devtest-lab-configure-marketplace-images.md). Později na taky budete moct vybrat z [vlastní image a vzorce](devtest-lab-add-vm.md) při vytváření více virtuálních počítačů. 

Tento kurz vás provede procesem přidání vaší první virtuální počítač do testovacího prostředí v DevTest Labs pomocí portálu Azure.

## <a name="steps-to-add-your-first-vm-to-a-lab-in-azure-devtest-labs"></a>Postup pro přidání do testovacího prostředí v Azure DevTest Labs vašeho prvního virtuálního počítače
1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby**a potom vyberte **DevTest Labs** ze seznamu.
1. Ze seznamu labs vyberte testovací prostředí, ve kterém chcete vytvořit virtuální počítač.  
1. V tomto prostředí **přehled** vyberte **+ přidat**.  

    ![Virtuální počítač tlačítko Přidat](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Na **zvolte na základní** okně, vyberte bitovou kopii na trhu pro virtuální počítač.
1. Na **virtuálního počítače** okno, zadejte název pro nový virtuální počítač **název virtuálního počítače** textového pole.

    ![Okno prostředí virtuálních počítačů](./media/devtest-lab-add-vm/devtestlab-lab-add-first-vm.png)

1. Zadejte **uživatelské jméno** , jsou udělena oprávnění správce na virtuálním počítači.  
1. Zadejte heslo do textového pole s názvem bez přípony **zadejte hodnotu**.
1. **Typ disku virtuálního počítače** určuje disku typů úložiště, které u virtuálních počítačů v testovacím prostředí.
1. Vyberte **velikost virtuálního počítače** a vyberte jednu z předdefinovaných položky, které zadejte jader procesoru, velikosti paměti RAM a velikost pevného disku pro vytvoření virtuálního počítače.
1. Vyberte **artefakty** a - ze seznamu artefaktů - vyberte a nakonfigurujte artefaktů, které chcete přidat do základní bitové kopie.
    **Poznámka:** Pokud nepracovali DevTest Labs nebo konfigurace artefaktů, vyhledejte [přidat existující artefaktů pro virtuální počítač](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) části a vraťte se sem po dokončení.
1. Vyberte **vytvořit** přidat zadaný virtuální počítač do testovacího prostředí.

   V okně prostředí zobrazí stav vytváření Virtuálního počítače – nejprve jako **vytváření**, pak jako **systémem** po spuštění virtuálního počítače.

## <a name="next-steps"></a>Další postup
* Po vytvoření virtuálního počítače, můžete připojit k virtuálnímu počítači tak, že vyberete **Connect** v okně Virtuálního počítače.
* Podívejte se na [přidat virtuální počítač do testovacího prostředí](devtest-lab-add-vm.md) podrobnější informace o přidávání dalších virtuálních počítačů ve svém testovacím prostředí.
* Prozkoumejte [Galerie šablon DevTest Labs Azure Resource Manager QuickStart](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates).
