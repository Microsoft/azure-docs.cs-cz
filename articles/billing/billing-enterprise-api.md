---
title: Azure fakturace Enterprise rozhraní API | Microsoft Docs
description: Další informace o rozhraních API vytváření sestav, které umožňují zákazníkům Enterprise Azure a si vyžádá data spotřeby prostřednictvím kódu programu.
services: ''
documentationcenter: ''
author: anandedwin
manager: aedwin
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: aedwin
ms.openlocfilehash: ff658fd14700e9fdf66b9d929da133f7a3b3f3a0
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831781"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Přehled rozhraní API pro vytváření sestav pro podnikové zákazníky
Rozhraní API pro vytváření sestav umožňují zákazníky, kteří Enterprise Azure prostřednictvím kódu programu vyžádání využívání a fakturační údaje do nástrojů pro analýzu dat upřednostňované. 

## <a name="enabling-data-access-to-the-api"></a>Povolení přístupu k datům v rozhraní API
* **Generovat nebo načíst klíč rozhraní API** -Přihlaste se k portálu organizace a přejděte do sestavy > stáhnout využití > přístupový klíč rozhraní API pro generování nebo načíst klíč rozhraní API.
* **Předávání klíče v rozhraní API** – klíč rozhraní API musí být předán pro každé volání pro ověřování a autorizaci. Následující vlastnost musí být hlavičkami protokolu HTTP

|Klíč hlavičky požadavku | Hodnota|
|-|-|
|Autorizace| Zadejte hodnotu v tomto formátu: **nosiče {API_KEY}** <br/> Příklad: nosiče eyr... 09| 

## <a name="consumption-apis"></a>Rozhraní API spotřeba
Koncový bod Swagger je k dispozici [sem](https://consumption.azure.com/swagger/ui/index) pro rozhraní API popsané, pod kterou by měl povolit snadno introspection rozhraní API a generovat klientské sady SDK, pomocí [AutoRest](https://github.com/Azure/AutoRest) nebo [Swagger CodeGen](http://swagger.io/swagger-codegen/). Data od 1 pravděpodobně 2014 je k dispozici prostřednictvím tohoto rozhraní API. 

* **Souhrn a vyrovnávat** – [vyrovnávat a souhrn rozhraní API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) nabízí měsíční souhrnné informace o zůstatky, nové nákupy, poplatky za služby Azure Marketplace, přizpůsobení a Nadlimitní poplatky.

* **Podrobnosti o použití** – [podrobnosti o použití rozhraní API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) nabízí denní rozpis těchto spotřebované počty a odhadované poplatky podle zápisu. Výsledek také obsahuje informace o instancích, měřidla a oddělení. Rozhraní API můžete položit dotaz na fakturační období nebo zadaný počáteční a koncové datum. 

* **Úložiště Marketplace poplatků** – [Marketplace úložiště poplatků API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) vrátí rozdělení na základě využití marketplace poplatky za den pro zadaný fakturační období nebo počáteční a koncové datum (jednou poplatky nejsou součástí).

* **Ceník** – [Price Sheet API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) umožňuje použít rychlost pro každé monitorování dané registraci a fakturační období. 

## <a name="data-freshness"></a>Aktuálnost dat
Značky etag binárním rozsáhlým, bude vrácen v odpovědi všechny výše uvedené rozhraní API. Ke změně v Etag označuje, že data byla aktualizována.  Při následných voláních stejné rozhraní API pomocí stejné parametry předejte zaznamenané Etag s klíčem "If-None-Match" v hlavičce požadavku protokolu http. Stavový kód odpovědi bude "NotModified", pokud data nebyla obnovena žádné další a nebudou vrácena žádná data. Rozhraní API vrátí úplné datové sadě pro požadované období vždy, když dojde změně značka etag.

## <a name="helper-apis"></a>Pomocná rozhraní API
 **Seznam fakturační období** – [fakturační období API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) vrátí seznam hodnot fakturační období, která mají data energie pro zadanou registraci v obráceném chronologickém pořadí. Každé období obsahuje vlastnost odkazuje na trasu rozhraní API pro čtyři sady dat – BalanceSummary, UsageDetails, Marketplace poplatky a ceníku.


## <a name="api-response-codes"></a>Kódy odpovědí rozhraní API   
|Stavový kód odpovědi|Zpráva|Popis|
|-|-|-|
|200| OK|Bez chyby|
|401| Neautorizováno| Klíč rozhraní API nebyl nalezen platný platnost atd.|
|404| Není k dispozici| Koncový bod sestavy nebyl nalezen|
|400| Chybná žádost| Neplatné parametry – rozsahy dat, EA čísel atd.|
|500| Chyba serveru| Unexoected při zpracování žádosti| 









