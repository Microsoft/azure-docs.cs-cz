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
ms.openlocfilehash: ea3f26d70c4a4ce07c988612890687504a4cf5ac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60624968"
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
| verze API-version    | Nejnovější verzi rozhraní API                                                   |  Datum         |
|  |  |  |


<a name="header"></a>Hlavička
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

| **Kód** |  **Popis**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` -Požadavek byl úspěšně přijat. Odpověď obsahuje umístění, kam chcete sledovat stav operace. |
|  400     | `Bad/Malformed request` -Další informace o chybě najdete v textu odpovědi. |
|  404     |  `Not found` -Zadaná entita neexistuje.                                       |
|  |  |
