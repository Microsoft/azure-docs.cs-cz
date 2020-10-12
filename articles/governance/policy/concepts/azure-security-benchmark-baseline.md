---
title: Základní Azure Policy zabezpečení pro Azure Security test
description: Základní Azure Policy zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: azure-policy
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8b18da5e41e235b560918ad26117a0162078862c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540978"
---
# <a name="azure-policy-security-baseline-for-azure-security-benchmark"></a>Základní Azure Policy zabezpečení pro Azure Security test

Tato základní hodnota zabezpečení se vztahuje na pokyny z [srovnávacího testu zabezpečení Azure](../../../security/benchmarks/overview.md) na Azure Policy. Srovnávací test zabezpečení Azure poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure. Obsah se seskupuje podle **domén dodržování předpisů** a **kontrolních** mechanismů zabezpečení, které definuje srovnávací test zabezpečení Azure, a souvisejících pokynů vztahujících se na Azure Policy. **Ovládací prvky** , které se nevztahují k Azure Policy byly vyloučeny. Pokud chcete zjistit, jak Azure Policy kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot Azure Policy Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Mapování kontrolních prvků srovnávacích testů zabezpečení Azure na předdefinované definice zásad prostřednictvím integrované iniciativy najdete v článku [dodržování předpisů v rámci legislativních předpisů: Azure Security test](../samples/azure-security-benchmark.md).

Azure Policy používá pojem _vlastnictví_ místo _zodpovědnosti_. Podrobnosti o _vlastnictví_najdete v tématu [Azure Policy definice zásad](./definition-structure.md#type) a [sdílená odpovědnost v cloudu](../../../security/fundamentals/shared-responsibility.md).


## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v tématu [řízení zabezpečení: protokolování a monitorování](../../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné**materiály: Azure Policy používá protokol aktivit, které jsou automaticky povoleny, aby zahrnoval zdroj událostí, datum, uživatele, časové razítko, zdrojové adresy, cílové adresy a další užitečné prvky.

* [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../../../azure-monitor/platform/diagnostic-settings.md)

* [Principy protokolování a různých typů protokolů v Azure](../../../azure-monitor/platform/platform-logs-overview.md)


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v tématu [řízení zabezpečení: identita a řízení přístupu](../../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné**materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu. 

Pomocí [Azure AD Privileged Identity Management](../../../active-directory/privileged-identity-management/pim-configure.md) privilegovaných rolí nebo [Azure Resource Manager](../../../azure-resource-manager/management/overview.md)můžete také povolit řešení s dostatečným přístupem (za běhu).


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Doprovodné**materiály: použití privilegovaným přístupem (privilegovaných pracovních stanic) s MFA nakonfigurovaným pro přihlášení a konfiguraci prostředků Azure.

* [Další informace o pracovních stanicích s privilegovaným přístupem](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Jak povolit vícefaktorové ověřování v Azure](../../../active-directory/authentication/howto-mfa-getstarted.md)


**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [řízení zabezpečení: Ochrana dat](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.

**Pokyny**: k řízení přístupu k Azure Policy použijte řízení přístupu na základě role Azure (Azure RBAC).

* [Oprávnění Azure RBAC v Azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy)

* [Jak nakonfigurovat službu Azure RBAC](../../../role-based-access-control/role-assignments-portal.md)


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné**materiály: pomocí Azure monitor s protokoly aktivit můžete vytvořit upozornění, když se změny probíhají v Azure Policy.

* [Vytvoření upozornění pro události protokolu aktivit Azure](../../../azure-monitor/platform/alerts-activity-log.md)


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [řízení zabezpečení: inventář a Správa prostředků](../../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné**materiály: použití značek pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie. Pomocí Azure Policy _Upravit_ efekt můžete vykázat a vymáhat dodržování předpisů a konzistentní zásady správného řízení značek.

* [Kurz: vytvoření a Správa zásad](../tutorials/create-and-manage.md)

* [Kurz: Správa zásad správného řízení značek](../tutorials/govern-tags.md)


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné**materiály: Vytvořte soupis schválených definic zásad a přiřazení zásad podle potřeb vaší organizace.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné**materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných.

* [Konfigurace a Správa Azure Policy](../tutorials/create-and-manage.md)


**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](../../../security/benchmarks/overview.md)
- Další informace o [plánech zabezpečení Azure](../../../security/benchmarks/security-baselines-overview.md)
