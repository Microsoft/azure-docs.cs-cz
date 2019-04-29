---
title: Žádosti o zvýšení kvóty virtuálních procesorů Azure Resource Manageru | Dokumentace Microsoftu
description: Žádostech o navýšení kvóty virtuálních procesorů Azure Resource Manageru
author: ganganarayanan
ms.author: gangan
ms.date: 6/13/2018
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 204deaf3a67984c0dd5eca5352686719e7767885
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60649247"
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>Žádostech o navýšení kvóty virtuálních procesorů Resource Manageru

Kvóty virtuálních procesorů Resource Manageru se vynucují na úrovni oblasti a řady úroveň SKU.
Další informace o tom, jak kvóty se vynucují na [předplatného Azure a omezení služeb](https://aka.ms/quotalimits) stránky.
Další informace o SKU rodiny, může porovnat náklady a výkon na [ceny Virtual Machines](https://aka.ms/pricingcompute) stránky.

Požádat o zvýšení, postupujte podle pokynů níže použití k vytvoření žádosti o podporu prostřednictvím Azure 'využití a kvóty"okno, které jsou k dispozici na webu Azure Portal. 

## <a name="request-quota-increase-at-subscription-level"></a>Požádat o zvýšení kvóty na úrovni předplatného

1. Z https://portal.azure.comvyberte **předplatná**.

   ![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Vyberte předplatné, které potřebuje navýšit kvótu.

   ![Výběr předplatného](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Vyberte **využití a kvóty**

   ![Vyberte využití a kvóty](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. V pravém horním rohu vyberte **požádat o zvýšení**.

   ![Zvýšení počtu žádostí](./media/resource-manager-core-quotas-request/request-increase.png)

5. Krok: 1 – vyberte **jader** jako typ nabídky. 

   ![Vyplňte formulář](./media/resource-manager-core-quotas-request/forms.png)
   
6. Krok: 2 – Výběr možnosti nasazení modelu jako "Resource Manageru" a vyberte umístění.

    ![Okno kvóta problému](./media/resource-manager-core-quotas-request/Problem-step.png)

3. Vyberte řad SKU, které vyžadují zvýšení.

    ![Vybraná řady SKU](./media/resource-manager-core-quotas-request/SKU-selected.png)

4. Zadejte nové limity, které chcete v rámci předplatného.

    ![Skladová položka novou žádost o kvótu](./media/resource-manager-core-quotas-request/SKU-new-quota.png)

- Chcete-li odebrat, zrušte zaškrtnutí políčka skladovou Položku z rozevíracího seznamu řady SKU nebo klikněte na ikonu zrušení "x".
Po zadání požadované kvóty pro každou řadu SKU, klikněte na "Následující" na stránce problém krok a pokračujte vytvoření žádosti o podporu.

