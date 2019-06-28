---
title: Na virtuální počítač Azure zvýšit kvótu virtuálních procesorů požadavky | Dokumentace Microsoftu
description: za kvóty virtuálních procesorů virtuálního počítače zvýšit požadavků
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 79879cd7f5ea5af1b794735f32e6e1367458e124
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310730"
---
# <a name="vm-series-vcpu-limit-increase"></a>Zvýšení limitu virtuálních jader řady virtuálních počítačů

Kvóty virtuálních procesorů Resource Manageru pro virtuální počítače a škálovací sady virtuálních počítačů se vynucují na dvou úrovních pro každé předplatné, v jednotlivých oblastech. 

První vrstva je **celkový počet místních virtuálních procesorů limit** (přes všechny řady virtuálních počítačů), a druhé vrstvy je **za virtuální počítač řady virtuálních procesorů omezit** (jako je například virtuální procesory řady D-series). Pokaždé, když je nový virtuální počítač nasadit, součet nové i stávající virtuální procesory využití pro tento virtuální počítač řady nesmí překročit kvóty virtuálních procesorů schválené pro konkrétní řady virtuálních počítačů. Dále celkový počet virtuálních procesorů nové i stávající počet nasazených na všechny řady virtuálních počítačů může být maximálně celkový počet překročení kvóty virtuálních procesorů schválení předplatného. Pokud některý z těchto kvót je překročena, nasazení virtuálního počítače nebude možné.
Můžete požádat o zvýšení limitu kvóty virtuálních procesorů pro virtuální počítače řady z webu Azure portal. Zvýšení kvóty řadu virtuálních počítačů automaticky zvětší, celkový počet místních virtuálních procesorů limit stejný jmenovce. 

Když se vytvoří nový odběr, nemusí být celkový počet místních virtuálních procesorů výchozí shoduje se součtem výchozí kvóty virtuálních procesorů pro všechny jednotlivé řady virtuálních počítačů. Výsledkem může být předplatné s dostatečnou kvótu pro každé jednotlivé řady virtuálních počítačů, které chcete nasadit, ale ne dostatečnou kvótu pro celkový počet místních virtuálních procesorů pro všechna nasazení. V takovém případě bude muset odeslat žádost o zvýšení limitu celkový počet místních virtuálních procesorů explicitně. Celkový počet místních virtuálních procesorů limit nesmí překročit součet schválené kvóty přes všechny řady virtuálních počítačů pro oblast.

Další informace o kvótách na [stránce kvóty virtuálních procesorů virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) a [předplatného Azure a omezení služeb](https://aka.ms/quotalimits) stránky. 

Teď si můžete vyžádat navýšení prostřednictvím příkazu **Nápověda a podpora** okno nebo **využití a kvóty** okno na portálu. 

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Požadavku na zvýšení kvóty virtuálních procesorů řady virtuálních počítačů na úrovni pomocí předplatného **Nápověda a podpora** okno

Postupujte podle níže uvedených pokynů a vytvořte žádost o podporu prostřednictvím Azure "Nápověda a podpora" okně k dispozici na webu Azure Portal. 

1. Z https://portal.azure.com vyberte **Nápověda a podpora**.

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


## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Požadavek na zvýšení kvóty virtuálních procesorů řady virtuálních počítačů na úrovni pomocí předplatného **využití a kvóty** okno

Postupujte podle pokynů níže použití k vytvoření žádosti o podporu prostřednictvím Azure 'využití a kvóty"okno, které jsou k dispozici na webu Azure Portal. 

1. Z https://portal.azure.com vyberte **předplatná**.

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

