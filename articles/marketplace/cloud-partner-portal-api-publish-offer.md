---
title: Publikování nabídky – Azure Marketplace
description: Rozhraní API pro publikování zadané nabídky
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 60e75aff79913896bdf1dcdc8754b6ecf5620b06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87272041"
---
# <a name="publish-an-offer"></a>Publikování nabídky

> [!NOTE]
> Rozhraní API pro portál partnerů cloudu jsou integrovaná s a budou pokračovat v práci v partnerském centru. Přechod přináší malé změny. Projděte si změny uvedené v části [portál partnerů cloudu rozhraní API](./cloud-partner-portal-api-overview.md) , abyste zajistili, že kód pokračuje v práci po přechodu do partnerského centra. Rozhraní API CPP by se mělo používat jenom pro existující produkty, které už jsou integrované před přechodem do partnerského centra; nové produkty by měly používat rozhraní API pro odesílání v partnerském centru.

Spustí proces publikování pro určenou nabídku. Toto volání je dlouhodobě běžící operace.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

|  **Název**      |    **Popis**                               |  **Datový typ** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Identifikátor vydavatele, například `contoso`      |   Řetězec       |
|  Hodnotami OfferId       | Identifikátor nabídky                                 |   Řetězec       |
|  verze-api   | Nejnovější verze rozhraní API                        |   Date (Datum)         |
|  |  |

## <a name="header"></a>Hlavička
------

|  **Název**        |    **Hodnota**          |
|  --------        |    ---------          |
|  Typ obsahu    | `application/json`    |
|  Autorizace   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Příklad textu
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

|  **Název**             |    **Hodnota**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Umístění    | Relativní cesta pro načtení stavu této operace     |
|  |  |

### <a name="response-status-codes"></a>Stavové kódy odpovědí

| **Kód** |  **Popis**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` -Požadavek byl úspěšně přijat. Odpověď obsahuje umístění, které lze použít ke sledování operace, která je spuštěna. |
| 400   | `Bad/Malformed request` – Tělo chybové odpovědi může poskytovat další informace.                                                               |
| 422   | `Un-processable entity` – Označuje, že entita, která má být publikována, se nezdařila.                                                        |
| 404   | `Not found` -Entita určená klientem neexistuje.                                                                              |
|  |  |
