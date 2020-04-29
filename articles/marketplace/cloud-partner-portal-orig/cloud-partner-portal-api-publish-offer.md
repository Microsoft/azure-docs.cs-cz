---
title: Publikování nabídky | Azure Marketplace
description: Rozhraní API pro publikování zadané nabídky
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 960d5facb53f20719045c5fdbe2179f549aca3f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255936"
---
# <a name="publish-an-offer"></a>Publikování nabídky

> [!NOTE]
> Rozhraní API pro portál partnerů cloudu jsou integrovaná do partnerského centra a budou fungovat i po migraci nabídek do partnerského centra. Integrace přináší malé změny. Projděte si změny uvedené v části [portál partnerů cloudu rozhraní API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) , abyste zajistili, že váš kód bude i nadále fungovat po migraci do partnerského centra.

Spustí proces publikování pro určenou nabídku. Toto volání je dlouhodobě běžící operace.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

|  **Název**      |    **Popis**                               |  **Datový typ** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Identifikátor vydavatele, například`contoso`      |   Řetězec       |
|  Hodnotami OfferId       | Identifikátor nabídky                                 |   Řetězec       |
|  verze-api   | Nejnovější verze rozhraní API                        |   Datum         |
|  |  |

## <a name="header"></a>Hlavička
------

|  **Název**        |    **Osa**          |
|  --------        |    ---------          |
|  Typ obsahu    | `application/json`    |
|  Autorizace   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Příklad textu
------------

### <a name="request"></a>Request

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>Vlastnosti textu žádosti

|  **Název**               |   **Popis**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  oznámení – e-maily    | Čárkami oddělený seznam e-mailových adres, které se mají upozornit na průběh operace publikování. |
|  |  |


### <a name="response"></a>Odpověď

#### <a name="migrated-offers"></a>Migrované nabídky

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Nemigrované nabídky

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Hlavička odpovědi

|  **Název**             |    **Osa**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Umístění    | Relativní cesta pro načtení stavu této operace     |
|  |  |


### <a name="response-status-codes"></a>Stavové kódy odpovědí

| **kód** |  **Popis**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`-Požadavek byl úspěšně přijat. Odpověď obsahuje umístění, které lze použít ke sledování operace, která je spuštěna. |
| 400   | `Bad/Malformed request`– Tělo chybové odpovědi může poskytovat další informace.                                                               |
| 422   | `Un-processable entity`– Označuje, že entita, která má být publikována, se nezdařila.                                                        |
| 404   | `Not found`-Entita určená klientem neexistuje.                                                                              |
|  |  |
