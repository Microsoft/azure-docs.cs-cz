---
title: Hlediska rozhraní API – Azure Marketplace
description: Správa verzí, zpracování chyb a problémy s autorizací při použití rozhraní API Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 0920bec160874f27c8b1b6f2132951b57719b31c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88035276"
---
# <a name="api-considerations"></a>Informace k rozhraním API

<a name="api-versioning"></a>Správa verzí rozhraní API
--------------

> [!NOTE]
> Rozhraní API pro portál partnerů cloudu jsou integrovaná s a budou pokračovat v práci v partnerském centru. Přechod přináší malé změny. Projděte si změny uvedené v části [portál partnerů cloudu rozhraní API](./cloud-partner-portal-api-overview.md) , abyste zajistili, že kód pokračuje v práci po přechodu do partnerského centra. Rozhraní API CPP by se mělo používat jenom pro existující produkty, které už jsou integrované před přechodem do partnerského centra; nové produkty by měly používat rozhraní API pro odesílání v partnerském centru.

Může existovat více verzí rozhraní API, které jsou k dispozici ve stejnou dobu. Klienti musí určit, kterou verzi chtějí vyvolat použití, zadáním `api-version` parametru jako součásti řetězce dotazu.

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

Pro všechna rozhraní API v tomto odkazu musíte předat autorizační hlavičku spolu s nosným tokenem získaným z Azure Active Directory (Azure AD). Tato hlavička je vyžadována pro příjem platné odpovědi; Pokud není k dispozici, `401 Unauthorized` je vrácena chyba. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
