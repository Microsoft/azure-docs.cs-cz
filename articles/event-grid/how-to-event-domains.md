---
title: Postupy při správě velkých sad témat ve službě Azure Event Grid a publikování událostí je používání událostí domén
description: Ukazuje, jak vytvořit a spravovat témata ve službě Azure Event Grid a publikování událostí je používání událostí domén.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 48a5356b03e38e864ba76f048febdb0b040893f5
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50633783"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Správa témat a publikovat události pomocí událostí domény

Tento článek popisuje, jak:

* Vytvoření domény služby Event Grid
* Přihlaste se na témata, odběru
* Vypsat klíče
* Publikování událostí do domény

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Vytvoření domény události

Vytváření událostí domény, můžete to udělat pomocí `eventgrid` rozšíření pro [příkazového řádku Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Po vytvoření domény, můžete ke správě velkých sad témat.

```azurecli-interactive
# if you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name>
  -l <location>
```

Po úspěšném vytvoření se vrátí následující hodnoty:

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

Poznámka: `endpoint` a `id` jako je třeba Správa domény a publikování události.

## <a name="create-topics-and-subscriptions"></a>Vytvoření témata a odběry

Služba Event Grid automaticky vytváří a spravuje odpovídajícím tématu v doméně podle volání za účelem vytvoření odběru událostí pro téma domény. Neexistuje žádný samostatný krok pro vytvoření tématu v doméně. Podobně když se odstraní poslední událost odběru tématu, téma je rovněž odstraněna.

Přihlášení k odběru tématu v doméně je stejný jako přihlášení k jakémukoli prostředku Azure:

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/<my-topic>" \
  --endpoint https://contoso.azurewebsites.net/api/f1?code=code
```

Dané ID prostředku se stejným ID vrácená při vytváření domény dříve. Chcete-li zadat chcete přihlásit k odběru tématu, přidejte `/topics/<my-topic>` za účelem ID prostředku.

Vytvoření odběru událostí obor domény, která bude přijímat všechny události v doméně, zadejte doménu `resource-id` bez zadání jakékoli téma například `/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>`.

Pokud potřebujete koncový bod testu k odběru události, vždycky můžete nasadit [předem vytvořené webové aplikace](https://github.com/Azure-Samples/azure-event-grid-viewer) , který zobrazuje příchozí události. Odesíláte události na váš web test na `https://<your-site-name>.azurewebsites.net/api/updates`.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

Oprávnění nastavená pro téma se ukládají ve službě Azure Active Directory a musí explicitně odstranit. Odstraňuje se odběr událostí nebude odvolat přístup uživatelů k vytvoření odběry událostí, pokud mají oprávnění k zápisu na téma.

## <a name="manage-access-to-topics"></a>Správa přístupu na témata

Správa přístupu k tématům se provádí prostřednictvím [přiřazení role](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-cli). Přiřazení rolí používá Role na základě kontroly přístupu pro omezení operací s prostředky Azure na oprávněné uživatele v určitém rozsahu.

Event Grid má dvě předdefinované role, které vám umožní přidělit konkrétním uživatelům přístup na různá témata v rámci domény. Tyto role jsou `EventGrid EventSubscription Contributor (Preview)`, což umožňuje vytváření a odstraňování předplatných, a `EventGrid EventSubscription Reader (Preview)`, která povoluje jenom pro seznam odběrů událostí.

Následující příkaz, omezí se `alice@contoso.com` k vytváření a odstraňování odběrů událostí pouze na téma `foo`:

```azurecli-interactive
az role assignment create --assignee alice@contoso.com --role "EventGrid EventSubscription Contributor (Preview)" --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/foo
```

Zobrazit [ověřování a zabezpečení služby Event Grid](./security-authentication.md) pro další informace o:

* Správa řízení přístupu
* Typy operací
* Vytvoření vlastní definice rolí

## <a name="publish-events-to-an-event-grid-domain"></a>Publikování událostí do domény služby Event Grid

Publikování událostí do domény je stejný jako [publikování do vlastního tématu](./post-to-custom-topic.md). Jediným rozdílem je, že je třeba zadat téma, které si přejete přejdete na každou událost. Následující pole událostí, které by mělo za následek událost s `"id": "1111"` téma `foo` při události s `"id": "2222"` by odeslané do tématu `bar`:

```json
[{
  "topic": "foo",
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
  "topic": "bar",
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

Chcete-li získat klíče pro doménu, použijte:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

A pak pomocí vašeho oblíbeného způsob vytváření metody POST protokolu HTTP k publikování událostí do domény Event Grid.

## <a name="next-steps"></a>Další postup

* Další informace o události domény a proč jsou užitečná základními koncepty, najdete v článku [koncepční přehled domén události](./event-domains.md).