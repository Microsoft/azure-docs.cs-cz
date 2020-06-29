---
title: Rozhraní API pro Live – Azure Marketplace
description: Rozhraní API na cestách spustí proces dynamického výpisu nabídky.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 5d550f576108447a88660321899f2f55ffeb3c77
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2020
ms.locfileid: "85516190"
---
# <a name="go-live"></a>Přejít na Live

> [!NOTE]
> Rozhraní API pro portál partnerů cloudu jsou integrovaná do partnerského centra a budou fungovat i po migraci nabídek do partnerského centra. Integrace přináší malé změny. Projděte si změny uvedené v části [portál partnerů cloudu rozhraní API](./cloud-partner-portal-api-overview.md) , abyste zajistili, že váš kód bude i nadále fungovat po migraci do partnerského centra.

Toto rozhraní API spustí proces pro vložení aplikace do produkčního prostředí. Tato operace je obvykle dlouhodobě spuštěna. Toto volání používá v rámci operace [publikování](./cloud-partner-portal-api-publish-offer.md) rozhraní API seznam e-mailových oznámení.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

|  **Název**      |   **Popis**                                                           | **Datový typ** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Identifikátor vydavatele pro nabídku, která se má načíst, například`contoso`       |  Řetězec       |
| Hodnotami OfferId        | Identifikátor nabídky nabídky, která se má načíst                                   |  Řetězec       |
| verze-api    | Nejnovější verze rozhraní API                                                   |  Datum         |
|  |  |  |

## <a name="header"></a>Záhlaví
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
|  202     | `Accepted`-Požadavek byl úspěšně přijat. Odpověď obsahuje umístění pro sledování stavu operace. |
|  400     | `Bad/Malformed request`-V těle odpovědi se zjistily Další informace o chybě. |
|  404     |  `Not found`-Zadaná entita neexistuje.                                       |
|  |  |
