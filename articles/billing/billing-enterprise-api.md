---
title: Azure Enterprise API Billing | Dokumentace Microsoftu
description: Další informace o rozhraních API vytváření sestav, které umožňují zákazníkům Enterprise Azure k vyžádání spotřeba dat prostřednictvím kódu programu.
services: ''
documentationcenter: ''
author: mumami
manager: mumami
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: erikre
ms.openlocfilehash: d2021873b36a55bd36cf500eff168fe7311d944c
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53579426"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Přehled rozhraní API pro generování sestav pro podnikové zákazníky
Rozhraní API pro vytváření sestav umožňují zákazníkům Enterprise Azure prostřednictvím kódu programu o přijetí změn využití a fakturace data do nástrojů pro analýzu dat upřednostňované. Podnikoví zákazníci si zaregistrovali [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) s Azure a ujistěte se, vyjednávaný peněžní závazky a získat přístup k získání vlastních cen pro prostředky Azure.

## <a name="enabling-data-access-to-the-api"></a>Povolení přístupu k datům k rozhraní API
* **Vytvořit nebo načíst klíč rozhraní API** – připojte se k portálu Enterprise portal a přechod na sestavy > stáhnout využití > přístupový klíč rozhraní API pro generování nebo načíst klíč rozhraní API.
* **Předáním klíče rozhraní API** – klíč rozhraní API musí být předán pro každé volání pro ověřování a autorizaci. Následující vlastnost musí být s hlavičkami protokolu HTTP

|Klíč hlavičky požadavku | Hodnota|
|-|-|
|Autorizace| Zadejte hodnotu v tomto formátu: **nosiče {klíč rozhraní API}** <br/> Příklad: nosiče eyr... 09| 

## <a name="consumption-apis"></a>Využití rozhraní API
Koncový bod Swaggeru je k dispozici [tady](https://consumption.azure.com/swagger/ui/index) pro rozhraní API je popsáno níže, které by měly umožnit snadné introspekci rozhraní API a možnost k vygenerování klientských sad SDK, pomocí [AutoRest](https://github.com/Azure/AutoRest) nebo [Swagger CodeGen](http://swagger.io/swagger-codegen/). Data od 1. května 2014 je k dispozici prostřednictvím tohoto rozhraní API. 

* **Zůstatek a Souhrn** – [zůstatek a souhrn rozhraní API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) nabízí měsíční souhrnné informace o zůstatcích, nové nákupy, poplatků za služby Azure Marketplace, úpravy a poplatky za Nadlimitní využití.

* **Podrobnosti o použití** – [podrobnosti o použití rozhraní API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) nabízí denní přehled spotřebované množství a odhadované poplatky registraci. Výsledek obsahuje také informace o instancích, měřičů a oddělení. Rozhraní API může být dotazována fakturační období nebo zadaný počáteční a koncové datum. 

* **Poplatek za Marketplace Store** – [API poplatky webu Marketplace Store](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) vrátí rozpis podle využití webu marketplace poplatky za den pro zadaný fakturační období nebo počáteční a koncové datum (jednou poplatky nejsou zahrnuty).

* **Ceník** – [cena list API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) poskytuje příslušný míru pro každého měřiče pro danou registrace a fakturační období. 

## <a name="data-freshness"></a>Aktuálnost dat
V odpovědi všechna rozhraní API výše vrátí se značek ETag. Změna v Etag označuje, že se že data aktualizovala.  V následných voláních se stejným rozhraním API pomocí stejné parametry předejte zachycené Etag klíčem "If-None-Match" v záhlaví požadavku http. Stavový kód odpovědi by "NotModified", pokud data nebyla obnovena dalšího a nevrátí se žádná data. Rozhraní API vrátí úplnou datovou sadu pro požadované období pokaždé, když dojde změně značky etag.

## <a name="helper-apis"></a>Pomocná rozhraní API
 **Seznam fakturační období** – [rozhraní API pro fakturaci období](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) vrátí seznam hodnot fakturačních obdobích, která, které mají data o spotřebě pro registraci zadané v chronologickém pořadí reverzní. Každé období obsahuje vlastnost odkazuje na trasu rozhraní API pro čtyři sady dat – BalanceSummary, UsageDetails, poplatky webu Marketplace a ceníku.


## <a name="api-response-codes"></a>Kódy odpovědí rozhraní API   
|Stavový kód odpovědi|Zpráva|Popis|
|-|-|-|
|200| OK|Bez chyby|
|401| Neautorizováno| Klíč rozhraní API nebyl nalezen, není platný, vypršela platnost atd.|
|404| Není dostupný| Koncový bod sestavy nebyl nalezen|
|400| Chybný požadavek| Neplatné parametry – rozsahy kalendářních dat, EA čísel atd.|
|500| Chyba serveru| Unexoected při zpracování požadavků| 









