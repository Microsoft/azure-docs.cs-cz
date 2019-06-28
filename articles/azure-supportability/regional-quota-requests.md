---
title: Azure překročení kvóty zvýšit požadavky | Dokumentace Microsoftu
description: Požadavky na zvýšení překročení kvóty
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 7ac6dce9aec1c363fa9772caabad9ce542d43888
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310651"
---
# <a name="total-regional-vcpu-limit-increase"></a>Celkové oblastní zvýšení limitu virtuálních jader 

Kvóty virtuálních procesorů Resource Manageru pro virtuální počítače a škálovací sady virtuálních počítačů se vynucují na dvou úrovních pro každé předplatné, v jednotlivých oblastech. 

První vrstva je **celkový počet místních virtuálních procesorů limit** (přes všechny řady virtuálních počítačů), a druhé vrstvy je **za virtuální počítač řady virtuálních procesorů omezit** (jako je například virtuální procesory řady D-series). Pokaždé, když je nový virtuální počítač nasadit, součet nové i stávající virtuální procesory využití pro tento virtuální počítač řady nesmí překročit kvóty virtuálních procesorů schválené pro konkrétní řady virtuálních počítačů. Dále celkový počet virtuálních procesorů nové i stávající počet nasazených na všechny řady virtuálních počítačů může být maximálně celkový počet překročení kvóty virtuálních procesorů schválení předplatného. Pokud některý z těchto kvót je překročena, nasazení virtuálního počítače nebude možné.
Můžete požádat o zvýšení limitu kvóty virtuálních procesorů pro virtuální počítače řady z webu Azure portal. Zvýšení kvóty řadu virtuálních počítačů automaticky zvětší, celkový počet místních virtuálních procesorů limit stejný jmenovce. 

Když se vytvoří nový odběr, nemusí být celkový počet místních virtuálních procesorů výchozí shoduje se součtem výchozí kvóty virtuálních procesorů pro všechny jednotlivé řady virtuálních počítačů. Výsledkem může být předplatné s dostatečnou kvótu pro každé jednotlivé řady virtuálních počítačů, které chcete nasadit, ale ne dostatečnou kvótu pro celkový počet místních virtuálních procesorů pro všechna nasazení. V takovém případě bude muset odeslat žádost o zvýšení limitu celkový počet místních virtuálních procesorů explicitně. Celkový počet místních virtuálních procesorů limit nesmí překročit součet schválené kvóty přes všechny řady virtuálních počítačů pro oblast.

Další informace o kvótách na [stránce kvóty virtuálních procesorů virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) a [předplatného Azure a omezení služeb](https://aka.ms/quotalimits) stránky. 

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



