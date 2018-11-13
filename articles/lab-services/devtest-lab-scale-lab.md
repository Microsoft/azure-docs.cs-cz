---
title: Kvóty a omezení horizontálně testovacího prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak škálovat testovacího prostředí ve službě Azure DevTest Labs
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
ms.openlocfilehash: 00f64deaa5e02f9bb74baaf86bfdf71690a3345d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248244"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Kvóty a omezení škálování ve službě DevTest Labs
Při práci ve službě DevTest Labs, můžete si všimnout, že jsou některé výchozí limity pro několik prostředků Azure, které mohou ovlivnit služby DevTest Labs. Tato omezení jsou označovány jako **kvóty**.

> [!NOTE]
> Služba DevTest Labs neurčuje žádné kvóty. Žádné kvóty, které se můžete setkat se výchozí omezení celkové předplatné.

Každého prostředku Azure můžete použít, dokud se nedostanete kvóta. Každé předplatné má samostatné kvóty a využití se sleduje podle předplatného.

Například každé předplatné má výchozí kvóta 20 jader. Proto pokud vytváříte virtuální počítače ve vaší laboratoři s čtyři jádra, pak můžete pouze vytvořit pět virtuálních počítačů. 

[Předplatné a omezení služby Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits) jsou uvedeny některé nejběžnější kvóty pro prostředky Azure. Prostředky se nejčastěji používá v testovacím prostředí, pro které můžete narazit kvóty, zahrnují počet jader virtuálního počítače, veřejné IP adresy, síťové rozhraní, spravované disky, přiřazení role RBAC a okruhy ExpressRoute.

## <a name="view-your-usage-and-quotas"></a>Zobrazit využití a kvóty
Tyto kroky ukazují, jak chcete-li zobrazit aktuální kvóty ve vašem předplatném pro konkrétní prostředky Azure a chcete zobrazit, jaké je procento každá kvóta, jste už použili.

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **další služby**a pak vyberte **fakturace** ze seznamu.
1. V okně fakturace vyberte předplatné.
4. Vyberte **využití a kvóty**.

   ![Tlačítko využití a kvóty](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   Využití a kvóty okno se zobrazí výpis různé prostředky k dispozici v tomto předplatném a procento kvóty, který se používá pro každý prostředek.

   ![Kvóty a využití](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Požadování další prostředky ve vašem předplatném
Pokud dosáhnete limitu kvóty, výchozí limit prostředků v předplatném je možné zvýšit až do maximálního limitu, jak je popsáno v [předplatné Azure a omezení služeb](https://docs.microsoft.com/azure/azure-subscription-service-limits).

Tyto kroky ukazují, jak požádat o zvýšení kvóty prostřednictvím [webu Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **další služby**vyberte **fakturace**a pak vyberte **využití a kvóty**.
1. V využití a kvóty okno, vyberte **požádat o zvýšení** tlačítko.

   ![Požádat o zvýšení tlačítko](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. Chcete-li vyplňte a odešlete požadavek, vyplňte požadované informace o všech tří karet **nová žádost o podporu** formuláře.

   ![Požádat o zvýšení formuláře](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

[Principy Azure omezení a zvýšení](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) poskytuje další informace o kontaktovat podporu Azure o navýšení kvóty.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Další postup
* Prozkoumejte [Galerie šablon QuickStart pro Azure Resource Manageru DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
