---
title: Azure Event Grid – řešení potíží s ověřováním předplatného
description: V tomto článku se dozvíte, jak můžete řešit problémy s ověřením předplatného.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 857760182675d5673a3b09495c2faaf7372a4164
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94592936"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>Řešení potíží s Azure Event Grid ověření předplatného
Pokud se při vytváření odběru událostí zobrazuje chybová zpráva, například `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation` , znamená to, že došlo k chybě ověřovací metody handshake. Chcete-li tuto chybu vyřešit, ověřte následující aspekty:

- Proveďte příspěvek HTTP na adresu URL Webhooku s [ukázkovým](webhook-event-delivery.md#validation-details) textem žádosti SubscriptionValidationEvent pomocí metody post nebo kudrlinkou nebo podobného nástroje.
- Pokud Webhook implementuje mechanismus synchronního ověřování metodou handshake, ověřte, že se ValidationCode vrací jako součást odpovědi.
- Pokud Webhook implementuje mechanismus ověřování metodou handshake s asynchronním ověřováním, ověřte, že jste příspěvek HTTP vrátil 200 OK.
- Pokud se Webhook vrátí `403 (Forbidden)` v odpovědi, ověřte, jestli je Webhook za službou Azure Application Gateway nebo firewallem webových aplikací. Pokud je, je nutné zakázat tato pravidla brány firewall a znovu provést operaci HTTP POST:
    - 920300 (žádost neobsahuje hlavičku Accept)
    - 942430 (omezené zjištění anomálií znaků SQL (args): počet speciálních znaků překročen (12))
    - 920230 (bylo zjištěno více kódování adresy URL)
    - 942130 (útok injektáže SQL: byl zjištěn SQL tautology.)
    - 931130 (možný útok na vzdálené zahrnutí souborů (RFI) = odkaz mimo doménu/odkaz)

> [!IMPORTANT]
> Podrobné informace o ověřování koncových bodů pro Webhooky najdete v tématu věnovaném [doručování událostí Webhooku](webhook-event-delivery.md).

V následujících částech se dozvíte, jak ověřit odběry událostí pomocí možností post a kudrlinkou.  

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

## <a name="troubleshoot-event-subscription-validation"></a>Řešení potíží s ověřováním odběru událostí

## <a name="next-steps"></a>Další kroky
Pokud potřebujete další pomoc, vystavte svůj problém ve [fóru Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) nebo otevřete [lístek podpory](https://azure.microsoft.com/support/options/). 
