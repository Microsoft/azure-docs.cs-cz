---
title: Žádosti o zvýšení kvóty vCPU pro Azure na virtuální počítač | Microsoft Docs
description: žádosti o zvýšení kvóty na vCPU na virtuální počítač
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9caf82d2fa4683e845d23b5496f9e7b8f9682ec7
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234821"
---
# <a name="vm-series-vcpu-limit-increase"></a>Zvýšení limitu virtuálních jader řady virtuálních počítačů

Správce prostředků kvóty vCPU pro virtuální počítače a sady škálování virtuálních počítačů se vynutily na dvou úrovních každého předplatného v každé oblasti. 

První úroveň je **celkový regionální limit vCPU** (v rámci všech řad virtuálních počítačů) a druhá úroveň je **limit vCPU na řadu virtuálních počítačů** (například vCPU řady D-Series). Při každém nasazení nového virtuálního počítače nesmí součet nového a stávajícího vCPU využití pro tuto řadu virtuálních počítačů překročit kvótu vCPU schválenou pro tuto řadu virtuálních počítačů. Celkový počet nových a existujících vCPUů nasazených ve všech řadách virtuálních počítačů by neměl přesáhnout celkovou kvótu vCPU schválenou pro předplatné. Pokud dojde k překročení některé z těchto kvót, nasazení virtuálního počítače se nepovolí.
Můžete požádat o zvýšení limitu kvóty vCPU pro řady virtuálních počítačů z Azure Portal. Zvýšení kvóty řady virtuálních počítačů automaticky zvýší celkový rozsah regionálního Vcpuu o stejné množství. 

Přečtěte si další informace o kvótách na stránce [vCPU kvóty virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) a [omezení pro předplatné a službu Azure](https://aka.ms/quotalimits) . 

Nyní můžete požádat o zvýšení prostřednictvím okna **help + support** nebo okna **používání + kvót** na portálu. 

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Požadavek na zvýšení kvóty vCPU řady virtuálních počítačů na úrovni předplatného pomocí okna pro **pomoc a podporu**

Podle pokynů níže vytvořte žádost o podporu prostřednictvím okna "Help + podpora" v Azure, které je dostupné v Azure Portal. 

1. V https://portal.azure.com klikněte na tlačítko **help + podpora**.

   ![Pomoc a podpora](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Vyberte **Nová žádost o podporu**. 

     ![Nová žádost o podporu](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. V rozevíracím seznamu typ problému vyberte **omezení služby a předplatné (kvóty)** .

   ![Rozevírací seznam typ problému](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Vyberte předplatné, které potřebuje navýšit kvótu.

   ![Vybrat příspěvek odběru](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Vyberte **COMPUTE – virtuální počítač (jádra – vCPU) zvýšení limitu** předplatného v rozevíracím seznamu **typ kvóty** . 

   ![Vybrat typ kvóty](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. V **podrobnostech o problémech**zadejte další informace, které vám pomůžou zpracovat vaši žádost kliknutím na **zadat podrobnosti**.

   ![Zadat podrobnosti](./media/resource-manager-core-quotas-request/provide-details.png)

7. Na panelu **Podrobnosti kvóty** vyberte model nasazení a vyberte umístění.

   ![Podrobnosti o kvótě DM](./media/resource-manager-core-quotas-request/quota-details.png)

8. Vyberte **rodiny SKU** , které vyžadují zvýšení. 

   ![Rodina SKU](./media/resource-manager-core-quotas-request/sku-family.png)

9. Zadejte nová omezení, která chcete v předplatném. Chcete-li odebrat řádek, zrušte položku SKU z rozevíracího seznamu rodina SKU nebo klikněte na ikonu zahodit "x". Po zadání požadované kvóty pro každou rodinu SKU klikněte na **Uložit a pokračujte** na panelu Podrobnosti o kvótě, aby bylo možné pokračovat v vytváření žádosti o podporu.

   ![Nová omezení](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Žádost o zvýšení kvóty vCPU na řadu virtuálních počítačů na úrovni předplatného pomocí okna **využití + kvóta**

Pomocí následujících pokynů můžete vytvořit žádost o podporu prostřednictvím okna Azure využití + kvóty dostupného v Azure Portal. 

1. V https://portal.azure.com vyberte **odběry**.

   ![Předplatná](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Vyberte předplatné, které potřebuje navýšit kvótu.

   ![Výběr předplatného](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Vybrat **využití a kvóty**

   ![Vybrat využití a kvóty](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. V pravém horním rohu vyberte **požádat o zvýšení**.

   ![Zvýšení žádosti](./media/resource-manager-core-quotas-request/request-increase.png)

5. Vyberte **COMPUTE-VM (cores-vCPU) limit** předplatného, který se zvyšuje jako typ citace. 

   ![Vyplnit formulář](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. Na panelu **Podrobnosti kvóty** vyberte model nasazení a vyberte umístění.

   ![Okno problém kvóty](./media/resource-manager-core-quotas-request/quota-details.png)

7. Vyberte **rodiny SKU** , které vyžadují zvýšení.

   ![Vybraná série SKU](./media/resource-manager-core-quotas-request/sku-family.png)

8. Zadejte nová omezení, která chcete v předplatném. Chcete-li odebrat řádek, zrušte položku SKU z rozevíracího seznamu rodina SKU nebo klikněte na ikonu zahodit "x". Po zadání požadované kvóty pro každou rodinu SKU klikněte na **Uložit a pokračujte** na stránce krok problému, abyste mohli pokračovat v vytváření žádosti o podporu.

   ![Nová žádost o kvótu SKU](./media/resource-manager-core-quotas-request/new-limits.png)
 
