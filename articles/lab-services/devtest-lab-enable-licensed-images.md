---
title: Povolení licencované image v testovacím prostředí v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Zjistěte, jak povolit licencovanou bitovou kopii v Azure DevTest Labs pomocí portálu Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 221390d2-8d3b-4e1f-b454-43d33f8072b7
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 11b6553fe8aceef0d3d15977998dd870c275128a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61294213"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Povolení licencované image v testovacím prostředí v laboratořích Azure DevTest Labs

V Azure DevTest Labs licencovaná bitová kopie obsahuje podmínky – obvykle od třetí strany – které musí být přijaty předtím, než je bitová kopie přístupná uživatelům v testovacím prostředí. Následující části popisují, jak pracovat s licencovanými bitovými kopiemi, aby byly k dispozici pro vytváření virtuálních počítačů.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Určení, zda je licencovaná bitová kopie dostupná uživatelům
Prvním krokem k povolení uživatelům vytvářet virtuální uživatele z licencované bitové kopie je zajistit, aby byly pro licencovanou bitovou kopii přijaty smluvní podmínky. Následující kroky ukazují, jak můžete zobrazit stav nabídky licencovaného obrázku a v případě potřeby přijmout její smluvní podmínky.

1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**

1. Ze seznamu testovacích prostředí vyberte požadovanou testovací prostředí.  

1. V levém panelu v části **NASTAVENÍ**vyberte **Možnost Konfigurace a zásady**.

1. V levém panelu pod **položkou ZÁKLADY VIRTUÁLNÍCH STROJŮ**vyberte **obrázky na marketplace**. 

    ![Položka nabídky Obrázky na webu Marketplace](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    Zobrazí se seznam všech dostupných bitových kopií na marketplace, včetně **stavu NABÍDKY** pro každý obrázek.

    ![Seznam obrázků na tržišti zobrazujících stav nabídky pro každý obrázek](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    Licencovaný obrázek zobrazuje stav nabídky 
    
    - **Přijaté podmínky:** Licencovaná bitová kopie je uživatelům k dispozici k vytvoření virtuálních počítačů. 
    - **Potřebná kontrola podmínek:** licencovaná bitová kopie není v současné době uživatelům k dispozici. Podmínky licence musí být přijaty před testovacím prostředí uživatelé můžete použít k vytvoření virtuálních počítačů. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Zpřístupnění licencované bitové kopie uživatelům testovacího prostředí
Chcete-li zajistit, aby byla licencovaná bitová kopie dostupná uživatelům testovacího prostředí, musí vlastník testovacího prostředí s oprávněními správce nejprve přijmout podmínky pro tuto licencovanou bitovou kopii. Povolení matné nasazení pro předplatné přidružené k licencované bitové kopii automaticky přijímá právní podmínky a prohlášení o zásadách ochrany osobních údajů pro tuto bitovou kopii. [Práce s image Marketplace ve Správci prostředků Azure](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) poskytuje další informace o programovém nasazení ibi k rezortu marketplace.

Programové nasazení licencované bitové kopie můžete povolit takto:

1. Na [webu Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)přejděte na seznam **ibi obrázků marketplace**.

1. Identifikujte licencovanou bitovou kopii, ke které chcete mít přístup uživatelům, ale jejíž podmínky nebyly přijaty. Může se například zobrazit virtuální počítač pro datové vědy, který zobrazuje stav **přijatých podmínek** nebo **potřebných podmínek**.

    ![Okno Konfigurovat programové nasazení](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Virtuální počítače pro datové vědy jsou image Virtuálního počítače Azure, předinstalované, nakonfigurované a testované s několika oblíbenými nástroji, které se běžně používají pro analýzu dat, strojové učení a školení ai. [Úvod do virtuálního počítače Azure Data Science pro Linux a Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) poskytuje velké množství informací o dsvms.
   >
   >

1. Ve sloupci **STAV NABÍDKY** pro obrázek vyberte **položku Terms review needed**.

1. V okně Konfigurovat programové nasazení vyberte **povolit**.

    ![Okno Konfigurovat programové nasazení](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > V okně Konfigurovat programové nasazení se může zobrazit více předplatných. Ujistěte se, že povolujete programové nasazení pouze pro zamýšlené předplatné.
   >
   >


1. Vyberte **Uložit**. 

    V seznamu bitových kopií marketplace, že obrázek nyní zobrazuje **podmínky přijaté** a je k dispozici pro uživatele k vytvoření virtuálních počítačů.

> [!NOTE]
> Uživatelé mohou vytvořit vlastní bitovou kopii z licencované bitové kopie. Další informace najdete [v tématu Vytvoření vlastního obrázku ze souboru Virtuálního pevného disku.](devtest-lab-create-template.md)
>
>


## <a name="related-blog-posts"></a>Související příspěvky blogu

- [Vlastní obrázky nebo vzorce?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopírování vlastních ibi mezi azure devtest labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Další kroky

- [Vytvoření vlastní image z virtuálního počítače](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Vytvoření vlastního obrázku ze souboru VHD](devtest-lab-create-template.md)
- [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)