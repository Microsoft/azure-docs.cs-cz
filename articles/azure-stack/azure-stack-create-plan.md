---
title: Vytvoření plánu v zásobníku Azure | Microsoft Docs
description: Jako správce cloudu vytvořte plán, který umožňuje odběratelům zřizování virtuálních počítačů.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/23/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: b1bfff16c4f51a9fa53204930df78cbd2cf19b8d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="create-a-plan-in-azure-stack"></a>Vytvoření plánu ve službě Azure Stack

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

[Plány](azure-stack-key-features.md) představují seskupení jedné nebo více služeb. Jako zprostředkovatele můžete vytvořit plány, které nabízejí uživatelům. Pak vaši uživatelé přihlásit k vaší nabídky pro plány a službách, které obsahují. Tento příklad ukazuje, jak vytvořit plán, který obsahuje výpočty, síť a zprostředkovatelé prostředků služby storage. Tento plán poskytuje odběratele umožňuje zřizovat virtuální počítače.

1. Přihlaste se k portálu správce Azure zásobníku (https://adminportal.local.azurestack.external).

2. Chcete-li vytvořit plán a nabídka, která uživatelé mohou přihlásit k odběru, vyberte **nový** > **nabízí + plány** > **plán**.  
   ![Vyberte plán](media/azure-stack-create-plan/select-plan.png)

3. V **nový plán** podokně, vyplňte **zobrazovaný název** a **název prostředku**. Zobrazovaný název je popisný název plánu, který uživatelé uvidí. Pouze správce můžete vidět název prostředku, který je název, který správci používat pro práci s plánem jako prostředek Azure Resource Manager.  
   ![Zadejte podrobnosti](media/azure-stack-create-plan/plan-name.png)

4. Vytvořte novou **skupiny prostředků**, nebo vyberte existující jako kontejner pro plán.  
   ![Zadejte skupinu prostředků](media/azure-stack-create-plan/resource-group.png)

5. Vyberte **služby** a pak zaškrtněte políčko pro **Microsoft.Compute**, **Microsoft.Network**, a **Microsoft.Storage**. V dalším kroku vyberte **vyberte** konfiguraci uložíte. Když ukazatel myši nachází jednotlivé možnosti zobrazí zaškrtávací políčka.  
   ![Vybrat služby](media/azure-stack-create-plan/services.png)

6. Vyberte **kvóty**, **Microsoft.Storage (místní)** a potom vyberte buď výchozí kvótu, nebo vyberte **vytvořit nové kvóty** přizpůsobit kvótu.  
   ![Kvóty](media/azure-stack-create-plan/quotas.png)

7. Pokud vytváříte novou kvótu, zadejte **název** pro kvótu > zadejte hodnoty kvóty > vyberte **OK**. **Vytvořit kvótu** podokno se zavře.
   ![Nové kvóty](media/azure-stack-create-plan/new-quota.png)

   Pak vyberte nový kvótu, kterou jste vytvořili. Výběr kvótu přiřadí ji a zavře podokno pro výběr.  
   ![Přiřadit kvótu](media/azure-stack-create-plan/assign-quota.png)

8. Opakujte kroky 6 a 7 můžete vytvořit a přiřadit kvóty pro **Microsoft.Network (místní)** a **Microsoft.Compute (místní)**.  Pokud všechny tři služby kvóty, které jsou přiřazeny, zobrazí se podobně jako na následujícím obrázku.  
   ![Dokončení kvóty přiřazení](media/azure-stack-create-plan/all-quotas-assigned.png)

9. V **kvóty** podokně vyberte **OK**a potom v **nový plán** podokně vyberte **vytvořit** vytvořte plán.  
    ![Vytvořte plán](media/azure-stack-create-plan/create.png)
10. Pokud chcete zobrazit nový plán, vyberte **všechny prostředky**, vyhledejte v plánu a vyberte jeho název. Pokud je dlouhý seznam prostředků, použijte **vyhledávání** plánu vyhledat podle názvu.  
   ![Zkontrolujte plán](media/azure-stack-create-plan/plan-overview.png)

### <a name="next-steps"></a>Další postup
[Vytvoření nabídky](azure-stack-create-offer.md)
