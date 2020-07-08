---
title: Azure Event Grid – řešení potíží s ověřováním předplatného
description: V tomto článku se dozvíte, jak můžete řešit problémy s ověřením předplatného.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: spelluru
ms.openlocfilehash: f292d70eaaca29e714ea35b4f61a141b2d5cd2b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83778718"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>Řešení potíží s Azure Event Grid ověření předplatného
Tento článek poskytuje informace o řešení ověřování odběrů událostí. 

> [!IMPORTANT]
> Podrobné informace o ověřování koncových bodů pro Webhooky najdete v tématu věnovaném [doručování událostí Webhooku](webhook-event-delivery.md).

## <a name="validate-event-grid-event-subscription-using-postman"></a>Ověření předplatného Event Gridch událostí pomocí metody post
Tady je příklad použití metody post pro ověření předplatného Webhooku události Event Grid: 

![Ověření odběru událostí Event gridu pomocí metody post](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

Tady je ukázkový **SubscriptionValidationEvent** JSON:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Tady je ukázková úspěšná odpověď:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Další informace o Event Grid ověřování událostí pro Webhooky najdete v tématu [ověřování koncového bodu s událostmi služby Event Grid](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).


## <a name="validate-event-grid-event-subscription-using-curl"></a>Ověřit odběr události Event Grid pomocí kudrlinkou 
Tady je ukázka složeného příkazu pro ověření předplatného Webhooku události Event Grid: 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="validate-cloud-event-subscription-using-postman"></a>Ověření předplatného cloudových událostí pomocí post
Tady je příklad použití metody post pro ověření předplatného Webhooku události cloudu: 

![Ověření předplatného cloudových událostí pomocí post](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

Použijte metodu **možností protokolu HTTP** pro ověřování s událostmi cloudu. Další informace o ověřování událostí v cloudu pro Webhooky najdete v tématu [ověřování koncového bodu s událostmi cloudu](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).

## <a name="error-code-403"></a>Kód chyby: 403
Pokud Webhook vrací v odpovědi 403 (zakázáno), ověřte, jestli je Webhook za Application Gateway nebo bránou firewall webových aplikací. Pokud je, je nutné zakázat následující pravidla brány firewall a znovu provést příkaz HTTP POST:

  - 920300 (žádost neobsahuje hlavičku Accept, můžeme to opravit)
  - 942430 (omezené zjištění anomálií znaků SQL (args): počet speciálních znaků překročen (12))
  - 920230 (bylo zjištěno více kódování adresy URL)
  - 942130 (útok injektáže SQL: byl zjištěn SQL Tautology.)
  - 931130 (možný útok na vzdálené zahrnutí souborů (RFI) = odkaz mimo doménu/odkaz)

## <a name="next-steps"></a>Další kroky
Pokud potřebujete další pomoc, vystavte svůj problém ve [fóru Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) nebo otevřete [lístek podpory](https://azure.microsoft.com/support/options/). 
