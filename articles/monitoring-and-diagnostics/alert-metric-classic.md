---
title: Vytvořit, zobrazit a spravovat klasické metriky upozornění pomocí Azure monitoru
description: Zjistěte, jak pomocí webu Azure portal, Powershellu nebo rozhraní příkazového řádku k vytvoření, zobrazení a Správa pravidel klasické metriky upozornění.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: e18b670b94962c0e7aa469402228fd4ed95d846b
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51287247"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>Vytvořit, zobrazit a spravovat klasické metriky upozornění pomocí Azure monitoru

Klasického upozornění metrik ve službě Azure Monitor poskytují způsob, jak chcete dostávat oznámení, když jeden metriky překročí prahovou hodnotu. Klasické metriky upozornění je starší funkce, které umožňuje upozorňování pouze na jiné jednodimenzionální metriky. Není existující novější funkce volána upozornění na metriku má vylepšené funkce prostřednictvím klasického upozornění metrik. Další informace o nových funkcích upozornění metrik v [přehled výstrah metrika](alert-metric-overview.md). V tomto článku jsme popisuje, jak vytvořit, zobrazit a spravovat klasické pravidla upozornění na metriky prostřednictvím webu Azure portal, Powershell a rozhraní příkazového řádku Azure.

## <a name="with-azure-portal"></a>Pomocí webu Azure portal

