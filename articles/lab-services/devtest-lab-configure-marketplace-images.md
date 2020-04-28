---
title: Konfigurace nastavení Azure Marketplace imagí v Azure DevTest Labs
description: Nakonfigurovat, které Azure Marketplace image se dají použít při vytváření virtuálního počítače v Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 804c6af2-17e9-4320-af3a-f454bd398379
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 2dc3aa000cefc0e65305b58f8fdce93b94bfd35f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76169556"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Konfigurace nastavení Azure Marketplace imagí v Azure DevTest Labs
DevTest Labs podporuje vytváření virtuálních počítačů na základě Azure Marketplace imagí v závislosti na tom, jak jste nakonfigurovali Azure Marketplace imagí pro použití v testovacím prostředí. V tomto článku se dozvíte, jak určit, které Azure Marketplace image se můžou použít při vytváření virtuálních počítačů v testovacím prostředí. Tím zajistíte, že váš tým bude mít přístup jenom k potřebným imagím Marketplace. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Vyberte, které Azure Marketplace image jsou povolené při vytváření virtuálního počítače.
1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **všechny služby**a v seznamu vyberte **DevTest Labs** .
3. V seznamu cvičení vyberte požadované testovací prostředí. 
4. V okně testovacího prostředí vyberte **Konfigurace a zásady**.
5. V okně **Konfigurace a zásady** testovacího prostředí v části **základy virtuálního počítače**vyberte **Image Marketplace**.
6. Určete, jestli chcete, aby všechny kvalifikované Azure Marketplace Image byly dostupné pro použití jako základ nového virtuálního počítače. Vyberete-li možnost **Ano**, budou v testovacím prostředí povoleny všechny Azure Marketplace bitové kopie, které splňují všechna následující kritéria:
   
   * Image vytvoří jeden virtuální počítač **a**
   * Image používá Azure Resource Manager k zřizování virtuálních počítačů **a**
   * Obrázek nevyžaduje zakoupení dalšího licenčního plánu.
     
     Pokud nechcete, aby byly povoleny žádné obrázky, nebo chcete určit, které image lze použít, vyberte možnost **ne**.
     
     ![Možnost povolení použití všech imagí na webu Marketplace jako základních imagí pro virtuální počítače](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Pokud vyberete možnost **ne** pro předchozí krok, je zaškrtnuté políčko **povolené obrázky/vybrat vše** . 
   Tuto možnost můžete použít spolu s polem hledání k rychlému výběru nebo výběru všech položek zobrazených v seznamu.
   * Zaškrtněte políčko Azure Marketplace image, u kterých chcete vytvořit virtuální počítač jednotlivě zaškrtnutím příslušného políčka každého obrázku.
   * Pokud nechcete povolit použití Azure Marketplace imagí v testovacím prostředí, vyberte v seznamu nic.
   
     ![Můžete určit, které Azure Marketplace image se dají použít jako základní image pro virtuální počítače.](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další kroky
Až nakonfigurujete způsob, jakým se při vytváření virtuálního počítače povolí Azure Marketplace image, je dalším krokem [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md).

