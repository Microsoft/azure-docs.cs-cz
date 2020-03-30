---
title: Zrušit operaci API | Azure Marketplace
description: Zrušit operace .
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d4c1f52f0f3b1e05ec06f5a66a36323f346d4eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280537"
---
# <a name="cancel-operation"></a>Zrušit operaci 

Toto rozhraní API zruší právě probíhající operaci nabídky. Pomocí [rozhraní API pro načtení operací](./cloud-partner-portal-api-retrieve-operations.md) získáte a `operationId` předejte toto rozhraní API. Zrušení je obvykle synchronní operace, ale v některých složitých scénářích může být vyžadována nová operace ke zrušení existující operace. V tomto případě tělo odpovědi HTTP obsahuje umístění operace, která by měla být použita k dotazování stavu.

Můžete poskytnout seznam e-mailových adres oddělených čárkami s požadavkem a rozhraní API bude tyto adresy upozorňovat na průběh operace.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

|  **Název**    |      **Popis**                                  |    **Datový typ**  |
| ------------ |     ----------------                                  |     -----------   |
| id vydavatele  |  Identifikátor vydavatele, například`contoso`         |   Řetězec          |
| offerId      |  Identifikátor nabídky                                     |   Řetězec          |
| verze-api  |  Aktuální verze rozhraní API                               |    Datum           |
|  |  |  |


<a name="header"></a>Hlavička
------

|  **Název**              |  **Hodnotu**         |
|  ---------             |  ----------        |
|  Typ obsahu          |  application/json  |
|  Autorizace         |  Nositel váš token |
|  |  |


<a name="body-example"></a>Příklad těla
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

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Hlavička odpovědi

|  **Název**             |    **Hodnotu**                       |
|  ---------            |    ----------                      |
| Umístění operace    | ADRESA URL, která může být dotazována k určení aktuálního stavu operace. |
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
