---
title: Publikování událostí s doménami událostí pomocí Azure Event Grid
description: Ukazuje, jak spravovat velké sady témat v Azure Event Grid a publikovat události na ně pomocí domén událostí.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 1d07227249806b7d54523af66817a170c19354ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72786554"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Správa témat a publikování událostí pomocí domén událostí

Tento článek ukazuje, jak:

* Vytvoření domény mřížky událostí
* Přihlásit se k odběru témat v mřížce událostí
* Klíče seznamu
* Publikování událostí do domény

Informace o doménách událostí najdete [v tématu Principy domén událostí pro správu témat Služby Event Grid](event-domains.md).

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instalace funkce náhledu

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Vytvoření domény událostí

Chcete-li spravovat velké sady témat, vytvořte doménu událostí.

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

Všimněte `endpoint` `id` si a jak jsou požadovány ke správě domény a publikování událostí.

## <a name="manage-access-to-topics"></a>Správa přístupu k tématům

Správa přístupu k tématům se provádí prostřednictvím [přiřazení role](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli). Přiřazení rolí používá řízení přístupu na základě rolí k omezení operací s prostředky Azure oprávněným uživatelům v určitém oboru.

Event Grid má dvě předdefinované role, které můžete použít k přiřazení přístupu konkrétních uživatelů k různým tématům v rámci domény. Tyto role `EventGrid EventSubscription Contributor (Preview)`jsou , který umožňuje vytváření a `EventGrid EventSubscription Reader (Preview)`odstraňování odběrů a , který umožňuje pouze pro výpis odběry událostí.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)
Následující příkaz azure cli omezení `alice@contoso.com` pro vytváření a odstranění `demotopic1`odběry událostí jenom na téma :

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
Následující příkaz prostředí `alice@contoso.com` PowerShell omezuje vytváření a mazání odběrů událostí jenom v tématu `demotopic1`:

```azurepowershell-interactive
New-AzureRmRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```
---

Další informace o správě přístupu k operacím event gridu naleznete v [tématu Zabezpečení a ověřování mřížky událostí](./security-authentication.md).

## <a name="create-topics-and-subscriptions"></a>Vytvoření témat a odběrů

Služba Event Grid automaticky vytvoří a spravuje odpovídající téma v doméně na základě volání k vytvoření odběru událostí pro téma domény. Neexistuje žádný samostatný krok k vytvoření tématu v doméně. Podobně při odstranění poslední ho odběr událostí pro téma, téma je odstraněn také.

Přihlášení k odběru tématu v doméně je stejné jako přihlášení k odběru jakéhokoli jiného prostředku Azure. Pro ID zdrojového prostředku zadejte ID domény události vrácené při dřívějším vytváření domény. Chcete-li určit téma, k `/topics/<my-topic>` jehož odběru se chcete přihlásit, přidejte na konec ID zdrojového prostředku. Chcete-li vytvořit odběr událostí oboru domény, který obdrží všechny události v doméně, zadejte ID domény události bez zadání témat.

Obvykle uživatel, kterému jste udělili přístup v předchozí části by vytvořit odběr. Chcete-li zjednodušit tento článek, vytvořte odběr. 

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

Pokud potřebujete testovací koncový bod k odběru událostí, můžete vždy nasadit [předem vytvořenou webovou aplikaci,](https://github.com/Azure-Samples/azure-event-grid-viewer) která zobrazuje příchozí události. Události můžete odeslat na testovací `https://<your-site-name>.azurewebsites.net/api/updates`web na adrese .

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Oprávnění nastavená pro téma jsou uložena ve službě Azure Active Directory a musí být explicitně odstraněna. Odstraněním odběru událostí neodvoláte přístup uživatelů k vytvoření odběrů událostí, pokud mají přístup pro zápis k tématu.


## <a name="publish-events-to-an-event-grid-domain"></a>Publikování událostí do domény mřížky událostí

Publikování událostí do domény je stejné jako [publikování na vlastní téma](./post-to-custom-topic.md). Místo publikování na vlastní téma však publikujete všechny události do koncového bodu domény. V datech události JSON zadáte téma, na které chcete přejít události. Následující pole událostí by mělo `"id": "1111"` za `demotopic1` následek událost `"id": "2222"` s tématem, zatímco událost s by být odeslány na téma `demotopic2`:

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
Chcete-li získat koncový bod domény pomocí azure cli, použijte

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

Chcete-li získat klíče pro doménu, použijte:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
Chcete-li získat koncový bod domény pomocí PowerShellu, použijte

```azurepowershell-interactive
Get-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Chcete-li získat klíče pro doménu, použijte:

```azurepowershell-interactive
Get-AzureRmEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```
---

A pak použijte svůj oblíbený způsob vytváření HTTP POST publikovat události do domény Event Grid.

## <a name="next-steps"></a>Další kroky

* Další informace o konceptech na vysoké úrovni v doménách událostí a o tom, proč jsou užitečné, najdete v [koncepčním přehledu domén událostí](event-domains.md).
