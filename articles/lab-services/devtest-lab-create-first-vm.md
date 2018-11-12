---
title: Vytvoření vašeho prvního virtuálního počítače v testovacím prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit svůj první virtuální počítač v testovacím prostředí ve službě Azure DevTest Labs
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
ms.openlocfilehash: 93ce9feaf52282b9477d49eaf270d6d89dca7811
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232524"
---
# <a name="create-your-first-vm-in-a-lab-in-azure-devtest-labs"></a>Vytvoření vašeho prvního virtuálního počítače v testovacím prostředí ve službě Azure DevTest Labs

Při počátečním přístup ke službě DevTest Labs a chcete vytvořit svůj první virtuální počítač, bude pravděpodobně uděláte pomocí předem načtené [image z marketplace základní](devtest-lab-configure-marketplace-images.md). Později také budete mít na výběr [vlastní image a vzorec](devtest-lab-add-vm.md) při vytváření více virtuálních počítačů. 

Tento kurz vás provede přidání vašeho prvního virtuálního počítače do testovacího prostředí v DevTest Labs pomocí webu Azure portal.

## <a name="steps-to-add-your-first-vm-to-a-lab-in-azure-devtest-labs"></a>Postup přidání vašeho prvního virtuálního počítače do testovacího prostředí ve službě Azure DevTest Labs
1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
1. V seznamu testovacích prostředí vyberte prostředí, ve kterém chcete vytvořit virtuální počítač.  
1. Cvičení **přehled** okně vyberte **+ přidat**.  

    ![Přidání tlačítka virtuálního počítače](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Na **vyberte bázi** okno, vyberte webu marketplace image virtuálního počítače.
1. Na **virtuálního počítače** okno, zadejte název pro nový virtuální počítač **název virtuálního počítače** textového pole.

    ![Okno virtuální počítače testovacího prostředí](./media/devtest-lab-add-vm/devtestlab-lab-add-first-vm.png)

1. Zadejte **uživatelské jméno** , která jsou udělena oprávnění správce na virtuálním počítači.  
1. Zadejte heslo do textového pole s popiskem **zadejte hodnotu**.
1. **Typ disku virtuálního počítače** Určuje, jaký typ disku úložiště je povolený pro virtuální počítače v testovacím prostředí.
1. Vyberte **velikost virtuálního počítače** a vyberte jednu z předdefinovaných položek, které určují jader procesoru, velikosti paměti RAM a velikost pevného disku virtuálního počítače k vytvoření.
1. Vyberte **artefakty** a – z seznam artefakty - vybrat a nakonfigurovat artefakty, které chcete přidat do základní image.
    **Poznámka:** Pokud začínáte s DevTest Labs začínáte nebo konfigurace artefakty, vyhledejte [k virtuálnímu počítači přidat existující artefakt](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) části a pak se sem vraťte po dokončení.
1. Vyberte **vytvořit** zadaný virtuální počítač přidat do testovacího prostředí.

   Stav vytváření Virtuálního počítače – okno lab nejprve zobrazí jako **vytváření**, pak jako **systémem** po spuštění virtuálního počítače.

## <a name="next-steps"></a>Další postup
* Po vytvoření virtuálního počítače může připojit k virtuálnímu počítači tak, že vyberete **připojit** v okně Virtuálního počítače.
* Podívejte se na [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md) úplnější informace o přidávání dalších virtuálních počítačů ve vaší laboratoři.
* Prozkoumejte [Galerie šablon QuickStart pro Azure Resource Manageru DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates).
