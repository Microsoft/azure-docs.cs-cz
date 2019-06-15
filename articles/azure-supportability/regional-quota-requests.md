---
title: Azure překročení kvóty zvýšit požadavky | Dokumentace Microsoftu
description: Požadavky na zvýšení překročení kvóty
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 4c5524cf450959db7055ca5d032c81f79ebac077
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083077"
---
# <a name="total-regional-vcpu-limit-increase"></a>Celkový počet virtuálních procesorů regionální navýšení limitu 

Teď si můžete vyžádat navýšení prostřednictvím příkazu **Nápověda a podpora** okno nebo **využití a kvóty** okno na portálu. 

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Požádat o celkové regionální zvýšení kvóty virtuálních procesorů na úrovni pomocí předplatného **Nápověda a podpora** okno

Postupujte podle níže uvedených pokynů a vytvořte žádost o podporu prostřednictvím Azure "Nápověda a podpora" okně k dispozici na webu Azure Portal. 

1. Z https://portal.azure.com vyberte **Nápověda a podpora**.

![Nápověda a podpora](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Vyberte **Nová žádost o podporu**. 

![Nová žádost o podporu](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. V rozevírací nabídce Typ problému zvolte **limity služby a předplatného (kvóty)** .

![Rozevírací nabídce Typ problému](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Vyberte předplatné, které potřebuje navýšit kvótu.

![Vyberte předplatné newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Vyberte **ostatní požadavky** v **typ kvóty** rozevíracího seznamu.

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. V **podrobnosti** podokno, uveďte další důležité informace podle příkladu níže usnadňují proces vaši žádost a pokračovat ve vytváření případu. 
    1.  **Model nasazení** – zadejte Resource Manager
    2.  **Požadovaná oblast** – zadejte požadované oblasti například USA – východ 2
    3.  **Nový limit hodnotu** – zadat nové oblasti limit. To může být maximálně součet schválené kvóty pro jednotlivé řady SKU pro toto předplatné

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>Požádat o celkové regionální zvýšení kvóty virtuálních procesorů na úrovni pomocí předplatného **využití a kvóty** okno

Postupujte podle pokynů níže použití k vytvoření žádosti o podporu prostřednictvím Azure 'využití a kvóty"okno, které jsou k dispozici na webu Azure Portal. 

1. Z https://portal.azure.com vyberte **předplatná**.

![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Vyberte předplatné, které potřebuje navýšit kvótu.

![Výběr předplatného](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Vyberte **využití a kvóty**

![Vyberte využití a kvóty](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. V pravém horním rohu vyberte **požádat o zvýšení**.

![Zvýšení počtu žádostí](./media/resource-manager-core-quotas-request/request-increase.png)

5. Vyberte **ostatní požadavky** v **typ kvóty** rozevíracího seznamu.

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. V **podrobnosti** podokno, uveďte další důležité informace podle příkladu níže usnadňují proces vaši žádost a pokračovat ve vytváření případu. 
    1.  **Model nasazení** – zadejte Resource Manager
    2.  **Požadovaná oblast** – zadejte požadované oblasti například USA – východ 2
    3.  **Nový limit hodnotu** – zadat nové oblasti limit. To může být maximálně součet schválené kvóty pro jednotlivé řady SKU pro toto předplatné

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)



