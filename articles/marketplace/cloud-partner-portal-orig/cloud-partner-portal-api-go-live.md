---
title: Přechod na živý přenos | Dokumentace Microsoftu
description: Přejděte Live API zahájí nabídka živé výpis procesu.
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
ms.openlocfilehash: c7d643f0c7885e64636a107d22ce332b1ba9371c
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809202"
---
<a name="go-live"></a>Přechod na živý přenos
=======

Toto rozhraní API zahájí proces pro nabízení aplikace do produkčního prostředí. Tato operace je obvykle dlouhotrvající. Seznam e-mailových oznámení z používá toto volání [publikovat](./cloud-partner-portal-api-publish-offer.md) operace rozhraní API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

|  **Název**      |   **Popis**                                                           | **Datový typ** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Identifikátor vydavatel nabídky. Pokud chcete načíst, například `contoso`       |  Řetězec       |
| offerId        | Nabízejí identifikátor nabídky k načtení                                   |  Řetězec       |
| verze API-version    | Nejnovější verzi rozhraní API                                                   |  Datum         |
|  |  |  |


<a name="header"></a>Záhlaví
------

|  **Název**       |     **Hodnota**       |
|  ---------      |     ----------      |
| Typ obsahu    | `application/json`  |
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

| **kód** |  ** Popis **                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` -Požadavek byl úspěšně přijat. Odpověď obsahuje umístění, kam chcete sledovat stav operace. |
|  400     | `Bad/Malformed request` -Další informace o chybě najdete v textu odpovědi. |
|  404     |  `Not found` -Zadaná entita neexistuje.                                       |
|  |  |
