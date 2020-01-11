---
title: Model nasazení Azure Classic | Microsoft Docs
description: Model nasazení Azure Classic
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/20/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: d8f4bf04251347c44ea1692cfdda2602e23117f4
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897049"
---
# <a name="classic-deployment-model"></a>Model nasazení Classic

Model nasazení Classic je model nasazení Azure starší generace. Vynutila globální limit vCPU kvóty pro virtuální počítače a služby Virtual Machine Scale Sets. Model nasazení Classic již není doporučen a je nyní nahrazen modelem Správce prostředků. 

Další informace o těchto dvou modelech nasazení a výhodách použití Správce prostředků najdete na stránce věnované [nasazení Správce prostředků a klasického nasazení](../../azure-resource-manager/management/deployment-models.md) .
 
Když se vytvoří nové předplatné, přiřadí se mu výchozí kvóta vCPU. I když je nový virtuální počítač nasazený pomocí modelu nasazení Classic, součet nového a stávajícího vCPUho využití ve všech oblastech nesmí překročit kvótu vCPU schválenou pro model nasazení Classic.
 
Přečtěte si další informace o kvótách na stránce [omezení předplatného a služeb Azure](https://aka.ms/quotalimits) .

Můžete požádat o zvýšení limitu kvóty vCPU pro model nasazení Classic prostřednictvím okna Help + support nebo pomocí okna využití + kvóta na portálu.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Požadavek na zvýšení kvóty vCPU řady virtuálních počítačů na úrovni předplatného pomocí okna pro **pomoc a podporu**

Podle pokynů níže vytvořte žádost o podporu prostřednictvím okna "Help + podpora" v Azure, které je dostupné v Azure Portal. 

1. V https://portal.azure.com vyberte možnost **help + podpora**.

   ![Nápověda a podpora](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Vyberte **Nová žádost o podporu**. 

      ![Nová žádost o podporu](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. V rozevíracím seznamu typ problému vyberte **omezení služby a předplatné (kvóty)** .

   ![Rozevírací seznam typ problému](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Vyberte předplatné, které potřebuje navýšit kvótu.

   ![Vybrat příspěvek odběru](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Vyberte **COMPUTE – virtuální počítač (jádra – vCPU) zvýšení limitu předplatného** v rozevíracím seznamu **typ kvóty** . 

   ![Vybrat typ kvóty](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. V **podrobnostech o problémech**zadejte další informace, které vám pomůžou zpracovat vaši žádost kliknutím na **zadat podrobnosti**.

   ![Zadat podrobnosti](./media/resource-manager-core-quotas-request/provide-details.png)

7. Na panelu **Podrobnosti kvóty** vyberte Classic a vyberte umístění.

   ![Podrobnosti o kvótě DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. Vyberte **rodiny SKU** , které vyžadují zvýšení. 

   ![Rodina SKU](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. Zadejte nová omezení, která chcete v předplatném. Chcete-li odebrat řádek, zrušte položku SKU z rozevíracího seznamu rodina SKU nebo klikněte na ikonu zahodit "x". Po zadání požadované kvóty pro každou rodinu SKU klikněte na **Uložit a pokračujte** na panelu Podrobnosti o kvótě, aby bylo možné pokračovat v vytváření žádosti o podporu.

   ![Nová omezení](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Žádost o zvýšení kvóty vCPU na řadu virtuálních počítačů na úrovni předplatného pomocí okna **využití + kvóta**

Pomocí následujících pokynů můžete vytvořit žádost o podporu prostřednictvím okna Azure využití + kvóty dostupného v Azure Portal. 

1. Z https://portal.azure.com vyberte **předplatná**.

   ![Předplatná](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Vyberte předplatné, které potřebuje navýšit kvótu.

   ![Výběr předplatného](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Vybrat **využití a kvóty**

   ![Vybrat využití a kvóty](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. V pravém horním rohu vyberte **požádat o zvýšení**.

   ![Zvýšení žádosti](./media/resource-manager-core-quotas-request/request-increase.png)

5. Vyberte **COMPUTE-VM (cores-vCPU) limit předplatného** , který se zvyšuje jako typ citace. 

   ![Vyplnit formulář](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. V **podrobnostech o problémech**zadejte další informace, které vám pomůžou zpracovat vaši žádost kliknutím na **zadat podrobnosti**.

   ![Zadat podrobnosti](./media/resource-manager-core-quotas-request/provide-details.png)

7. Na panelu **Podrobnosti kvóty** vyberte Classic a vyberte umístění.

   ![Podrobnosti o kvótě DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. Vyberte **rodiny SKU** , které vyžadují zvýšení. 

   ![Rodina SKU](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. Zadejte nová omezení, která chcete v předplatném. Chcete-li odebrat řádek, zrušte položku SKU z rozevíracího seznamu rodina SKU nebo klikněte na ikonu zahodit "x". Po zadání požadované kvóty pro každou rodinu SKU klikněte na **Uložit a pokračujte** na panelu Podrobnosti o kvótě, aby bylo možné pokračovat v vytváření žádosti o podporu.

   ![Nová omezení](./media/resource-manager-core-quotas-request/new-limits-classic.png)

