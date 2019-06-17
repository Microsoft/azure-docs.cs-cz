---
title: Publikování nabídky | Azure Marketplace
description: Rozhraní API publikovat zadané nabídky.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 117a4e5e238e754524ff813ce25ebc1105e2153c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64934983"
---
<a name="publish-an-offer"></a>Publikování nabídky
================

Spustí proces publikování pro zadaný nabídku. Toto volání je dlouho běžící operace.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

|  **Název**      |    **Popis**                               |  **Datový typ** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Identifikátor vydavatel, například `contoso`      |   String       |
|  offerId       | Identifikátor nabídky                                 |   String       |
|  api-version   | Nejnovější verzi rozhraní API                        |   Datum         |
|  |  |


<a name="header"></a>Záhlaví
------

|  **Název**        |    **Hodnota**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
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

### <a name="request-body-properties"></a>Vlastnosti textu požadavku

|  **Název**               |   **Popis**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  oznámení e-mailů    | Čárkami oddělený seznam e-mailové adresy, která vás upozorní na průběh operace publikování. |
|  |  |


### <a name="response"></a>Odpověď

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Hlavička odezvy

|  **Název**             |    **Hodnota**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Operace umístění    | Adresa URL může být dotazována k určení stavu aktuální operace.    |
|  |  |


### <a name="response-status-codes"></a>Stavové kódy odezvy

| **Kód** |  **Popis**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` -Požadavek byl úspěšně přijat. Odpověď obsahuje umístění, které je možné sledovat operace, která se spustí. |
| 400   | `Bad/Malformed request` Text odpovědi – chyba mohou poskytnout další informace.                                                               |
| 422   | `Un-processable entity` – Označuje, že entita, která má být publikování se nezdařilo ověření.                                                        |
| 404   | `Not found` -Určená klientem entita neexistuje.                                                                              |
|  |  |
