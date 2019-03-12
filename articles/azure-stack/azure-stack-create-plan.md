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
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: a2ac138228b7b54f486acb0f42975748136a8da7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759465"
---
# <a name="create-a-plan-in-azure-stack"></a>Vytvoření plánu ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

[Plány](azure-stack-key-features.md) představují seskupení jedné nebo více služeb a jejich kvóty. Jako poskytovatel můžete vytvořit plány, které nabídnete uživatelům. Uživatelé pak předplatit nabídek, aby mohli používat plány, služby a kvóty, které patří mezi ně. Tento příklad ukazuje, jak vytvořit plán, který obsahuje výpočty, síť a poskytovatele prostředků úložiště. Tento plán poskytuje předplatitele možnost zřizování virtuálních počítačů.

## <a name="create-a-plan-1902-and-later"></a>Vytvoření plánu (1902 a novější)

1. Přihlaste se k [portálu Správce služby Azure Stack](https://adminportal.local.azurestack.external).

2. Chcete-li vytvořit plán a nabídku, uživatelé mohou přihlásit k odběru, vyberte **+ vytvořit prostředek**, pak **nabízí + plány**, pak **plán**.
  
   ![Vybrat plán](media/azure-stack-create-plan/select-plan.png)

3. Zobrazí se záložkami uživatelské rozhraní, která umožňuje zadat název plánu, přidat služby a definovat kvóty pro každou z vybraných služeb. Co je nejdůležitější můžete zkontrolovat podrobnosti o nabídku, kterou vytvoříte, než se rozhodnete k jeho vytvoření.

   V části **Základy** karty **nový plán** okno, zadejte **zobrazovaný název** a **název prostředku**. Zobrazovaný název je popisný název plánu, který můžete zobrazit operátory. Všimněte si, že na portálu pro správce, podrobnosti o plánu jsou viditelné pouze pro operátory.

   ![Zadejte podrobnosti](media/azure-stack-create-plan/plan-name.png)

4. Vytvořte nový **skupiny prostředků**, nebo vyberte existující jako kontejner plánu.

   ![Zadejte skupinu prostředků](media/azure-stack-create-plan/resource-group.png)

5. Vyberte **služby** kartu a pak zaškrtněte políčko pro **Microsoft.Compute**, **Microsoft.Network**, a **Microsoft.Storage** .
  
   ![Vybrat služby](media/azure-stack-create-plan/services.png)

6. Vyberte **kvóty** kartu. Vedle položky **Microsoft.Storage**, zvolte výchozí kvóta v rozevíracím seznamu, nebo vyberte **vytvořit nový** k vytvoření vlastní kvóty.
  
   ![Kvóty](media/azure-stack-create-plan/quotas.png)

7. Pokud vytváříte novou kvótu, zadejte **název** u kvóty a poté zadejte hodnoty kvóty. Vyberte **OK** k vytvoření této kvóty.

   ![Nová kvóta](media/azure-stack-create-plan/new-quota.png)

8. Zopakujte kroky 6 a 7 můžete vytvořit a přiřadit kvóty pro **Microsoft.Network** a **Microsoft.Compute**. Všechny tři služby mají přiřazeny kvóty, budete vypadají podobně jako následující příklad.

   ![Kompletní kvóty přiřazení](media/azure-stack-create-plan/all-quotas-assigned.png)

9. Vyberte **zkontrolujte + vytvořit** k revizi plánu. Zkontrolujte všechny hodnoty a kvóty, které se ujistěte, že jsou správné. Všimněte si rozšíření šipky vlevo od každý pár služby a kvóty. Nová funkce umožňuje rozšířit kvót ve vybraných plánech, postupně, chcete-li zobrazit podrobnosti o každé kvóty v plánu a vraťte se do proveďte veškeré nezbytné úpravy.

   ![Vytvoření plánu](media/azure-stack-create-plan/create.png)

10. Až budete připravení, vyberte **vytvořit** vytvořte plán.

11. Pokud chcete zobrazit nový plán, vyberte **plány**, vyhledejte v plánu a vyberte jeho název. Pokud je dlouhý seznam prostředků, použijte **hledání** najít váš plán podle názvu.

## <a name="create-a-plan-1901-and-earlier"></a>Vytvoření plánu (1901 a starší)

1. Přihlaste se k [portálu Správce služby Azure Stack](https://adminportal.local.azurestack.external).

2. Chcete-li vytvořit plán a nabídku, uživatelé mohou přihlásit k odběru, vyberte **+ vytvořit prostředek**, pak **nabízí + plány**, pak **plán**.
  
   ![Vybrat plán](media/azure-stack-create-plan/select-plan1901.png)

3. V části **nový plán**, zadejte **zobrazovaný název** a **název prostředku**. Zobrazovaný název je popisný název plánu, který uživatelé uvidí. Název prostředku, který správci používají pro práci s plánem jako s prostředkem Azure Resource Manageru můžete vidět jenom správce.

   ![Zadejte podrobnosti](media/azure-stack-create-plan/plan-name1901.png)

4. Vytvořte nový **skupiny prostředků**, nebo vyberte existující jako kontejner plánu.

   ![Zadejte skupinu prostředků](media/azure-stack-create-plan/resource-group1901.png)

5. Vyberte **služby** a pak zaškrtněte políčko pro **Microsoft.Compute**, **Microsoft.Network**, a **Microsoft.Storage**. Dále zvolte **vyberte** uložte konfiguraci. Po umístění ukazatele myši nad jednotlivé možnosti se zobrazí zaškrtávací políčka.
  
   ![Vybrat služby](media/azure-stack-create-plan/services1901.png)

6. Vyberte **kvóty**, **Microsoft.Storage (místní)** a klikněte na tlačítko buď výchozí kvótu, nebo vyberte **vytvořit novou kvótu** k vytvoření vlastní kvóty.
  
   ![Kvóty](media/azure-stack-create-plan/quotas1901.png)

7. Pokud vytváříte novou kvótu, zadejte **název** u kvóty > zadejte hodnoty kvóty > vyberte **OK**. **Vytvořit kvótu** dialogové okno se zavře.

   ![Nová kvóta](media/azure-stack-create-plan/new-quota1901.png)

   Potom vyberte nové kvóty, které jste vytvořili. Vyberte kvótu přiřadí ji a zavře dialogové okno pro výběr.
  
   ![Přiřaďte kvóty](media/azure-stack-create-plan/assign-quota1901.png)

8. Zopakujte kroky 6 a 7 můžete vytvořit a přiřadit kvóty pro **Microsoft.Network (místní)** a **Microsoft.Compute (místní)**. Všechny tři služby mají přiřazeny kvóty, budete vypadají podobně jako následující příklad.

   ![Kompletní kvóty přiřazení](media/azure-stack-create-plan/all-quotas-assigned1901.png)

9. V části **kvóty**, zvolte **OK**a potom v části **nový plán**, zvolte **vytvořit** vytvořte plán.

    ![Vytvoření plánu](media/azure-stack-create-plan/create1901.png)

10. Pokud chcete zobrazit nový plán, vyberte **všechny prostředky**, vyhledejte v plánu a vyberte jeho název. Pokud je dlouhý seznam prostředků, použijte **hledání** najít váš plán podle názvu.

    ![Zkontrolujte plán](media/azure-stack-create-plan/plan-overview1901.png)

## <a name="next-steps"></a>Další postup

* [Vytvoření nabídky](azure-stack-create-offer.md)
