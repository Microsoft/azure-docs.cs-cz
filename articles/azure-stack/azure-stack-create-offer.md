---
title: Vytvořit nabídku v zásobníku Azure | Microsoft Docs
description: Jako správce cloudu zjistěte, jak vytvořit nabídku pro vaše uživatele v Azure zásobníku.
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
ms.date: 03/27/2018
ms.author: brenduns
ms.openlocfilehash: 6a59d0c8144492ef907e5c395f05e4e8a16678a5
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="create-an-offer-in-azure-stack"></a>Vytvoření nabídky ve službě Azure Stack

[Nabízí](azure-stack-key-features.md) jsou skupiny jeden nebo více plánů, která představují zprostředkovatelů pro uživatele se koupit nebo přihlášení k odběru. Tento dokument ukazuje, jak vytvořit nabídku, která zahrnuje [plán, který jste vytvořili](azure-stack-create-plan.md) v posledním kroku. Tato nabídka poskytuje odběratele umožňuje zřizovat virtuální počítače.

1. Přihlaste se k portálu správce Azure zásobníku (https://adminportal.local.azurestack.external) > klikněte na tlačítko **nový** > **klienta nabízí + plány** > **nabízejí**.

   ![](media/azure-stack-create-offer/image01.png)
2. V **nové nabízejí** podokně, vyplňte **zobrazovaný název** a **název prostředku**a poté vyberte nový nebo existující **skupiny prostředků**. Zobrazovaný název je popisný název nabídky. Tento popisný název se jenom informace o nabídka, která se zobrazí uživatelům při přihlášení k odběru. Proto je nutné používat intuitivní název, který pomáhá uživateli pochopit, co se dodává s nabídkou. Název prostředku může vidět jenom správce. Je to název, který správci používají pro práci s nabídkou jako s prostředkem Azure Resource Manageru.

   ![](media/azure-stack-create-offer/image01a.png)
3. Klikněte na tlačítko **základní plány** otevřete **plánování** podokně vyberte plány, které chcete zahrnout do nabídky a pak klikněte na tlačítko **vyberte**. Kliknutím na **Vytvořit** vytvořte svou nabídku.

   ![](media/azure-stack-create-offer/image02.png)
4. Po vytvoření nabídku, můžete změnit její stav. Musí být provedeny nabízí *veřejné* uživatelům získat úplné zobrazení, pokud se přihlášení k odběru. Nabízí může být:
   - **Veřejné**: viditelné pro uživatele.
   - **Privátní**: viditelné do cloudu správci. Užitečné při navrhování plán nebo nabídky, nebo pokud chce správce cloudu [vytvořit každé předplatné pro uživatele](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Vyřazeno z provozu**: Uzavřené pro nové odběratele. Můžete použít Správce cloudu vyřazení zabránit budoucím odběry, ale ponechat nezměněný aktuální odběratele.

   > [!TIP]  
   > Změny nabídku nejsou okamžitě viditelné pro uživatele. Chcete-li zobrazit změny, uživatelé možná muset odhlásit a znovu přihlaste k portálu user portal zobrazíte nové nabídky. 

   Chcete-li změnit stav nabídky: 

   - **Verze 1803 a novější**:  
     V okně Přehled nabídky, klikněte na **usnadnění stavu**, vyberte stav, kterou chcete použít, jako je třeba *veřejné*a potom klikněte na **Uložit**. 
 
     ![Vyberte stav usnadnění přístupu](media/azure-stack-create-offer/change-state.png) 

     Alternativně poté, co otevřete nabídku můžete goto **nabízejí nastavení**a potom vyberte **usnadnění stavu** na změnu stavu. 

   - **Starší než verze 1803**:  
     Klikněte na tlačítko **všechny prostředky**, vyhledejte novou nabídku, klikněte na nabídku nový, klikněte na **změny stavu**a potom klikněte na **veřejné**.

  
   > [!NOTE] 
   > Můžete také použít PowerShell k vytvoření nabídky výchozí, plány a kvóty. Další informace najdete v tématu [Správce služby Azure zásobníku readme](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin).
   >


### <a name="next-steps"></a>Další postup
[Vytvořte odběr](azure-stack-subscribe-plan-provision-vm.md)      
[Zřízení virtuálního počítače](azure-stack-provision-vm.md)
