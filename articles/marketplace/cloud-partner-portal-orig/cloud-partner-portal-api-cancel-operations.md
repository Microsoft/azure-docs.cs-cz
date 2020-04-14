---
title: Zrušit operaci API | Azure Marketplace
description: Zrušit operace .
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: f9e55ff2c581f9392a125f6dc3ec8d903e9876a4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256429"
---
# <a name="cancel-operation"></a>Zrušit operaci

> [!NOTE]
> Api portálu pro partnery cloudu jsou integrovaná s Partnerským centrem a budou fungovat i po migraci nabídek do Centra partnerů. Integrace přináší malé změny. Zkontrolujte změny uvedené v [referenčním rozhraní API portálu cloudových partnerů a](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) ujistěte se, že váš kód bude fungovat i po migraci do Centra partnerů.

Toto rozhraní API zruší právě probíhající operaci nabídky. Pomocí [rozhraní API pro načtení operací](./cloud-partner-portal-api-retrieve-operations.md) získáte a `operationId` předejte toto rozhraní API. Zrušení je obvykle synchronní operace, ale v některých složitých scénářích může být vyžadována nová operace ke zrušení existující operace. V tomto případě tělo odpovědi HTTP obsahuje umístění operace, která by měla být použita k dotazování stavu.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parametry identifikátoru URI

--------------

|  **Název**    |      **Popis**                                  |    **Datový typ**  |
| ------------ |     ----------------                                  |     -----------   |
| id vydavatele  |  Identifikátor vydavatele, například`contoso`         |   Řetězec          |
| offerId      |  Identifikátor nabídky                                     |   Řetězec          |
| verze-api  |  Aktuální verze rozhraní API                               |    Datum           |
|  |  |  |

## <a name="header"></a>Hlavička
------

|  **Název**              |  **Hodnotu**         |
|  ---------             |  ----------        |
|  Typ obsahu          |  application/json  |
|  Autorizace         |  Nositel váš token |
|  |  |

## <a name="body-example"></a>Příklad těla
------------

### <a name="request"></a>Žádost

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Vlastnosti těla požadavku

|  **Název**                |  **Popis**                                               |
|  --------                |  ---------------                                               |
|  e-maily s oznámením     | Seznam e-mailových ID oddělených čárkami, která mají být oznámena průběhu operace publikování. |
|  |  |

### <a name="response"></a>Odpověď

#### <a name="migrated-offers"></a>Migrované nabídky

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Nemigrované nabídky

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Hlavička odpovědi

|  **Název**             |    **Hodnotu**                       |
|  ---------            |    ----------                      |
| Umístění    | Relativní cesta k načtení stavu této operace. |
|  |  |

### <a name="response-status-codes"></a>Stavové kódy odpovědi

| **kód**  |  **Popis**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | Ok. Požadavek byl úspěšně zpracován a operace je synchronně zrušena. |
|  202      | Přijata. Požadavek byl úspěšně zpracován a operace je v procesu jejich zrušení. Umístění operace zrušení je vrácena v hlavičce odpovědi. |
|  400      | Chybný/poškozený požadavek. Tělo odpovědi na chybu může poskytnout další informace.  |
|  403      | Přístup zakázán. Klient nemá přístup k oboru názvů zadanému v požadavku. |
|  404      | Nebyl nalezen. Zadaná entita neexistuje. |
|  |  |
