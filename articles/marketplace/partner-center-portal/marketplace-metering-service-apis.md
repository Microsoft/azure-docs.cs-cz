---
title: Marketplace měření služby API | Azure Marketplace
description: Událost využití nabídek SaaS na Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 315f36e5aed9dee0a89e1f9f504b18a6bed806e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275743"
---
# <a name="marketplace-metering-service-apis"></a>Rozhraní API služeb měření na marketplace

Rozhraní API události využití umožňuje vyzařovat události využití pro konkrétní zakoupenou entitu. Požadavek na událost použití odkazuje na dimenzi služeb měření definovanou vydavatelem při publikování nabídky.

## <a name="usage-event"></a>Událost použití

**PŘÍSPĚVEK**:`https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Parametry dotazu:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | Verze operace, která má být pro tento požadavek používána. Nejnovější verze ROZHRANÍ API je 2018-08-31. |

*Hlavičky požadavku:*

| Typ obsahu       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Jedinečná hodnota řetězce pro sledování požadavku od klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičce odpovědi. |
| `x-ms-correlationid` | Jedinečná hodnota řetězce pro operaci na straně klienta. Tento parametr koreluje všechny události z operace klienta s událostmi na straně serveru. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi. |
| `authorization`   | [Získejte JSON webový token (JWT) nosné token.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Poznámka: Při vytváření požadavku HTTP `Bearer` předpona tokenu získaného z odkazovaného odkazu. |

*Požadavek:*

```json
{
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

### <a name="responses"></a>Odezvy

Kód: 200<br>
OK 

```json
{
  "usageEventId": "Unique identifier associated with the usage event",
  "status": "Accepted",
  "messageTime": "Time this message was created in UTC",
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

Kód: 400 <br>
Chybný požadavek, chybějící nebo neplatná data poskytnutá nebo vypršela jeho platnost

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

Kód: 403<br>
Chybný požadavek, chybějící nebo neplatná data poskytnutá nebo vypršela jeho platnost

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

Kód: 409<br>
Konflikt, když obdržíme volání využití pro ID prostředku využití a efektivní využití, které již existuje. Odpověď bude `additionalInfo` obsahovat pole obsahující informace o přijaté zprávě.

```json
{
  "code": "Conflict",
  "additionalInfo": {
    "usageEventId": "Unique identifier associated with the usage event",
    "status": "Accepted|NotProcessed|Expired",
    "messageTime": "Time this message was created in UTC",
    "resourceId": "Identifier of the resource against which usage is emitted",
    "quantity": 5.0,
    "dimension": "Dimension identifier",
    "effectiveStartTime": "Time in UTC when the usage event occurred",
    "planId": "Plan associated with the purchased offer"
  }
}
```

## <a name="batch-usage-event"></a>Událost dávkového využití

Rozhraní API událostí dávkového využití umožňuje vyzařovat události využití pro více než jednu zakoupenou entitu najednou. Požadavek na událost dávkového využití odkazuje na dimenzi služeb měření definovanou vydavatelem při publikování nabídky.

>[!Note]
>Můžete zaregistrovat více nabídek SaaS na komerčním trhu společnosti Microsoft. Každá registrovaná nabídka SaaS má jedinečnou aplikaci Azure AD, která je registrovaná pro účely ověřování a autorizace. Události vyzařované v dávce by měly patřit do nabídek se stejnou aplikací Azure AD v době registrace nabídky.

**PŘÍSPĚVEK:**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Parametry dotazu:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | Verze operace, která má být pro tento požadavek používána. Nejnovější verze ROZHRANÍ API je 2018-08-31. |

*Hlavičky požadavku:*

| Typ obsahu       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Jedinečná hodnota řetězce pro sledování požadavku od klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, jeden bude generována a za předpokladu, v hlavičce odpovědi. |
| `x-ms-correlationid` | Jedinečná hodnota řetězce pro operaci na straně klienta. Tento parametr koreluje všechny události z operace klienta s událostmi na straně serveru. Pokud tato hodnota není k dispozici, jeden bude generována a za předpokladu, v záhlaví odpovědi. |
| `authorization`      | [Získejte JSON webový token (JWT) nosné token.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Poznámka: Při vytváření požadavku HTTP `Bearer` předpona tokenu získaného z odkazovaného odkazu.  |

*Požadavek:*
```json
{
  "request": [
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    },
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    }
  ]
}
```
### <a name="responses"></a>Odezvy

Kód: 200<br>
OK

```json
{
  "count": 2,
  "result": [
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    },
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    }
  ]
}
```

Popis stavového kódu, `BatchUsageEvent` na který se odkazuje v odpovědi rozhraní API:

| Kód stavu  | Popis |
| ---------- | -------------------- |
| `Accepted` | Přijatý kód. |
| `Expired` | Vypršela platnost použití. |
| `Duplicate` | Duplikát využití k dispozici. |
| `Error` | Kód chyby |
| `ResourceNotFound` | Poskytnutý prostředek využití je neplatný. |
| `ResourceNotAuthorized` | Nejste oprávněni poskytovat využití tohoto prostředku. |
| `InvalidDimension` | Dimenze, pro kterou je použití předáno, je pro tuto nabídku/plán neplatná. |
| `InvalidQuantity` | Uplynulé množství je < 0. |
| `BadArgument` | Vstup chybí nebo je poškozený. |

Kód: 400<br>
Chybný požadavek, chybějící nebo neplatná data poskytnutá nebo vypršela jeho platnost

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "Invalid data format.",
      "target": "usageEventRequest",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```
Kód: 403<br>
Uživatel není oprávněn provést tento hovor.

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>Další kroky

Další informace naleznete v [tématu SaaS měřené fakturace](./saas-metered-billing.md).
