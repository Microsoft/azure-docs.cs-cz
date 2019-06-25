---
title: Důležité informace o rozhraní API | Azure Marketplace
description: Správa verzí, zpracování chyb a autorizace problémy při použití rozhraní API na webu marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 6bf27db27daee50f78552344ae1b2b116d48a5c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935581"
---
# <a name="api-considerations"></a>Důležité informace o rozhraní API


<a name="api-versioning"></a>Správa verzí API
--------------

Může existovat více verzí rozhraní API, které jsou k dispozici ve stejnou dobu. Klienti musí uvádět kterou verzi si přejí vyvolání použití tím, že poskytuje `api-version` parametr jako součást řetězce dotazu.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

Odpověď na žádost s neznámou nebo neplatnou verzí rozhraní API je kód HTTP 400. Tato chyba vrátí kolekci známé verze rozhraní API v textu odpovědi.

``` json
    {
        "error”: { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Chyby
------

Rozhraní API reagují na chyby s odpovídající stavové kódy HTTP a volitelně také další informace v reakci serializovanou jako JSON.
Pokud obdržíte chybu, zejména třídy 400 chyba, opakování požadavku před opravou základní příčinu. Například ve výše uvedené ukázkové odpovědi, opravte parametr verze rozhraní API zprávu a odešlete požadavek znovu.

<a name="authorization-header"></a>Autorizační hlavičky.
--------------------

Pro všechna rozhraní API v tomto odkazu musí projít autorizační hlavičky spolu s nosný token získaný ze služby Azure Active Directory (Azure AD). Tato hlavička se vyžaduje k přijetí platné odpovědi; Pokud není k dispozici, `401 Unauthorized` chyba je vrácena. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
