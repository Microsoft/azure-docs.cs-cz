---
title: Vytváření, zobrazení a správa klasických upozornění na metriky pomocí Azure Monitoru
description: Přečtěte si, jak pomocí portálu Azure, CLI nebo Powershellu vytvářet, zobrazovat a spravovat klasická pravidla upozornění na metriky.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/18/2018
ms.openlocfilehash: b770b9bd34c8267889db536ec81332de32cb8776
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668311"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>Vytváření, zobrazení a správa klasických upozornění na metriky pomocí Azure Monitoru

Klasické metriky výstrahy ve službě Azure Monitor poskytují způsob, jak získat upozornění, když jeden z vašich metrik přes prahovou hodnotu. Klasické metriky výstrahy je starší funkce, která umožňuje upozorňovat pouze na nedimenzionální metriky. Existuje novější funkce s názvem Metrika výstrahy, která má vylepšené funkce přes klasické metriky výstrahy. Další informace o nové funkci upozornění na metriky najdete v [přehledu upozornění na metriky](../../azure-monitor/platform/alerts-metric-overview.md). V tomto článku popíšeme, jak vytvořit, zobrazit a spravovat klasická pravidla upozornění metriky prostřednictvím portálu Azure, Azure CLI a Powershellu.

## <a name="with-azure-portal"></a>S portálem Azure

1. Na [portálu](https://portal.azure.com/)vyhledejte prostředek, který chcete sledovat, a vyberte jej.

2. V části **MONITOROVÁNÍ** vyberte **výstrahy (klasické).** Text a ikona se mohou u různých zdrojů mírně lišit. Pokud zde **výstrahy (klasické)** nenajdete, můžete je najít v **pravidlech upozornění** nebo **výstrah**.

    ![Monitorování](media/alerts-classic-portal/AlertRulesButton.png)

3. Vyberte příkaz **Přidat metriku výstrahy (klasické)** a vyplňte pole.

    ![Přidat výstrahu](media/alerts-classic-portal/AddAlertOnlyParamsPage.png)

4. **Pojmenujte** pravidlo upozornění. Pak zvolte **popis**, který se také zobrazí v e-mailech s oznámením.

5. Vyberte **metriku,** kterou chcete sledovat. Pak zvolte hodnotu **Podmínka** a **Prahová hodnota** pro metriku. Zvolte také **časové období,** které musí být pravidlo metriky splněno, než se aktivuje výstraha. Pokud například použijete tečku "Za posledních 5 minut" a výstraha vyhledá procesor nad 80 %, výstraha se aktivuje, když procesor byl po dobu 5 minut konzistentně vyšší než 80 %. Po první aktivační události se aktivuje znovu, když procesor zůstane pod 80 % po dobu 5 minut. Měření metriky procesoru se děje každou minutu.

6. Vyberte **vlastníci e-mailu...** pokud chcete, aby správci a spolusprávci přijímali e-mailová oznámení při požáru výstrahy.

7. Pokud chcete při vyvolání výstrahy odesílat oznámení na další e-mailové adresy, přidejte je do pole **Další e-maily správce.** Oddělte více e-mailů středníky v následujícím formátu: *e-mail\@contoso.com;email2\@contoso.com*

8. Pokud chcete, aby byl volán při vyvolání výstrahy, vložte do pole **Webhook** platný identifikátor URI.

9. Pokud používáte Azure Automation, můžete vybrat runbook, který se má spustit při spuštění výstrahy.

10. Chcete-li výstrahu vytvořit, vyberte **ok.**

Během několika minut je výstraha aktivní a aktivuje se, jak bylo popsáno dříve.

Po vytvoření výstrahy ji můžete vybrat a provést jeden z následujících úkolů:

* Zobrazení grafu, který zobrazuje prahovou hodnotu metriky a skutečné hodnoty z předchozího dne.
* Upravte nebo odstraňte.
* **Zakázat** nebo **povolit,** pokud chcete dočasně zastavit nebo obnovit příjem oznámení pro tuto výstrahu.

## <a name="with-azure-cli"></a>S využitím rozhraní příkazového řádku Azure

V předchozích částech je popsáno, jak vytvořit, zobrazit a spravovat pravidla upozornění metriky pomocí portálu Azure Portal. Tato část popisuje, jak provést totéž pomocí [příkazového příkazu Azure pro](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)více platforem . Nejrychlejší způsob, jak začít používat Azure CLI je přes [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest).

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>Získání všech klasických pravidel upozornění metriky ve skupině prostředků

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>Zobrazit podrobnosti o konkrétním pravidle klasické metriky

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>Vytvoření pravidla upozornění na klasickou metriku

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>Odstranění pravidla upozornění klasické metriky

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="with-powershell"></a>S využitím PowerShellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tato část ukazuje, jak používat příkazy Prostředí PowerShell vytvářet, zobrazovat a spravovat klasické metriky výstrahy. Příklady v článku ilustrují, jak můžete použít rutiny Azure Monitor pro klasické metriky výstrahy.

1. Pokud jste tak ještě neučinili, nastavte PowerShell pro spuštění v počítači. Další informace naleznete v tématu [Jak nainstalovat a nakonfigurovat prostředí PowerShell](/powershell/azure/overview). Můžete také zkontrolovat celý seznam rutin Azure Monitor PowerShell na [Rutiny Azure Monitor (Insights).](https://docs.microsoft.com/powershell/module/az.applicationinsights)

2. Nejprve se přihlaste k předplatnému Azure.

    ```powershell
    Connect-AzAccount
    ```

3. Zobrazí se přihlašovací obrazovka. Po přihlášení se zobrazí Id tenanta a výchozí ID předplatného. Všechny rutiny Azure fungují v kontextu výchozího předplatného. Chcete-li zobrazit seznam předplatných, ke které máte přístup, použijte následující příkaz:

    ```powershell
    Get-AzSubscription
    ```

4. Chcete-li změnit pracovní kontext na jiné předplatné, použijte následující příkaz:

    ```powershell
    Set-AzContext -SubscriptionId <subscriptionid>
    ```

5. Můžete načíst všechna klasická pravidla upozornění metriky pro skupinu prostředků:

    ```powershell
    Get-AzAlertRule -ResourceGroup montest
    ```

6. Můžete zobrazit podrobnosti o klasickém pravidle upozornění metriky.

    ```powershell
    Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. Můžete načíst všechna pravidla výstrah nastavená pro cílový prostředek. Například všechna pravidla výstrah nastavená na virtuálním počítači.

    ```powershell
    Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. Klasická pravidla výstrah již nelze vytvořit prostřednictvím prostředí PowerShell. Chcete-li vytvořit pravidlo výstrahy, musíte použít nový příkaz [Add-AzMetricAlertRule.](/powershell/module/az.monitor/add-azmetricalertrule)

## <a name="next-steps"></a>Další kroky

- [Vytvořte klasickou metriku s šablonou Správce prostředků](../../azure-monitor/platform/alerts-enable-template.md).
- [Mít klasické metriky upozornění systému, který není Azure pomocí webhooku](../../azure-monitor/platform/alerts-webhooks.md).
