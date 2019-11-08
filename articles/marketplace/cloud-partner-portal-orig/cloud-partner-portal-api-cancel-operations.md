---
title: Zrušit rozhraní API pro operace | Azure Marketplace
description: Zrušit operace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 374425dbd2abacb2114b5792d7476bc341fa353a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819790"
---
# <a name="cancel-operation"></a>Zrušit operaci 

Toto rozhraní API zruší aktuálně probíhající operaci na této nabídce. K předání `operationId` k tomuto rozhraní API použijte [rozhraní API pro načtení operací](./cloud-partner-portal-api-retrieve-operations.md) . Zrušení je obvykle synchronní operace, ale v některých složitých scénářích může být pro zrušení existující operace nutná nová operace. V tomto případě tělo odpovědi HTTP obsahuje umístění operace, které by se mělo použít k dotazování na stav.

V rámci žádosti můžete zadat čárkami oddělený seznam e-mailových adres a rozhraní API tyto adresy bude informovat o průběhu operace.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

|  **Název**    |      **Popis**                                  |    **Datový typ**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Identifikátor vydavatele, například `contoso`         |   Řetězec          |
| Hodnotami OfferId      |  Identifikátor nabídky                                     |   Řetězec          |
| verze API-Version  |  Aktuální verze rozhraní API                               |    Datum           |
|  |  |  |


<a name="header"></a>Hlavička
------

|  **Název**              |  **Hodnota**         |
|  ---------             |  ----------        |
|  Typ obsahu          |  application/json  |
|  Autorizace         |  Držitel TOKENu |
|  |  |


<a name="body-example"></a>Příklad textu
------------

### <a name="request"></a>Žádost

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Vlastnosti textu žádosti

|  **Název**                |  **Popis**                                               |
|  --------                |  ---------------                                               |
|  oznámení – e-maily     | Čárkami oddělený seznam ID e-mailů, které se mají upozornit na průběh operace publikování. |
|  |  |


### <a name="response"></a>Odpověď

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Hlavička odezvy

|  **Název**             |    **Hodnota**                       |
|  ---------            |    ----------                      |
| Operace – umístění    | Adresa URL, na kterou se dá dotázat, aby bylo možné zjistit aktuální stav operace. |
|  |  |


### <a name="response-status-codes"></a>Stavové kódy odpovědí

| **Znakovou**  |  **Popis**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | Ok. Požadavek byl úspěšně zpracován a operace byla zrušena synchronně. |
|  202      | Přijata. Požadavek byl úspěšně zpracován a probíhá rušení operace. Umístění operace zrušení je vráceno v hlavičce odpovědi. |
|  400      | Chybná nebo poškozená žádost. Tělo odpovědi na chyby může poskytnout další informace.  |
|  403      | Přístup je zakázán. Klient nemá přístup k oboru názvů, který je zadaný v požadavku. |
|  404      | Nenalezeno. Zadaná entita neexistuje. |
|  |  |
