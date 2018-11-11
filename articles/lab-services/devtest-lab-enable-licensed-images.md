---
title: Povolení licencované image ve vaší laboratoři ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Informace o povolení licencované image ve službě Azure DevTest Labs pomocí webu Azure portal
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
ms.openlocfilehash: 42f3378196b4e3e1dfe1234e801e87875f9c61fc
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247649"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Povolení licencované image ve vaší laboratoři ve službě Azure DevTest Labs

Ve službě Azure DevTest Labs licencované image je takový, který obsahuje podmínky a ujednání – obvykle od třetích stran –, který musí být přijata před bitová kopie je přístupný pro uživatele v testovacím prostředí. Následující části popisují, jak pracovat s licencovanou Image tak, aby byly k dispozici pro použití při vytváření virtuálních počítačů.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Určení, zda je k dispozici uživatelům licencované image
Prvním krokem povolení uživatelům vytvářet virtuální počítače z licencované image se zajišťuje, že pro licencované image jste přijali podmínky a ujednání. Následující kroky ukazují, jak můžete zobrazit stav nabídky licencované image a, v případě potřeby, přijměte jeho podmínky a ujednání.

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.

1. V seznamu testovacích prostředí vyberte požadované prostředí.  

1. V levém podokně v části **nastavení**vyberte **konfigurace a zásad**.

1. V levém podokně v části **BÁZE pro virtuální počítače**vyberte **Image z Marketplace**. 

    ![Položka nabídky imagí Marketplace](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    Zobrazí seznam všech imagí marketplace k dispozici, včetně **nabízejí stav** každé bitové kopie.

    ![Seznam zobrazující stav nabídky pro každý obrázek imagí marketplace](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    Licencované image se zobrazí nabídka stav 
    
    - **Podmínky přijaty:** licencované image je k dispozici pro uživatele k vytvoření virtuálních počítačů. 
    - **Nutné přezkoumání podmínek:** licencované image není aktuálně k dispozici pro uživatele. Podmínky a ujednání licence musí být přijata před uživatelé testovacího prostředí můžete použít k vytvoření virtuálních počítačů. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Zpřístupnění licencované image uživatelé testovacího prostředí
Pokud chcete mít jistotu, že licencované image je k dispozici uživatelům testovacího prostředí, musí vlastník testovacího prostředí s oprávněními správce nejdřív přijali podmínky a ujednání pro tuto licencovanou image. Povolením programové nasazení pro předplatné spojené s licencované image automaticky přijme právní podmínky a prohlášení o zásadách pro tuto bitovou kopii. [Práce s Imagí Marketplace na Azure Resource Manageru](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) poskytuje další informace o programové nasazení Image z marketplace.

Můžete povolit programové nasazení pro licencované image pomocí následujících kroků:

1. V [webu Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040), přejděte na seznam **Image z Marketplace**.

1. Identifikujte licencované image, pro kterou mají uživatelé mají přístup, ale jejíž podmínky nebyly přijaty. Například může zobrazit virtuální počítač pro datové vědy, která se zobrazuje stav buď **podmínky přijaty** nebo **nutné přezkoumání podmínek**.

    ![Konfigurovat programové nasazení časový interval](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Virtuální počítače datové vědy jsou Image virtuálních počítačů Azure, předem nainstalovaný, nakonfigurovaných a otestovaných s několika oblíbenými nástroji, které se běžně používají pro analýzu dat, strojové učení a trénování AI. [Úvod do Azure virtuální počítač pro datové vědy pro Linux a Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) obsahuje spoustu informací o datové.
   >
   >

1. V **nabízejí stav** sloupec pro bitovou kopii, vyberte **nutné přezkoumání podmínek**.

1. V okně Konfigurovat programové nasazení vyberte **povolit**.

    ![Konfigurovat programové nasazení časový interval](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > Může se zobrazit více předplatných, které jsou uvedené v okně Konfigurovat programové nasazení. Ujistěte se, že chcete povolit programové nasazení pouze pro odpovídající předplatné.
   >
   >


1. Vyberte **Uložit**. 

    V seznamu Image z marketplace, který obrázku nyní **podmínky přijaty** a je k dispozici pro uživatele k vytvoření virtuálních počítačů.

> [!NOTE]
> Uživatelé mohou vytvářet vlastní image z licencované image. Zobrazit [vytvořit vlastní image ze souboru VHD](devtest-lab-create-template.md) Další informace.
>
>


## <a name="related-blog-posts"></a>Související blogové příspěvky

- [Vlastní Image nebo vzorce?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopírování vlastních Imagí mezi Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Další postup

- [Vytvoření vlastní image z virtuálního počítače](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Vytvoření vlastní image ze souboru virtuálního pevného disku](devtest-lab-create-template.md)
- [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)