---
title: Kvóty a limity škálování v testovacím prostředí v Azure DevTest Labs | Microsoft Docs
description: Tento článek popisuje, jak můžete škálovat testovací prostředí v Azure DevTest Labs. Zobrazení kvót a omezení využití a vyžádání zvýšení.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2166eaab073e2abc24ca24494ae13eb876db1fcf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87533950"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Kvóty a omezení škálování v DevTest Labs
Při práci v DevTest Labs si můžete všimnout, že existují určitá výchozí omezení pro některé prostředky Azure, což může ovlivnit službu DevTest Labs. Tato omezení se označují jako **kvóty**.

> [!NOTE]
> Služba DevTest Labs neukládá žádné kvóty. Všechny kvóty, se kterými se můžete setkat, jsou výchozími omezeními celkového předplatného Azure.

Každý prostředek Azure můžete použít až do dosažení kvóty. Každé předplatné má samostatné kvóty a využití se sleduje za předplatné.

Například každé předplatné má výchozí kvótu 20 jader. Pokud tedy vytváříte virtuální počítače v testovacím prostředí se čtyřmi jádry, můžete vytvořit pouze pět virtuálních počítačů.

[Omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) uvádí některé z nejběžnějších kvót pro prostředky Azure. Prostředky nejčastěji používané v testovacím prostředí, pro které se můžete setkat s kvótami, zahrnovat jádra virtuálních počítačů, veřejné IP adresy, síťové rozhraní, spravované disky, přiřazení role Azure a okruhy ExpressRoute.

## <a name="view-your-usage-and-quotas"></a>Zobrazení využití a kvót
Tyto kroky ukazují, jak zobrazit aktuální kvóty v předplatném pro konkrétní prostředky Azure a zjistit, jaké procento z každé používané kvóty jste použili.

1. Přihlaste se na [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **Další služby** a potom v seznamu vyberte **fakturace** .
1. V okně fakturace vyberte předplatné.
4. Vyberte **využití a kvóty**.

   ![Tlačítko použití a kvóty](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas-new.png)

   Zobrazí se okno využití + kvóty, v němž najdete seznam různých prostředků dostupných v tomto předplatném a procento kvóty, která se používá pro jednotlivé prostředky.

   ![Kvóty a využití](./media/devtest-lab-scale-lab/devtestlab-view-quotas-new.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Vyžádání dalších prostředků ve vašem předplatném
Pokud dosáhnete limitu kvóty, můžete zvýšit výchozí limit prostředku v předplatném na maximální limit, jak je popsáno v tématu [omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

Tyto kroky ukazují, jak požádat o zvýšení kvóty pomocí [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **Další služby**, vyberte **fakturace** a pak vyberte **využití + kvóty**.
1. V okně používání a kvóty vyberte tlačítko **zvýšení žádosti** .

   ![Tlačítko zvýšení žádosti](./media/devtest-lab-scale-lab/devtestlab-request-increase-new.png)

1. K dokončení a odeslání žádosti vyplňte požadované informace na všech třech kartách **nového formuláře žádosti o podporu** .

   ![Formulář žádosti o zvýšení](./media/devtest-lab-scale-lab/devtestlab-support-form-new.png)

[Porozumění omezením a zvýšením Azure](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) získáte další informace o tom, jak kontaktovat podporu Azure a požádat o zvýšení kvóty.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Další kroky
* Prozkoumejte [galerii šablon rychlého startu Azure Resource Manager DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
