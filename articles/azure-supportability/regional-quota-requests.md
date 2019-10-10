---
title: Žádosti o zvýšení kvóty pro místní Azure | Microsoft Docs
description: Žádosti o zvýšení kvóty pro místní kvótu
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e73f22b0e617ad8f20b98c3bb0fb1647bf5fe61d
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249081"
---
# <a name="total-regional-vcpu-limit-increase"></a>Zvýšení celkového počtu místních vCPU 

Správce prostředků kvóty vCPU pro virtuální počítače a sady škálování virtuálních počítačů se vynutily na dvou úrovních každého předplatného v každé oblasti. 

První úroveň je **celkový regionální limit vCPU** (v rámci všech řad virtuálních počítačů) a druhá úroveň je **limit vCPU na řadu virtuálních počítačů** (například vCPU řady D-Series). Při každém nasazení nového virtuálního počítače nesmí součet nového a stávajícího vCPU využití pro tuto řadu virtuálních počítačů překročit kvótu vCPU schválenou pro tuto řadu virtuálních počítačů. Celkový počet nových a existujících vCPUů nasazených ve všech řadách virtuálních počítačů by neměl přesáhnout celkovou kvótu vCPU schválenou pro předplatné. Pokud dojde k překročení některé z těchto kvót, nasazení virtuálního počítače se nepovolí.
Můžete požádat o zvýšení limitu kvóty vCPU pro řady virtuálních počítačů z Azure Portal. Zvýšení kvóty řady virtuálních počítačů automaticky zvýší celkový rozsah regionálního Vcpuu o stejné množství. 

Při vytvoření nového předplatného se nemusí výchozí celková oblast vCPU rovnat součtu výchozích kvót vCPU pro všechny jednotlivé řady virtuálních počítačů. To může mít za následek předplatné s dostatečnou kvótou pro každou jednotlivou řadu virtuálních počítačů, kterou chcete nasadit, ale ne dostatečnou kvótu pro celkový počet regionálních vCPU pro všechna nasazení. V takovém případě budete muset odeslat žádost o explicitní zvýšení celkového počtu místních vCPU. Celkový počet regionálních vCPU nesmí překročit součet schválené kvóty v rámci všech řad virtuálních počítačů v dané oblasti.

Přečtěte si další informace o kvótách na stránce [vCPU kvóty virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) a [omezení pro předplatné a službu Azure](https://aka.ms/quotalimits) . 

Nyní můžete požádat o zvýšení prostřednictvím okna **help + support** nebo okna **používání + kvót** na portálu. 

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Vyžádat celkovou velikost regionální kvóty vCPU na úrovni předplatného pomocí okna pro **pomoc a podporu**

Podle pokynů níže vytvořte žádost o podporu prostřednictvím okna "Help + podpora" v Azure, které je dostupné v Azure Portal. 

1. V https://portal.azure.com vyberte možnost **help + podpora**.

![Pomoc a podpora](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Vyberte **novou žádost o podporu**. 

![Nová žádost o podporu](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. V rozevíracím seznamu typ problému vyberte **omezení služby a předplatné (kvóty)** .

![Rozevírací seznam typ problému](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Vyberte předplatné, které vyžaduje zvýšenou kvótu.

![Vybrat příspěvek odběru](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. V rozevíracím seznamu **typ kvóty** vyberte **Další požadavky** .

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. V podokně **podrobností** zadejte další informace podle níže uvedeného příkladu, které vám pomůžou zpracovat vaši žádost a pokračovat v vytváření případu. 
    1.  **Model nasazení** – zadejte ' správce prostředků '
    2.  **Požadovaná oblast** – zadejte požadovanou oblast, například východní USA 2
    3.  **Nová hodnota limitu** – zadejte limit nové oblasti. To by nemělo překročit součet schválené kvóty pro jednotlivé rodiny SKU pro toto předplatné.

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>Vyžádat celkovou velikost regionální kvóty vCPU na úrovni předplatného pomocí okna **používání a kvóta**

Pomocí následujících pokynů můžete vytvořit žádost o podporu prostřednictvím okna Azure využití + kvóty dostupného v Azure Portal. 

1. Z https://portal.azure.com vyberte **odběry**.

![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Vyberte předplatné, které vyžaduje zvýšenou kvótu.

![Vybrat předplatné](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Vybrat **využití a kvóty**

![Vybrat využití a kvóty](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. V pravém horním rohu vyberte **požádat o zvýšení**.

![Zvýšení žádosti](./media/resource-manager-core-quotas-request/request-increase.png)

5. V rozevíracím seznamu **typ kvóty** vyberte **Další požadavky** .

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. V podokně **podrobností** zadejte další informace podle níže uvedeného příkladu, které vám pomůžou zpracovat vaši žádost a pokračovat v vytváření případu. 
    1.  **Model nasazení** – zadejte ' správce prostředků '
    2.  **Požadovaná oblast** – zadejte požadovanou oblast, například východní USA 2
    3.  **Nová hodnota limitu** – zadejte limit nové oblasti. To by nemělo překročit součet schválené kvóty pro jednotlivé rodiny SKU pro toto předplatné.

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)



