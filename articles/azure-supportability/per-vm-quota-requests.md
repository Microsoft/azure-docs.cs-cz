---
title: Požádat o zvýšení limitů kvót vCPU na každou řadu virtuálních počítačů Azure | Microsoft Docs
description: Tento článek popisuje, jak vyžádat omezení kvóty na vCPU virtuálního počítače.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: fc59ffda245834c716067bc63e6c3745fa5d23b3
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750192"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Standardní kvóta: zvýšení limitů podle řady virtuálních počítačů

Azure Resource Manager podporuje dva typy kvót vCPU pro virtuální počítače:
* *Virtuální počítače* s průběžnými platbami a *rezervované instance virtuálních počítačů* podléhají *standardní vCPU kvótě*.
* *Virtuální počítače na místě* podléhají *vCPU kvótě*. 

Standardní kvóta vCPU pro průběžné platby a rezervované instance virtuálních počítačů se vynutily na dvou úrovních pro každé předplatné v každé oblasti:
* První úroveň je *Celkový místní vCPU limit* (v rámci všech řad virtuálních počítačů).
* Druhá úroveň je *vCPU limit řady per-VM* (například Dv3-Series vCPU). 

Když nasadíte nový virtuální počítač s přímým odkazem, nesmí celkové nové a stávající využití vCPU pro tuto řadu virtuálních počítačů překročit schválenou kvótu vCPU pro tuto řadu virtuálních počítačů. Celkový počet nových a existujících vCPU nasazených ve všech řadách virtuálních počítačů by navíc neměl překročit celkovou schválenou kvótu místní vCPU pro předplatné. Pokud dojde k překročení některé z těchto kvót, nasazení virtuálního počítače se nepovoluje.

Můžete požádat o zvýšení limitu kvóty vCPU pro řady virtuálních počítačů pomocí Azure Portal. Zvýšení kvóty řady virtuálních počítačů automaticky zvýší celkový rozsah regionálního vCPUu o stejné množství. 

Další informace o standardních kvótách vCPU najdete v tématu věnovaném [kvótám virtuálních počítačů vCPU](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) a [omezením předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests). 

Další informace o zvýšení limitu vCPU podle oblasti pro standardní kvótu najdete v tématu [standardní kvóta: zvýšení limitů podle oblasti](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests). 

