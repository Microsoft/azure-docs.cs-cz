---
title: Jak aktualizovat pravidla upozornění nebo pravidla akcí, když se jejich cílový prostředek přesune do jiné oblasti Azure
description: Základní informace o tom, jak aktualizovat pravidla upozornění nebo pravidla akcí, když se jejich cílový prostředek přesune do jiné oblasti Azure.
author: harelbr
ms.author: harelbr
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/14/2021
ms.openlocfilehash: d21ee7a60d11a154737c5380ec20d3e9c4490962
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786058"
---
# <a name="how-to-update-alert-rules-or-action-rules-when-their-target-resource-moves-to-a-different-azure-region"></a>Jak aktualizovat pravidla upozornění nebo pravidla akcí, když se jejich cílový prostředek přesune do jiné oblasti Azure

Tento článek popisuje, proč můžou být ovlivněná stávající pravidla a [pravidla](./alerts-action-rules.md) pro [výstrahy](./alerts-overview.md) při přesunu dalších prostředků Azure mezi oblastmi a jak tyto problémy identifikovat a vyřešit. Podívejte se do dokumentace k hlavnímu [prostředku přesunutí](../../azure-resource-manager/management/move-resources-overview.md) , kde najdete další informace o tom, kdy se prostředek přesouvá mezi oblastmi, které jsou užitečné, a kontrolní seznam návrhu procesu přesunutí.

## <a name="why-the-problem-exists"></a>Proč existuje problém

Pravidla výstrah a pravidla akcí odkazují na další prostředky Azure. Mezi příklady patří [virtuální počítače Azure](../../site-recovery/azure-to-azure-tutorial-migrate.md), [azure SQL](../../azure-sql/database/move-resources-across-regions.md)a [Azure Storage](../../storage/common/storage-account-move.md). Když přesunete prostředky, na které pravidla odkazují, tato pravidla pravděpodobně přestanou fungovat správně, protože nemůžou najít prostředky, na které odkazují.

Existují dva hlavní důvody, proč můžou vaše pravidla přestat fungovat po přesunutí cílových prostředků:

- Rozsah pravidla explicitně odkazuje na původní prostředek.
- Vaše pravidlo upozornění je založené na metrikách.

## <a name="rule-scope-explicitly-refers-to-the-old-resource"></a>Obor pravidla explicitně odkazuje na starý prostředek.

Když přesunete prostředek, změní se ve většině případů jeho ID prostředku. Po pozadí systém replikuje prostředek do nové oblasti před jeho odstraněním ze staré oblasti. Tento proces vyžaduje, aby v malých časových intervalech existovaly dva prostředky a tedy dvě odlišná ID prostředků. Vzhledem k tomu, že ID prostředku musí být jedinečný, musí být během procesu vytvořen nový identifikátor. 

**Jak přesouvá prostředek vliv na existující pravidla?**

Pravidla výstrah a pravidla akcí mají obor prostředků, na které se vztahují. Rozsahem může být celé předplatné, skupina prostředků nebo jeden nebo několik konkrétních prostředků.
Tady je například pravidlo s oborem se dvěma prostředky (dva virtuální počítače):

![Pravidlo upozornění na více prostředků](media/alerts-resource-move/multi-resource-alert-rule.png)

Pokud obor pravidla výslovně zmiňuje prostředek a tento prostředek přesunul a změnil jeho ID prostředku, pak toto pravidlo bude hledat nesprávný nebo neexistující prostředek, a proto selže.

**Jak tento problém vyřešit?**

Aktualizujte nebo znovu vytvořte ovlivněné pravidlo, které odkazuje na nový prostředek. Postup aktualizace oboru najdete dále v tomto článku.

Tento problém se týká těchto typů pravidel:

