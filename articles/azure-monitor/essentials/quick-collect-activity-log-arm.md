---
title: Odeslání protokolu aktivit Azure do Log Analytics pracovního prostoru pomocí šablony Azure Resource Manager
description: Použijte šablony ARM k vytvoření pracovního prostoru Log Analytics a nastavení diagnostiky pro odeslání protokolu aktivit do protokolů Azure Monitor.
ms.subservice: logs
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 7465127ed9c52941d6c3ccfd40446546f0795455
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635468"
---
# <a name="quickstart-send-azure-activity-log-to-log-analytics-workspace-using-an-arm-template"></a>Rychlý Start: odeslání protokolu aktivit Azure do Log Analytics pracovního prostoru pomocí šablony ARM

Protokol aktivit je protokol platformy v Azure, který poskytuje přehled o událostech na úrovni předplatného. Obsahuje například informace o úpravách prostředků nebo spouštění virtuálních počítačů. Protokol aktivit můžete zobrazit v Azure Portal nebo načíst položky pomocí PowerShellu a rozhraní příkazového řádku. V tomto rychlém startu se dozvíte, jak pomocí šablon Azure Resource Manager (šablony ARM) vytvořit pracovní prostor Log Analytics a nastavení diagnostiky pro odeslání protokolu aktivit do Azure Monitor protokolů, kde je můžete analyzovat pomocí [dotazů protokolu](../log-query/log-query-overview.md) a povolit další funkce, jako jsou například výstrahy a [sešity](../visualize/workbooks-overview.md) [protokolu](../alerts/alerts-log-query.md) .

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
- Pokud chcete spustit příkazy z místního počítače, nainstalujte rozhraní příkazového řádku Azure CLI nebo moduly Azure PowerShell. Další informace najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli) a [instalace Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="create-a-log-analytics-workspace"></a>Vytvoření pracovního prostoru služby Log Analytics

### <a name="review-the-template"></a>Kontrola šablony

Následující šablona vytvoří prázdný pracovní prostor Log Analytics. Tuto šablonu uložte jako *CreateWorkspace.jsna*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the workspace."
      }
    },
    "sku": {
      "type": "string",
      "defaultValue": "pergb2018",
      "allowedValues": [
        "pergb2018",
        "Free",
        "Standalone",
        "PerNode",
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Pricing tier: PerGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "australiacentral",
        "australiaeast",
        "australiasoutheast",
        "brazilsouth",
        "canadacentral",
        "centralindia",
        "centralus",
        "eastasia",
        "eastus",
        "eastus2",
        "francecentral",
        "japaneast",
        "koreacentral",
        "northcentralus",
        "northeurope",
        "southafricanorth",
        "southcentralus",
        "southeastasia",
        "switzerlandnorth",
        "switzerlandwest",
        "uksouth",
        "ukwest",
        "westcentralus",
        "westeurope",
        "westus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for the workspace."
      }
    },
    "retentionInDays": {
      "type": "int",
      "defaultValue": 120,
      "metadata": {
        "description": "Number of days to retain data."
      }
    },
    "resourcePermissions": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "true to use resource or workspace permissions. false to require workspace permissions."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-08-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "properties": {
        "sku": {
          "name": "[parameters('sku')]"
        },
        "retentionInDays": "[parameters('retentionInDays')]",
        "features": {
          "searchVersion": 1,
          "legacy": 0,
          "enableLogAccessUsingOnlyResourcePermissions": "[parameters('resourcePermissions')]"
        }
      }
    }
  ]
}
```

Tato šablona definuje jeden prostředek:

- [Microsoft. OperationalInsights/pracovní prostory](/azure/templates/microsoft.operationalinsights/workspaces)

### <a name="deploy-the-template"></a>Nasazení šablony

Nasaďte šablonu pomocí libovolné standardní metody pro [nasazení šablony ARM](../../azure-resource-manager/templates/deploy-portal.md) , jako jsou následující příklady, pomocí rozhraní příkazového řádku a PowerShellu. Nahraďte ukázkové hodnoty pro **skupinu prostředků**, **pracovní prostor** a **umístění** odpovídajícími hodnotami pro vaše prostředí. Název pracovního prostoru musí být jedinečný mezi všemi předplatnými Azure.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli
az login
az deployment group create \
    --name CreateWorkspace \
    --resource-group my-resource-group \
    --template-file CreateWorkspace.json \
    --parameters workspaceName='my-workspace-01' location='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile CreateWorkspace.json -workspaceName my-workspace-01 -location eastus
```

---

### <a name="validate-the-deployment"></a>Ověření nasazení

Ověřte, zda byl pracovní prostor vytvořen pomocí jednoho z následujících příkazů. Nahraďte vzorové hodnoty pro **skupinu prostředků** a parametrem **pracovního prostoru** hodnotami, které jste použili výše.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli
az monitor log-analytics workspace show --resource-group my-workspace-01 --workspace-name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzOperationalInsightsWorkspace -Name my-workspace-01 -ResourceGroupName my-resource-group
```

---

## <a name="create-diagnostic-setting"></a>Vytvoření nastavení diagnostiky

### <a name="review-the-template"></a>Kontrola šablony

Následující šablona vytvoří nastavení diagnostiky, které odesílá protokol aktivit do Log Analytics pracovního prostoru. Tuto šablonu uložte jako *CreateDiagnosticSetting.jsna*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "settingName": {
        "type": "String"
    },
    "workspaceId": {
        "type": "String"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/diagnosticSettings",
      "apiVersion": "2017-05-01-preview",
      "name": "[parameters('settingName')]",
      "dependsOn": [],
      "properties": {
        "workspaceId": "[parameters('workspaceId')]",
        "logs": [
          {
          "category": "Administrative",
          "enabled": true
          },
          {
          "category": "Alert",
          "enabled": true
          },
          {
          "category": "Autoscale",
          "enabled": true
          },
          {
          "category": "Policy",
          "enabled": true
          },
          {
          "category": "Recommendation",
          "enabled": true
          },
          {
          "category": "ResourceHealth",
          "enabled": true
          },
          {
          "category": "Security",
          "enabled": true
          },
          {
          "category": "ServiceHealth",
          "enabled": true
          }
        ]
      }
    }
  ]
}
```

