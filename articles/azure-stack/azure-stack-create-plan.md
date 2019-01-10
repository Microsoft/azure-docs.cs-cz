---
title: Vytvoření plánu ve službě Azure Stack | Dokumentace Microsoftu
description: Jako správce cloudu vytvořte plán, který umožňuje odběratelům zřizování virtuálních počítačů.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 6399e1838b00fc533ab42a76e4f1f1667e3a434a
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159515"
---
# <a name="create-a-plan-in-azure-stack"></a>Vytvoření plánu ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

[Plány](azure-stack-key-features.md) představují seskupení jedné nebo více služeb. Jako poskytovatel můžete vytvořit plány, které nabídnete uživatelům. Uživatelé pak předplatit nabídek, aby mohli používat plány a služby, které patří mezi ně. Tento příklad ukazuje, jak vytvořit plán, který obsahuje výpočty, síť a poskytovatele prostředků úložiště. Tento plán poskytuje předplatitele možnost zřizování virtuálních počítačů.

1. Přihlaste se k [portálu Správce služby Azure Stack](https://adminportal.local.azurestack.external).

2. Chcete-li vytvořit plán a nabídku, uživatelé mohou přihlásit k odběru, vyberte **+ vytvořit prostředek**, pak **nabízí + plány**, pak **plán**.
  
   ![Vybrat plán](media/azure-stack-create-plan/select-plan.png)

3. V části **nový plán**, zadejte **zobrazovaný název** a **název prostředku**. Zobrazovaný název je popisný název plánu, který uživatelé uvidí. Název prostředku, který správci používají pro práci s plánem jako s prostředkem Azure Resource Manageru můžete vidět jenom správce.

   ![Zadejte podrobnosti](media/azure-stack-create-plan/plan-name.png)

4. Vytvořte nový **skupiny prostředků**, nebo vyberte existující jako kontejner plánu.

   ![Zadejte skupinu prostředků](media/azure-stack-create-plan/resource-group.png)

5. Vyberte **služby** a pak zaškrtněte políčko pro **Microsoft.Compute**, **Microsoft.Network**, a **Microsoft.Storage**. Dále zvolte **vyberte** uložte konfiguraci. Po umístění ukazatele myši nad jednotlivé možnosti se zobrazí zaškrtávací políčka.
  
   ![Vybrat služby](media/azure-stack-create-plan/services.png)

6. Vyberte **kvóty**, **Microsoft.Storage (místní)** a klikněte na tlačítko buď výchozí kvótu, nebo vyberte **vytvořit novou kvótu** k vytvoření vlastní kvóty.
  
   ![Kvóty](media/azure-stack-create-plan/quotas.png)

7. Pokud vytváříte novou kvótu, zadejte **název** u kvóty > zadejte hodnoty kvóty > vyberte **OK**. **Vytvořit kvótu** dialogové okno se zavře.

   ![Nová kvóta](media/azure-stack-create-plan/new-quota.png)

   Potom vyberte nové kvóty, které jste vytvořili. Vyberte kvótu přiřadí ji a zavře dialogové okno pro výběr.
  
   ![Přiřaďte kvóty](media/azure-stack-create-plan/assign-quota.png)

8. Zopakujte kroky 6 a 7 můžete vytvořit a přiřadit kvóty pro **Microsoft.Network (místní)** a **Microsoft.Compute (místní)**. Všechny tři služby mají přiřazeny kvóty, budete vypadají podobně jako následující příklad.

   ![Kompletní kvóty přiřazení](media/azure-stack-create-plan/all-quotas-assigned.png)

9. V části **kvóty**, zvolte **OK**a potom v části **nový plán**, zvolte **vytvořit** vytvořte plán.

    ![Vytvoření plánu](media/azure-stack-create-plan/create.png)

10. Pokud chcete zobrazit nový plán, vyberte **všechny prostředky**, vyhledejte v plánu a vyberte jeho název. Pokud je dlouhý seznam prostředků, použijte **hledání** najít váš plán podle názvu.

   ![Zkontrolujte plán](media/azure-stack-create-plan/plan-overview.png)

## <a name="next-steps"></a>Další postup

* [Vytvoření nabídky](azure-stack-create-offer.md)
