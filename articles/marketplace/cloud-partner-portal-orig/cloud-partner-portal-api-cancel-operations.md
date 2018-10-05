---
title: Zrušit operaci rozhraní API | Dokumentace Microsoftu
description: Zrušení operací.
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
ms.openlocfilehash: 18f00391beded0744c80eab73bb1efe1c6ab8dbc
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809197"
---
<a name="cancel-operation"></a>Zrušit operaci 
=================

Toto rozhraní API zruší aktuálně probíhá operace týkající se nabídky. Použití [načíst operace rozhraní API](./cloud-partner-portal-api-retrieve-operations.md) zobrazíte `operationId` k předání do tohoto rozhraní API. Zrušení je obvykle synchronní operace, ale v některých složitých případech může být nutné novou operaci zrušit existující. V takovém případě textu odpovědi HTTP obsahuje operace umístění, který se má použít k dotazování stavu.

Můžete zadat čárkou oddělený seznam e-mailové adresy s požadavkem a informovat tyto adresy o průběhu operace rozhraní API.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

|  **Název**    |      **Popis**                                  |    **Datový typ**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Identifikátor vydavatel, například `contoso`         |   Řetězec          |
| offerId      |  Identifikátor nabídky                                     |   Řetězec          |
| verze API-version  |  Aktuální verze rozhraní API                               |    Datum           |
|  |  |  |


<a name="header"></a>Záhlaví
------

|  **Název**              |  **Hodnota**         |
|  ---------             |  ----------        |
|  Typ obsahu          |  application/json  |
|  Autorizace         |  VÁŠ TOKEN nosiče |
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

### <a name="request-body-properties"></a>Vlastnosti textu požadavku

|  **Název**                |  **Popis**                                               |
|  --------                |  ---------------                                               |
|  oznámení e-mailů     | Čárkami oddělený seznam e-mailu ID, která vás upozorní na průběh operace publikování. |
|  |  |


### <a name="response"></a>Odpověď

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Hlavička odezvy

|  **Název**             |    **Hodnota**                       |
|  ---------            |    ----------                      |
| Operace umístění    | Adresa URL, která může být dotazována k určení stavu aktuální operace. |
|  |  |


### <a name="response-status-codes"></a>Stavové kódy odezvy

| **kód**  |  **Popis**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | Ok. Požadavek byl úspěšně zpracován a operace se zrušila synchronně. |
|  202      | Přijmout. Požadavek byl úspěšně zpracován a Probíhá rušení operace. Umístění zrušení operace je vrácená v hlavičce odpovědi. |
|  400      | Chybný/Malformed požadavku. Text odpovědi chyba by mohla nabídnout podrobnější informace.  |
|  403      | Přístup zakázán. Klient nemá přístup k oboru názvů v požadavku. |
|  404      | Nenalezeno Zadaná entita neexistuje. |
|  |  |
