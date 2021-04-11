---
title: Základní plán zabezpečení Azure pro Azure Resource Graph
description: Základní hodnoty zabezpečení Azure Resource graphu poskytují pokyny a zdroje pro implementaci doporučení zabezpečení, která jsou určená v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: resource-graph
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ad8968fdb6548da29a031f0e44bd3671f67b5553
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557713"
---
# <a name="azure-security-baseline-for-azure-resource-graph"></a>Základní plán zabezpečení Azure pro Azure Resource Graph

Tato základní hodnota zabezpečení se týká pokynů z [Azure Security test 1,0](../../../security/benchmarks/overview-v1.md) na Azure Resource Graph. Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure.
Obsah je seskupen podle **ovládacích prvků zabezpečení** , které jsou definovány pomocí srovnávacího testu zabezpečení Azure, a souvisejících pokynů vztahujících se na Azure Resource Graph. **Ovládací prvky** , které se nevztahují na Azure Resource Graph, se vyloučily.

 
Pokud chcete zjistit, jak se graf prostředků Azure kompletně mapuje na srovnávací test zabezpečení Azure, přečtěte si [kompletní soubor mapování standardních hodnot zabezpečení Azure Resource Graph](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a Access Control](../../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure Resource Graph poskytuje přístup k typům a vlastnostem prostředků na základě řízení přístupu na základě role Azure (RBAC). Pravidelně provádějte audit a zkontrolujte přístup k objektům zabezpečení (uživatelům, skupinám a účtům služeb), abyste se ujistili, že dotazy vrátí výsledky pro příslušné prostředky.

- [Oprávnění v Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)

- [Jak používat recenze Azure identity Access](../../../active-directory/governance/access-reviews-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC. 

**Pokyny**: použití služby Azure RBAC k řízení přístupu k datům a prostředkům. Pokud chcete použít Azure Resource Graph, musíte mít také odpovídající přístup k prostředkům, které chcete dotazovat. Tento přístup by měl být určený jen pro čtení a měl by být povolený jenom pro požadované pracovníky.

- [Oprávnění v Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)

- [Jak nakonfigurovat službu Azure RBAC](../../../role-based-access-control/role-assignments-rest.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat a zjišťovat všechny podporované prostředky v rámci předplatných, skupin pro správu a tenantů. Ujistěte se, že máte ve svém tenantovi příslušná oprávnění a že máte v rámci předplatných všechny odběry Azure i prostředky.

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../first-query-portal.md)

- [Pochopení Azure RBAC](../../../role-based-access-control/overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné** materiály: Vytvořte inventarizaci schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky podle potřeb vaší organizace. Pomocí Azure Resource graphu se můžete dotazovat na schválené prostředky Azure a historii změn (Preview) a zkontrolovat snímky a zjistit, co se změnilo.

- [Dotazování prostředků Azure pomocí grafu prostředků Azure](../first-query-portal.md)

- [Získání změn prostředků](../how-to/get-resource-changes.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat a zjišťovat prostředky ve vašich předplatných, skupinách pro správu a klientech. Ujistěte se, že jsou všechny prostředky Azure v prostředí schválené.

- [Dotazování prostředků Azure pomocí grafu prostředků Azure](../first-query-portal.md)

- [Ukázky: Úvodní dotazy pro Azure Resource Graph](../samples/starter.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](../../../security/benchmarks/overview.md).
- Další informace o [základních úrovních zabezpečení Azure](../../../security/benchmarks/security-baselines-overview.md)