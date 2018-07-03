---
title: Vytvoření nabídky ve službě Azure Stack | Dokumentace Microsoftu
description: Jako správce cloudu zjistěte, jak vytvořit nabídku pro své uživatele ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/2/2018
ms.author: brenduns
ms.openlocfilehash: eed715a7c2cb967f6c9ea0b7d4442a4f9976bd17
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37345885"
---
# <a name="create-an-offer-in-azure-stack"></a>Vytvoření nabídky ve službě Azure Stack

[Nabízí](azure-stack-key-features.md) jsou skupiny jednoho nebo několika plánů, které poskytovatelé předkládají uživatelům kupovat nebo přihlášení k odběru. Tento dokument ukazuje, jak vytvořit nabídku, která zahrnuje [plán, který jste vytvořili](azure-stack-create-plan.md). Tato nabídka umožňuje odběratelům nastavení virtuálních počítačů.

1. Přihlaste se k portálu Správce služby Azure Stack (https://adminportal.local.azurestack.external) a vyberte **nový** > **nabízí Tenanta + plány** > **nabízejí**.

   ![Vytvoření nabídky](media/azure-stack-create-offer/image01.png)
  
2. V části **nová nabídka**, zadejte **zobrazovaný název** a **název prostředku**a potom v části **skupiny prostředků**vyberte **vytvořit nové** nebo **použít existující**. Zobrazovaný název představuje popisný název pro tuto nabídku. Tento popisný název, je pouze informace o nabídce, která se zobrazí uživatelům při přihlášení k odběru nabídky. Pomocí intuitivního název, který pomáhá uživatelům pomoct pochopit, co se dodává s nabídkou. Název prostředku může vidět jenom správce. Je to název, který správci používají pro práci s nabídkou jako s prostředkem Azure Resource Manageru.

   ![Nová nabídka](media/azure-stack-create-offer/image01a.png)
  
3. Vyberte **základní plány** otevřít **plánování**. Vyberte plány, které chcete do nabídky zahrnout a klikněte na tlačítko **vyberte**. Vytvoření nabídky vyberte **vytvořit**.

   ![Vybrat plán](media/azure-stack-create-offer/image02.png)
  
4. Po vytvoření nabídky, můžete změnit její stav. Nabídky se musí provádět *veřejné* pro uživatele získat úplné zobrazení při přihlášení k odběru. Nabídek může být:

   - **Veřejné**: viditelné pro uživatele.
   - **Privátní**: viditelné jenom pro správce cloudu. Toto nastavení je užitečné při sestavování plánu nebo nabídky, nebo pokud chce správce cloudu [vytvoření každé předplatné pro uživatele](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Vyřazeno z provozu**: Uzavřené pro nové odběratele. Správce cloudu můžete zakázat budoucí předplatných, ale nechat to neovlivní stávající Odběratelé Vyřazená z provozu.

   > [!TIP]  
   > Změny do nabídky se okamžitě viditelné pro uživatele. Aby se změny projevily, uživatelé můžou mít se odhlásit a znovu přihlásit k portálu user portal zobrazíte nové nabídky.

   Chcete-li změnit stav nabídky:

   - **Verze 1803 nebo novější**:  
     V přehledu pro tuto nabídku, vyberte **stav usnadnění**. Zvolte stav, který chcete použít (například *veřejné*) a pak vyberte **Uložit**.
 
     ![Vyberte stav usnadnění](media/azure-stack-create-offer/change-state.png)

     Jako alternativu po získání přístupu v rámci nabídky můžete přejít na **nabízejí nastavení**. Vyberte **stav usnadnění** na změnu stavu.

   - **Starší než verze 1803**:  
     Vyberte **všechny prostředky**, vyhledejte novou nabídku a vyberte nové nabídky. Vyberte **změnit stav**a pak vyberte **veřejné**.

   > [!NOTE]
   > Prostředí PowerShell můžete také vytvořit výchozí nabídek, plánů a kvót. Další informace najdete v tématu [modulem Powershellu pro službu Azure Stack 1.3.0](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0).

## <a name="next-steps"></a>Další postup

- [Vytvořit odběry](azure-stack-subscribe-plan-provision-vm.md)
- [Zřízení virtuálního počítače](azure-stack-provision-vm.md)
