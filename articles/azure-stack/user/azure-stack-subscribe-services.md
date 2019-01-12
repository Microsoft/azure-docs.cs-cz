---
title: V tomto kurzu se dozvíte, jak k odběru nabídky Azure Stack | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak vytvořit nové předplatné služby Azure Stack a otestovat nabídky vytvořením testovacího virtuálního počítače.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/13/2018
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 4c7c149a9541af0417e7548c78ab3dc9a8084ec6
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247346"
---
# <a name="tutorial-create-and-test-a-subscription"></a>Kurz: vytvoření a otestování předplatné

V tomto kurzu se dozvíte, jak vytvořit předplatné obsahující nabídky a pak ho otestujte. Pro testování Přihlaste se k portálu user portal Azure Stack jako správce cloudu předplacení nabídky a pak vytvořte virtuální počítač.

> [!TIP]
> Pro více pokročilejších vyhodnocení výkon můžete [vytvořte odběr pro určitého uživatele](../azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator) a pak se přihlaste jako tohoto uživatele na portál user portal. 

Tento kurz ukazuje vytvoření odběru na nabídku Azure Stack.

Co se naučíte:

> [!div class="checklist"]
> * Přihlášení k odběru nabídky 
> * Test nabídky

## <a name="subscribe-to-an-offer"></a>Přihlášení k odběru nabídky

K odběru nabídky jako uživatel, přihlášení k portálu user portal pro Azure Stack ke zjišťování služby, které byly zveřejněny operátorem služby Azure Stack.

1. Přihlaste se k portálu a vyberte uživatele **pořiďte si předplatné**.

   ![Získat předplatné](media/azure-stack-subscribe-services/get-subscription.png)

2. Do pole **Zobrazovaný název** zadejte název předplatného. Potom vyberte **nabízejí** zvolit jednu z dostupných nabídek v **zvolit nabídku** oddílu. Potom vyberte **Vytvořit**.

   ![Vytvoření nabídky](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Nyní je nutné aktualizovat portál user portal, začněte využívat vaše předplatné.

3. Chcete-li zobrazit předplatné, které jste vytvořili, vyberte **všechny služby**. Potom v části **Obecné** vyberte kategorii **předplatná**a pak vyberte své nové předplatné. Až se přihlásíte k odběru nabídky, aktualizujte stránku portálu, abyste zjistili, pokud nové služby byly součástí nového předplatného. V tomto příkladu **virtuálních počítačů** byla přidána.

   ![Zobrazení odběru](media/azure-stack-subscribe-services/view-subscription.png)

## <a name="test-the-offer"></a>Test nabídky

Při přihlášení k portálu user portal, můžete otestovat nabídky zřízení virtuálního počítače s využitím nových možností předplatného. 

> [!NOTE]
> Tento test vyžaduje, aby virtuální počítač s Windows serverem 2016 Datacenter nejprve byl přidán do Tržiště Azure Stack. 

1. Přihlaste se k portálu user portal.

2. Na portálu user portal, vyberte **virtuálních počítačů**, pak **přidat**, pak **systému Windows Server 2016 Datacenter**a potom klikněte na tlačítko **vytvořit**.

3. V **Základy** části, zadejte **název**, **uživatelské jméno**, a **heslo**, zvolte **předplatné**, vytvoření **skupiny prostředků** (nebo vyberte existující skupinu) a pak vyberte **OK**.

4. V **zvolte velikost** vyberte **A1 Standard**a potom klikněte na tlačítko **vyberte**.  

5. V **nastavení** okno, přijměte výchozí hodnoty a vyberte **OK**.

6. V **Souhrn** klikněte na tlačítko **OK** k vytvoření virtuálního počítače.  

7. Pokud chcete zobrazit nový virtuální počítač, vyberte **virtuálních počítačů**, vyhledejte nový virtuální počítač a klikněte na jeho název.

    ![Všechny zdroje](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> Nasazení virtuálního počítače trvá několik minut.


## <a name="next-steps"></a>Další postup

Co jste se naučili v tomto kurzu:

> [!div class="checklist"]
> * Přihlášení k odběru nabídky 
> * Test nabídky


> [!div class="nextstepaction"]
> [Vytvoření virtuálního počítače ze šablony komunity](azure-stack-create-vm-template.md)