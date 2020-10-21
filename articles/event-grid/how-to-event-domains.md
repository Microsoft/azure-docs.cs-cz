---
title: Publikování událostí s doménami událostí pomocí Azure Event Grid
description: Ukazuje, jak spravovat velké sady témat v Azure Event Grid a publikovat do nich události pomocí domén událostí.
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 277db97211b196c9853470c2d12cc2246a4005b2
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92330073"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Správa témat a publikování událostí pomocí domén událostí

V tomto článku se dozvíte, jak:

* Vytvoření domény Event Grid
* Přihlášení k odběru témat z Event gridu
* Zobrazit seznam klíčů
* Publikování událostí v doméně

Další informace o doménách událostí najdete v tématu [Principy domén událostí při správě Event Grid témata](event-domains.md).

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Nainstalovat funkci Preview

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Vytvoření domény události

Pokud chcete spravovat velké sady témat, vytvořte doménu události.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```
---

Úspěšné vytvoření vrátí následující hodnoty:

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

Všimněte si, že `endpoint` a jsou `id` nutné ke správě událostí domény a publikování.

## <a name="manage-access-to-topics"></a>Správa přístupu k tématům

Správa přístupu k tématům se provádí prostřednictvím [přiřazení role](../role-based-access-control/role-assignments-cli.md). Přiřazení role používá řízení přístupu na základě role v Azure k omezení operací s prostředky Azure u autorizovaných uživatelů v určitém oboru.

Event Grid má dvě předdefinované role, které můžete použít k přiřazení přístupu konkrétním uživatelům k různým tématům v rámci domény. Tyto role jsou `EventGrid EventSubscription Contributor (Preview)` , což umožňuje vytvořit a odstranit odběry a `EventGrid EventSubscription Reader (Preview)` , které umožňují pouze výpis odběrů událostí.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)
Následující příkaz Azure CLI omezuje `alice@contoso.com` vytváření a odstraňování odběrů událostí jenom v tématu `demotopic1` :

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
Následující příkaz prostředí PowerShell omezuje `alice@contoso.com` vytváření a odstraňování odběrů událostí pouze v tématu `demotopic1` :

```azurepowershell-interactive
New-AzureRmRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```
---

Další informace o správě přístupu pro operace Event Grid najdete v tématu [Event Grid zabezpečení a ověřování](./security-authentication.md).

## <a name="create-topics-and-subscriptions"></a>Vytvoření témat a odběrů

Služba Event Grid automaticky vytvoří a spravuje příslušné téma v doméně na základě volání pro vytvoření odběru událostí pro doménu. Neexistuje žádný samostatný krok k vytvoření tématu v doméně. Podobně platí, že když se odstraní poslední odběr události pro téma, odstraní se i téma.

Přihlášení k odběru tématu v doméně se shoduje s přihlášením k jakémukoli jinému prostředku Azure. V poli ID zdrojového prostředku zadejte ID domény události, které se vrátilo při dřívějším vytvoření domény. Chcete-li určit téma, ke kterému se chcete přihlásit, přidejte `/topics/<my-topic>` na konec ID zdrojového prostředku. Pokud chcete vytvořit odběr událostí oboru domény, který přijme všechny události v doméně, zadejte ID domény události bez zadání jakýchkoli témat.

Uživatel, kterému jste udělili přístup v předchozí části, obvykle vytvoří odběr. Pro zjednodušení tohoto článku vytvoříte předplatné. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

---

Pokud potřebujete testovat koncový bod pro přihlášení k odběru událostí, můžete vždy nasadit [předem vytvořenou webovou aplikaci](https://github.com/Azure-Samples/azure-event-grid-viewer) , která zobrazí příchozí události. Své události můžete odeslat na zkušební web na adrese `https://<your-site-name>.azurewebsites.net/api/updates` .

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"  alt="Button to Deploy to Aquent." /></a>

Oprávnění nastavená pro téma jsou uložena v Azure Active Directory a je nutné je odstranit explicitně. Odstraněním odběru události nedojde k odvolání přístupu uživatelů k vytváření odběrů událostí, pokud mají v tématu přístup pro zápis.


## <a name="publish-events-to-an-event-grid-domain"></a>Publikování událostí do domény Event Grid

Publikování událostí do domény je stejné jako [publikování do vlastního tématu](./post-to-custom-topic.md). Místo publikování do vlastního tématu ale publikujete všechny události do koncového bodu domény. V datech události JSON zadáte téma, na které chcete události přejít. Následující pole událostí by vedlo k události s `"id": "1111"` tématem v `demotopic1` době, kdy se událost s `"id": "2222"` pošle do tématu `demotopic2` :

```json
[{
  "topic": "demotopic1",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "demotopic2",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)
Pokud chcete získat koncový bod domény pomocí Azure CLI, použijte

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

Pro získání klíčů pro doménu použijte:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
K získání koncového bodu domény pomocí prostředí PowerShell použijte

```azurepowershell-interactive
Get-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Pro získání klíčů pro doménu použijte:

```azurepowershell-interactive
Get-AzureRmEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```
---

A pak použijte svou oblíbenou metodu, která odešle příspěvek HTTP k publikování událostí do domény Event Grid.

## <a name="next-steps"></a>Další kroky

* Další informace o konceptech vysoké úrovně v doménách událostí a o tom, proč jsou užitečné, najdete v tématu [koncepční přehled domén událostí](event-domains.md).
