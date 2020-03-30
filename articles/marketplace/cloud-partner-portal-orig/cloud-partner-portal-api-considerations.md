---
title: Důležité informace o rozhraní API | Azure Marketplace
description: Správa verzí, zpracování chyb a problémy s autorizací při použití rozhraní API marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 4e04f521ed2023dfb9cd562549cb2e1bcd319b8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288627"
---
# <a name="api-considerations"></a>Důležité informace o rozhraní API


<a name="api-versioning"></a>Správa verzí rozhraní API
--------------

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
