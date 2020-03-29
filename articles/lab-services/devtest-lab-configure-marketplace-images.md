---
title: Konfigurace nastavení image Azure Marketplace v Azure DevTest Labs
description: Konfigurace image Azure Marketplace, které se dá použít při vytváření virtuálního počítače v Azure DevTest Labs
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169556"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Konfigurace nastavení image Azure Marketplace v Azure DevTest Labs
DevTest Labs podporuje vytváření virtuálních počítačů založených na ibi Azure Marketplace v závislosti na tom, jak jste nakonfigurovali image Azure Marketplace pro použití ve vašem testovacím prostředí. Tento článek ukazuje, jak určit, které image Azure Marketplace se dá použít při vytváření virtuálních počítačů v testovacím prostředí. Tím zajistíte, že váš tým bude mít přístup jenom k bitovým kopiím marketplace, které potřebuje. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Vyberte, které image Azure Marketplace jsou povolené při vytváření virtuálního počítače
1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**
3. Ze seznamu testovacích prostředí vyberte požadovanou testovací prostředí. 
4. Na okně testovacího prostředí vyberte **Konfigurace a zásady**.
5. V okně **Konfigurace a zásady** testovacího prostředí v části **Základy virtuálních počítačů**vyberte **image Marketplace**.
6. Určete, jestli chcete, aby všechny kvalifikované image Azure Marketplace byly dostupné pro použití jako základ nového virtuálního počítače. Pokud vyberete **Ano**, budou v testovacím prostředí povoleny všechny image Azure Marketplace, které splňují všechna následující kritéria:
   
   * Obraz vytvoří jeden virtuální **and** virtuální
   * Image používá Azure Resource Manager k zřizování virtuálních počítačů **a**
   * Obrázek nevyžaduje zakoupení dalšího licenčního plánu
     
     Pokud nechcete, aby byly povoleny žádné obrázky nebo chcete určit, které obrazy lze použít, vyberte **možnost Ne**.
     
     ![Možnost povolit použití všech iobrazek Marketplace jako základních iobrazů pro virtuální chod](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Pokud vyberete **ne** k předchozímu kroku, je povoleno **políčko Povolené obrázky/Vybrat vše.** 
   Tuto možnost můžete použít společně s vyhledávacím polem k rychlému výběru nebo zrušení výběru všech položek zobrazených v seznamu.
   * Vyberte image Azure Marketplace, které chcete povolit pro vytváření virtuálních počítačů jednotlivě kontrolou odpovídajícího políčka každé image.
   * Pokud nechcete povolit použití image Azure Marketplace v testovacím prostředí, nic ze seznamu nevyberte.
   
     ![Můžete určit, které image Azure Marketplace se můžou používat jako základní image pro virtuální počítače.](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další kroky
Jakmile nakonfigurujete, jak jsou image Azure Marketplace povoleny při vytváření virtuálního počítače, dalším krokem je [přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md).

