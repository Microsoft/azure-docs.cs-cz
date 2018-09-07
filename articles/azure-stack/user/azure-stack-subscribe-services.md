---
title: V tomto kurzu se dozvíte, jak k odběru nabídky Azure Stack | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak vytvořit nové předplatné služby Azure Stack a otestovat nabídky vytvořením testovacího virtuálního počítače.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/05/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 0e2fa9b01d27d68c1eab9097a20b6e350ba47f99
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028801"
---
# <a name="tutorial-create-and-test-a-subscription"></a>Kurz: vytvoření a otestování předplatné
V tomto kurzu se dozvíte, jak vytvořit předplatné obsahující nabídky a pak ho otestujte. Pro testování se přihlaste k portálu user portal Azure Stack jako správce cloudu, předplacení nabídky a pak vytvořte virtuální počítač.

> [!TIP]
> Pro více pokročilejších vyhodnocení výkon můžete [vytvořte odběr pro určitého uživatele](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm#create-a-subscription-as-a-cloud-operator) a potom se přihlaste jako tohoto uživatele na portál user portal. 

Tento kurz ukazuje vytvoření odběru na nabídku Azure Stack.

Co se naučíte:

> [!div class="checklist"]
> * Přihlášení k odběru nabídky 
> * Test nabídky

## <a name="subscribe-to-an-offer"></a>Přihlášení k odběru nabídky
K odběru nabídky jako uživatel, budete muset přihlásit k portálu user portal Azure Stack ke zjišťování služby, které byly zveřejněny operátorem služby Azure Stack.

1. Přihlaste se k portálu user portal a klikněte na tlačítko **pořiďte si předplatné**.

   ![Získat předplatné](media/azure-stack-subscribe-services/get-subscription.png)

2. Do pole **Zobrazovaný název** zadejte název předplatného. Potom klikněte na **nabízejí** vyberte jednu z dostupných nabídek v **zvolit nabídku** části a pak klikněte na tlačítko **vytvořit**.

   ![Vytvoření nabídky](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Nyní je nutné aktualizovat portál user portal, začněte využívat vaše předplatné.

3. Chcete-li zobrazit předplatné, které jste vytvořili, klikněte na tlačítko **všechny služby**.  Potom v části **Obecné** vyberte kategorii **předplatná**a pak vyberte své nové předplatné. Až se přihlásíte k odběru nabídky, aktualizujte stránku portálu, abyste zjistili, pokud nové služby byly součástí nového předplatného. V tomto příkladu **virtuálních počítačů** byla přidána.

   ![Zobrazení odběru](media/azure-stack-subscribe-services/view-subscription.png)


## <a name="test-the-offer"></a>Test nabídky
Když jste přihlášení k portálu user portal, můžete otestovat nabídky zřízení virtuálního počítače s využitím nových možností předplatného. 

> [!NOTE]
> Tento test vyžaduje, aby virtuální počítač s Windows serverem 2016 Datacenter nejprve byl přidán do Tržiště Azure Stack. 

1. Přihlaste se k portálu user portal.

2. Na portálu user portal, klikněte na tlačítko **virtuálních počítačů** > **přidat** > **systému Windows Server 2016 Datacenter**a potom klikněte na tlačítko **vytvořit** .

3. V **Základy** části, zadejte **název**, **uživatelské jméno**, a **heslo**, zvolte **předplatné**, vytvoření **skupiny prostředků** (nebo vyberte existující skupinu) a potom klikněte na tlačítko **OK**.

4. V **zvolte velikost** klikněte na tlačítko **A1 Standard**a potom klikněte na tlačítko **vyberte**.  

5. V okně Nastavení přijměte výchozí hodnoty a klikněte na tlačítko **OK**.

6. V **Souhrn** klikněte na tlačítko **OK** k vytvoření virtuálního počítače.  

7. Nový virtuální počítač, klikněte na **virtuálních počítačů**, vyhledejte nový virtuální počítač a klikněte na jeho název.

    ![Všechny zdroje](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> Nasazení virtuálních počítačů bude trvat několik minut.


## <a name="next-steps"></a>Další postup

Co jste se naučili v tomto kurzu:

> [!div class="checklist"]
> * Přihlášení k odběru nabídky 
> * Test nabídky


> [!div class="nextstepaction"]
> [Vytvoření virtuálního počítače ze šablony komunity](azure-stack-create-vm-template.md)