- Pravidla upozornění protokolu aktivit
- Pravidla akcí
- Upozornění na metriku – Další informace najdete v dalších částech [pravidla výstrahy na základě metrik](#alert-rules-based-on-metrics).

> [!NOTE]
> Pravidla upozornění prohledávání protokolu a pravidla výstrah inteligentního detektoru nejsou ovlivněna, protože jejich obor je buď pracovní prostor, nebo Application Insights. Ani jeden z těchto oborů aktuálně nepodporuje oblast.

## <a name="alert-rules-based-on-metrics"></a>Pravidla výstrah založená na metrikách

Metriky, které emitují prostředky Azure, jsou regionální. Pokaždé, když se prostředek přesune do nové oblasti, začne vysílat jeho metriky v této nové oblasti. V důsledku toho je potřeba aktualizovat nebo znovu vytvořit všechna pravidla výstrah založená na metrikách, aby odkazovala na aktuální datový proud metrik ve správné oblasti.

Toto vysvětlení platí pro pravidla [Upozornění na metriky](alerts-metric-overview.md) a [pravidla upozornění testů dostupnosti](../app/monitor-web-app-availability.md).

Pokud jste přesunuli **všechny** prostředky v oboru, nemusíte pravidlo znovu vytvořit. Můžete jenom aktualizovat jakékoli pole pravidla výstrahy, jako je popis pravidla výstrahy, a uložit ho.
Pokud se přesunuly **jenom některé** prostředky v oboru, je potřeba odebrat přesunuté prostředky z existujícího pravidla a vytvořit nové pravidlo, které pokrývá jenom přesunuté prostředky.

## <a name="procedures-to-fix-problems"></a>Postupy řešení problémů

### <a name="identifying-rules-associated-with-a-moved-resource-from-the-azure-portal"></a>Identifikují se pravidla přidružená k přesunutému prostředku z Azure Portal

- **Pro pravidla upozornění** – můžete přejít na výstrahy > spravovat pravidla výstrah > filtrovat podle obsahujícího předplatného a přesunutého prostředku.
> [!NOTE]
> Pravidla upozornění protokolu aktivit tento proces nepodporují. Není možné aktualizovat rozsah pravidla upozornění protokolu aktivit a odkazovat na prostředek v jiném předplatném. Místo toho můžete vytvořit nové pravidlo, které nahradí starou.

- **Pro pravidla akcí** – přejděte na výstrahy > spravovat akce > pravidla akcí (preview) > filtr podle obsahujícího předplatného a přesunutého prostředku.

### <a name="change-scope-of-a-rule-from-the-azure-portal"></a>Změna rozsahu pravidla z Azure Portal

1. Kliknutím na něj otevřete pravidlo, které jste identifikovali v předchozím kroku.
2. V části **prostředek** klikněte na **Upravit** a podle potřeby upravte rozsah.
3. Podle potřeby upravte další vlastnosti pravidla.
4. Klikněte na **Uložit**.

![Změnit rozsah pravidla výstrahy](media/alerts-resource-move/change-alert-rule-scope.png)

### <a name="change-the-scope-of-a-rule-using-azure-resource-manager-templates"></a>Změna rozsahu pravidla pomocí Azure Resource Manager šablon

1. Získejte šablonu Azure Resource Manager pravidla.   Export šablony pravidla z Azure Portal:
   1. Přejděte do části skupiny prostředků na portálu a otevřete skupinu prostředků, která pravidlo obsahuje.
   2. V části Přehled zaškrtněte políčko **Zobrazit skrytý typ** a proveďte filtrování podle příslušného typu pravidla.
   3. Vyberte příslušné pravidlo a zobrazte jeho podrobnosti.
   4. V části **Nastavení** vyberte **Exportovat šablonu**.
2. Upravte šablonu. V případě potřeby ho rozdělte do dvou pravidel (v některých případech upozornění na metriky, jak je uvedeno výše).
3. Znovu nasaďte šablonu.

### <a name="change-scope-of-a-rule-using-rest-api"></a>Změna rozsahu pravidla pomocí REST API

1. Získá stávající pravidlo ([výstrahy metrik](/rest/api/monitor/metricalerts/get), [výstrahy protokolu aktivit](/rest/api/monitor/activitylogalerts/get)).
2. Úprava oboru ([výstrahy protokolu aktivit](/rest/api/monitor/activitylogalerts/update))
3. Znovu nasaďte pravidlo ([výstrahy metrik](/rest/api/monitor/metricalerts/createorupdate), [výstrahy protokolu aktivit](/rest/api/monitor/activitylogalerts/createorupdate)).

### <a name="change-scope-of-a-rule-using-powershell"></a>Změna rozsahu pravidla pomocí PowerShellu

1. Získá stávající pravidlo ([výstrahy metrik](/powershell/module/az.monitor/get-azmetricalertrulev2), [výstrahy protokolu aktivit](/powershell/module/az.monitor/get-azactivitylogalert), [pravidla akcí](/powershell/module/az.alertsmanagement/get-azactionrule)).
2. Upravte obor. V případě potřeby ho rozdělte do dvou pravidel (v některých případech upozornění na metriky, jak je uvedeno výše).
3. Znovu nasaďte pravidlo ([výstrahy metrik](/powershell/module/az.monitor/add-azmetricalertrulev2), [výstrahy protokolu aktivit](/powershell/module/az.monitor/enable-azactivitylogalert), [pravidla akcí](/powershell/module/az.alertsmanagement/set-azactionrule)).

### <a name="change-the-scope-of-a-rule-using-azure-cli"></a>Změna rozsahu pravidla pomocí Azure CLI

1.  Získá stávající pravidlo ([výstrahy metrik](/cli/azure/monitor/metrics/alert#az_monitor_metrics_alert_show), [výstrahy protokolu aktivit](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log-alert_list)).
2.  Přímo aktualizovat rozsah pravidla ([výstrahy metrik](/cli/azure/monitor/metrics/alert#az_monitor_metrics_alert_update), [výstrahy protokolu aktivit](/cli/azure/monitor/activity-log/alert/scope))
3.  V případě potřeby ho rozdělte do dvou pravidel (v některých případech upozornění na metriky, jak je uvedeno výše).

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak opravit jiné problémy s [upozorněními na výstrahy](alerts-troubleshoot.md), [upozorněními na metriky](alerts-troubleshoot-metric.md)a [výstrahami protokolu](alerts-troubleshoot-log.md).