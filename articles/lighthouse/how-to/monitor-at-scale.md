---
title: Sledování delegovaných prostředků ve velkém měřítku
description: Zjistěte, jak efektivně využívat protokoly azure monitoru škálovatelným způsobem napříč klienty zákazníků, které spravujete.
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 9a600cec524c9f715ada9da09dea35f3baa98151
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985197"
---
# <a name="monitor-delegated-resources-at-scale"></a>Sledování delegovaných prostředků ve velkém měřítku

Jako poskytovatel služeb můžete mít na palubě více klienty zákazníků pro správu delegovaných prostředků Azure. [Azure Lighthouse](../overview.md) umožňuje poskytovatelům služeb provádět operace ve velkém měřítku napříč několika tenanty najednou, což zefektivňuje úlohy správy.

Toto téma ukazuje, jak používat [protokoly monitorování Azure](../../azure-monitor/platform/data-platform-logs.md) škálovatelným způsobem napříč klienty zákazníků, které spravujete.

## <a name="create-log-analytics-workspaces"></a>Vytvoření pracovních prostorů analýzy protokolů

Chcete-li shromažďovat data, budete muset vytvořit pracovní prostory Log Analytics. Tyto pracovní prostory Log Analytics jsou jedinečná prostředí pro data shromážděná službou Azure Monitor. Každý pracovní prostor má své vlastní úložiště dat a konfiguraci a zdroje dat a řešení jsou nakonfigurovány tak, aby ukládali data v určitém pracovním prostoru.

Doporučujeme vytvořit tyto pracovní prostory přímo v klientech zákazníků. Tímto způsobem jejich data zůstanou v jejich tenantech, spíše než exportovat do vás. To také umožňuje centralizované monitorování všech prostředků nebo služeb podporovaných log analytics, což vám dává větší flexibilitu na jaké typy dat, které sledujete.

Pracovní prostor Analýzy protokolů můžete vytvořit pomocí [portálu Azure](../../azure-monitor/learn/quick-create-workspace.md), pomocí [azure cli](../../azure-monitor/learn/quick-create-workspace-cli.md)nebo pomocí [Azure PowerShellu](../../azure-monitor/learn/quick-create-workspace-posh.md).

## <a name="deploy-policies-that-log-data"></a>Nasazení zásad, které protokolují data

Po vytvoření pracovních prostorů Analýzy protokolů můžete nasadit [zásady Azure](../../governance/policy/index.yml) napříč hierarchiemi zákazníků, aby se diagnostická data odesílala do příslušného pracovního prostoru v každém tenantovi. Přesné zásady, které nasadíte, se mohou lišit v závislosti na typech prostředků, které chcete sledovat.

Další informace o vytváření zásad najdete [v tématu Výuka: Vytvoření a správa zásad pro vynucení dodržování předpisů](../../governance/policy/tutorials/create-and-manage.md). Tento [komunitní nástroj](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator) poskytuje skript, který vám pomůže vytvořit zásady pro sledování konkrétních typů prostředků, které zvolíte.

Až určíte, které zásady se mají nasadit, můžete [je nasadit do delegovaných předplatných ve velkém měřítku](policy-at-scale.md).

## <a name="analyze-the-gathered-data"></a>Analýza shromážděných dat

Po nasazení zásad se data zaznamená do pracovních prostorů Analýzy protokolů, které jste vytvořili v každém klientovi zákazníka. Chcete-li získat přehledy mezi všemi spravovanými zákazníky, můžete pomocí nástrojů, jako jsou [sešity Azure Monitor,](../../azure-monitor/platform/workbooks-overview.md) shromažďovat a analyzovat informace z více zdrojů dat.

## <a name="next-steps"></a>Další kroky

- Další informace o [Azure Monitor](../../azure-monitor/index.yml).
- Další informace o [protokolech monitorování Azure](../../azure-monitor/platform/data-platform-logs.md).
- Další informace o [prostředích správy mezi tenanty](../concepts/cross-tenant-management-experience.md).
