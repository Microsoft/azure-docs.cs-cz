---
title: Rozhraní API pro Live – Azure Marketplace
description: Rozhraní API na cestách spustí proces dynamického výpisu nabídky.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: d612b796f85c9eaab1600c55cde7e79acb49f352
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87292942"
---
# <a name="go-live"></a>Přejít na Live

> [!NOTE]
> Rozhraní API pro portál partnerů cloudu jsou integrovaná s a budou pokračovat v práci v partnerském centru. Přechod přináší malé změny. Projděte si změny uvedené v části [portál partnerů cloudu rozhraní API](./cloud-partner-portal-api-overview.md) , abyste zajistili, že kód pokračuje v práci po přechodu do partnerského centra. Rozhraní API CPP by se mělo používat jenom pro existující produkty, které už jsou integrované před přechodem do partnerského centra; nové produkty by měly používat rozhraní API pro odesílání v partnerském centru.

Toto rozhraní API spustí proces pro vložení aplikace do produkčního prostředí. Tato operace je obvykle dlouhodobě spuštěna. Toto volání používá v rámci operace [publikování](./cloud-partner-portal-api-publish-offer.md) rozhraní API seznam e-mailových oznámení.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

|  **Název**      |   **Popis**                                                           | **Datový typ** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Identifikátor vydavatele pro nabídku, která se má načíst, například `contoso`       |  Řetězec       |
| Hodnotami OfferId        | Identifikátor nabídky nabídky, která se má načíst                                   |  Řetězec       |
| verze-api    | Nejnovější verze rozhraní API                                                   |  Date (Datum)         |
|  |  |  |

## <a name="header"></a>Hlavička
------

|  **Název**       |     **Hodnota**       |
|  ---------      |     ----------      |
| Typ obsahu    | `application/json`  |
| Autorizace   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Příklad textu

### <a name="response"></a>Odpověď

#### <a name="migrated-offers"></a>Migrované nabídky

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Nemigrované nabídky

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Hlavička odpovědi

|  **Název**             |      **Hodnota**                                                            |
|  --------             |      ----------                                                           |
| Umístění    |  Relativní cesta pro načtení stavu této operace            |
|  |  |

### <a name="response-status-codes"></a>Stavové kódy odpovědí

| **Kód** |  **Popis**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` -Požadavek byl úspěšně přijat. Odpověď obsahuje umístění pro sledování stavu operace. |
|  400     | `Bad/Malformed request` -V těle odpovědi se zjistily Další informace o chybě. |
|  404     |  `Not found` -Zadaná entita neexistuje.                                       |
|  |  |