Tato šablona definuje jeden prostředek:

- [Microsoft. Insights/diagnosticSettings](/azure/templates/microsoft.insights/diagnosticsettings)

### <a name="deploy-the-template"></a>Nasazení šablony

Nasaďte šablonu pomocí libovolné standardní metody pro [nasazení šablony ARM](../../azure-resource-manager/templates/deploy-portal.md) , jako jsou následující příklady, pomocí rozhraní příkazového řádku a PowerShellu. Nahraďte ukázkové hodnoty pro **skupinu prostředků**, **pracovní prostor** a **umístění** odpovídajícími hodnotami pro vaše prostředí. Název pracovního prostoru musí být jedinečný mezi všemi předplatnými Azure.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli
az deployment sub create --name CreateDiagnosticSetting --location eastus --template-file CreateDiagnosticSetting.json --parameters settingName='Send Activity log to workspace' workspaceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace-01'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzSubscriptionDeployment -Name CreateDiagnosticSetting -location eastus -TemplateFile CreateDiagnosticSetting.json -settingName "Send Activity log to workspace" -workspaceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace-01"
```
---

### <a name="validate-the-deployment"></a>Ověření nasazení

Ověřte, zda bylo nastavení diagnostiky vytvořeno pomocí jednoho z následujících příkazů. Nahraďte vzorové hodnoty pro předplatné a název nastavení hodnotami, které jste použili výše.

> [!NOTE]
> V současné době nejde v prostředí PowerShell načíst nastavení diagnostiky na úrovni předplatného.

```azurecli
az monitor diagnostic-settings show --resource '/subscriptions/00000000-0000-0000-0000-000000000000' --name 'Send Activity log to workspace'
```

## <a name="generate-log-data"></a>Generovat data protokolu

Do pracovního prostoru Log Analytics se odešlou jenom nové položky protokolu aktivit, takže se v předplatném provede několik akcí, které se budou protokolovat jako spuštění nebo zastavení virtuálního počítače nebo vytvoření nebo úprava jiného prostředku. Možná budete muset několik minut počkat, než se nastavení diagnostiky vytvoří a data, která se mají zpočátku zapsat do pracovního prostoru. Po této prodlevě se všechny události zapsané do protokolu aktivit odešlou do pracovního prostoru během několika sekund.

## <a name="retrieve-data-with-a-log-query"></a>Načtení dat pomocí dotazu protokolu

Pomocí Azure Portal můžete pomocí Log Analytics načíst data z pracovního prostoru. V Azure Portal vyhledejte a pak vyberte **monitor**.

![portál Azure](media/quick-collect-activity-log/azure-portal-monitor.png)

V nabídce **Azure monitor** vyberte **protokoly** . Zavřete stránku **příklady dotazů** . Pokud obor není nastavený na pracovní prostor, který jste vytvořili, klikněte na **Vybrat obor** a vyhledejte ho.

![Rozsah Log Analytics](media/quick-collect-activity-log/log-analytics-scope.png)

V okně dotazu zadejte `AzureActivity` a klikněte na **Spustit**. Jedná se o jednoduchý dotaz, který vrátí všechny záznamy v tabulce *AzureActivity* , která obsahuje všechny záznamy odeslané z protokolu aktivit.

![Jednoduchý dotaz](media/quick-collect-activity-log/query-01.png)

Rozbalte jeden ze záznamů, abyste zobrazili jeho podrobné vlastnosti.

![Rozbalit vlastnosti](media/quick-collect-activity-log/expand-properties.png)

Vyzkoušejte složitější dotaz, například to, `AzureActivity | summarize count() by CategoryValue` který poskytuje počet událostí shrnutých podle kategorie.

![Složitý dotaz](media/quick-collect-activity-log/query-02.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky ponechat na místě. Pokud už je nepotřebujete, odstraňte skupinu prostředků, která odstraní pravidlo upozornění a související prostředky. Odstranění skupiny prostředků pomocí rozhraní příkazového řádku Azure nebo Azure PowerShell

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli
az group delete --name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Remove-AzResourceGroup -Name my-resource-group
```

---

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nakonfigurovali protokol aktivit, který se má odeslat do Log Analytics pracovního prostoru. Teď můžete nakonfigurovat další data, která se mají shromáždit, do pracovního prostoru, kde je můžete analyzovat pomocí [dotazů protokolu](../log-query/log-query-overview.md) v Azure monitor a využívat funkce, jako jsou výstrahy a [sešity](../visualize/workbooks-overview.md) [protokolu](../alerts/alerts-log-query.md) . Měli byste další shromáždit [protokoly prostředků](../essentials/resource-logs.md) z vašich prostředků Azure, které vám poskytnou přehled o operacích, které byly provedeny v rámci jednotlivých prostředků v protokolu aktivit.

> [!div class="nextstepaction"]
> [Shromažďování a analýza protokolů prostředků pomocí Azure Monitor](../essentials/tutorial-resource-logs.md)