1. V [portál](https://portal.azure.com/), najděte prostředek, který chcete monitorovat a vyberte ji.

2. V **monitorování** vyberte **upozornění (klasická)**. Text a ikona se mohou mírně lišit pro různé prostředky. Pokud nenajdete **upozornění (klasická)** tady, je možné v **výstrahy** nebo **pravidla upozornění**.

    ![Sledování](media/alert-metric-classic/AlertRulesButton.png)

3. Vyberte **přidat upozornění metriky (klasické)** příkaz a potom přejít k vyplnění polí.

    ![Přidat výstrahu](media/alert-metric-classic/AddAlertOnlyParamsPage.png)

4. **Název** pravidlo výstrahy. Klikněte na tlačítko **popis**, která také se zobrazí v oznamovací e-maily.

5. Vyberte **metrika** , kterou chcete monitorovat. Klikněte na tlačítko **podmínku** a **prahová hodnota** hodnoty pro metriku. Se také rozhodnout **období** , kterou pravidlo metriky musí být splněny před výstrah aktivační události. Například pokud používáte období "za posledních 5 minut" a upozornění hledá CPU vyšší než 80 %, aktivuje výstrahu, když procesor byl trvale výše 80 % po dobu 5 minut. Po prvním nastane aktivační událost aktivuje akci, když procesor zůstává nižší než 80 % po dobu 5 minut. Metriky měření využití procesoru dochází každou minutu.

6. Vyberte **e-mailu vlastníky...**  Pokud chcete, aby správci a spolusprávci e-mailová oznámení dostávat, když se aktivuje upozornění.

7. Pokud chcete odeslat oznámení na další e-mailové adresy, když se aktivuje upozornění, přidejte je **další email(s) správce** pole. Více e-mailů oddělte středníky v následujícím formátu:  *email@contoso.com;email2@contoso.com*

8. Vložit platný identifikátor URI, **Webhooku** pole, pokud má být volána, když se aktivuje upozornění.

9. Pokud používáte Azure Automation, můžete vybrat sadu runbook spustit, když se aktivuje upozornění.

10. Vyberte **OK** k vytvoření upozornění.

Během několika minut upozornění je aktivní a aktivuje jak bylo popsáno dříve.

Po vytvoření výstrahy, můžete ho vyberte a proveďte jednu z následujících úloh:

* Zobrazení grafu, který zobrazuje mezní hodnota metriky a skutečnými hodnotami z předchozího dne.
* Upravit nebo odstranit.
* **Zakázat** nebo **povolit** ji, pokud chcete dočasně zastavit nebo obnovit příjem oznámení pro toto upozornění.

## <a name="with-azure-cli"></a>S využitím rozhraní příkazového řádku Azure

V předchozích částech popsané, jak vytvořit, zobrazit a spravovat pravidla upozornění na metriky pomocí webu Azure portal. Tato část popisuje, jak provádět to stejné napříč platformami pomocí [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest). Nejrychlejší způsob, abyste mohli začít používat Azure CLI je prostřednictvím [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest).

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>Získat všechny klasické metriky upozornění pravidel ve skupině prostředků

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>Viz podrobnosti o konkrétní klasické metriky upozornění pravidla

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>Vytvořit pravidlo klasické metriky upozornění

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>Odstranit pravidlo klasické metriky upozornění

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="with-powershell"></a>S využitím PowerShellu

Tato část ukazuje, jak pomocí prostředí PowerShell příkazů vytvořit, zobrazit a spravovat klasické metriky upozornění. V příkladech v tomto článku ukazují, jak můžete použít rutiny Azure Monitor pro klasické metriky upozornění.

1. Pokud jste tak dosud neučinili, nastavení prostředí PowerShell pro spuštění ve vašem počítači. Další informace najdete v tématu [instalace a konfigurace Powershellu](/powershell/azure/overview). Můžete také zkontrolovat celý seznam rutin Powershellu pro monitorování Azure v [rutiny Azure Monitor (přehled)](https://docs.microsoft.com/powershell/module/azurerm.insights).

2. Nejdřív přihlaste ke svému předplatnému Azure.

    ```PowerShell
    Connect-AzureRmAccount
    ```

3. Zobrazí přihlašovací obrazovka. Jednou přihlásíte ve vašem účtu, TenantID, a jsou zobrazeny výchozí ID předplatného. Všechny rutiny služby Azure fungují v rámci výchozího předplatného. Chcete-li zobrazit seznam předplatných, ke kterým máte přístup, použijte následující příkaz:

    ```PowerShell
    Get-AzureRmSubscription
    ```

4. Chcete-li změnit pracovního kontextu do jiného předplatného, použijte následující příkaz:

    ```PowerShell
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

5. Můžete načíst všechny klasické pravidla upozornění metrik pro skupinu prostředků:

    ```PowerShell
    Get-AzureRmAlertRule -ResourceGroup montest
    ```

6. Můžete zobrazit podrobnosti o klasické metriky upozornění pravidla

    ```PowerShell
    Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. Můžete načíst všechna pravidla výstrah pro cílový prostředek. Všechna pravidla výstrah je třeba nastavit na virtuálním počítači.

    ```PowerShell
    Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. Můžete použít `Add-AlertRule` rutina pro vytvoření, aktualizace nebo zakázání pravidla upozornění. Můžete vytvořit e-mailu a webhook vlastností pomocí `New-AzureRmAlertRuleEmail` a `New-AzureRmAlertRuleWebhook`v uvedeném pořadí. V rutině pravidlo upozornění přiřadit jako akce, které tyto vlastnosti **akce** vlastnost pravidla výstrahy. Následující tabulka popisuje parametry a hodnoty použité k vytvoření upozornění použitím metrik.

    | parametr | hodnota |
    | --- | --- |
    | Název |simpletestdiskwrite |
    | Umístění tohoto upozornění pravidla |Východ USA |
    | Skupina prostředků |montest |
    | TargetResourceId |/subscriptions/S1/resourceGroups/montest/providers/Microsoft.COMPUTE/virtualMachines/testconfig |
    | MetricName výstrahy, která je vytvořena |\PhysicalDisk (využití _celkem) \Disk zápisů za sekundu. Zobrazit `Get-MetricDefinitions` rutiny o tom, jak získat přesné názvy metrik |
    | – Operátor |GreaterThan |
    | Prahová hodnota (počet za sekundu za pro tuto metriku) |1 |
    | Velikost_okna (ve formátu hh: mm:) |00:05:00 |
    | agregátoru (Statistika metriky, které v tomto případě používá průměrný počet) |Průměr |
    | vlastní e-mailů (pole řetězců) |'foo@example.com','bar@example.com' |
    | poslat vlastníci, přispěvatelé a čtenáři e-mailu |-SendToServiceOwners |

9. Vytvořit e-mailové akce

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    ```

10. Vytvoření akce Webhooku

    ```PowerShell
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
    ```

## <a name="next-steps"></a>Další postup

- [Vytvoření klasických upozornění na metriku pomocí šablony Resource Manageru](monitoring-enable-alerts-using-template.md).
- [Mít klasických upozornění na metriku oznámení do systému mimo Azure, pomocí webhooku](insights-webhooks-alerts.md).

