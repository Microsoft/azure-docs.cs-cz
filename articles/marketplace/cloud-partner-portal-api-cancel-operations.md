---
title: Zrušení operace API – Microsoft Commercial Marketplace
description: Rozhraní API pro zrušení aktuálně probíhající operace v nabídce
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 095d9cf68374ee81cafdb6e7a32df29a57d122cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87287929"
---
# <a name="cancel-operation"></a>Zrušit operaci

> [!NOTE]
> Rozhraní API pro portál partnerů cloudu jsou integrovaná s a budou pokračovat v práci v partnerském centru. Přechod přináší malé změny. Projděte si změny uvedené v části [portál partnerů cloudu rozhraní API](./cloud-partner-portal-api-overview.md) , abyste zajistili, že kód pokračuje v práci po přechodu do partnerského centra. Rozhraní API CPP by se mělo používat jenom pro existující produkty, které už jsou integrované před přechodem do partnerského centra; nové produkty by měly používat rozhraní API pro odesílání v partnerském centru.

Toto rozhraní API zruší aktuálně probíhající operaci na této nabídce. K předání do tohoto rozhraní API použijte [rozhraní API pro načtení operací](./cloud-partner-portal-api-retrieve-operations.md) `operationId` . Zrušení je obvykle synchronní operace, ale v některých složitých scénářích může být pro zrušení existující operace nutná nová operace. V tomto případě tělo odpovědi HTTP obsahuje umístění operace, které by se mělo použít k dotazování na stav.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parametry identifikátoru URI

--------------

|  **Název**    |      **Popis**                                  |    **Datový typ**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Identifikátor vydavatele, například `contoso`         |   Řetězec          |
| Hodnotami OfferId      |  Identifikátor nabídky                                     |   Řetězec          |
| verze-api  |  Aktuální verze rozhraní API                               |    Date (Datum)           |
|  |  |  |

## <a name="header"></a>Hlavička
------

|  **Název**              |  **Hodnota**         |
|  ---------             |  ----------        |
|  Typ obsahu          |  application/json  |
|  Autorizace         |  Držitel TOKENu |
|  |  |

## <a name="body-example"></a>Příklad textu
------------

### <a name="request"></a>Žádost

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Vlastnosti textu žádosti

|  **Název**                |  **Popis**                                               |
|  --------                |  ---------------                                               |
|  oznámení – e-maily     | Čárkami oddělený seznam ID e-mailů, které se mají upozornit na průběh operace publikování. |
|  |  |

### <a name="response"></a>Odpověď

#### <a name="migrated-offers"></a>Migrované nabídky

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Nemigrované nabídky

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Hlavička odpovědi

|  **Název**             |    **Hodnota**                       |
|  ---------            |    ----------                      |
| Umístění    | Relativní cesta pro načtení stavu této operace. |
|  |  |

### <a name="response-status-codes"></a>Stavové kódy odpovědí

| **Kód**  |  **Popis**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | Ok. Požadavek byl úspěšně zpracován a operace byla zrušena synchronně. |
|  202      | Přijata. Požadavek byl úspěšně zpracován a probíhá rušení operace. Umístění operace zrušení je vráceno v hlavičce odpovědi. |
|  400      | Chybná nebo poškozená žádost. Tělo odpovědi na chyby může poskytnout další informace.  |
|  403      | Přístup je zakázán. Klient nemá přístup k oboru názvů, který je zadaný v požadavku. |
|  404      | Nenalezeno. Zadaná entita neexistuje. |
|  |  |
