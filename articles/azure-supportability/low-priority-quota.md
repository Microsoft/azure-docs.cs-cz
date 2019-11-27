---
title: Kvóta s nízkou prioritou | Microsoft Docs
description: Žádosti o kvótu s nízkou prioritou
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 49ac478330cf73dff050a3edcc15933692fa6448
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535168"
---
# <a name="low-priority-quota-limit-increase-for-all-vm-series"></a>Kvóta s nízkou prioritou: zvýšení limitu pro všechny řady virtuálních počítačů

Virtuální počítače s nízkou prioritou poskytují jiný model využití Azure a nabízejí nižší náklady na to, aby Azure odebrali virtuální počítač podle potřeby pro nasazení s průběžnými platbami nebo pro rezervované instance virtuálních počítačů. Přečtěte si další informace o [virtuálních počítačích](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-low-priority)s nízkou prioritou.

Správce prostředků podporuje dva typy kvót vCPU pro virtuální počítače. **Virtuální počítače s průběžnými platbami a rezervované instance virtuálních počítačů** používají standardní kvótu. **Virtuální počítače s nízkou prioritou** používají kvótu s nízkou prioritou. 

U typu **kvóty s nízkou prioritou** se správce prostředků kvóty vCPU vynutily napříč všemi dostupnými řadami virtuálních počítačů jako s jediným oblastním limitem.

Kdykoli je nasazení nového virtuálního počítače s nízkou prioritou nasazeno, součet nového a stávajícího Vcpuho využití pro všechny instance virtuálních počítačů s nízkou prioritou nesmí překročit schválenou kvótu vCPU s nízkou prioritou. Pokud je překročená kvóta s nízkou prioritou, nasazení virtuálního počítače s nízkou prioritou se nepovolí. Můžete požádat o zvýšení limitu kvóty vCPU s nízkou prioritou z Azure Portal. 

Přečtěte si další informace o standardních kvótách vCPU na stránce kvóty vCPU pro virtuální počítače a na stránce omezení pro předplatné a služby Azure. Přečtěte si další informace o zvyšování omezení regionálního vCPU pro kvótu Standard na této [stránce](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests).

Nyní si můžete vyžádat zvýšení **kvóty s nízkou prioritou pro všechny řady virtuálních počítačů** prostřednictvím okna **help + podpora** nebo z okna **používání a kvót** na portálu.

## <a name="request-low-priority-quota-limit-increase-for-all-vm-series-per-subscription-using-the-help--support-blade"></a>Požadavek na zvýšení kvóty s nízkou prioritou pro všechny řady virtuálních počítačů na předplatné pomocí okna pro nápovědu a podporu

Podle pokynů níže vytvořte žádost o podporu prostřednictvím okna "Help + podpora" v Azure, které je dostupné v Azure Portal.

Můžete také **požádat o kvótu pro více oblastí** prostřednictvím jediného případu podpory. Podrobnosti najdete v kroku 10 níže. 


