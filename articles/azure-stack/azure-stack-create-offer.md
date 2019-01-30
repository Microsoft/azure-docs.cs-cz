---
title: Vytvoření nabídky ve službě Azure Stack | Dokumentace Microsoftu
description: Jako správce cloudu zjistěte, jak vytvořit nabídku pro své uživatele ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 230415b84f06beac549693d49055d7cb998163d8
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251448"
---
# <a name="create-an-offer-in-azure-stack"></a>Vytvoření nabídky ve službě Azure Stack

[Nabízí](azure-stack-key-features.md) jsou skupiny jednoho nebo několika plánů, které poskytovatelé předkládají pro uživatele, které tito uživatelé mohou koupit nebo přihlášení k odběru. Tento článek popisuje postup vytvoření nabídky, která zahrnuje [plán, který jste vytvořili](azure-stack-create-plan.md). Tato nabídka umožňuje předplatitele nastavení virtuálních počítačů (VM).

1. Přihlaste se k [portálu Správce služby Azure Stack](https://adminportal.local.azurestack.external) a vyberte **+ vytvořit prostředek**, pak **nabízí Tenanta + plány**a potom **nabízejí**.

   ![Vytvoření nabídky](media/azure-stack-create-offer/image01.png)
  
2. V části **nová nabídka**, zadejte **zobrazovaný název** a **název prostředku**a potom v části **skupiny prostředků**vyberte **vytvořit nové** nebo **použít existující**. Zobrazovaný název představuje popisný název pro tuto nabídku. Tento popisný název, je pouze informace o nabídce, která se uživatelům zobrazí při přihlášení k odběru nabídky. Pomocí intuitivního název, který pomáhá uživatelům pomoct pochopit, co se dodává s nabídkou. Pouze správce uvidí název prostředku. Je to název, který správci používají pro práci s nabídkou jako s prostředkem Azure Resource Manageru.

   ![Nová nabídka](media/azure-stack-create-offer/image01a.png)
  
3. Vyberte **základní plány** otevřít **plánování**. Vyberte plány, které chcete do nabídky zahrnout a klikněte na tlačítko **vyberte**. Vytvoření nabídky vyberte **vytvořit**.

   ![Vybrat plán](media/azure-stack-create-offer/image02.png)
  
4. Po vytvoření nabídky, můžete změnit její stav. Nabídky se musí provádět **veřejné** pro uživatele získat úplné zobrazení při přihlášení k odběru. Nabídek může být:

   - **Veřejné**: Viditelné pro uživatele.
   - **Privátní**: Viditelné pouze pro správce cloudu. Toto nastavení je užitečné při sestavování plánu nebo nabídky, nebo pokud chce správce cloudu [vytvoření každé předplatné pro uživatele](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Vyřazení z provozu**: Uzavřené pro nové odběratele. Správce cloudu umožňuje vyřadit z provozu nabídek zabránit budoucím předplatných, ale nechat to neovlivní stávající Odběratelé.

   > [!TIP]  
   > Změny do nabídky nejsou okamžitě viditelné pro uživatele. Aby se změny projevily, uživatelé můžou mít se odhlásit a znovu přihlásit k portálu user portal zobrazíte nové nabídky.

   Na obrazovce Přehled pro tuto nabídku, vyberte **stav usnadnění**. Zvolte stav, který chcete použít (například **veřejné**) a pak vyberte **Uložit**.

     ![Vyberte stav](media/azure-stack-create-offer/change-stage-1807.png)

     Jako alternativu, vyberte **změnit stav** a klikněte na tlačítko stavu.

    ![Vyberte stav usnadnění](media/azure-stack-create-offer/change-stage-select-1807.png)

   > [!NOTE]
   > Prostředí PowerShell můžete také vytvořit výchozí nabídek, plánů a kvót. Další informace najdete v tématu [modulem Powershellu pro službu Azure Stack 1.4.0](/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0).

## <a name="next-steps"></a>Další postup

- [Vytvořit odběry](azure-stack-subscribe-plan-provision-vm.md)
- [Zřízení virtuálního počítače](azure-stack-provision-vm.md)
