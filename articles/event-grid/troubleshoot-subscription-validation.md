---
title: Azure Event Grid – řešení potíží s ověřováním předplatného
description: V tomto článku se dozvíte, jak můžete řešit problémy s ověřením předplatného.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 48844859013507ab684ef8879b7b85dd6b6fe8cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86118983"
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
  - 931130 (možný útok vzdáleného začlenění souborů (RFI) = odkaz na Off-Domain/odkaz)

## <a name="next-steps"></a>Další kroky
Pokud potřebujete další pomoc, vystavte svůj problém ve [fóru Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) nebo otevřete [lístek podpory](https://azure.microsoft.com/support/options/). 
