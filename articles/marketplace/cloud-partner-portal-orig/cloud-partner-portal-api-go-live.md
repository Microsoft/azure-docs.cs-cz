---
title: Přechod na živý přenos | Azure Marketplace
description: Přejděte Live API zahájí nabídka živé výpis procesu.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: ac56f86bad132f3e00a4b5c2507d65c0722c628c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935492"
---
<a name="go-live"></a>Přechod na živý přenos
=======

Toto rozhraní API zahájí proces pro nabízení aplikace do produkčního prostředí. Tato operace je obvykle dlouhotrvající. Seznam e-mailových oznámení z používá toto volání [publikovat](./cloud-partner-portal-api-publish-offer.md) operace rozhraní API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

|  **Název**      |   **Popis**                                                           | **Datový typ** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Identifikátor vydavatel nabídky. Pokud chcete načíst, například `contoso`       |  String       |
| offerId        | Nabízejí identifikátor nabídky k načtení                                   |  String       |
| api-version    | Nejnovější verzi rozhraní API                                                   |  Datum         |
|  |  |  |


<a name="header"></a>Záhlaví
------

|  **Název**       |     **Hodnota**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Autorizace   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>Příklad těla
------------

### <a name="response"></a>Odpověď

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Hlavička odezvy

|  **Název**             |      **Hodnota**                                                            |
|  --------             |      ----------                                                           |
| Operace umístění    |  Adresa URL dotazu k určení stavu aktuální operace            |
|  |  |


### <a name="response-status-codes"></a>Stavové kódy odezvy

| **Kód** |  **Popis**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` -Požadavek byl úspěšně přijat. Odpověď obsahuje umístění, kam chcete sledovat stav operace. |
|  400     | `Bad/Malformed request` -Další informace o chybě najdete v textu odpovědi. |
|  404     |  `Not found` -Zadaná entita neexistuje.                                       |
|  |  |
