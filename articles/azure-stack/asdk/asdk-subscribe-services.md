---
title: "V tomto kurzu zjistíte, jak k odběru nabídku Azure zásobníku | Microsoft Docs"
description: "V tomto kurzu se dozvíte, jak vytvořit nový odběr ke službám Azure zásobníku a otestovat nabídku vytvořením testovacího virtuálního počítače."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: d2adda5613b9a10bc3314045b9d68b0f3196f19a
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-create-and-test-a-subscription"></a>Kurz: vytvoření a otestování předplatné
V tomto kurzu se dozvíte, jak vytvořit odběr obsahující nabídku a pak ho otestovat. Pro test, budou přihlašovat do [portálu user portal](https://portal.local.azurestack.external) jako správce a cloudové předplatné na nabídku a pak vytvořit virtuální počítač.

> [!TIP]
> Pro více pokročilejší prostředí vyhodnocení, můžete [vytvořit odběr pro určitého uživatele](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm#create-a-subscription-as-a-cloud-operator) a pak Přihlaste se jako tento uživatel v portálu user portal. 

V tomto kurzu se dozvíte, jak k odběru nabídku vytvořené v [předchozí kurzu](asdk-offer-services.md).

Co se dozvíte:

> [!div class="checklist"]
> * Přihlášení k odběru nabídky 
> * Testování nabídky

## <a name="subscribe-to-an-offer"></a>Přihlášení k odběru nabídky
K odběru nabídku jako uživatel, musíte se přihlásit k portálu user portal zásobník Azure ke zjišťování služby, které byly nabídnuty operátorem zásobník Azure.

1. Přihlaste se k [portálu user portal](https://portal.local.azurestack.external) a klikněte na tlačítko **získat předplatné**.

   ![Získat předplatné](media/asdk-subscribe-services/get-subscription.png)

2. Do pole **Zobrazovaný název** zadejte název předplatného. Potom klikněte na **nabízejí** vyberte jednu z nabídky v **zvolte nabídku** části a pak klikněte na **vytvořit**.

   ![Vytvoření nabídky](media/asdk-subscribe-services/create-subscription.png)

   > [!TIP]
   > Nyní je nutné aktualizovat portálu user portal k použití vašeho předplatného.

3. Chcete-li zobrazit předplatné, kterou jste právě vytvořili, klikněte na tlačítko **další služby**, klikněte na tlačítko **odběry**, pak klikněte na nové předplatné. Jakmile se přihlásíte k nabídku, aktualizujte portálu, abyste zjistili, pokud byly nové služby součástí nové předplatné. V tomto příkladu **virtuální počítače** byla přidána.

   ![Zobrazení odběru](media/asdk-subscribe-services/view-subscription.png)


## <a name="test-the-offer"></a>Testování nabídky
Při přihlášení k [portálu user portal](https://portal.local.azurestack.external), můžete otestovat nabídku zřízení virtuálního počítače pomocí nové funkce, které předplatné. 

> [!NOTE]
> Tento test vyžaduje bitovou kopii virtuálního počítače Windows serveru 2016 Datacenter, která byla přidána do zásobníku Azure marketplace v [předchozí kurzu](asdk-marketplace-item.md). 

1. Přihlaste se k [portálu user portal](https://portal.local.azurestack.external).

2. V portálu user portal, klikněte na tlačítko **virtuální počítače** > **přidat** > **Windows Server 2016 Datacenter**a pak klikněte na tlačítko **vytvořit** .

3. V **Základy** zadejte **název**, **uživatelské jméno**, a **heslo**, vyberte **předplatné**, vytvoření **skupiny prostředků** (nebo vyberte nějaký existující) a potom klikněte na **OK**.

4. V **zvolte velikost** klikněte na tlačítko **A1 standardní**a potom klikněte na **vyberte**.  

5. V okně Nastavení přijměte výchozí hodnoty a klikněte na tlačítko **OK**.

6. V **Souhrn** klikněte na tlačítko **OK** k vytvoření virtuálního počítače.  

7. Chcete-li zobrazit nový virtuální počítač, klikněte na tlačítko **virtuální počítače**, vyhledejte nový virtuální počítač a klikněte na jeho název.

    ![Všechny zdroje](media/asdk-subscribe-services/view-vm.png)

> [!NOTE]
> Nasazení virtuálního počítače bude trvat několik minut.


## <a name="next-steps"></a>Další postup

Co jste se naučili v tomto kurzu:

> [!div class="checklist"]
> * Přihlášení k odběru nabídky 
> * Testování nabídky

Přechodu na v dalším kurzu se dozvíte, jak vytvořit virtuální počítač pomocí šablony.

> [!div class="nextstepaction"]
> [Vytvoření virtuálního počítače ze šablony](asdk-create-vm-template.md)