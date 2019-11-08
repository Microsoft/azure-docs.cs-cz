---
title: Přejít do provozu | Azure Marketplace
description: Rozhraní API na cestách spustí proces dynamického výpisu nabídky.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 30500e9dfae9411563fc727290d0569998ba3550
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819686"
---
<a name="go-live"></a>Přejít na Live
=======

Toto rozhraní API spustí proces pro vložení aplikace do produkčního prostředí. Tato operace je obvykle dlouhodobě spuštěna. Toto volání používá v rámci operace [publikování](./cloud-partner-portal-api-publish-offer.md) rozhraní API seznam e-mailových oznámení.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

|  **Název**      |   **Popis**                                                           | **Datový typ** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Identifikátor vydavatele pro nabídku, která se má načíst, například `contoso`       |  Řetězec       |
| Hodnotami OfferId        | Identifikátor nabídky nabídky, která se má načíst                                   |  Řetězec       |
| verze API-Version    | Nejnovější verze rozhraní API                                                   |  Datum         |
|  |  |  |


<a name="header"></a>Hlavička
------

|  **Název**       |     **Hodnota**       |
|  ---------      |     ----------      |
| Typ obsahu    | `application/json`  |
| Autorizace   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>Příklad textu
------------

### <a name="response"></a>Odpověď

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Hlavička odezvy

|  **Název**             |      **Hodnota**                                                            |
|  --------             |      ----------                                                           |
| Operace – umístění    |  Adresa URL, na kterou se má dotaz zjistit aktuální stav operace            |
|  |  |


### <a name="response-status-codes"></a>Stavové kódy odpovědí

| **Znakovou** |  **Popis**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` – požadavek byl úspěšně přijat. Odpověď obsahuje umístění pro sledování stavu operace. |
|  400     | `Bad/Malformed request` – v těle odpovědi se nachází další informace o chybě. |
|  404     |  `Not found` – zadaná entita neexistuje.                                       |
|  |  |
