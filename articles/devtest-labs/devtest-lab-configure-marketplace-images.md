---
title: Konfigurace nastavení Azure Marketplace imagí v Azure DevTest Labs
description: Nakonfigurovat, které Azure Marketplace image se dají použít při vytváření virtuálního počítače v Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a5158ed33bf253db1dbe0eb3232bc43d27ce15e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86512431"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Konfigurace nastavení Azure Marketplace imagí v Azure DevTest Labs
DevTest Labs podporuje vytváření virtuálních počítačů na základě Azure Marketplace imagí v závislosti na tom, jak jste nakonfigurovali Azure Marketplace imagí pro použití v testovacím prostředí. V tomto článku se dozvíte, jak určit, které Azure Marketplace image se můžou použít při vytváření virtuálních počítačů v testovacím prostředí. Zajišťuje, aby váš tým měl přístup jenom k potřebným imagím Marketplace. 

## <a name="specify-allowed-images-for-creating-vms"></a>Určení povolených imagí pro vytváření virtuálních počítačů
Pomocí těchto kroků určíte, které Azure Marketplace image se při vytváření virtuálního počítače povolí. 

1. Přihlaste se na [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **všechny služby** a v seznamu vyberte **DevTest Labs** .
3. V seznamu cvičení vyberte testovací prostředí. 
4. Na stránce Domovská stránka testovacího prostředí vyberte **Konfigurace a zásady**.
5. Na stránce **Konfigurace a zásady** testovacího prostředí v části **základy virtuálních počítačů** vyberte **Image Marketplace**.
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


## <a name="troubleshoot"></a>Řešení potíží
Pokud nemůžete najít konkrétní bitovou kopii, kterou by bylo možné použít pro testovací prostředí, postupujte podle následujících kroků: 

- Zkontrolujte, jestli si nemůžete zobrazit image při vytváření výpočetního virtuálního počítače.
- Bitová kopie nemusí být k dispozici v typu předplatného, které používáte. Obraťte se na správce předplatného pro daný typ předplatného (například MSDN, free, průběžné platby atd.). 
- Podpora imagí Gen 2 v DevTest Labs je omezená. Pokud je pro Image k dispozici jak 1, tak i Gen 2, při vytváření virtuálního počítače se v DevTest Labs zobrazí jenom verze 1. generace. Alternativním řešením je vytvořit vlastní obrázek Gen 2 mimo testovací prostředí a použít ho k vytvoření virtuálního počítače. Pokud je k dispozici jenom verze Gen 2, je podporovaná a zobrazená v seznamu podle DevTest Labs. 
      


## <a name="next-steps"></a>Další kroky
Až nakonfigurujete způsob, jakým se při vytváření virtuálního počítače povolí Azure Marketplace image, je dalším krokem [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md).

