---
title: Povolte ve svém testovacím prostředí licencovaný obraz v Azure DevTest Labs | Microsoft Docs
description: Naučte se, jak povolit licencovanou image v Azure DevTest Labs pomocí Azure Portal
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "61294213"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Povolte ve svém testovacím prostředí licencovaný obraz v Azure DevTest Labs

V Azure DevTest Labs je licencovaná image taková, která zahrnuje podmínky a ujednání – obvykle od třetí strany – to je potřeba přijmout, než bude image přístupná uživatelům v testovacím prostředí. Následující části popisují, jak pracovat s licencovanými bitovými kopiemi, aby byly k dispozici pro použití při vytváření virtuálních počítačů.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Zjištění, jestli je k dispozici licencovaná image pro uživatele
Prvním krokem, který uživatelům umožňuje vytvářet virtuální počítače z licencované image, je zajistit, aby byly podmínky a ujednání přijaté pro licencovanou bitovou kopii. Následující kroky ukazují, jak můžete zobrazit stav nabídky licencované image a v případě potřeby přijmout podmínky a ujednání.

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **všechny služby**a v seznamu vyberte **DevTest Labs** .

1. V seznamu cvičení vyberte požadované testovací prostředí.  

1. Na levém panelu v části **Nastavení**vyberte **Konfigurace a zásady**.

1. Na levém panelu v části **základy virtuálních počítačů**vyberte **Image Marketplace**. 

    ![Položka nabídky imagí Marketplace](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    Zobrazí se seznam všech dostupných imagí na webu Marketplace, včetně **stavu nabídky** pro jednotlivé obrázky.

    ![Seznam imagí Marketplace znázorňující stav nabídky pro každý obrázek](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    Licencovaná image zobrazuje stav nabídky 
    
    - **Přijaté výrazy:** licencovaná Image je k dispozici uživatelům k vytváření virtuálních počítačů. 
    - **Nutná kontrola podmínek:** licencovaná bitová kopie není aktuálně k dispozici pro uživatele. Podmínky a ujednání licence musí být přijaty předtím, než je uživatelé testovacího prostředí můžou použít k vytvoření virtuálních počítačů. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Zpřístupnění licencované image uživatelům testovacího prostředí
Aby bylo zajištěno, že je k dispozici licencovaná bitová kopie pro uživatele testovacího prostředí, musí nejdřív vlastník testovacího prostředí s oprávněním správce přijmout podmínky a ujednání pro tuto licencovanou image. Povolení programového nasazení pro předplatné přidružené k licencované imagi automaticky přijímá právní podmínky a prohlášení o ochraně osobních údajů pro tuto image. [Práce s imagemi Marketplace na Azure Resource Manager](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) poskytuje další informace o programovém nasazení imagí z Marketplace.

Pomocí následujícího postupu můžete povolit programové nasazení pro licencovanou Image:

1. V [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)otevřete seznam **imagí na webu Marketplace**.

1. Identifikujte licencovaný obrázek, pro který chcete, aby měli uživatelé přístup, ale jejichž výrazy nebyly přijaty. Může se například zobrazit Data Science Virtual Machine, který zobrazuje stav buď **přijatých podmínek** , nebo je **potřeba přezkoumání podmínek**.

    ![Konfigurovat okno programového nasazení](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Virtuální počítače pro datové vědy jsou image virtuálních počítačů Azure, předem nainstalované, nakonfigurované a testované pomocí několika oblíbených nástrojů, které se běžně používají pro analýzu dat, strojové učení a školení AI. [Úvod do Azure Data Science Virtual Machine pro Linux a Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) přináší skvělé informace o DSVMs.
   >
   >

1. Ve sloupci **stav nabídky** pro obrázek vyberte požadované **výrazy nutná kontrola**.

1. V okně Konfigurace programového nasazení vyberte **Povolit**.

    ![Konfigurovat okno programového nasazení](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > V okně Konfigurovat programové nasazení se může zobrazit několik předplatných. Ujistěte se, že povolujete programové nasazení pouze pro zamýšlené předplatné.
   >
   >


1. Vyberte **Uložit**. 

    V seznamu imagí z Marketplace teď tento obrázek zobrazuje **přijaté výrazy** a je k dispozici uživatelům k vytváření virtuálních počítačů.

> [!NOTE]
> Uživatelé můžou z licencované image vytvořit vlastní image. Další informace najdete v tématu [Vytvoření vlastní image ze souboru VHD](devtest-lab-create-template.md) .
>
>


## <a name="related-blog-posts"></a>Související blogové příspěvky

- [Vlastní obrázky nebo vzorce?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopírování vlastních imagí mezi Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Další kroky

- [Vytvoření vlastní image z virtuálního počítače](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Vytvoření vlastní image ze souboru VHD](devtest-lab-create-template.md)
- [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)