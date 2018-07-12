---
title: Konfigurace nastavení image Azure Marketplace ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Konfigurace, které Image Azure Marketplace se dají použít při vytváření virtuálního počítače ve službě Azure DevTest Labs
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
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 90df853eeca89e61fa69e6967218c4c18274ac1b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38633032"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Konfigurace nastavení image Azure Marketplace ve službě Azure DevTest Labs
DevTest Labs podporuje vytváření virtuálních počítačů založených na imagích Azure Marketplace v závislosti na tom, jak jste nakonfigurovali Image Azure Marketplace pro použití ve vaší laboratoři. Tento článek ukazuje, jak určit, které případně Image Azure Marketplace lze použít při vytváření virtuálních počítačů v testovacím prostředí. Tím se zajistí, že váš tým má přístup pouze k Image Marketplace, které potřebují. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Vyberte, které Image Azure Marketplace se povolují při vytváření virtuálního počítače
1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
3. V seznamu testovacích prostředí vyberte požadované prostředí. 
4. V okně testovacího prostředí, vyberte **konfigurace a zásad**.
5. V labu **konfigurace a zásad** okně v části **báze pro virtuální počítače**vyberte **Image z Marketplace**.
6. Určete, zda všechny kvalifikovaný Image Azure Marketplace k dispozici pro použití jako základ nového virtuálního počítače. Pokud vyberete **Ano**, pak na Azure Marketplace Image, které splňují všechna následující kritéria jsou povoleny v testovacím prostředí:
   
   * Na obrázku vytvoří jeden virtuální počítač, **a**
   * Obrázek použije ke zřízení virtuálních počítačů, Azure Resource Manageru **a**
   * Na obrázku nevyžaduje, aby zakoupení dalších licenčního plánu
     
    Pokud chcete povolit se žádné Image, nebo chcete zadat imagí, které je možné, vyberte **ne**.
     
     ![Možnost, povolíte všechny Image Marketplace, které má být použit jako základní Image pro virtuální počítače](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Pokud vyberete **ne** na předchozí krok **povolené Image/vybrat všechny** zaškrtávací políčko je povolená. 
   Tuto možnost spolu s vyhledávací pole můžete rychle vybrat nebo zrušit výběr všech položek v seznamu zobrazí.
   * Vyberte Image Azure Marketplace, které chcete povolit vytváření virtuálních počítačů jednotlivě zaškrtnutím políčka odpovídající každé image.
   * Pokud už nechcete, aby všechny Image Azure Marketplace, který se má použít v testovacím prostředí, vyberte ze seznamu žádnou akci.
   
    ![Můžete určit, které Image Azure Marketplace můžete použít jako základní Image pro virtuální počítače](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další postup
Jakmile jste nakonfigurovali, jak jsou při vytváření virtuálního počítače povolené Image Azure Marketplace, dalším krokem je [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md).

