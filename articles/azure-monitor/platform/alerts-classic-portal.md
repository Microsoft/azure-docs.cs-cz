---
title: Vytváření a Správa klasických výstrah metrik pomocí Azure Monitor
description: Naučte se, jak pomocí Azure Portal, CLI nebo PowerShellu vytvářet, zobrazovat a spravovat pravidla upozornění na klasické metriky.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/18/2018
ms.openlocfilehash: 613620ddf4889efb7cf3df95150be97f79724a98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87852136"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>Vytváření, zobrazování a Správa klasických výstrah metrik pomocí Azure Monitor

> [!WARNING]
> Tento článek popisuje, jak vytvořit starší klasické výstrahy metrik. Azure Monitor teď podporuje [novější výstrahy na metriky téměř v reálném čase a nové prostředí výstrah](./alerts-overview.md). Klasické výstrahy jsou [vyřazeny](./monitoring-classic-retirement.md), i když jsou v omezeném rozsahu používány pro prostředky, které ještě nepodporují nové výstrahy.
>

Klasické výstrahy metrik v Azure Monitor poskytují způsob, jak dostávat oznámení, když jedna z vašich metrik překračuje prahovou hodnotu. Klasické výstrahy metriky jsou starší funkce, které umožňují upozorňování jenom na nedimenzionální metriky. Existují nové funkce s názvem výstrahy metriky, které mají vylepšené funkce pro klasické výstrahy metrik. Další informace o nových funkcích výstrah metrik najdete v článku [Přehled výstrah metrik](./alerts-metric-overview.md). V tomto článku popíšeme, jak pomocí Azure Portal, Azure CLI a PowerShellu vytvářet, zobrazovat a spravovat pravidla metriky s klasickými výstrahami.

## <a name="with-azure-portal"></a>S Azure Portal

1. Na [portálu](https://portal.azure.com/)vyhledejte prostředek, který chcete monitorovat, a pak ho vyberte.

2. V části **monitorování** vyberte **výstrahy (klasické)**. Text a ikona se mohou mírně lišit pro různé prostředky. Pokud tady nenajdete **upozornění (Classic)** , můžete ho najít v okně **výstrahy** nebo **pravidla výstrah**.

    ![Monitorování](media/alerts-classic-portal/AlertRulesButton.png)

3. Vyberte příkaz **Přidat výstrahu metriky (Classic)** a potom vyplňte pole.

    ![Přidat výstrahu](media/alerts-classic-portal/AddAlertOnlyParamsPage.png)

4. **Pojmenujte** pravidlo upozornění. Pak zvolte **Popis**, který se také zobrazí v oznamovacích e-mailech.

5. Vyberte **metriku** , kterou chcete monitorovat. Pak zvolte **podmínku** a **prahovou** hodnotu pro metriku. Také vyberte časové **období** , po které musí být pravidlo metriky splněné před triggerem výstrahy. Pokud například použijete období "za posledních 5 minut" a vaše výstraha vyhledá procesor nad 80%, výstraha se aktivuje v případě, že byl procesor po dobu 5 minut konzistentně vyšší než 80%%. Po prvním triggeru se znovu spustí, když procesor zůstane pod 80% po dobu 5 minut. Měření metrik CPU probíhá každou minutu.

6. Vyberte **vlastníky e-mailů...** Pokud chcete, aby správci a spolusprávci dostávali e-mailová oznámení, když se výstraha aktivuje.

7. Pokud chcete odesílat oznámení na další e-mailové adresy, když se výstraha aktivuje, přidejte je do pole **Další e-maily správce** . Více e-mailů oddělujte středníkem v následujícím formátu: *e-mail \@ contoso. com; email2 \@ contoso.com*

8. Vložte do pole **Webhooku** platný identifikátor URI, pokud chcete, aby byl volán při aktivaci výstrahy.

9. Pokud používáte Azure Automation, můžete vybrat sadu Runbook, která se spustí, když se výstraha aktivuje.

10. Vyberte **OK** a vytvořte výstrahu.

Během několika minut je výstraha aktivní a triggery, jak je popsáno výše.

Po vytvoření výstrahy ji můžete vybrat a provést jednu z následujících úloh:

* Zobrazí graf, který zobrazuje prahovou hodnotu metriky a skutečné hodnoty z předchozího dne.
* Upravte nebo odstraňte.
* Tuto možnost **zakažte** nebo **Povolte** , pokud chcete dočasně zastavit nebo obnovit přijímání oznámení pro tuto výstrahu.

## <a name="with-azure-cli"></a>S využitím rozhraní příkazového řádku Azure

Předchozí části popisují, jak vytvářet, zobrazovat a spravovat pravidla upozornění metrik pomocí Azure Portal. V této části se dozvíte, jak to samé provést pomocí [Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)pro různé platformy. Nejrychlejší způsob, jak začít používat Azure CLI, je prostřednictvím [Azure Cloud Shell](../../cloud-shell/overview.md?view=azure-cli-latest).

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>Načíst všechna klasická pravidla upozornění na metriky ve skupině prostředků

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>Zobrazit podrobnosti konkrétního pravidla pro upozornění na klasické metriky

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>Vytvoření pravidla pro upozornění na klasické metriky

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>Odstraní klasické pravidlo pro upozornění na metriku.

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="with-powershell"></a>S využitím PowerShellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

V této části se dozvíte, jak pomocí příkazů PowerShellu vytvářet, zobrazovat a spravovat klasické výstrahy metriky. Příklady v článku ukazují, jak můžete použít rutiny Azure Monitor pro klasické výstrahy metrik.

1. Pokud jste to ještě neudělali, nastavte PowerShell tak, aby se spouštěl v počítači. Další informace najdete v tématu [instalace a konfigurace PowerShellu](/powershell/azure/). Můžete si také projít celý seznam rutin Azure Monitor PowerShellu na [Azure monitor (Insights) rutiny](/powershell/module/az.applicationinsights).

2. Nejdřív se přihlaste ke svému předplatnému Azure.

    ```powershell
    Connect-AzAccount
    ```

3. Zobrazí se přihlašovací obrazovka. Po přihlášení účtu se zobrazí TenantID a ID výchozího předplatného. Všechny rutiny Azure fungují v kontextu výchozího předplatného. Chcete-li zobrazit seznam předplatných, ke kterým máte přístup, použijte následující příkaz:

    ```powershell
    Get-AzSubscription
    ```

4. Chcete-li změnit svůj pracovní kontext na jiné předplatné, použijte následující příkaz:

    ```powershell
    Set-AzContext -SubscriptionId <subscriptionid>
    ```

5. Pro skupinu prostředků můžete načíst všechna pravidla metriky pro Classic:

    ```powershell
    Get-AzAlertRule -ResourceGroup montest
    ```

6. Můžete zobrazit podrobnosti pravidla upozornění na klasické metriky.

    ```powershell
    Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. Můžete načíst všechna pravidla upozornění nastavená pro cílový prostředek. Například všechna pravidla upozornění nastavená na virtuálním počítači.

    ```powershell
    Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. Klasická pravidla upozornění se už nedají vytvářet přes PowerShell. Chcete-li vytvořit pravidlo upozornění, musíte použít nový příkaz [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) .

## <a name="next-steps"></a>Další kroky

- [Vytvořte klasické upozornění na metriku pomocí šablony Správce prostředků](./alerts-enable-template.md).
- [Máte klasické upozornění na metriku, které není systémem Azure pomocí Webhooku](./alerts-webhooks.md).

