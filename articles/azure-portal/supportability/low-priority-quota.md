---
title: Kvóta bodu virtuální počítač | Microsoft Docs
description: Zvýšení limitů kvót tím, že se vyžádají požadavky na kvótu
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9567df95e4e66d9c6b82b29cb9bba9adaf39349d
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897296"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Kvóta na místě: Zvyšte limity pro všechny řady virtuálních počítačů.

Virtuální počítače na místě poskytují jiný model využití Azure. Umožňují vám snížit náklady na Exchangi, aby mohli Azure odebírat virtuální počítače podle potřeby pro nasazení instancí virtuálních počítačů s průběžnými platbami nebo pro rezervované instance. Další informace o virtuálních počítačích s přímým odkazem najdete v tématu [virtuální počítače Azure na místě pro službu Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

Azure Resource Manager podporuje dva typy kvót vCPU pro virtuální počítače:
* *Virtuální počítače* s průběžnými platbami a *rezervované instance virtuálních počítačů* podléhají *standardní vCPU kvótě*.
* *Virtuální počítače na místě* podléhají *vCPU kvótě*. 

Pro typ *kvóty vCPU typu quote* se vynutily správce prostředků vCPU kvóty napříč všemi dostupnými řadami virtuálních počítačů jako s jediným oblastním limitem.

Když nasadíte nový virtuální počítač s přímým odkazem, nemusíte mít všechny nové a stávající vCPU využití pro všechny instance virtuálních počítačů na místě překročit schválenou kvótu vCPU limitu. Pokud dojde k překročení kvóty na místě, nasazení na místě není povoleno. 

Tento článek pojednává o tom, jak požádat o zvýšení limitu kvóty bodu vCPU pomocí Azure Portal. 

Další informace o standardních kvótách vCPU najdete v tématu věnovaném [kvótám virtuálních počítačů vCPU](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) a [omezením předplatného a služeb Azure](https://aka.ms/quotalimits). 

Další informace o zvýšení limitu vCPU podle oblasti najdete v tématu [standardní kvóta. zvýšení limitu regionálního vCPU](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests).

## <a name="request-a-quota-limit-increase-from-the-help--support-pane"></a>Vyžádat zvýšení kvóty z podokna pomoc a podpora 

Pokud chcete požádat o zvýšení kvóty omezení pro všechny řady virtuálních počítačů v podokně **nápovědu a podpora** , udělejte toto:

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

1. Vyberte kartu **Podrobnosti** a potom v části **Podrobnosti o problému**vyberte **zadat podrobnosti**a zadejte další informace, které vám pomůžou zpracovat vaši žádost.

   ![Odkaz "poskytnout podrobnosti"](./media/resource-manager-core-quotas-request/provide-details.png)
   
1. V podokně **Podrobnosti kvóty** v pravém horním rohu udělejte toto:

   ![Podokno s podrobnostmi o kvótě](./media/resource-manager-core-quotas-request/3-7.png)

   a. V rozevíracím seznamu **model nasazení** vyberte příslušný model.

   b. V rozevíracím seznamu **umístění** vyberte umístění. Pro vybrané umístění v části **typy**v poli **Vyberte typ** zadejte **bodové**. 
   
   ![Karta Podrobnosti nové žádosti o podporu](./media/resource-manager-core-quotas-request/3-8.png)

    V části **typy**můžete požadovat typy kvót Standard a quote z jediného případu podpory prostřednictvím podpory vícenásobného výběru. 
    
    Další informace najdete v tématu [standardní kvóta: vCPU limit řady na virtuální počítače](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

   c. Zadejte nové omezení kvóty, které chcete pro toto předplatné. 
 
   ![Textové pole "nové omezení vCPU"](./media/resource-manager-core-quotas-request/3-9.png)

1. Pokud chcete požádat o zvýšení kvóty pro více než jedno umístění, vyberte v rozevíracím seznamu další umístění a pak vyberte vhodný typ virtuálního počítače. Pak můžete zadat limit, který platí pro další umístění.

   ![Další místa v podokně Podrobnosti o kvótě](./media/resource-manager-core-quotas-request/3-10.png)

1. Pokud chcete pokračovat v vytváření žádosti o podporu, vyberte **Uložit a pokračovat** .

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Vyžádat zvýšení kvóty v podokně odběry

Pokud chcete požádat o zvýšení kvóty omezení pro všechny řady virtuálních počítačů z podokna **předplatná** , udělejte toto:

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

   ![Podokno s podrobnostmi o kvótě](./media/resource-manager-core-quotas-request/3-2-6.png)
 
   a. V rozevíracím seznamu **model nasazení** vyberte příslušný model.

   b. V rozevíracím seznamu **umístění** vyberte umístění. 
   
   c. Pro vybrané umístění v části **typy**v poli **Vyberte typ** zadejte **bodové**.

   ![Podokno s podrobnostmi o kvótě](./media/resource-manager-core-quotas-request/3-2-7.png)

   Další informace najdete v tématu [standardní kvóta: vCPU limit řady na virtuální počítače](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

   d. Zadejte nové omezení kvóty, které chcete pro toto předplatné.

   ![Textové pole "nové omezení vCPU"](./media/resource-manager-core-quotas-request/3-2-8.png)
 
1. Pokud chcete požádat o zvýšení kvóty pro více než jedno umístění, vyberte v rozevíracím seznamu další umístění a pak vyberte vhodný typ virtuálního počítače. Pak můžete zadat limit, který platí pro další umístění.

   ![Další místa v podokně Podrobnosti o kvótě](./media/resource-manager-core-quotas-request/3-2-9.png)
 
1. Pokud chcete pokračovat v vytváření žádosti o podporu, vyberte **Uložit a pokračovat** .


