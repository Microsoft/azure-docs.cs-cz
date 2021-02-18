---
title: Získání výstrah protokolu aktivit v oznámeních služby Azure pomocí šablony Správce prostředků
description: Když dojde ke službě Azure, dostanete oznámení prostřednictvím SMS, e-mailu nebo Webhooku.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: 532fbae505e0bcaa6ab31a2e935362114537d134
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594955"
---
# <a name="quickstart-create-activity-log-alerts-on-service-notifications-using-an-arm-template"></a>Rychlý Start: vytvoření výstrah protokolu aktivit u oznámení služby pomocí šablony ARM

V tomto článku se dozvíte, jak nastavit výstrahy protokolu aktivit pro oznámení o stavu služby pomocí šablony Azure Resource Manager (šablona ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Oznámení o stavu služby se ukládají do [protokolu aktivit Azure](../azure-monitor/essentials/platform-logs-overview.md). Vzhledem k tomu, že je možné velké množství informací uložených v protokolu aktivit, je k dispozici samostatné uživatelské rozhraní, které usnadňuje zobrazení a nastavení výstrah pro oznámení o stavu služby.

Když Azure pošle oznámení o stavu služby do vašeho předplatného Azure, můžete obdržet upozornění. Můžete nakonfigurovat výstrahu na základě:

- Třída oznámení o stavu služby (problémy se službami, plánovaná údržba, Poradce pro stav).
- Ovlivněné předplatné.
- Služba (y) ovlivnila.
- Ovlivněné oblasti.

> [!NOTE]
> Oznámení o stavu služby neodesílají výstrahy týkající se událostí stavu prostředku.

Můžete také nakonfigurovat, komu má být upozornění odesláno:

- Vyberte existující skupinu akcí.
- Vytvořte novou skupinu akcí (kterou lze použít k budoucím výstrahám).

Další informace o skupinách akcí naleznete v tématu [Create and Manage Action Groups](../azure-monitor/alerts/action-groups.md).

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
- Pokud chcete spustit příkazy z místního počítače, nainstalujte rozhraní příkazového řádku Azure CLI nebo moduly Azure PowerShell. Další informace najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli) a [instalace Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Kontrola šablony

Následující šablona vytvoří skupinu akcí s cílem e-mailu a povolí všechna oznámení o stavu služby pro cílové předplatné. Tuto šablonu uložte jako *CreateServiceHealthAlert.jsna*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "type": "String",
      "defaultValue": "SubHealth"
    },
    "activityLogAlerts_name": {
      "type": "String",
      "defaultValue": "ServiceHealthActivityLogAlert"
    },
    "emailAddress": {
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "apiVersion": "2019-06-01",
      "name": "[parameters('actionGroups_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [],
      "tags": {},
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      }
    },
    {
      "comments": "Service Health Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ],
      "tags": {},
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ServiceHealth"
            },
            {
              "field": "properties.incidentType",
              "equals": "Incident"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      }
    }
  ]
}
```

Šablona definuje dva prostředky:

- [Microsoft. Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft. Insights/Upozorněníprotokoluaktivit](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>Nasazení šablony

Nasaďte šablonu pomocí libovolné standardní metody pro [nasazení šablony ARM](../azure-resource-manager/templates/deploy-portal.md) , jako jsou následující příklady, pomocí rozhraní příkazového řádku a PowerShellu. Nahraďte ukázkové hodnoty pro **skupinu prostředků** a **EmailAddress** příslušnými hodnotami pro vaše prostředí.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateServiceHealthAlert --resource-group my-resource-group --template-file CreateServiceHealthAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateServiceHealthAlert -ResourceGroupName my-resource-group -TemplateFile CreateServiceHealthAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>Ověření nasazení

Ověřte, zda byl pracovní prostor vytvořen pomocí jednoho z následujících příkazů. Nahraďte ukázkové hodnoty pro **skupinu prostředků** hodnotou, kterou jste použili výše.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name ServiceHealthActivityLogAlert
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name ServiceHealthActivityLogAlert
```

---

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

- Přečtěte si o [osvědčených postupech pro nastavení výstrah Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Přečtěte si, jak [nastavit mobilní nabízená oznámení pro Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Naučte se [Konfigurovat oznámení Webhooku pro stávající systémy správy problémů](service-health-alert-webhook-guide.md).
- Přečtěte si o [oznámeních o stavu služby](service-notifications.md).
- Seznamte se s [omezením rychlosti oznámení](../azure-monitor/alerts/alerts-rate-limiting.md).
- Zkontrolujte [schéma Webhooku upozornění protokolu aktivit](../azure-monitor/alerts/activity-log-alerts-webhook.md).
- Získejte [Přehled výstrah protokolu aktivit](../azure-monitor/alerts/alerts-overview.md)a Naučte se přijímat výstrahy.
- Přečtěte si další informace o [skupinách akcí](../azure-monitor/alerts/action-groups.md).
