---
title: Zveřejnit nabídku | Azure Marketplace
description: Rozhraní API pro publikování zadané nabídky.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 960d5facb53f20719045c5fdbe2179f549aca3f2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255936"
---
# <a name="publish-an-offer"></a>Publikování nabídky

> [!NOTE]
> Api portálu pro partnery cloudu jsou integrovaná s Partnerským centrem a budou fungovat i po migraci nabídek do Centra partnerů. Integrace přináší malé změny. Zkontrolujte změny uvedené v [referenčním rozhraní API portálu cloudových partnerů a](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) ujistěte se, že váš kód bude fungovat i po migraci do Centra partnerů.

Spustí proces publikování pro zadanou nabídku. Toto volání je dlouho běžící operace.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

|  **Název**      |    **Popis**                               |  **Datový typ** |
|  ------------- |  ------------------------------------            |   -----------  |
|  id vydavatele   | Identifikátor vydavatele, například`contoso`      |   Řetězec       |
|  offerId       | Identifikátor nabídky                                 |   Řetězec       |
|  verze-api   | Nejnovější verze rozhraní API                        |   Datum         |
|  |  |

## <a name="header"></a>Hlavička
------

|  **Název**        |    **Hodnotu**          |
|  --------        |    ---------          |
|  Typ obsahu    | `application/json`    |
|  Autorizace   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Příklad těla
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

#### <a name="migrated-offers"></a>Migrované nabídky

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Nemigrované nabídky

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Hlavička odpovědi

|  **Název**             |    **Hodnotu**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Umístění    | Relativní cesta k načtení stavu této operace     |
|  |  |


### <a name="response-status-codes"></a>Stavové kódy odpovědi

| **kód** |  **Popis**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`- Požadavek byl úspěšně přijat. Odpověď obsahuje umístění, které lze použít ke sledování operace, která je spuštěna. |
| 400   | `Bad/Malformed request`- Tělo odpovědi na chybu může poskytnout více informací.                                                               |
| 422   | `Un-processable entity`- Označuje, že entita, která má být publikována, se nezdařila ověření.                                                        |
| 404   | `Not found`- Entita určená klientem neexistuje.                                                                              |
|  |  |