1. V https://portal.azure.comvyberte možnost **help + podpora**.

   ![Pomoc a podpora](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Vyberte **Nová žádost o podporu**. 

     ![Nová žádost o podporu](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. V rozevíracím seznamu typ problému vyberte **omezení služby a předplatné (kvóty)** .

   ![Rozevírací seznam typu problému](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Vyberte předplatné, které potřebuje navýšit kvótu.

   ![Vybrat příspěvek odběru](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Vyberte možnost **COMPUTE – virtuální počítač (jádra – vCPU) zvýšení limitu předplatného** v rozevíracím seznamu **typ kvóty** . 

   ![Vybrat typ kvóty](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. V **podrobnostech o problémech**zadejte další informace, které vám pomůžou zpracovat vaši žádost kliknutím na **zadat podrobnosti**.

   ![Zadat podrobnosti](./media/resource-manager-core-quotas-request/provide-details.png)
   
7.  Na panelu **Podrobnosti o kvótě*** vyberte **model nasazení** a vyberte **umístění**.

![Zadat podrobnosti](./media/resource-manager-core-quotas-request/3-7.png)

8. Pro vybrané umístění vyberte možnost **typ** hodnota jako **Nízká priorita**. V rámci podpory vícenásobného výběru v poli **typ** můžete požádat o typy kvót standardní i nízkou prioritu z jediného případu podpory. Přečtěte si další informace o **zvýšení standardní kvóty na řadu virtuálních počítačů** na této [stránce](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

![Zadat podrobnosti](./media/resource-manager-core-quotas-request/3-8.png)

9.  Zadejte nový limit, který chcete v předplatném. 
 
 ![Zadat podrobnosti](./media/resource-manager-core-quotas-request/3-9.png)

10. Chcete-li požádat o kvótu pro více než jedno umístění, můžete v rozevíracím seznamu ověřit jiné umístění a vybrat vhodný typ virtuálního počítače. Pak můžete zadat nová omezení, která chcete.

![Zadat podrobnosti](./media/resource-manager-core-quotas-request/3-10.png)

11. Až zadáte požadovanou kvótu, klikněte na **Uložit a pokračujte** na panelu Podrobnosti o kvótě a pokračujte v vytváření žádosti o podporu.

## <a name="request-low-priority-quota-limit-increase-for-all-vm-series-per-subscription-using-usages--quota-blade"></a>Požadavek na zvýšení kvóty s nízkou prioritou pro všechny řady virtuálních počítačů na předplatné pomocí okna využití + kvóta

Pomocí následujících pokynů můžete vytvořit žádost o podporu prostřednictvím okna Azure využití + kvóty dostupného v Azure Portal.

Můžete také **požádat o kvótu pro více oblastí** prostřednictvím jediného případu podpory. Podrobnosti najdete níže v kroku 9. 

1.  Z https://portal.azure.comvyberte **předplatná**.

 ![Předplatná](./media/resource-manager-core-quotas-request/subscriptions.png)

2.  Vyberte předplatné, které potřebuje navýšit kvótu.

 ![Výběr předplatného](./media/resource-manager-core-quotas-request/select-subscription.png)

3.  Vyberte **využití a kvóty**.

 ![Vybrat využití a kvóty](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4.  V pravém horním rohu vyberte **požádat o zvýšení.**

   ![Zvýšení žádosti](./media/resource-manager-core-quotas-request/request-increase.png)

5.  Vyberte **COMPUTE-VM (cores-vCPU) limit předplatného** , který se zvyšuje jako typ citace.

  ![Vyplnit formulář](./media/resource-manager-core-quotas-request/select-quota-type.png)

6.  Na panelu **Podrobnosti kvóty** vyberte model nasazení a vyberte umístění.

  ![Vyplnit formulář](./media/resource-manager-core-quotas-request/3-2-6.png)
 
7.  Pro vybrané umístění vyberte možnost **typ** hodnota jako **Nízká priorita.** V rámci podpory vícenásobného výběru v poli **typ** můžete požádat o typy kvót standardní i nízkou prioritu z jediného případu podpory. Přečtěte si další informace o **zvýšení standardní kvóty na řadu virtuálních počítačů** na této [stránce](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

  ![Vyplnit formulář](./media/resource-manager-core-quotas-request/3-2-7.png)
 
8.  Zadejte nový limit, který chcete v předplatném.

  ![Vyplnit formulář](./media/resource-manager-core-quotas-request/3-2-8.png)
 
9.  Chcete-li požádat o kvótu pro více než jedno umístění, můžete v rozevíracím seznamu ověřit jiné **umístění** a vybrat vhodný typ virtuálního počítače. Pak můžete zadat nová omezení, která chcete.

  ![Vyplnit formulář](./media/resource-manager-core-quotas-request/3-2-9.png)
 
10. Až zadáte požadovanou kvótu, klikněte na **Uložit a pokračujte** na panelu Podrobnosti o kvótě a pokračujte v vytváření žádosti o podporu.

