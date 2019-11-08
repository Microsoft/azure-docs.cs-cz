---
title: Publikování nabídky | Azure Marketplace
description: Rozhraní API pro publikování zadané nabídky
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: b7ad8086c417cf1f14d9116fa4abcb0a88030922
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819641"
---
<a name="publish-an-offer"></a>Publikování nabídky
================

Spustí proces publikování pro určenou nabídku. Toto volání je dlouhodobě běžící operace.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

|  **Název**      |    **Popis**                               |  **Datový typ** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Identifikátor vydavatele, například `contoso`      |   Řetězec       |
|  Hodnotami OfferId       | Identifikátor nabídky                                 |   Řetězec       |
|  verze API-Version   | Nejnovější verze rozhraní API                        |   Datum         |
|  |  |


<a name="header"></a>Hlavička
------

|  **Název**        |    **Hodnota**          |
|  --------        |    ---------          |
|  Typ obsahu    | `application/json`    |
|  Autorizace   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Příklad textu
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

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Hlavička odezvy

|  **Název**             |    **Hodnota**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Operace – umístění    | Adresa URL, na kterou lze zadat dotaz, aby bylo možné zjistit aktuální stav operace.    |
|  |  |


### <a name="response-status-codes"></a>Stavové kódy odpovědí

| **Znakovou** |  **Popis**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` – požadavek byl úspěšně přijat. Odpověď obsahuje umístění, které lze použít ke sledování operace, která je spuštěna. |
| 400   | `Bad/Malformed request` – tělo chybové odpovědi může poskytovat další informace.                                                               |
| 422   | `Un-processable entity` – určuje, že entita, která má být publikována, se nezdařila.                                                        |
| 404   | `Not found` – entita určená klientem neexistuje.                                                                              |
|  |  |
