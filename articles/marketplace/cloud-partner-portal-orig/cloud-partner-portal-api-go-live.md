---
title: Přejít na živo | Azure Marketplace
description: Go Live API iniciuje nabídku live výpis procesu.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: ef22f7720a4af2239c55d1a01f9d3f11c878d66e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256310"
---
# <a name="go-live"></a>Přejít na živo

> [!NOTE]
> Api portálu pro partnery cloudu jsou integrovaná s Partnerským centrem a budou fungovat i po migraci nabídek do Centra partnerů. Integrace přináší malé změny. Zkontrolujte změny uvedené v [referenčním rozhraní API portálu cloudových partnerů a](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) ujistěte se, že váš kód bude fungovat i po migraci do Centra partnerů.

Toto rozhraní API spustí proces odesílání aplikace do produkčního prostředí. Tato operace je obvykle dlouhotrvající. Toto volání používá seznam e-mailů s oznámením z operace [publikovat](./cloud-partner-portal-api-publish-offer.md) rozhraní API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

|  **Název**      |   **Popis**                                                           | **Datový typ** |
|  --------      |   ---------------                                                           | ------------- |
| id vydavatele    | Identifikátor vydavatele pro nabídku k načtení, například`contoso`       |  Řetězec       |
| offerId        | Identifikátor nabídky k načtení                                   |  Řetězec       |
| verze-api    | Nejnovější verze rozhraní API                                                   |  Datum         |
|  |  |  |

## <a name="header"></a>Hlavička
------

|  **Název**       |     **Hodnotu**       |
|  ---------      |     ----------      |
| Typ obsahu    | `application/json`  |
| Autorizace   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Příklad těla

### <a name="response"></a>Odpověď

#### <a name="migrated-offers"></a>Migrované nabídky

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Nemigrované nabídky

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Hlavička odpovědi

|  **Název**             |      **Hodnotu**                                                            |
|  --------             |      ----------                                                           |
| Umístění    |  Relativní cesta k načtení stavu této operace            |
|  |  |

### <a name="response-status-codes"></a>Stavové kódy odpovědi

| **kód** |  **Popis**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`- Požadavek byl úspěšně přijat. Odpověď obsahuje umístění pro sledování stavu operace. |
|  400     | `Bad/Malformed request`- Další informace o chybě se nachází v těle odezvy. |
|  404     |  `Not found`- Zadaná entita neexistuje.                                       |
|  |  |
