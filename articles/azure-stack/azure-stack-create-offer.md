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
ms.date: 06/07/2018
ms.author: brenduns
ms.openlocfilehash: e5b96a9464bf4d0e3b69d2f635da32c6648ce793
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247513"
---
# <a name="create-an-offer-in-azure-stack"></a>Vytvoření nabídky ve službě Azure Stack

[Nabízí](azure-stack-key-features.md) jsou skupiny jeden nebo více plánů, které zprostředkovatelé prezentovat uživatelům koupit nebo přihlášení k odběru. Tento dokument ukazuje, jak vytvořit nabídku, která zahrnuje [plán, který jste vytvořili](azure-stack-create-plan.md). Tato nabídka dává možnost nastavit virtuální počítače odběratele.

1. Přihlaste se k portálu správce Azure zásobníku (https://adminportal.local.azurestack.external) a vyberte **nový** > **klienta nabízí + plány** > **nabízejí**.

   ![Vytvoření nabídky](media/azure-stack-create-offer/image01.png)
  
2. V části **nové nabízejí**, zadejte **zobrazovaný název** a **název prostředku**a potom v části **skupiny prostředků**, vyberte **vytvořit nové** nebo **použít existující**. Zobrazovaný název je popisný název nabídky. Tento popisný název, je jenom informace o nabídka, která se zobrazí uživatelům při jejich přihlášení k odběru na nabídku. Použijte intuitivní název, který pomáhá uživatelům pochopit, co se dodává s nabídkou. Název prostředku může vidět jenom správce. Je to název, který správci používají pro práci s nabídkou jako s prostředkem Azure Resource Manageru.

   ![Nová nabídka](media/azure-stack-create-offer/image01a.png)
  
3. Vyberte **základní plány** otevřete **plánování**. Vyberte plány, které chcete zahrnout do nabídky a potom zvolte **vyberte**. Chcete-li vytvořit nabídky vyberte **vytvořit**.

   ![Vyberte plán](media/azure-stack-create-offer/image02.png)
  
4. Po vytvoření nabídku, můžete změnit její stav. Musí být provedeny nabízí *veřejné* uživatelům získat úplné zobrazení, pokud se přihlášení k odběru. Nabízí může být:

   - **Veřejné**: viditelné pro uživatele.
   - **Privátní**: viditelné do cloudu správci. Toto nastavení je užitečné při navrhování plán nebo nabídky, nebo pokud chce správce cloudu [vytvořit každé předplatné pro uživatele](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Vyřazeno z provozu**: Uzavřené pro nové odběratele. Můžete použít Správce cloudu vyřazení zabránit budoucím odběry, ale nechat aktuální odběratele neovlivní.

   > [!TIP]  
   > Změny nabídku nejsou okamžitě viditelné pro uživatele. Chcete-li zobrazit změny, uživatelé možná muset odhlásit a znovu přihlaste k portálu user portal zobrazíte nové nabídky.

   Chcete-li změnit stav nabídky:

   - **Verze 1803 a novější**:  
     V přehledu nabídky, vyberte **usnadnění stavu**. Vyberte stav, který chcete použít (například *veřejné*) a potom vyberte **Uložit**.
 
     ![Vyberte stav usnadnění přístupu](media/azure-stack-create-offer/change-state.png)

     Jako alternativu, poté, co otevřete nabídku můžete přejít na **nabízejí nastavení**. Vyberte **usnadnění stavu** na změnu stavu.

   - **Starší než verze 1803**:  
     Vyberte **všechny prostředky**, vyhledejte novou nabídku a pak vyberte novou nabídku. Vyberte **změny stavu**a potom vyberte **veřejné**.

   > [!NOTE]
   > Můžete také použít PowerShell k vytvoření nabídky výchozí, plány a kvóty. Další informace najdete v tématu [Správce služby Azure zásobníku readme](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin).

## <a name="next-steps"></a>Další postup

- [Vytvořte odběr](azure-stack-subscribe-plan-provision-vm.md)
- [Zřízení virtuálního počítače](azure-stack-provision-vm.md)
