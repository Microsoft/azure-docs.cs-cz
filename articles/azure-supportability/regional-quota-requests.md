---
title: Žádosti o zvýšení kvóty pro místní Azure | Microsoft Docs
description: Žádosti o zvýšení kvóty pro místní kvótu
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 132cf6ccfec5af9951f5dc6d6a3c6d3c81363d81
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850002"
---
# <a name="standard-quota-regional-vcpu-limit-increase"></a>Standardní kvóta: zvýšení limitu regionálního vCPU 

Správce prostředků podporuje dva typy kvót vCPU pro virtuální počítače. **Virtuální počítače** s průběžnými platbami a **rezervované instance virtuálních počítačů** používají standardní kvótu. **Virtuální počítače** používají kvótu na místě. 

VCPU kvóta úrovně Standard pro průběžné platby a rezervované instance virtuálních počítačů se vynutily na dvou úrovních pro každé předplatné v každé oblasti.
 
První úroveň je **celkový regionální limit vCPU** (v rámci všech řad virtuálních počítačů) a druhá úroveň je **limit vCPU na řadu virtuálních počítačů** (například vCPU řady D-Series). Kdykoli je nasazen nový virtuální počítač, součet nového a stávajícího Vcpuho využití pro tuto řadu virtuálních počítačů nesmí překročit kvótu vCPU schválenou pro danou řadu virtuálních počítačů. Celkový počet nových a existujících vCPUů nasazených ve všech řadách virtuálních počítačů by neměl přesáhnout celkovou kvótu vCPU schválenou pro předplatné. Pokud dojde k překročení některé z těchto kvót, nasazení virtuálního počítače se nepovolí. Můžete požádat o zvýšení limitu kvóty vCPU pro řady virtuálních počítačů z Azure Portal. Zvýšení kvóty řady virtuálních počítačů automaticky zvýší celkový rozsah regionálního Vcpuu o stejné množství.

Při vytvoření nového předplatného se nemusí výchozí celková oblast vCPU rovnat součtu výchozích kvót vCPU pro všechny jednotlivé řady virtuálních počítačů. To může mít za následek předplatné s dostatečnou kvótou pro každou jednotlivou řadu virtuálních počítačů, kterou chcete nasadit, ale ne dostatečnou kvótu pro celkový počet regionálních vCPU pro všechna nasazení. V takovém případě budete muset odeslat žádost o explicitní zvýšení celkového počtu místních vCPU. Celkový počet regionálních vCPU nesmí překročit součet schválené kvóty v rámci všech řad virtuálních počítačů v dané oblasti.

Přečtěte si další informace o standardních kvótách vCPU na stránce [kvóty vCPU pro virtuální počítače](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) a na stránce [omezení pro předplatné a služby Azure](https://aka.ms/quotalimits) .

Další informace o **zvýšení limitů vCPU virtuálních počítačů** [najdete tady](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

V okně **nápovědu a podpora** nebo v okně **používání a kvóta** na portálu můžete požádat o zvýšení **celkového počtu místních VCPU limitů v rámci standardního virtuálního počítače** .

## <a name="request-standard-quota-regional-vcpu-limit-increase-at-subscription-level-using-the-help--support-blade"></a>Požadavek na standardní kvótu vCPU omezit na úrovni předplatného pomocí okna pro pomoc a podporu

Podle pokynů níže vytvořte žádost o podporu prostřednictvím okna "Help + podpora" v Azure, které je dostupné v Azure Portal. 

1. V https://portal.azure.com vyberte možnost **help + podpora**.

![Nápověda a podpora](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Vyberte **Nová žádost o podporu**. 

![Nová žádost o podporu](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. V rozevíracím seznamu typ problému vyberte **omezení služby a předplatné (kvóty)** .

![Rozevírací seznam typu problému](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Vyberte předplatné, které potřebuje navýšit kvótu.

![Vybrat příspěvek odběru](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. V rozevíracím seznamu **typ kvóty** vyberte **Další požadavky** .

![Typ kvóty](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. V podokně **podrobností** zadejte další informace podle níže uvedeného příkladu, které vám pomůžou zpracovat vaši žádost a pokračovat v vytváření případu. 
    1.  **Model nasazení** – zadejte ' správce prostředků '
    2.  **Požadovaná oblast** – zadejte požadovanou oblast, například východní USA 2
    3.  **Nová hodnota limitu** – zadejte limit nové oblasti. To by nemělo překročit součet schválené kvóty pro jednotlivé rodiny SKU pro toto předplatné.

![Podrobnosti kvóty](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>Vyžádat celkovou velikost regionální kvóty vCPU na úrovni předplatného pomocí okna **používání a kvóta**

Pomocí následujících pokynů můžete vytvořit žádost o podporu prostřednictvím okna Azure využití + kvóty dostupného v Azure Portal. 

1. Z https://portal.azure.com vyberte **předplatná**.

![Předplatná](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Vyberte předplatné, které potřebuje navýšit kvótu.

![Výběr předplatného](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Vybrat **využití a kvóty**

![Vybrat využití a kvóty](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. V pravém horním rohu vyberte **požádat o zvýšení**.

![Zvýšení žádosti](./media/resource-manager-core-quotas-request/request-increase.png)

5. V rozevíracím seznamu **typ kvóty** vyberte **Další požadavky** .

![Typ kvóty](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. V podokně **podrobností** zadejte další informace podle níže uvedeného příkladu, které vám pomůžou zpracovat vaši žádost a pokračovat v vytváření případu. 
    1.  **Model nasazení** – zadejte ' správce prostředků '
    2.  **Požadovaná oblast** – zadejte požadovanou oblast, například východní USA 2
    3.  **Nová hodnota limitu** – zadejte limit nové oblasti. To by nemělo překročit součet schválené kvóty pro jednotlivé rodiny SKU pro toto předplatné.

![Podrobnosti kvóty](./media/resource-manager-core-quotas-request/regional-details.png)



