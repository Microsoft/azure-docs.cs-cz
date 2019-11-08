---
title: Rozhraní API služby měření softwaru Marketplace | Azure Marketplace
description: Událost použití pro nabídky SaaS v Azure Marketplace.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 36ca95191e0e6422bd93360b98243393acad8147
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825480"
---
# <a name="marketplace-metering-service-apis"></a>Rozhraní API služeb měření na marketplace

Rozhraní API události využití umožňuje generovat události využití pro konkrétní zakoupenou entitu. Požadavek na událost využití odkazuje na dimenzi služby měření, kterou definoval Vydavatel při publikování nabídky.

## <a name="usage-event"></a>Událost použití

**Příspěvek**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Parametry dotazu:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | Verze operace, která se má použít pro tento požadavek. Nejnovější verze rozhraní API je 2018-08-31. |

*Hlavičky žádosti:*

| typ obsahu       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe pro identifikátor GUID. Pokud tato hodnota není zadána, bude vygenerována a uvedena v hlavičkách odpovědi. |
| `x-ms-correlationid` | Jedinečná řetězcová hodnota pro operaci na klientovi. Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
| `authorization`   | [Získat token nosiče webového tokenu JSON (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Poznámka: při vytváření požadavku HTTP předpona `Bearer` tokenu získaného z odkazovaného odkazu. |

*Request*

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
Chybný požadavek, chybí nebo jsou zadána neplatná data nebo vypršela jejich platnost.

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
Chybný požadavek, chybí nebo jsou zadána neplatná data nebo vypršela jejich platnost.

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

Kód: 409<br>
Konflikt, když obdržením volání použití pro ID prostředku využití a efektivní využití, které již existuje. Odpověď bude obsahovat pole `additionalInfo`, které obsahuje informace o přijaté zprávě.

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

## <a name="batch-usage-event"></a>Událost použití dávky

Rozhraní API události využití dávky umožňuje generovat události využití pro více než jednu zakoupenou entitu najednou. Požadavek na událost využití dávky se odkazuje na dimenzi služby měření, kterou definoval Vydavatel při publikování nabídky.

>[!Note]
>V komerčním tržišti společnosti Microsoft můžete zaregistrovat několik nabídek SaaS. Každá registrovaná nabídka SaaS má jedinečnou aplikaci Azure AD, která je zaregistrovaná pro účely ověřování a autorizace. Události emitované v dávce by měly patřit do nabídek se stejnou aplikací Azure AD v době registrace této nabídky.

**Příspěvek:** `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Parametry dotazu:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | Verze operace, která se má použít pro tento požadavek. Nejnovější verze rozhraní API je 2018-08-31. |

*Hlavičky žádosti:*

| typ obsahu       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe pro identifikátor GUID. Pokud tato hodnota není zadána, bude vygenerována a uvedena v hlavičkách odpovědi. |
| `x-ms-correlationid` | Jedinečná řetězcová hodnota pro operaci na klientovi. Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
| `authorization`      | [Získat token nosiče webového tokenu JSON (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Poznámka: při vytváření požadavku HTTP předpona `Bearer` tokenu získaného z odkazovaného odkazu.  |

*Request*
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

Popis stavového kódu odkazovaného v `BatchUsageEvent` odezva rozhraní API:

| Kód stavu  | Popis |
| ---------- | -------------------- |
| `Accepted` | Přijatý kód. |
| `Expired` | Použití vypršelo. |
| `Duplicate` | Bylo zadáno duplicitní použití. |
| `Error` | Kód chyby. |
| `ResourceNotFound` | Poskytnutý prostředek využití je neplatný. |
| `ResourceNotAuthorized` | Nemáte oprávnění k zajištění využití tohoto prostředku. |
| `InvalidDimension` | Dimenze, pro kterou je toto použití předáno, není pro tuto nabídku nebo plán platná. |
| `InvalidQuantity` | Předané množství je < 0. |
| `BadArgument` | Vstup chybí nebo je poškozený. |

Kód: 400<br>
Chybný požadavek, chybí nebo jsou zadána neplatná data nebo vypršela jejich platnost.

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
Uživatel není autorizovaný k provedení tohoto volání.

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [SaaS (účtované podle objemu](./saas-metered-billing.md)).
