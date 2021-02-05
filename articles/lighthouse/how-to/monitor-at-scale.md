---
title: Monitorování delegovaných prostředků ve velkém měřítku
description: Naučte se efektivně používat Azure Monitor protokoly škálovatelným způsobem napříč klienty zákazníka, které spravujete.
ms.date: 02/02/2021
ms.topic: how-to
ms.openlocfilehash: 8847c2e5ee4986d35ad676440720b150794003e8
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575902"
---
# <a name="monitor-delegated-resources-at-scale"></a>Monitorování delegovaných prostředků ve velkém měřítku

Jako poskytovatel služeb můžete mít k [Azure Lighthouse](../overview.md)k dispozici několik klientů pro zákazníky. Azure Lighthouse umožňuje poskytovatelům služeb provádět operace ve velkém měřítku napříč několika klienty najednou, což usnadňuje úlohy správy.

V tomto tématu se dozvíte, jak používat [Azure monitor protokoly](../../azure-monitor/platform/data-platform-logs.md) škálovatelným způsobem napříč klienty zákazníka, které spravujete. I když v tomto tématu odkazujeme na poskytovatele služeb a zákazníky, tyto doprovodné materiály se vztahují také na [podniky, které používají Azure Lighthouse ke správě více tenantů](../concepts/enterprise.md).

> [!NOTE]
> Ujistěte se, že uživatelé ve vaší správě tenantů mají k dispozici [potřebné role pro správu pracovních prostorů Log Analytics](../../azure-monitor/platform/manage-access.md#manage-access-using-azure-permissions) na delegovaných zákaznických předplatných.

## <a name="create-log-analytics-workspaces"></a>Vytváření Log Analyticsch pracovních prostorů

Aby bylo možné shromažďovat data, budete muset vytvořit Log Analytics pracovní prostory. Tyto pracovní prostory Log Analytics jsou jedinečná prostředí pro data shromažďovaná pomocí Azure Monitor. Každý pracovní prostor má své vlastní úložiště a konfiguraci dat a zdroje dat a řešení jsou nakonfigurovány tak, aby ukládaly data do konkrétního pracovního prostoru.

Tyto pracovní prostory doporučujeme vytvořit přímo v klientech zákazníka. Tímto způsobem jejich data zůstanou ve svých klientech a nebudete je exportovat do vašich představ. Díky tomu je možné centralizované monitorování všech prostředků nebo služeb, které Log Analytics podporuje, a poskytuje větší flexibilitu v tom, jaké typy dat sledujete.

> [!TIP]
> Jakýkoli účet Automation, který se používá pro přístup k datům z Log Analytics pracovního prostoru, se musí vytvořit ve stejném tenantovi jako pracovní prostor.

Pracovní prostor Log Analytics můžete vytvořit pomocí [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md), pomocí rozhraní příkazového [řádku Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)nebo pomocí [Azure PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md).

> [!IMPORTANT]
> I v případě, že jsou všechny pracovní prostory vytvořeny v tenantovi zákazníka, musí být poskytovatel prostředků Microsoft. Insights také zaregistrován v rámci předplatného ve správě tenanta.

## <a name="deploy-policies-that-log-data"></a>Nasazení zásad, které protokolují data

Po vytvoření pracovního prostoru Log Analytics můžete nasadit [Azure Policy](../../governance/policy/index.yml) v rámci hierarchií zákazníků, aby se diagnostická data odesílala do příslušného pracovního prostoru v každém tenantovi. Přesné zásady, které nasadíte, se můžou lišit v závislosti na typech prostředků, které chcete monitorovat.

Další informace o vytváření zásad najdete v tématu [kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů](../../governance/policy/tutorials/create-and-manage.md). Tento [Nástroj Community](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator) poskytuje skript, který vám pomůže vytvořit zásady pro monitorování konkrétních typů prostředků, které zvolíte.

Když určíte, které zásady se mají nasadit, můžete [je nasadit na škálovatelné své delegované předplatné](policy-at-scale.md).

## <a name="analyze-the-gathered-data"></a>Analyzovat shromážděná data

Po nasazení zásad se data přihlásí do Log Analytics pracovních prostorů, které jste vytvořili v každém tenantovi zákazníka. Pokud chcete získat přehledy pro všechny spravované zákazníky, můžete pomocí nástrojů, jako jsou [Azure monitor sešity](../../azure-monitor/platform/workbooks-overview.md) , shromažďovat a analyzovat informace z více zdrojů dat.

## <a name="view-alerts-across-customers"></a>Zobrazit výstrahy mezi zákazníky

Můžete zobrazit [výstrahy](../../azure-monitor/platform/alerts-overview.md) pro delegovaná předplatná v klientech zákazníků, které spravujete.

Ve spravovaném tenantovi můžete [výstrahy protokolu aktivit vytvářet, zobrazovat a spravovat](../../azure-monitor/platform/alerts-activity-log.md) v Azure Portal nebo prostřednictvím rozhraní API a nástrojů pro správu.

Pokud chcete výstrahy aktualizovat automaticky napříč více zákazníky, použijte dotaz na [Azure Resource Graph](../../governance/resource-graph/overview.md) k filtrování výstrah. Dotaz můžete připnout na řídicí panel a vybrat všechny příslušné zákazníky a odběry. Například níže uvedený dotaz zobrazí výstrahy o závažnosti 0 a 1 a aktualizuje každých 60 minut.

```kusto
alertsmanagementresources
| where type == "microsoft.alertsmanagement/alerts"
| where properties.essentials.severity =~ "Sev0" or properties.essentials.severity =~ "Sev1"
| where properties.essentials.monitorCondition == "Fired"
| where properties.essentials.startDateTime > ago(60m)
| project StartTime=properties.essentials.startDateTime,name,Description=properties.essentials.description, Severity=properties.essentials.severity, subscriptionId
| sort by tostring(StartTime)
```

## <a name="next-steps"></a>Další kroky

- Prozkoumejte tento [ukázkový sešit MVP (MVP](https://github.com/scautomation/Azure-Automation-Update-Management-Workbooks)), který sleduje generování sestav dodržování předpisů pomocí [dotazování Update Management protokolů](../../automation/update-management/query-logs.md) napříč více Log Analytics pracovních prostorů. 
- Přečtěte si o [Azure monitor](../../azure-monitor/index.yml).
- Přečtěte si o [Azure Monitorch protokolech](../../azure-monitor/platform/data-platform-logs.md).
- Přečtěte si o [prostředích pro správu mezi klienty](../concepts/cross-tenant-management-experience.md).