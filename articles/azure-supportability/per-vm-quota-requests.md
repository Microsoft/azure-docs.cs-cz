---
title: Žádosti o zvýšení kvóty vCPU pro Azure na virtuální počítač | Microsoft Docs
description: žádosti o zvýšení kvóty na vCPU na virtuální počítač
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 280561126186e4c70399b3a1ddd177ff4eb54400
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850036"
---
# <a name="standard-quota-per-vm-series-vcpu-limit-increase"></a>Zvýšení limitu úrovně Standard: na vCPU řady virtuálních počítačů

Správce prostředků podporuje dva typy kvót vCPU pro virtuální počítače. **Virtuální počítače s průběžnými platbami a rezervované instance virtuálních počítačů** používají standardní kvótu. **Virtuální počítače** používají kvótu na místě. VCPU kvóta úrovně Standard pro průběžné platby a rezervované instance virtuálních počítačů se vynutily na dvou úrovních pro každé předplatné v každé oblasti.

První úroveň je **celkový regionální limit vCPU** (v rámci všech řad virtuálních počítačů) a druhá úroveň je **limit vCPU na řadu virtuálních počítačů** (například Dv3-Series vCPU). Kdykoli je nasazen nový virtuální počítač, součet nového a stávajícího Vcpuho využití pro tuto řadu virtuálních počítačů nesmí překročit kvótu vCPU schválenou pro danou řadu virtuálních počítačů. Celkový počet nových a existujících vCPUů nasazených ve všech řadách virtuálních počítačů by neměl přesáhnout celkovou kvótu vCPU schválenou pro předplatné. Pokud dojde k překročení některé z těchto kvót, nasazení virtuálního počítače se nepovolí.
Můžete požádat o zvýšení limitu kvóty vCPU pro řady virtuálních počítačů z Azure Portal. Zvýšení kvóty řady virtuálních počítačů automaticky zvýší celkový rozsah regionálního Vcpuu o stejné množství. 

Přečtěte si další informace o standardních kvótách vCPU na stránce [kvóty vCPU pro virtuální počítače](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) a na [stránce omezení pro předplatné a služby Azure](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests). 

Přečtěte si další informace o tom, jak prodloužit regionální limit vCPU [pro kvótu](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)Standard. 

Další informace o **zvýšení limitů vCPU virtuálních počítačů** [najdete tady](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

V okně pro **nápovědu a podporu** **nebo na portálu** můžete požádat o zvýšení **standardních omezení vCPU kvót na řadu virtuálních počítačů** .

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-the-help--support-blade"></a>Požadavek na standardní zvýšení kvóty vCPU na řadu virtuálních počítačů na úrovni předplatného pomocí okna pro pomoc a podporu

Podle pokynů níže vytvořte žádost o podporu prostřednictvím okna "Help + podpora" v Azure, které je dostupné v Azure Portal. 

Můžete také požádat o kvótu pro více oblastí prostřednictvím jediného případu podpory. Podrobnosti najdete v kroku 11 níže.

1. V https://portal.azure.com vyberte možnost **help + podpora**.

   ![Nápověda a podpora](./media/resource-manager-core-quotas-request/helpsupport.png)
 
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

7. Na panelu **Podrobnosti kvóty** vyberte **model nasazení** a vyberte **umístění.**

   ![Podrobnosti o kvótě DM](./media/resource-manager-core-quotas-request/1-7.png)

8. Pro vybrané umístění vyberte možnost **typ** hodnota jako **standardní**. Můžete požadovat typy kvót Standard a quote z jediného případu podpory prostřednictvím podpory více výběrů v poli **typ** . Další informace o **zvýšení limitů kvóty** na tuto [stránku](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)

   ![Rodina SKU](./media/resource-manager-core-quotas-request/1-8.png)

9. Výběr **rodin SKU** , které vyžadují zvýšení

   ![Rodina SKU](./media/resource-manager-core-quotas-request/1-9.png)

10. Zadejte nová omezení, která chcete v předplatném. Chcete-li odebrat řádek, zrušte položku SKU z rozevírací nabídky Rodina SKU nebo klikněte na ikonu zahodit "x". 

   ![Nová omezení](./media/resource-manager-core-quotas-request/1-10.png)

11. Chcete-li požádat o kvótu pro více než jedno umístění, můžete v rozevíracím seznamu ověřit jiné **umístění** a vybrat vhodný typ virtuálního počítače. Tento krok předem načte rodiny SKU vybrané pro předchozí **umístění** na nové místo a můžete jednoduše zadat nové limity, které chcete.

   ![Více míst](./media/resource-manager-core-quotas-request/1-11.png)
   
12. Po zadání požadované kvóty pro každou rodinu SKU klikněte na **Uložit a pokračujte** na panelu Podrobnosti o kvótě, aby bylo možné pokračovat v vytváření žádosti o podporu.

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-usages--quota-blade"></a>Požadavek na standardní zvýšení kvóty vCPU na řadu virtuálních počítačů na úrovni předplatného pomocí okna využití + kvóta

Pomocí následujících pokynů můžete vytvořit žádost o podporu prostřednictvím okna Azure využití + kvóty dostupného v Azure Portal.

Můžete také **požádat o kvótu pro více oblastí** prostřednictvím jediného případu podpory. Podrobnosti najdete v kroku 10 níže.

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
   
6. Na panelu **Podrobnosti kvóty** vyberte model nasazení a vyberte umístění.

   ![Okno problém kvóty](./media/resource-manager-core-quotas-request/1-1-6.png)

7. Pro vybrané umístění vyberte možnost **typ** hodnota jako **standardní**. V rámci podpory vícenásobného výběru v poli **typ** můžete požádat o typy kvót standardní i nízkou prioritu z jediného případu podpory. Přečtěte si další informace o **zvýšení limitů vCPU** na této [stránce](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

   ![Vybraná série SKU](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   
8. Výběr **rodin SKU** , které vyžadují zvýšení

   ![Vybraná série SKU](./media/resource-manager-core-quotas-request/1-1-8.png)

9. Zadejte nová omezení, která chcete v předplatném. Chcete-li odebrat řádek, zrušte položku SKU z rozevírací nabídky Rodina SKU nebo klikněte na ikonu zahodit "x". 

   ![Nová žádost o kvótu SKU](./media/resource-manager-core-quotas-request/1-1-9.png)
   

10. Chcete-li požádat o kvótu pro více než jedno umístění, můžete v rozevíracím seznamu ověřit jiné **umístění** a vybrat vhodný typ virtuálního počítače. Tento krok předem načte rodiny SKU vybrané pro předchozí **umístění** na nové místo a můžete jednoduše zadat nové limity, které chcete.
   
    ![Nová žádost o kvótu SKU](./media/resource-manager-core-quotas-request/1-1-10.png)
 
