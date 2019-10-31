---
title: Hlediska rozhraní API | Azure Marketplace
description: Správa verzí, zpracování chyb a problémy s autorizací při použití rozhraní API Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 762c90b62ed2a9347ae88a50a11bfe02f3b23ba4
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162664"
---
# <a name="api-considerations"></a>Požadavky rozhraní API


<a name="api-versioning"></a>Správa verzí API
--------------

Může existovat více verzí rozhraní API, které jsou k dispozici ve stejnou dobu. Klienti musí určit, kterou verzi chtějí vyvolat použití, zadáním parametru `api-version` jako součásti řetězce dotazu.

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

<a name="errors"></a>Chyby
------

Rozhraní API reaguje na chyby s odpovídajícími kódy stavu HTTP a volitelně také další informace v odpovědi serializované jako JSON.
Pokud obdržíte chybu, obzvláště Chyba 400, nemusíte požadavek opakovat, než opravíte původní příčinu. Například před odesláním požadavku opravte parametr verze rozhraní API v ukázkové odpovědi výše.

<a name="authorization-header"></a>Autorizační hlavička
--------------------

Pro všechna rozhraní API v tomto odkazu musíte předat autorizační hlavičku spolu s nosným tokenem získaným z Azure Active Directory (Azure AD). Tato hlavička je vyžadována pro příjem platné odpovědi; Pokud není k dispozici, je vrácena `401 Unauthorized` chyba. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