Další informace o zvýšení limitů vCPU virtuálních počítačů najdete v tématu věnovaném [kvótám na místě: zvýšení limitů pro všechny řady virtuálních počítačů](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

Můžete požádat o zvýšení standardních omezení vCPU kvót na řadu virtuálních počítačů jedním ze dvou způsobů, jak je popsáno v následujících částech.

## <a name="request-a-standard-quota-increase-from-the-help--support-pane"></a>Vyžádejte si standardní kvótu v podokně pomoc a podpora.

Pokud si chcete vyžádat standardní zvýšení kvóty vCPU na řadu virtuálních počítačů v podokně **help + support** , udělejte toto: 

> [!NOTE]
> Můžete také požádat o zvýšení kvóty na více oblastí prostřednictvím jediného případu podpory. Podrobnosti najdete v kroku 8.

1. V levém podokně [Azure Portal](https://portal.azure.com)vyberte možnost **help + podpora**.

   ![Odkaz Help + Support (podpora a podpora)](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. V podokně **pomoc a podpora** vyberte **Nová žádost o podporu**. 

    ![Nová žádost o podporu](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. V rozevíracím seznamu **typ problému** vyberte **omezení služby a předplatné (kvóty)** .

   ![Rozevírací seznam "typ problému"](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. V rozevíracím seznamu **předplatné** vyberte předplatné, jehož kvótu chcete zvýšit.

   ![Rozevírací seznam předplatné](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. V rozevíracím seznamu **typ kvóty** vyberte **COMPUTE – virtuální počítač (jádra – vCPU) zvýšení limitu předplatného**. 

   ![Rozevírací seznam "typ kvóty"](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Na kartě **Podrobnosti** v části **Podrobnosti o problému**vyberte **zadat podrobnosti**a potom zadejte další informace, které vám pomůžou zpracovat vaši žádost.

   ![Odkaz "poskytnout podrobnosti"](./media/resource-manager-core-quotas-request/provide-details.png)

1. V podokně **Podrobnosti kvóty** v pravém horním rohu udělejte toto:

   ![Podokno s podrobnostmi o kvótě](./media/resource-manager-core-quotas-request/1-7.png)

   a. V rozevíracím seznamu **model nasazení** vyberte příslušný model.

   b. V rozevíracím seznamu **umístění** vyberte umístění. Pro vybrané umístění v části **typy**v poli **Vyberte typ** zadejte **Standard**.

   ![Podokno s podrobnostmi o kvótě – typy kvót](./media/resource-manager-core-quotas-request/1-8.png)

   V části **typy**můžete požadovat typy kvót Standard a quote z jediného případu podpory prostřednictvím podpory vícenásobného výběru.
   
   Další informace o zvýšení limitů kvót na místě najdete v tématu [virtuální počítače Azure na místě pro službu Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

   c. Pod **standardním** rozevíracím seznamem vyberte řadu SKU, jejichž kvóty chcete zvýšit.

   ![Podokno Podrobnosti kvóty – série SKU](./media/resource-manager-core-quotas-request/1-9.png)

   d. Zadejte nové limity kvót, které chcete pro toto předplatné. Chcete-li ze seznamu odebrat SKU, zrušte zaškrtnutí políčka vedle skladové položky (SKU) nebo vyberte ikonu **Odstranit** (X). 

   ![Textové pole "nové omezení vCPU"](./media/resource-manager-core-quotas-request/1-10.png)

1. Pokud chcete požádat o zvýšení kvóty pro více než jedno umístění, vyberte v rozevíracím seznamu další umístění a pak vyberte vhodný typ virtuálního počítače. Pak můžete zadat limit, který platí pro další umístění.

   ![Další místa v podokně Podrobnosti o kvótě](./media/resource-manager-core-quotas-request/1-11.png)
   
1. Pokud chcete pokračovat v vytváření žádosti o podporu, vyberte **Uložit a pokračovat** .

## <a name="request-a-standard-quota-increase-from-the-subscriptions-pane"></a>Požadavek na zvýšení standardní kvóty z podokna předplatné

Pokud si chcete vyžádat standardní zvýšení kvóty vCPU na řadu virtuálních počítačů v podokně **předplatná** , udělejte toto:

> [!NOTE]
> Můžete také požádat o zvýšení kvóty na více oblastí prostřednictvím jediného případu podpory. Podrobnosti najdete v kroku 7.

1. V levém podokně [Azure Portal](https://portal.azure.com)vyberte **předplatná**.

   ![Odkaz předplatné](./media/resource-manager-core-quotas-request/subscriptions.png)

1. Vyberte předplatné, jehož kvóta se má zvýšit.

   ![Podokno odběry](./media/resource-manager-core-quotas-request/select-subscription.png)

1. V levém podokně **> název Předplatného\<** vyberte možnost **využití a kvóty**.

   ![Odkaz využití a kvóty](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. V pravém horním rohu vyberte **požádat o zvýšení**.

   ![Zvýšení žádosti](./media/resource-manager-core-quotas-request/request-increase.png)

1. V rozevíracím seznamu **typ kvóty** vyberte **COMPUTE – virtuální počítač (jádra – vCPU) zvýšení limitu předplatného**.

   ![Rozevírací seznam "typ kvóty"](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
1. V podokně **Podrobnosti kvóty** v pravém horním rohu udělejte toto:

   ![Podokno s podrobnostmi o kvótě](./media/resource-manager-core-quotas-request/1-1-6.png)

   a. V rozevíracím seznamu **model nasazení** vyberte příslušný model.

   b. V rozevíracím seznamu **umístění** vyberte umístění. 
   
   c. Pro vybrané umístění vyberte v části **typy**možnost **Vybrat typ**a pak zaškrtněte políčko **standardní** .

   ![Zaškrtávací políčko Standard](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   V části **typy**můžete požadovat typy kvót standardní i nízkou prioritu z jediného případu podpory prostřednictvím podpory vícenásobného výběru.
   
   Další informace o zvýšení limitů kvót na místě najdete v tématu [virtuální počítače Azure na místě pro službu Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

   d. Pod **standardním** rozevíracím seznamem vyberte řadu SKU, jejichž kvóty chcete zvýšit.

   ![Podokno Podrobnosti kvóty – série SKU](./media/resource-manager-core-quotas-request/1-1-8.png)

   e. Zadejte nové limity kvót, které chcete pro toto předplatné. Chcete-li ze seznamu odebrat SKU, zrušte zaškrtnutí políčka u položky SKU nebo vyberte možnost **Odstranit** (X). 

   ![Textové pole "nové omezení vCPU"](./media/resource-manager-core-quotas-request/1-1-9.png)
   
1. Pokud chcete požádat o zvýšení kvóty pro více než jedno umístění, vyberte v rozevíracím seznamu další umístění a pak vyberte vhodný typ virtuálního počítače. 

   Tento krok předem načte řadu SKU, kterou jste vybrali pro starší umístění. Zadejte omezení kvóty, která chcete použít pro další řady.
   
   ![Další místa v podokně Podrobnosti o kvótě](./media/resource-manager-core-quotas-request/1-1-10.png)
 
1. Pokud chcete pokračovat v vytváření žádosti o podporu, vyberte **Uložit a pokračovat** .
