---
title: Požádat o zvýšení limitů kvót regionálních vCPU Azure
description: Jak požádat o zvýšení limitu kvóty vCPU pro oblast v Azure Portal.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: eadf740c6b5caccbf678a1238f993d4ec0b34095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745413"
---
# <a name="standard-quota-increase-limits-by-region"></a>Standardní kvóta: zvýšení omezení podle oblasti

Azure Resource Manager podporuje dva typy kvót vCPU pro virtuální počítače:

* *Virtuální počítače* s průběžnými platbami a *rezervované instance virtuálních počítačů* podléhají *standardní vCPU kvótě*.
* *Virtuální počítače na místě* podléhají *vCPU kvótě*.

Standardní kvóta vCPU pro průběžné platby a rezervované instance virtuálních počítačů se vynutily na dvou úrovních pro každé předplatné v každé oblasti:

* První úroveň je *celkový regionální limit vCPU* ve všech řadách virtuálních počítačů.
* Druhá úroveň je *vCPU limit řady per-VM*, jako je například vCPU řady D-Series.

Když nasadíte nový virtuální počítač s přímým odkazem, nesmí celkové nové a stávající využití vCPU pro tuto řadu virtuálních počítačů překročit schválenou kvótu vCPU pro tuto řadu virtuálních počítačů. Celkový počet nových a existujících vCPU nasazených ve všech řadách virtuálních počítačů by neměl přesáhnout celkovou schválenou kvótu místní vCPU pro předplatné. Pokud dojde k překročení některé z těchto kvót, nasazení virtuálního počítače se nepovoluje.

Můžete požádat o zvýšení limitu kvóty vCPU pro řady virtuálních počítačů pomocí Azure Portal. Zvýšení kvóty řady virtuálních počítačů automaticky zvýší celkový rozsah regionálního vCPUu o stejné množství.

Když vytvoříte nové předplatné, výchozí celkový počet regionálních vCPU nemusí být stejný jako celková výchozí kvóta vCPU pro všechny jednotlivé řady virtuálních počítačů. Výsledkem této nesrovnalosti může být předplatné s dostatečnou kvótou pro každou jednotlivou řadu virtuálních počítačů, kterou chcete nasadit. Možná ale není dostatečná kvóta pro přizpůsobení celkového regionu vCPU pro všechna nasazení. V takovém případě musíte odeslat žádost o explicitní zvýšení limitu celkového počtu regionálních vCPU. Celkový limit regionálního vCPU nesmí překročit celkovou schválenou kvótu v rámci všech řad virtuálních počítačů v dané oblasti.

Další informace o standardních kvótách vCPU najdete v tématu [kvóty pro virtuální počítače vCPU](../../virtual-machines/windows/quotas.md) a [omezení, kvóty a omezení předplatného a služeb Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Další informace o zvýšení limitů vCPU virtuálních počítačů najdete v tématu věnovaném [kvótám na místě: zvýšení limitů pro všechny řady virtuálních počítačů](low-priority-quota.md).

Můžete požádat o zvýšení limitu kvóty standardní vCPU podle oblasti v obou dvou ohledech.

## <a name="request-a-quota-increase-by-region-from-help--support"></a>Požádat o zvýšení kvóty podle oblasti v nápovědě a podpoře

Požadavek na zvýšení kvóty vCPU podle oblasti v **nápovědě a podpoře**:

1. V nabídce [Azure Portal](https://portal.azure.com) vyberte možnost **help + podpora**.

   ![Odkaz Help + Support (podpora a podpora)](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. V **okně pomoc a podpora** vyberte **Nová žádost o podporu**.

    ![Nová žádost o podporu](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Jako **typ problému** vyberte **omezení služby a předplatné (kvóty)**.

   ![Vyberte typ problému.](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. V poli **předplatné** vyberte předplatné, jehož kvótu chcete zvýšit.

   ![Výběr předplatného](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Jako **typ kvóty** vyberte **jiné požadavky**.

   ![Vyberte typ kvóty.](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Vyberte **Další: řešení** pro otevření **podrobností problému**. V části **Popis** zadejte následující informace:

    1. V případě **modelu nasazení** zadejte **Správce prostředků**.  
    1. V **oblasti oblast** zadejte požadovanou oblast, například **východní USA 2**.  
    1. Pro **nový limit** zadejte nový limit vCPU pro oblast. Tato hodnota by neměla překročit součet schválených kvót pro jednotlivé série SKU pro toto předplatné.

    ![Zadejte podrobnosti o žádosti o kvótu.](./media/resource-manager-core-quotas-request/regional-details.png)

1. Pokud chcete pokračovat v vytváření žádosti o podporu, vyberte **zkontrolovat + vytvořit** .

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>Požádat o zvýšení kvóty podle oblasti z předplatných

Požadavek na zvýšení kvóty vCPU podle oblasti z **předplatných**:

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **předplatná**.

   ![Přejít na předplatná v Azure Portal](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Vyberte předplatné, jehož kvóta se má zvýšit.

   ![Vyberte předplatné, které chcete upravit.](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. V levém podokně vyberte **využití + kvóty**.

   ![Odkaz sledování využití a kvót](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. V pravém horním rohu vyberte **požádat o zvýšení**.

   ![Vyberte, chcete-li zvýšit kvótu.](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Z **typu kvóty** vyberte **jiné požadavky**.

   ![Vyberte typ kvóty.](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Vyberte **Další: řešení** pro otevření **podrobností problému**. V poli **Popis** zadejte následující další informace:

    1. V případě **modelu nasazení** zadejte **Správce prostředků**.  
    1. V **oblasti oblast** zadejte požadovanou oblast, například **východní USA 2**.  
    1. Pro **nový limit** zadejte nový limit vCPU pro oblast. Tato hodnota by neměla překročit součet schválených kvót pro jednotlivé série SKU pro toto předplatné.

    ![Zadat informace v podrobnostech](./media/resource-manager-core-quotas-request/regional-details.png)

1. Pokud chcete pokračovat v vytváření žádosti o podporu, vyberte **zkontrolovat + vytvořit** .
