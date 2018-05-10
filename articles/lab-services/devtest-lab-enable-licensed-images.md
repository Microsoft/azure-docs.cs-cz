---
title: Povolit licencovanou image ve vašem testovacím prostředí v Azure DevTest Labs | Microsoft Docs
description: Informace o povolení licencovanou image v Azure DevTest Labs pomocí portálu Azure
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
ms.openlocfilehash: 0e5de93f8a10d27c28b3f07567f9b6fa7e41d482
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Povolit licencovanou image ve vašem testovacím prostředí v Azure DevTest Labs

V Azure DevTest Labs licencovanou image je ten, který zahrnuje podmínky a ujednání – obvykle od třetích stran –, musí přijmout před bitovou kopii je přístupný pro uživatele v testovacím prostředí. Následující části popisují, jak pracovat s licencovanou Image, aby byly k dispozici pro použití vytváření virtuálních počítačů.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Stanovení, zda je k dispozici uživatelům licencovanou image
Prvním krokem k povolení uživatelům vytvářet virtuální počítače z licencovanou image je zajistit, aby podmínky a ujednání byly přijaty licencovanou bitové kopie. Následující postup ukazuje, jak můžete zobrazit stav nabídka licencovanou image a, v případě potřeby, přijměte jeho podmínky a ujednání.

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **všechny služby**a potom vyberte **DevTest Labs** ze seznamu.

1. Ze seznamu labs vyberte požadované testovací prostředí.  

1. V levém podokně v části **nastavení**, vyberte **konfiguraci a zásady**.

1. V levém podokně v části **základů VIRTUÁLNÍHO počítače**, vyberte **Marketplace image**. 

    ![Položky nabídky Marketplace bitové kopie](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    Zobrazí seznam všech dostupných marketplace obrázků, včetně **nabízejí stav** každé bitové kopie.

    ![Seznam marketplace obrázky znázorňující nabídka stav každé bitové kopie](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    Licencovaný obrázek ukazuje nabídka stav 
    
    - **Podmínky, přijato:** licencovanou image je k dispozici uživatelům k vytvoření virtuálních počítačů. 
    - **Podmínky potřeby zkontrolujte:** licencovanou image není aktuálně k dispozici pro uživatele. Podmínky a ujednání licenčních musí přijmout před testovacího prostředí uživatelů můžete použít k vytvoření virtuálních počítačů. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Zpřístupnění licencovanou image uživatelům testovacího prostředí
Pokud chcete mít jistotu, že licencovanou image je k dispozici uživatelům prostředí, musí vlastník testovacího prostředí s oprávněními správce nejdřív přijmout podmínky a ujednání licencovanou obrázku. Povolení programové nasazení pro předplatné spojené s licencovanou image automaticky přijme právní podmínky a prohlášení o ochraně osobních údajů pro této bitové kopie. [Práce s obrázky Marketplace na Azure Resource Manager](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) poskytuje další informace o programové nasazení bitových kopií marketplace.

Pomocí následujícího postupu můžete povolit programové nasazení licencovanou bitové kopie:

1. V [portál Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), přejděte na seznam **Marketplace image**.

1. Identifikujte licencovanou bitovou kopii, pro které chcete mít přístup k uživatelům ale jehož podmínky nebyly přijaty. Například může zobrazit Data vědecké účely virtuální počítač, který zobrazí stav buď **podmínky přijmout** nebo **podmínky zkontrolujte potřeby**.

    ![Konfigurovat programové nasazení časový interval](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Virtuální počítače, vědecké účely data jsou bitové kopie virtuálního počítače Azure, předinstalovaným, konfiguraci a testovány s několik oblíbených nástrojů, které se běžně používají pro data analytics, machine learning a školení AI. [Úvod do Azure Data vědecké účely virtuálního počítače pro systémy Linux a Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) poskytuje značnou část informace o DSVMs.
   >
   >

1. V **nabízejí stav** této bitové kopie, vyberte **podmínky zkontrolujte potřeby**.

1. V okně Konfigurovat programová nasazení vyberte **povolit**.

    ![Konfigurovat programové nasazení časový interval](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > Může se zobrazit více odběry v seznamu v okně Konfigurovat programová nasazení. Zkontrolujte, zda že chcete povolit programová nasazení pouze pro určený odběr.
   >
   >


1. Vyberte **Uložit**. 

    V seznamu obrázků marketplace, které obrázku teď **podmínky přijmout** a je k dispozici pro uživatele k vytvoření virtuálních počítačů.

> [!NOTE]
> Uživatelé mohou vytvářet vlastní image z licencovanou image. V tématu [vytvořit vlastní image ze souboru virtuálního pevného disku](devtest-lab-create-template.md) Další informace.
>
>


## <a name="related-blog-posts"></a>Příspěvky blogu související

- [Vlastní Image nebo vzorce?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopírování vlastních bitových kopií mezi Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Další postup

- [Vytvořit vlastní image z virtuálního počítače](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Vytvořit vlastní image ze souboru virtuálního pevného disku](devtest-lab-create-template.md)
- [Přidejte virtuální počítač do testovacího prostředí](devtest-lab-add-vm.md)