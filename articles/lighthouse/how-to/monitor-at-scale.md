---
title: Monitorování delegovaných prostředků ve velkém měřítku
description: Naučte se efektivně používat Azure Monitor protokoly škálovatelným způsobem napříč klienty zákazníka, které spravujete.
ms.date: 02/03/2020
ms.topic: how-to
ms.openlocfilehash: bdd91cec650d6810d1d62230219f840a172141d9
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84634111"
---
# <a name="monitor-delegated-resources-at-scale"></a>Monitorování delegovaných prostředků ve velkém měřítku

Jako poskytovatel služeb můžete mít k dispozici více zákazníků pro správu delegovaných prostředků Azure. [Azure Lighthouse](../overview.md) umožňuje poskytovatelům služeb provádět operace ve velkém měřítku napříč několika klienty najednou, což usnadňuje úlohy správy.

V tomto tématu se dozvíte, jak používat [Azure monitor protokoly](../../azure-monitor/platform/data-platform-logs.md) škálovatelným způsobem napříč klienty zákazníka, které spravujete.

## <a name="create-log-analytics-workspaces"></a>Vytváření Log Analyticsch pracovních prostorů

Aby bylo možné shromažďovat data, budete muset vytvořit Log Analytics pracovní prostory. Tyto pracovní prostory Log Analytics jsou jedinečná prostředí pro data shromažďovaná pomocí Azure Monitor. Každý pracovní prostor má své vlastní úložiště a konfiguraci dat a zdroje dat a řešení jsou nakonfigurovány tak, aby ukládaly data do konkrétního pracovního prostoru.

Tyto pracovní prostory doporučujeme vytvořit přímo v klientech zákazníka. Tímto způsobem jejich data zůstanou ve svých klientech a nebudete je exportovat do vašich představ. Díky tomu je možné centralizované monitorování všech prostředků nebo služeb, které Log Analytics podporuje, a poskytuje větší flexibilitu v tom, jaké typy dat sledujete.

Pracovní prostor Log Analytics můžete vytvořit pomocí [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md), pomocí rozhraní příkazového [řádku Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)nebo pomocí [Azure PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md).

## <a name="deploy-policies-that-log-data"></a>Nasazení zásad, které protokolují data

Po vytvoření pracovního prostoru Log Analytics můžete nasadit [Azure Policy](../../governance/policy/index.yml) v rámci hierarchií zákazníků, aby se diagnostická data odesílala do příslušného pracovního prostoru v každém tenantovi. Přesné zásady, které nasadíte, se můžou lišit v závislosti na typech prostředků, které chcete monitorovat.

Další informace o vytváření zásad najdete v tématu [kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů](../../governance/policy/tutorials/create-and-manage.md). Tento [Nástroj Community](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator) poskytuje skript, který vám pomůže vytvořit zásady pro monitorování konkrétních typů prostředků, které zvolíte.

Když určíte, které zásady se mají nasadit, můžete [je nasadit na škálovatelné své delegované předplatné](policy-at-scale.md).

## <a name="analyze-the-gathered-data"></a>Analyzovat shromážděná data

Po nasazení zásad se data přihlásí do Log Analytics pracovních prostorů, které jste vytvořili v každém tenantovi zákazníka. Pokud chcete získat přehledy pro všechny spravované zákazníky, můžete pomocí nástrojů, jako jsou [Azure monitor sešity](../../azure-monitor/platform/workbooks-overview.md) , shromažďovat a analyzovat informace z více zdrojů dat.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [Azure monitor](../../azure-monitor/index.yml).
- Přečtěte si o [Azure Monitorch protokolech](../../azure-monitor/platform/data-platform-logs.md).
- Přečtěte si o [prostředích pro správu mezi klienty](../concepts/cross-tenant-management-experience.md).
