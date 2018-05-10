---
title: Škálování kvóty a limity ve svém testovacím prostředí v Azure DevTest Labs | Microsoft Docs
description: Zjistěte, jak změnit velikost testovacího prostředí v Azure DevTest Labs
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 5a179c0c6b777ee6b2afdd0f2e9267d247665d8d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Škálování kvóty a omezení v DevTest Labs
Při práci v DevTest Labs, můžete si všimnout, že existují určitá omezení výchozí některé prostředky Azure, což může ovlivnit DevTest Labs služby. Tato omezení jsou označovány jako **kvóty**.

> [!NOTE]
> Službu DevTest Labs nebude ukládat žádné kvóty. Žádné kvóty, kterými se můžete setkat se výchozí omezení celkového předplatné.

Každý prostředek Azure můžete použít, dokud se nedostanete dosáhlo kvóty. Každé předplatné má samostatné kvóty a sledování využití podle předplatného.

Například každé předplatné má výchozí kvótu 20 jader. Ano při vytváření virtuálních počítačů ve svém testovacím prostředí s čtyři jader, pak můžete pouze vytvořit pět virtuálních počítačů. 

[Azure předplatné a omezení služby](https://docs.microsoft.com/azure/azure-subscription-service-limits) uvádí některé z nejběžnějších kvóty pro prostředky Azure. Prostředky se nejčastěji používá v testovacím prostředí a pro které můžete narazit kvóty, zahrnete jader virtuálního počítače, veřejné IP adresy, síťové rozhraní, spravované disky, přiřazení role RBAC, okruhy ExpressRoute.

## <a name="view-your-usage-and-quotas"></a>Zobrazit využití a kvóty
Tyto kroky ukazují, jak chcete zobrazit aktuální kvóty ve vašem předplatném pro konkrétní prostředky Azure a chcete zobrazit, jaké procento každé kvóty jste už použili.

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **více služeb**a potom vyberte **fakturace** ze seznamu.
1. V okně fakturace vyberte předplatné.
4. Vyberte **využití + kvóty**.

   ![Tlačítko využití a kvóty](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   Využití + kvóty okno se zobrazí, výpis různé prostředky k dispozici v tomto předplatném a procento kvóty, který se používá na prostředek.

   ![Kvóty a využití](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Vyžaduje další prostředky ve vašem předplatném
Dosažení limitu kvóty výchozí limit prostředků v předplatném může být zvýšeno až maximální limit, jak je popsáno v [předplatné Azure a omezení služby](https://docs.microsoft.com/azure/azure-subscription-service-limits).

Tyto kroky ukazují, jak požádat o zvýšení kvóty prostřednictvím [portál Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **více služeb**, vyberte **fakturace**a potom vyberte **využití + kvóty**.
1. V využití + kvóty vyberte **požádat o zvýšení** tlačítko.

   ![Žádost o zvýšení tlačítko](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. K dokončení a odeslat žádost o, zadejte požadované informace o všech tří karet **nová žádost o podporu** formuláře.

   ![Formulář žádosti o zvýšení](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

[Pochopení omezení Azure a zvyšuje](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) poskytuje další informace o kontaktovat podporu Azure o navýšení kvóty.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Další postup
* Prozkoumejte [Galerie šablon DevTest Labs Azure Resource Manager QuickStart](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
