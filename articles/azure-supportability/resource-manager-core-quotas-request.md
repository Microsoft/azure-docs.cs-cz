---
title: "Virtuální procesory Azure Resource Manager kvótu zvýšit požadavky | Microsoft Docs"
description: "Žádosti o zvýšení kvóty Azure Resource Manager virtuální procesory"
author: ganganarayanan
ms.author: gangan
ms.date: 1/18/2017
ms.topic: article
ms.service: microsoft-docs
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c22a6dde0067385a1bf8d889cc76178bb44dd0ac
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2017
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>Žádosti o zvýšení kvóty virtuální procesor Resource Manager

Kvóty správce prostředků virtuální procesor prosazují na úrovni rodiny SKU a úrovně oblast.
Další informace o tom, jak se vynucují kvóty na [předplatného Azure a omezení služby](http://aka.ms/quotalimits) stránky.
Další informace o SKU rodiny, může porovnat nákladů a výkonu na [ceny služeb Virtual Machines](http://aka.ms/pricingcompute) stránky.

Chcete-li požádat o zvýšení, vytvoření kvóty případu podpory pro Vcpu na portálu Azure [https://portal.azure.com](https://portal.azure.com).

> [!NOTE]
> Zjistěte, jak [vytvořit žádost o podporu](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) na portálu Azure

1. Na stránce nové žádosti o podporu vyberte typ problému jako "Kvóty" a typ kvóty jako "Jader".

    ![Okno základy kvóty](./media/resource-manager-core-quotas-request/Basics-blade.png)

2. Vyberte model nasazení jako "Resource Manager" a vyberte umístění.

    ![Okno problém kvóty](./media/resource-manager-core-quotas-request/Problem-step.png)

3. Vyberte rodiny SKU, které vyžadují zvětšit.

    ![Řada SKU vybrané](./media/resource-manager-core-quotas-request/SKU-selected.png)

4. Zadejte nové omezení, která má být u předplatného.

    ![Skladová položka novou žádost o kvótu](./media/resource-manager-core-quotas-request/SKU-new-quota.png)

- Chcete-li odebrat, zrušte zaškrtnutí políčka SKU z rozevíracího seznamu rodiny SKU nebo klikněte na ikonu zahození "x".
Po zadání požadovaných kvóty pro každou řadu SKU, klikněte na tlačítko "Další" na stránce problém krok a pokračujte vytvoření žádosti o podporu.
