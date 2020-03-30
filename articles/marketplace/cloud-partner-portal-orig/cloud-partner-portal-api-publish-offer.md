---
title: Zveřejnit nabídku | Azure Marketplace
description: Rozhraní API pro publikování zadané nabídky.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 4163bf5727c327d559b81db42f99684aa0cc8d5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280520"
---
<a name="publish-an-offer"></a>Publikování nabídky
================

Spustí proces publikování pro zadanou nabídku. Toto volání je dlouho běžící operace.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

|  **Název**      |    **Popis**                               |  **Datový typ** |
|  ------------- |  ------------------------------------            |   -----------  |
|  id vydavatele   | Identifikátor vydavatele, například`contoso`      |   Řetězec       |
|  offerId       | Identifikátor nabídky                                 |   Řetězec       |
|  verze-api   | Nejnovější verze rozhraní API                        |   Datum         |
|  |  |


<a name="header"></a>Hlavička
------

|  **Název**        |    **Hodnotu**          |
|  --------        |    ---------          |
|  Typ obsahu    | `application/json`    |
|  Autorizace   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Příklad těla
------------

### <a name="request"></a>Žádost

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>Vlastnosti těla požadavku

|  **Název**               |   **Popis**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  e-maily s oznámením    | Seznam e-mailových adres oddělených čárkami, které mají být oznámeny průběhu operace publikování. |
|  |  |


### <a name="response"></a>Odpověď

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Hlavička odpovědi

|  **Název**             |    **Hodnotu**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Umístění operace    | Adresa URL, která může být dotazována k určení aktuálního stavu operace.    |
|  |  |


### <a name="response-status-codes"></a>Stavové kódy odpovědi

| **kód** |  **Popis**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`- Požadavek byl úspěšně přijat. Odpověď obsahuje umístění, které lze použít ke sledování operace, která je spuštěna. |
| 400   | `Bad/Malformed request`- Tělo odpovědi na chybu může poskytnout více informací.                                                               |
| 422   | `Un-processable entity`- Označuje, že entita, která má být publikována, se nezdařila ověření.                                                        |
| 404   | `Not found`- Entita určená klientem neexistuje.                                                                              |
|  |  |
