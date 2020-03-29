---
title: Škálování kvót a limitů v testovacím prostředí v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak můžete škálovat testovací prostředí v Azure DevTest Labs. Zobrazení kvót a omezení využití a žádosti o zvýšení.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: ae624155-9181-45fa-bd2b-1983339b7e0e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: f3299c24bc751263ccd284a4fddb492fba5dd1e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761181"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Škálování kvót a limitů v devTest Labs
Při práci v DevTest Labs, můžete si všimnout, že existují určité výchozí omezení pro některé prostředky Azure, které mohou mít vliv na službu DevTest Labs. Tyto limity se označují jako **kvóty**.

> [!NOTE]
> Služba DevTest Labs neukládá žádné kvóty. Všechny kvóty, se kterými se můžete setkat, jsou výchozí omezení celkového předplatného Azure.

Každý prostředek Azure můžete použít, dokud nedosáhnete jeho kvóty. Každé předplatné má samostatné kvóty a využití se sleduje za předplatné.

Například každé předplatné má výchozí kvótu 20 jader. Takže pokud vytváříte virtuální počítače v testovacím prostředí se čtyřmi jádry, pak můžete vytvořit jenom pět virtuálních počítačů.

[Limity předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) uvádí některé nejběžnější kvóty pro prostředky Azure. Prostředky, které se nejčastěji používají v testovacím prostředí a u kterých se mohou vyskytnou kvóty, zahrnují jádra virtuálních počítačů, veřejné IP adresy, síťové rozhraní, spravované disky, přiřazení rolí RBAC a okruhy ExpressRoute.

## <a name="view-your-usage-and-quotas"></a>Zobrazení využití a kvót
V těchto krocích se zobrazí aktuální kvóty ve vašem předplatném pro konkrétní prostředky Azure a zjistíte, jaké procento z každé kvóty, kterou jste použili.

1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **Další služby**a ze seznamu vyberte **Fakturace.**
1. V okně Fakturace vyberte předplatné.
4. Vyberte **možnost Využití + kvóty**.

   ![Tlačítko Využití a kvóty](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   Zobrazí se okno Využití + kvóty, se seznamem různých prostředků dostupných v tomto předplatném a procento kvóty, která se používá na prostředek.

   ![Kvóty a využití](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Vyžádání dalších prostředků v předplatném
Pokud dosáhnete limitu kvóty, výchozí limit prostředku v předplatném lze zvýšit až na maximální limit, jak je popsáno v [Azure Subscription and Service Limits](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

Tyto kroky ukazují, jak požádat o zvýšení kvóty prostřednictvím [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **Další služby**, vyberte **Fakturace**a pak vyberte **Využití + kvóty**.
1. V okně Využití + kvóty vyberte tlačítko **Požádat o zvýšení.**

   ![Tlačítko Požádat o zvýšení](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. Chcete-li žádost vyplnit a odeslat, vyplňte požadované informace na všech třech kartách formuláře **žádosti o novou podporu.**

   ![Formulář pro zvýšení požadavku](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

[Principy Azure limity a zvýšení](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) poskytuje další informace o kontaktování podpory Azure požádat o zvýšení kvóty.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Další kroky
* Prozkoumejte [galerii šablon rychlého startu nástroje DevTest Labs Azure .](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
