---
title: Správa velkých sad témat ve službě Azure Event Grid s doménami události
description: Ukazuje, jak spravovat velké sady témat ve službě Azure Event Grid a publikování událostí je používání událostí domén.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 11/08/2018
ms.openlocfilehash: ad23599d1df5d07e912f634435f8b44b441d87e6
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51298523"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Správa témat a publikovat události pomocí událostí domény

Tento článek popisuje, jak:

* Vytvoření domény služby Event Grid
* Předplatit témata event gridu
* Vypsat klíče
* Publikování událostí do domény

Další informace o události domén najdete v tématu [pochopit události domén pro Event Grid témata týkající se správy](event-domains.md).

## <a name="install-preview-feature"></a>Nainstalujte funkci ve verzi preview

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Vytvoření domény události

Ke správě velkých sad témat, vytvoření domény události.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```

Po úspěšném vytvoření vrátí následující hodnoty:

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

Poznámka: `endpoint` a `id` jako vyžadovány pro správu domény a publikovat události.

## <a name="manage-access-to-topics"></a>Správa přístupu na témata

Správa přístupu k tématům se provádí prostřednictvím [přiřazení role](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli). Přiřazení role řízení přístupu na základě rolí používá k omezení operací s prostředky Azure na oprávněné uživatele v určitém rozsahu.

Event Grid má dvě předdefinované role, které vám umožní přidělit konkrétním uživatelům přístup na různá témata v rámci domény. Tyto role jsou `EventGrid EventSubscription Contributor (Preview)`, což umožňuje vytváření a odstraňování předplatných, a `EventGrid EventSubscription Reader (Preview)`, která povoluje jenom pro seznam odběrů událostí.

Následující omezení příkaz rozhraní příkazového řádku Azure `alice@contoso.com` k vytváření a odstraňování odběrů událostí pouze na téma `demotopic1`:

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

Následující omezení příkaz prostředí PowerShell `alice@contoso.com` k vytváření a odstraňování odběrů událostí pouze na téma `demotopic1`:

```azurepowershell-interactive
New-AzureRmRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

Další informace o správě přístupu pro operace služby Event Grid najdete v tématu [ověřování a zabezpečení služby Event Grid](./security-authentication.md).

## <a name="create-topics-and-subscriptions"></a>Vytvoření témata a odběry

Služba Event Grid automaticky vytváří a spravuje odpovídajícím tématu v doméně podle volání za účelem vytvoření odběru událostí pro téma domény. Neexistuje žádný samostatný krok pro vytvoření tématu v doméně. Podobně když se odstraní poslední událost odběru tématu, téma je rovněž odstraněna.

Přihlášení k odběru tématu v doméně je stejný jako přihlášení k jakémukoli prostředku Azure. ID prostředku zdrojového zadejte ID události domény vrácená při vytváření domény dříve. Chcete-li zadat chcete přihlásit k odběru tématu, přidejte `/topics/<my-topic>` za účelem ID zdrojového prostředku. Vytvoření odběru událostí obor domény, která bude přijímat všechny události v doméně, zadejte ID události domény bez zadání jakékoli téma.

Obvykle uživateli je udělen přístup k v předchozí části by vytvořit odběr. Pro zjednodušení tohoto článku, vytvoříte odběr. 

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

Pokud potřebujete koncový bod testu k odběru události, vždycky můžete nasadit [předem vytvořené webové aplikace](https://github.com/Azure-Samples/azure-event-grid-viewer) , který zobrazuje příchozí události. Odesíláte události na váš web test na `https://<your-site-name>.azurewebsites.net/api/updates`.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

Oprávnění nastavená pro téma se ukládají ve službě Azure Active Directory a musí explicitně odstranit. Odstraňuje se odběr událostí nebude odvolat přístup uživatelů k vytvoření odběry událostí, pokud mají oprávnění k zápisu na téma.


## <a name="publish-events-to-an-event-grid-domain"></a>Publikování událostí do domény služby Event Grid

Publikování událostí do domény je stejný jako [publikování do vlastního tématu](./post-to-custom-topic.md). Jediným rozdílem je, že je třeba zadat téma, které si přejete přejdete na každou událost. Následující pole událostí, které by mělo za následek událost s `"id": "1111"` téma `foo` při události s `"id": "2222"` by odeslané do tématu `bar`:

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

Získat klíče pro doménu pomocí Azure CLI, použijte:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzureRmEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

A pak pomocí vašeho oblíbeného způsob vytváření metody POST protokolu HTTP k publikování událostí do služby Event Grid domény.

## <a name="next-steps"></a>Další postup

* Další informace o události domény a proč jsou užitečná základními koncepty, najdete v článku [koncepční přehled domén události](event-domains.md).