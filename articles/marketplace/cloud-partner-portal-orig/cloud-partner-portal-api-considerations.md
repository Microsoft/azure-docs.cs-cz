---
title: Důležité informace o rozhraní API | Dokumentace Microsoftu
description: Správa verzí, zpracování chyb a autorizace problémy při použití rozhraní API na webu marketplace.
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
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: c6cfb41cb6254145821ab3fef662e9a5e54f6298
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809373"
---
<a name="api-considerations"></a>Důležité informace o rozhraní API
=================

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

<a name="authorization-header"></a>Autorizační hlavička
--------------------

Pro všechna rozhraní API v tomto odkazu musí projít autorizační hlavičky spolu s nosný token získaný ze služby Azure Active Directory (Azure AD). Tato hlavička se vyžaduje k přijetí platné odpovědi; Pokud není k dispozici, `401 Unauthorized` chyba je vrácena. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
