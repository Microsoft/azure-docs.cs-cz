---
title: Důležité informace o rozhraní API | Azure Marketplace
description: Správa verzí, zpracování chyb a problémy s autorizací při použití rozhraní API marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: e4d4d5cb16e1037458d09f8c7681ab2d2ecf8676
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256327"
---
# <a name="api-considerations"></a>Důležité informace o rozhraní API

<a name="api-versioning"></a>Správa verzí rozhraní API
--------------

> [!NOTE]
> Api portálu pro partnery cloudu jsou integrovaná s Partnerským centrem a budou fungovat i po migraci nabídek do Centra partnerů. Integrace přináší malé změny. Zkontrolujte změny uvedené v [referenčním rozhraní API portálu cloudových partnerů a](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) ujistěte se, že váš kód bude fungovat i po migraci do Centra partnerů.

Může existovat více verzí rozhraní API, které jsou k dispozici současně. Klienti musí určit, kterou verzi chtějí `api-version` vyvolat použití zadáním parametru jako součást řetězce dotazu.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

Odpověď na požadavek s neznámou nebo neplatnou verzí rozhraní API je kód HTTP 400. Tato chyba vrátí kolekci známých verzí rozhraní API v těle odpovědi.

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>chyby
------

Rozhraní API reaguje na chyby s odpovídající kódy stavu HTTP a volitelně další informace v odpovědi serializované jako JSON.
Pokud se zobrazí chyba, zejména chyba třídy 400, neopakujte požadavek před opravou základní příčiny. Například ve výše vzorové odpovědi opravte parametr verze rozhraní API před opětovnou odesláním požadavku.

<a name="authorization-header"></a>Hlavička autorizace
--------------------

Pro všechna rozhraní API v tomto odkazu musíte předat hlavičku autorizace spolu s tokenem nosiče získaným z Azure Active Directory (Azure AD). Tato hlavička je vyžadována pro příjem platné odpovědi; pokud není k `401 Unauthorized` dispozici, je vrácena chyba. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
