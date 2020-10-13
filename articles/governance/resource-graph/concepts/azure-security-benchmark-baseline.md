---
title: Směrný plán zabezpečení Azure Resource graphu pro Azure Security test
description: Základní hodnoty zabezpečení Azure Resource graphu poskytují pokyny a zdroje pro implementaci doporučení zabezpečení, která jsou určená v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: resource-graph
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a85fdf1e1e27b1ce5abb0c7890b717d48ba3bd3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89230445"
---
# <a name="azure-resource-graph-security-baseline-for-azure-security-benchmark"></a>Směrný plán zabezpečení Azure Resource graphu pro Azure Security test

Tyto základní hodnoty zabezpečení se týkají pokynů z [Azure Security srovnávacích testů](../../../security/benchmarks/overview.md) na Azure Resource Graph. Srovnávací test zabezpečení Azure poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure. Obsah je seskupen podle **ovládacích prvků zabezpečení** , které jsou definovány pomocí srovnávacího testu zabezpečení Azure, a souvisejících pokynů vztahujících se na Azure Resource Graph. **Ovládací prvky** , které se nevztahují na Azure Resource Graph, se vyloučily. Pokud chcete zjistit, jak se graf prostředků Azure kompletně mapuje na srovnávací test zabezpečení Azure, přečtěte si [kompletní soubor mapování standardních hodnot azure Virtual Network Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).



## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v tématu [řízení zabezpečení: identita a řízení přístupu](../../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné**materiály: Azure Resource Graph poskytuje přístup k typům a vlastnostem prostředků na základě řízení přístupu na základě role Azure (RBAC). Pravidelně provádějte audit a zkontrolujte přístup k objektům zabezpečení (uživatelům, skupinám a účtům služeb), abyste se ujistili, že dotazy vrátí výsledky pro příslušné prostředky.

* [Oprávnění v Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)

* [Jak používat recenze Azure identity Access](../../../active-directory/governance/access-reviews-overview.md)


**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [řízení zabezpečení: Ochrana dat](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.

**Pokyny**: k řízení přístupu k datům a prostředkům použijte řízení přístupu na základě role Azure (Azure RBAC). Pokud chcete použít Azure Resource Graph, musíte mít také odpovídající přístup k prostředkům, které chcete dotazovat. Tento přístup by měl být určený jen pro čtení a měl by být povolený jenom pro požadované pracovníky.

* [Oprávnění v Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)

* [Jak nakonfigurovat službu Azure RBAC](../../../role-based-access-control/role-assignments-rest.md)


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [řízení zabezpečení: inventář a Správa prostředků](../../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat a zjišťovat všechny podporované prostředky v rámci předplatných, skupin pro správu a tenantů. Ujistěte se, že máte ve svém tenantovi příslušná oprávnění a že máte v rámci předplatných všechny odběry Azure i prostředky.

* [Jak vytvářet dotazy pomocí Azure Resource graphu](../first-query-portal.md)

* [Pochopení Azure RBAC](../../../role-based-access-control/overview.md)


**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné**materiály: Vytvořte inventarizaci schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky podle potřeb vaší organizace. Pomocí Azure Resource graphu se můžete dotazovat na schválené prostředky Azure a historii změn (Preview) a zkontrolovat snímky a zjistit, co se změnilo.

* [Dotazování prostředků Azure pomocí grafu prostředků Azure](../first-query-portal.md)

* [Získání změn prostředků](../how-to/get-resource-changes.md)


**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat a zjišťovat prostředky ve vašich předplatných, skupinách pro správu a klientech. Ujistěte se, že jsou všechny prostředky Azure v prostředí schválené.

* [Dotazování prostředků Azure pomocí grafu prostředků Azure](../first-query-portal.md)

* [Ukázky: Úvodní dotazy pro Azure Resource Graph](../samples/starter.md)


**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](../../../security/benchmarks/overview.md)
- Další informace o [plánech zabezpečení Azure](../../../security/benchmarks/security-baselines-overview.md)
