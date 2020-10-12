---
title: Monitorování delegovaných prostředků ve velkém měřítku
description: Naučte se efektivně používat Azure Monitor protokoly škálovatelným způsobem napříč klienty zákazníka, které spravujete.
ms.date: 08/12/2020
ms.topic: how-to
ms.openlocfilehash: fdd0147737da47613d6b7ef1bf6005e4c03de0dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88163284"
---
# <a name="monitor-delegated-resources-at-scale"></a>Monitorování delegovaných prostředků ve velkém měřítku

Jako poskytovatel služeb můžete mít k [Azure Lighthouse](../overview.md)k dispozici několik klientů pro zákazníky. Azure Lighthouse umožňuje poskytovatelům služeb provádět operace ve velkém měřítku napříč několika klienty najednou, což usnadňuje úlohy správy.

V tomto tématu se dozvíte, jak používat [Azure monitor protokoly](../../azure-monitor/platform/data-platform-logs.md) škálovatelným způsobem napříč klienty zákazníka, které spravujete.

> [!TIP]
> I když v tomto tématu odkazujeme na poskytovatele služeb a zákazníky, tyto doprovodné materiály se vztahují také na [podniky, které používají Azure Lighthouse ke správě více tenantů](../concepts/enterprise.md).

## <a name="create-log-analytics-workspaces"></a>Vytváření Log Analyticsch pracovních prostorů

Aby bylo možné shromažďovat data, budete muset vytvořit Log Analytics pracovní prostory. Tyto pracovní prostory Log Analytics jsou jedinečná prostředí pro data shromažďovaná pomocí Azure Monitor. Každý pracovní prostor má své vlastní úložiště a konfiguraci dat a zdroje dat a řešení jsou nakonfigurovány tak, aby ukládaly data do konkrétního pracovního prostoru.

Tyto pracovní prostory doporučujeme vytvořit přímo v klientech zákazníka. Tímto způsobem jejich data zůstanou ve svých klientech a nebudete je exportovat do vašich představ. Díky tomu je možné centralizované monitorování všech prostředků nebo služeb, které Log Analytics podporuje, a poskytuje větší flexibilitu v tom, jaké typy dat sledujete.

Pracovní prostor Log Analytics můžete vytvořit pomocí [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md), pomocí rozhraní příkazového [řádku Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)nebo pomocí [Azure PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md).

## <a name="deploy-policies-that-log-data"></a>Nasazení zásad, které protokolují data

Po vytvoření pracovního prostoru Log Analytics můžete nasadit [Azure Policy](../../governance/policy/index.yml) v rámci hierarchií zákazníků, aby se diagnostická data odesílala do příslušného pracovního prostoru v každém tenantovi. Přesné zásady, které nasadíte, se můžou lišit v závislosti na typech prostředků, které chcete monitorovat.

Další informace o vytváření zásad najdete v tématu [kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů](../../governance/policy/tutorials/create-and-manage.md). Tento [Nástroj Community](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator) poskytuje skript, který vám pomůže vytvořit zásady pro monitorování konkrétních typů prostředků, které zvolíte.

Když určíte, které zásady se mají nasadit, můžete [je nasadit na škálovatelné své delegované předplatné](policy-at-scale.md).

## <a name="analyze-the-gathered-data"></a>Analyzovat shromážděná data

Po nasazení zásad se data přihlásí do Log Analytics pracovních prostorů, které jste vytvořili v každém tenantovi zákazníka. Pokud chcete získat přehledy pro všechny spravované zákazníky, můžete pomocí nástrojů, jako jsou [Azure monitor sešity](../../azure-monitor/platform/workbooks-overview.md) , shromažďovat a analyzovat informace z více zdrojů dat. 

## <a name="next-steps"></a>Další kroky

- Prozkoumejte tento [ukázkový sešit MVP (MVP](https://github.com/scautomation/Azure-Automation-Update-Management-Workbooks)), který sleduje generování sestav dodržování předpisů pomocí [dotazování Update Management protokolů](../../automation/update-management/update-mgmt-query-logs.md) napříč více Log Analytics pracovních prostorů. 
- Přečtěte si o [Azure monitor](../../azure-monitor/index.yml).
- Přečtěte si o [Azure Monitorch protokolech](../../azure-monitor/platform/data-platform-logs.md).
- Přečtěte si o [prostředích pro správu mezi klienty](../concepts/cross-tenant-management-experience.md).
