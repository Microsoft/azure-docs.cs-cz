---
title: Základní hodnoty zabezpečení Azure pro Azure Policy
description: Základní Azure Policy zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: azure-policy
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6e8bb4cf715c6cb8d0729399c1985376de18687b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561283"
---
# <a name="azure-security-baseline-for-azure-policy"></a>Základní hodnoty zabezpečení Azure pro Azure Policy

Tato základní hodnota zabezpečení se vztahuje na pokyny z [srovnávacího testu zabezpečení Azure](../../../security/benchmarks/overview.md) na Azure Policy. Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure. Obsah se seskupuje podle **domén dodržování předpisů** a **kontrolních** mechanismů zabezpečení, které definuje srovnávací test zabezpečení Azure, a souvisejících pokynů vztahujících se na Azure Policy. **Ovládací prvky** , které se nevztahují k Azure Policy byly vyloučeny. Pokud chcete zjistit, jak Azure Policy kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot Azure Policy Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Mapování kontrolních prvků srovnávacích testů zabezpečení Azure na předdefinované definice zásad prostřednictvím integrované iniciativy najdete v článku [dodržování předpisů v rámci legislativních předpisů: Azure Security test](../samples/azure-security-benchmark.md).

Azure Policy používá pojem _vlastnictví_ místo _zodpovědnosti_. Podrobnosti o _vlastnictví_ najdete v tématu [Azure Policy definice zásad](./definition-structure.md#type) a [sdílená odpovědnost v cloudu](../../../security/fundamentals/shared-responsibility.md).

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: Azure Policy používá protokol aktivit, které jsou automaticky povoleny, aby zahrnoval zdroj událostí, datum, uživatele, časové razítko, zdrojové adresy, cílové adresy a další užitečné prvky.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../../../azure-monitor/essentials/diagnostic-settings.md)

- [Principy protokolování a různých typů protokolů v Azure](../../../azure-monitor/essentials/platform-logs-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a Access Control](../../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu. Pomocí [Azure Active Directory (Azure AD) Privileged Identity Management](../../../active-directory/privileged-identity-management/pim-configure.md) privilegované role nebo [Azure Resource Manager](../../../azure-resource-manager/management/overview.md)můžete také povolit řešení s dostatečným přístupem (za běhu).

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy předdefinované definice – Microsoft. GuestConfiguration**:

[!INCLUDE [Resource Policy for Microsoft.GuestConfiguration 3.3](../../../../includes/policy/standards/asb/rp-controls/microsoft.guestconfiguration-3-3.md)]

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Používejte zabezpečené pracovní stanice spravované Azure pro úlohy správy

**Doprovodné** materiály: použití pracovních stanic s privilegovaným přístupem (privilegovaným přístupem) s nakonfigurovaným vícefaktorového ověřováním, které je nakonfigurované pro přihlášení k prostředkům Azure a jejich konfigurace.

- [Další informace o pracovních stanicích s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Postup povolení vícefaktorového ověřování v Azure](../../../active-directory/authentication/howto-mfa-getstarted.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC. 

**Pokyny**: k řízení přístupu k Azure Policy použijte řízení přístupu na základě role Azure (Azure RBAC).

- [Oprávnění Azure RBAC v Azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy)

- [Jak nakonfigurovat službu Azure RBAC](../../../role-based-access-control/role-assignments-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné** materiály: pomocí Azure monitor s protokoly aktivit můžete vytvořit upozornění, když se změny probíhají v Azure Policy.

- [Vytvoření upozornění pro události protokolu aktivit Azure](../../../azure-monitor/alerts/alerts-activity-log.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie. Pomocí Azure Policy _Upravit_ efekt můžete vykázat a vymáhat dodržování předpisů a konzistentní zásady správného řízení značek.

- [Kurz: vytvoření a Správa zásad](../tutorials/create-and-manage.md)

- [Kurz: Správa zásad správného řízení značek](../tutorials/govern-tags.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné** materiály: Vytvořte soupis schválených definic zásad a přiřazení zásad podle potřeb vaší organizace.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných.

- [Jak nakonfigurovat a spravovat Azure Policy](../tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](../../../security/benchmarks/overview.md).
- Další informace o [základních úrovních zabezpečení Azure](../../../security/benchmarks/security-baselines-overview.md)