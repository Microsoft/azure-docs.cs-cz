---
title: Publikování nabídky | Dokumentace Microsoftu
description: Rozhraní API publikovat zadané nabídky.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: cb1293a771a137f4df7e36a2b412f68b384f16ef
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809557"
---
<a name="publish-an-offer"></a>Publikování nabídky
================

Spustí proces publikování pro zadaný nabídku. Toto volání je dlouho běžící operace.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

|  **Název**      |    **Popis**                               |  **Datový typ** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Identifikátor vydavatel, například `contoso`      |   Řetězec       |
|  offerId       | Identifikátor nabídky                                 |   Řetězec       |
|  verze API-version   | Nejnovější verzi rozhraní API                        |   Datum         |
|  |  |


<a name="header"></a>Záhlaví
------

|  **Název**        |    **Hodnota**          |
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

| **kód** |  **Popis**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` -Požadavek byl úspěšně přijat. Odpověď obsahuje umístění, které je možné sledovat operace, která se spustí. |
| 400   | `Bad/Malformed request` Text odpovědi – chyba mohou poskytnout další informace.                                                               |
| 422   | `Un-processable entity` – Označuje, že entita, která má být publikování se nezdařilo ověření.                                                        |
| 404   | `Not found` -Určená klientem entita neexistuje.                                                                              |
|  |  |
