---
title: Požádat o zvýšení limitů kvót regionálních vCPU Azure | Microsoft Docs
description: Žádosti o zvýšení kvóty pro místní kvótu
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e262651a6e040c40dbe240ad3437eff1914aa3e5
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896737"
---
# <a name="standard-quota-increase-limits-by-region"></a>Standardní kvóta: zvýšení omezení podle oblasti 

Azure Resource Manager podporuje dva typy kvót vCPU pro virtuální počítače:
* *Virtuální počítače* s průběžnými platbami a *rezervované instance virtuálních počítačů* podléhají *standardní vCPU kvótě*.
* *Virtuální počítače na místě* podléhají *vCPU kvótě*. 

Standardní kvóta vCPU pro průběžné platby a rezervované instance virtuálních počítačů se vynutily na dvou úrovních pro každé předplatné v každé oblasti:
* První úroveň je *Celkový místní vCPU limit* (v rámci všech řad virtuálních počítačů).
* Druhá úroveň je *vCPU limit řady per-VM* (například vCPU řady D-Series).
 
Když nasadíte nový virtuální počítač s přímým odkazem, nesmí celkové nové a stávající využití vCPU pro tuto řadu virtuálních počítačů překročit schválenou kvótu vCPU pro tuto řadu virtuálních počítačů. Celkový počet nových a existujících vCPU nasazených ve všech řadách virtuálních počítačů by navíc neměl překročit celkovou schválenou kvótu místní vCPU pro předplatné. Pokud dojde k překročení některé z těchto kvót, nasazení virtuálního počítače se nepovoluje. 

Můžete požádat o zvýšení limitu kvóty vCPU pro řady virtuálních počítačů pomocí Azure Portal. Zvýšení kvóty řady virtuálních počítačů automaticky zvýší celkový rozsah regionálního vCPUu o stejné množství.

Když vytvoříte nové předplatné, výchozí celkový počet regionálních vCPU nemusí být stejný jako celková výchozí kvóta vCPU pro všechny jednotlivé řady virtuálních počítačů. Výsledkem této nesrovnalosti může být předplatné s dostatečnou kvótou pro každou jednotlivou řadu virtuálních počítačů, kterou chcete nasadit. Možná ale není dostatečná kvóta pro přizpůsobení celkového regionu vCPU pro všechna nasazení. V takovém případě musíte odeslat žádost o explicitní zvýšení limitu celkového počtu regionálních vCPU. Celkový limit regionálního vCPU nesmí překročit celkovou schválenou kvótu v rámci všech řad virtuálních počítačů v dané oblasti.

Další informace o standardních kvótách vCPU najdete v tématu věnovaném [kvótám virtuálních počítačů vCPU](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) a [omezením předplatného a služeb Azure](https://aka.ms/quotalimits).

Další informace o zvýšení limitů vCPU virtuálních počítačů najdete v tématu věnovaném [kvótám na místě: zvýšení limitů pro všechny řady virtuálních počítačů](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

Můžete požádat o zvýšení limitu kvóty standardu vCPU podle oblasti v jednom ze dvou způsobů, jak je popsáno v dalších částech.

## <a name="request-a-quota-increase-by-region-from-the-help--support-pane"></a>Požádat o zvýšení kvóty podle oblasti v podokně pomoc a podpora

Pokud chcete požádat o zvýšení kvóty vCPU podle oblasti v podokně **pomoc a podpora** , udělejte toto: 

1. V levém podokně [Azure Portal](https://portal.azure.com)vyberte možnost **help + podpora**.

   ![Odkaz Help + Support (podpora a podpora)](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. V podokně **pomoc a podpora** vyberte **Nová žádost o podporu**. 

    ![Nová žádost o podporu](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. V rozevíracím seznamu **typ problému** vyberte **omezení služby a předplatné (kvóty)** .

   ![Rozevírací seznam "typ problému"](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. V rozevíracím seznamu **předplatné** vyberte předplatné, jehož kvótu chcete zvýšit.

   ![Rozevírací seznam předplatné](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. V rozevíracím seznamu **typ kvóty** vyberte **Další požadavky**.

   ![Rozevírací seznam "typ kvóty"](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. V podokně **podrobností problému** zadejte do pole **Popis** tyto další informace: 

    a. V případě **modelu nasazení**zadejte **Správce prostředků**.  
    b. V **oblasti oblast**zadejte požadovanou oblast (například **východní USA 2**).  
    c. Pro **nový limit**zadejte nový limit vCPU pro oblast. Tato hodnota by neměla překročit součet schválených kvót pro jednotlivé série SKU pro toto předplatné.

    ![Podokno Podrobnosti o problému](./media/resource-manager-core-quotas-request/regional-details.png)

1. Pokud chcete pokračovat v vytváření žádosti o podporu, vyberte **Uložit a pokračovat** .

## <a name="request-a-quota-increase-by-region-from-the-subscriptions-pane"></a>Požádat o zvýšení kvóty podle oblasti v podokně předplatná

Pokud chcete požádat o zvýšení kvóty vCPU podle oblasti v podokně **předplatná** , udělejte toto: 

1. V levém podokně [Azure Portal](https://portal.azure.com)vyberte **předplatná**.

   ![Odkaz předplatné](./media/resource-manager-core-quotas-request/subscriptions.png)

1. Vyberte předplatné, jehož kvóta se má zvýšit.

   ![Podokno odběry](./media/resource-manager-core-quotas-request/select-subscription.png)

1. V levém podokně **> název Předplatného\<** vyberte možnost **využití a kvóty**.

   ![Odkaz využití a kvóty](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. V pravém horním rohu vyberte **požádat o zvýšení**.

   ![Zvýšení žádosti](./media/resource-manager-core-quotas-request/request-increase.png)

1. V rozevíracím seznamu **typ kvóty** vyberte **Další požadavky**.

   ![Rozevírací seznam "typ kvóty"](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. V podokně **podrobností problému** zadejte do pole **Popis** tyto další informace: 

    a. V případě **modelu nasazení**zadejte **Správce prostředků**.  
    b. V **oblasti oblast**zadejte požadovanou oblast (například **východní USA 2**).  
    c. Pro **nový limit**zadejte nový limit vCPU pro oblast. Tato hodnota by neměla překročit součet schválených kvót pro jednotlivé série SKU pro toto předplatné.

    ![Podokno Podrobnosti o problému](./media/resource-manager-core-quotas-request/regional-details.png)

1. Pokud chcete pokračovat v vytváření žádosti o podporu, vyberte **Uložit a pokračovat** .

