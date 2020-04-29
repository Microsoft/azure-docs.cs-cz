---
title: Hlediska rozhraní API | Azure Marketplace
description: Správa verzí, zpracování chyb a problémy s autorizací při použití rozhraní API Marketplace
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: e4d4d5cb16e1037458d09f8c7681ab2d2ecf8676
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81256327"
---
# <a name="api-considerations"></a>Požadavky rozhraní API

<a name="api-versioning"></a>Správa verzí rozhraní API
--------------

> [!NOTE]
> Rozhraní API pro portál partnerů cloudu jsou integrovaná do partnerského centra a budou fungovat i po migraci nabídek do partnerského centra. Integrace přináší malé změny. Projděte si změny uvedené v části [portál partnerů cloudu rozhraní API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) , abyste zajistili, že váš kód bude i nadále fungovat po migraci do partnerského centra.

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

<a name="errors"></a>chyby
------

Rozhraní API reaguje na chyby s odpovídajícími kódy stavu HTTP a volitelně také další informace v odpovědi serializované jako JSON.
Pokud obdržíte chybu, obzvláště Chyba 400, nemusíte požadavek opakovat, než opravíte původní příčinu. Například před odesláním požadavku opravte parametr verze rozhraní API v ukázkové odpovědi výše.

<a name="authorization-header"></a>Autorizační hlavička
--------------------

Pro všechna rozhraní API v tomto odkazu musíte předat autorizační hlavičku spolu s nosným tokenem získaným z Azure Active Directory (Azure AD). Tato hlavička je vyžadována pro příjem platné odpovědi; Pokud není k `401 Unauthorized` dispozici, je vrácena chyba. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
