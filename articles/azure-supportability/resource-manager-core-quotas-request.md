---
title: Žádosti o zvýšení kvóty virtuálních procesorů Azure Resource Manageru | Dokumentace Microsoftu
description: Žádostech o navýšení kvóty virtuálních procesorů Azure Resource Manageru
author: sowmyavenkat86
ms.author: svenkat
ms.date: 05/09/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9d19d1a993d574777aa630b9c58f2472b94716cd
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479371"
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>Žádostech o navýšení kvóty virtuálních procesorů Resource Manageru

Kvóty virtuálních procesorů Resource Manageru pro virtuální počítače a škálovací sady virtuálních počítačů se vynucují na dvou úrovních pro každé předplatné, v jednotlivých oblastech. 

První úroveň je celkový počet místních virtuálních procesorů limit (v všechny řady virtuálních počítačů) a druhé vrstvy je omezení virtuálních počítačů řady virtuálních procesorů (jako je například virtuální procesory řady D-series). Pokaždé, když je nový virtuální počítač nasadit, součet nové i stávající virtuální procesory využití pro tento virtuální počítač řady nesmí překročit schválení této konkrétní virtuální počítač řady dalších kvóty virtuálních procesorů, počet celkový počet virtuálních procesorů, které jsou nasazené ve všech virtuálních počítačů řady by neměl být delší než celkový počet překročení kvóty virtuálních procesorů  schválení předplatného. Pokud některý z těchto kvót je překročena, nasazení virtuálního počítače nebude možné.
Můžete požádat o zvýšení limitu kvóty virtuálních procesorů pro virtuální počítače řady z webu Azure portal. Zvýšení kvóty řadu virtuálních počítačů automaticky zvětší, celkový počet místních virtuálních procesorů limit stejný jmenovce. 

Když si nové předplatné je vytvořen, celkový počet místních virtuálních procesorů nemusí být rovna součtu výchozí kvóty virtuálních procesorů pro všechny jednotlivých virtuálních počítačů řady tento může vést k přihlášení k odběru s dostatečnou kvótu pro každé jednotlivé řady virtuálních počítačů, které chcete nasadit výchozí , ale předplatné nemá dostatečnou kvótu pro celkový počet místních virtuálních procesorů pro všechna nasazení. V takovém případě bude muset odeslat žádost o zvýšení zvýšení celkové kvóty pro omezení oblasti explicitně. Celkový počet místních virtuálních procesorů limit nesmí překročit součet schválené kvóty přes všechny řady virtuálních počítačů pro oblast.

Další informace o kvótách na [stránce kvóty virtuálních procesorů virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) a [předplatného Azure a omezení služeb](https://aka.ms/quotalimits) stránky. 

Teď si můžete vyžádat navýšení prostřednictvím příkazu **Nápověda a podpora** okno nebo **využití a kvóty** okno na portálu. 

## <a name="request-quota-increase-using-the-help--support-blade"></a>Žádost o kvótu zvýšení prostřednictvím **Nápověda a podpora** okno

Postupujte podle níže uvedených pokynů a vytvořte žádost o podporu prostřednictvím Azure "Nápověda a podpora" okně k dispozici na webu Azure Portal. 

1. Z https://portal.azure.comvyberte **Nápověda a podpora**.

![Nápověda a podpora](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Vyberte **Nová žádost o podporu**. 

![Nová žádost o podporu](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. V rozevírací nabídce Typ problému zvolte **limity služby a předplatného (kvóty)** .

![Rozevírací nabídce Typ problému](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Vyberte předplatné, které potřebuje navýšit kvótu.

![Vyberte předplatné newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Vyberte **Compute - VM (počet jader virtuálních procesorů) předplatné zvýší** v **typ kvóty** rozevíracího seznamu. 

![Vyberte typ kvóty](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. V **podrobnosti o problému**, poskytují další informace, které pomohou proces vaši žádost kliknutím **zadání podrobností o**.

![Zadejte podrobnosti](./media/resource-manager-core-quotas-request/provide-details.png)

7. V **podrobnosti o kvótě** panelu, vyberte model nasazení a vyberte umístění.

![Podrobnosti o kvótě DM](./media/resource-manager-core-quotas-request/quota-details.png)

8. Vyberte **řad SKU** , které vyžadují zvýšení. 

![Rodina SKU](./media/resource-manager-core-quotas-request/sku-family.png)

9. Zadejte nové limity, které chcete v rámci předplatného. Chcete-li odebrat, zrušte zaškrtnutí políčka skladovou Položku z rozevíracího seznamu řady SKU nebo klikněte na ikonu zrušení "x". Po zadání požadované kvóty pro každou řadu SKU, klikněte na **uložit a pokračovat** na panelu podrobností kvóty pokračovat ve vytváření žádosti o podporu.

![Nové limity](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-quota-increase-at-subscription-level-using-usages--quota"></a>Požádat o zvýšení kvóty na úrovni předplatného pomocí využití a kvóty

Postupujte podle pokynů níže použití k vytvoření žádosti o podporu prostřednictvím Azure 'využití a kvóty"okno, které jsou k dispozici na webu Azure Portal. 

1. Z https://portal.azure.comvyberte **předplatná**.

![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Vyberte předplatné, které potřebuje navýšit kvótu.

![Výběr předplatného](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Vyberte **využití a kvóty**

![Vyberte využití a kvóty](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. V pravém horním rohu vyberte **požádat o zvýšení**.

![Zvýšení počtu žádostí](./media/resource-manager-core-quotas-request/request-increase.png)

5. Vyberte **výpočetní-VM (počet jader virtuálních procesorů) předplatné zvýší** jako typ nabídky. 

![Vyplňte formulář](./media/resource-manager-core-quotas-request/forms.png)
   
6. V **podrobnosti o kvótě** panelu, vyberte model nasazení a vyberte umístění.

![Okno kvóta problému](./media/resource-manager-core-quotas-request/problemstep.png)

7. Vyberte **řad SKU** , které vyžadují zvýšení.

![Vybraná řady SKU](./media/resource-manager-core-quotas-request/sku-family.png)

8. Zadejte nové limity, které chcete v rámci předplatného. Chcete-li odebrat, zrušte zaškrtnutí políčka skladovou Položku z rozevíracího seznamu řady SKU nebo klikněte na ikonu zrušení "x". Po zadání požadované kvóty pro každou řadu SKU, klikněte na **uložit a pokračovat** na stránce problém krok a pokračujte vytvoření žádosti o podporu.

![Skladová položka novou žádost o kvótu](./media/resource-manager-core-quotas-request/new-limits.png)


