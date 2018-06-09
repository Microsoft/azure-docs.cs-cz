---
title: V tomto kurzu zjistíte, jak k odběru nabídku Azure zásobníku | Microsoft Docs
description: V tomto kurzu se dozvíte, jak vytvořit nový odběr ke službám Azure zásobníku a otestovat nabídku vytvořením testovacího virtuálního počítače.
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
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 96b82ede71e7957105dce25096d7873ee876211f
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238364"
---
# <a name="tutorial-create-and-test-a-subscription"></a>Kurz: vytvoření a otestování předplatné
V tomto kurzu se dozvíte, jak vytvořit odběr obsahující nabídku a pak ho otestovat. Pro test Přihlaste se k portálu user portal zásobník Azure jako správce cloudu, přihlášení k odběru na nabídku a pak vytvořit virtuální počítač.

> [!TIP]
> Pro více pokročilejší prostředí vyhodnocení, můžete [vytvořit odběr pro určitého uživatele](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm#create-a-subscription-as-a-cloud-operator) a pak Přihlaste se jako tento uživatel v portálu user portal. 

V tomto kurzu se dozvíte, jak k odběru nabídku Azure zásobníku.

Co se dozvíte:

> [!div class="checklist"]
> * Přihlášení k odběru nabídky 
> * Testování nabídky

## <a name="subscribe-to-an-offer"></a>Přihlášení k odběru nabídky
K odběru nabídku jako uživatel, musíte se přihlásit k portálu user portal zásobník Azure ke zjišťování služby, které byly nabídnuty operátorem zásobník Azure.

1. Přihlaste se k portálu user portal a klikněte na tlačítko **získat předplatné**.

   ![Získat předplatné](media/azure-stack-subscribe-services/get-subscription.png)

2. Do pole **Zobrazovaný název** zadejte název předplatného. Potom klikněte na **nabízejí** vyberte jednu z nabídky v **zvolte nabídku** části a pak klikněte na **vytvořit**.

   ![Vytvoření nabídky](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Nyní je nutné aktualizovat portálu user portal k použití vašeho předplatného.

3. Chcete-li zobrazit předplatné, kterou jste právě vytvořili, klikněte na tlačítko **další služby**, klikněte na tlačítko **odběry**, pak klikněte na nové předplatné. Jakmile se přihlásíte k nabídku, aktualizujte portálu, abyste zjistili, pokud byly nové služby součástí nové předplatné. V tomto příkladu **virtuální počítače** byla přidána.

   ![Zobrazení odběru](media/azure-stack-subscribe-services/view-subscription.png)


## <a name="test-the-offer"></a>Testování nabídky
Při přihlášení k portálu user portal, můžete otestovat nabídku zřízení virtuálního počítače pomocí nové funkce, které předplatné. 

> [!NOTE]
> Tento test vyžaduje, aby virtuální počítač s Windows Server 2016 Datacenter nejprve se přidal do zásobníku Azure marketplace. 

1. Přihlaste se k portálu user portal.

2. V portálu user portal, klikněte na tlačítko **virtuální počítače** > **přidat** > **Windows Server 2016 Datacenter**a pak klikněte na tlačítko **vytvořit** .

3. V **Základy** zadejte **název**, **uživatelské jméno**, a **heslo**, vyberte **předplatné**, vytvoření **skupiny prostředků** (nebo vyberte nějaký existující) a potom klikněte na **OK**.

4. V **zvolte velikost** klikněte na tlačítko **A1 standardní**a potom klikněte na **vyberte**.  

5. V okně Nastavení přijměte výchozí hodnoty a klikněte na tlačítko **OK**.

6. V **Souhrn** klikněte na tlačítko **OK** k vytvoření virtuálního počítače.  

7. Chcete-li zobrazit nový virtuální počítač, klikněte na tlačítko **virtuální počítače**, vyhledejte nový virtuální počítač a klikněte na jeho název.

    ![Všechny zdroje](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> Nasazení virtuálního počítače bude trvat několik minut.


## <a name="next-steps"></a>Další postup

Co jste se naučili v tomto kurzu:

> [!div class="checklist"]
> * Přihlášení k odběru nabídky 
> * Testování nabídky


> [!div class="nextstepaction"]
> [Vytvoření virtuálního počítače ze šablony komunity](azure-stack-create-vm-template.md)