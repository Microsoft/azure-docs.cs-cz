---
title: Fakturační podniková rozhraní API Azure | Microsoft Docs
description: Přečtěte si o rozhraních API pro vytváření sestav, která zákazníkům podnikového Azure umožní získat data spotřeby prostřednictvím kódu programu.
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
ms.author: banders
ms.openlocfilehash: f706ad86493981d5b38248ec209a7c8b936f6817
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443214"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Přehled rozhraní API pro vytváření sestav pro podnikové zákazníky
Rozhraní API pro vytváření sestav umožňují zákazníkům Enterprise Azure programově získat spotřebu a fakturační data do preferovaných nástrojů pro analýzu dat. Podnikoví zákazníci si zaregistrovali [smlouva Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) s Azure, aby uzavřeli sjednané peněžní závazky a získali přístup k vlastním cenám pro prostředky Azure.

## <a name="enabling-data-access-to-the-api"></a>Povolení přístupu k datům do rozhraní API
* **Vygenerujte nebo načtěte klíč rozhraní API** – Přihlaste se k portálu Enterprise a přejděte k sestavám > Stáhněte si klíč rozhraní API > přístupového klíče k rozhraní API a vygenerujte nebo načtěte ho.
* **Předávání klíčů v rozhraní API** – klíč rozhraní API je potřeba předat pro každé volání ověřování a autorizaci. Následující vlastnost musí být do hlaviček protokolu HTTP.

|Klíč hlavičky požadavku | Value|
|-|-|
|Authorization| Zadejte hodnotu v tomto formátu: **nosič {API_KEY}** . <br/> Příklad: nosič Eyr.... karet| 

## <a name="consumption-apis"></a>Rozhraní API pro spotřebu
Koncový bod Swagger je k [dispozici pro](https://consumption.azure.com/swagger/ui/index) rozhraní API popsaná níže, která by měla umožňovat snadné introspekce rozhraní API a schopnost generovat klientské sady SDK pomocí funkce AutoRest nebo [Swagger CodeGen](https://swagger.io/swagger-codegen/). [](https://github.com/Azure/AutoRest) Data od 1. května 2014 jsou k dispozici prostřednictvím tohoto rozhraní API. 

* **Vyvážení a shrnutí** – [Saldo a souhrnné rozhraní API](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) nabízí Měsíční souhrn informací o zůstatcích, nových nákupech, Azure Marketplace poplatků za služby, adjustacích a překročení poplatků.

* **Podrobnosti o využití** – [rozhraní API pro podrobné](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) informace o využití nabízí denní rozpis spotřebovaných množství a odhadované poplatky za registraci. Výsledek také obsahuje informace o instancích, měřičích a odděleních. Na rozhraní API se dá dotazovat podle fakturačního období nebo podle zadaného počátečního a koncového data. 

* **Poplatek za Store** na webu Marketplace – [API pro poplatky za Store na webu Marketplace](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) vrací rozpis poplatků za službu Marketplace na základě využití po dnech pro zadané nebo počáteční a koncové datum (jednorázové poplatky nejsou zahrnuté).

* **Ceník** – [rozhraní API ceníku](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) poskytuje platnou sazbu pro každý měřič pro danou registraci a fakturační období.

* **Podrobnosti rezervované instance** – [rozhraní API pro využití rezervovaných](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) instancí vrátí použití rezervovaných instancí. [Rozhraní API pro poplatky za rezervované instance](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) ukazuje provedené fakturační transakce. 

## <a name="data-freshness"></a>Aktuálnost dat
V reakci na výše uvedené rozhraní API se vrátí ETag. Změna ve ETag indikuje, že se data aktualizovala.  V následných voláních stejného rozhraní API pomocí stejných parametrů předejte zachycenou značku ETag s klíčem "If-None-Match" v hlavičce požadavku HTTP. Stavový kód odpovědi by byl "NotModified", pokud data nebyla aktualizována ještě dále a nebudou vráceny žádná data. Rozhraní API vrátí úplnou datovou sadu pro požadované období vždy, když dojde ke změně ETag.

## <a name="helper-apis"></a>Pomocná rozhraní API
 **Výpis fakturačních období** – [rozhraní API fakturačních období](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) vrací seznam fakturačních období, která mají data spotřeby pro zadaný zápis v opačném pořadí. Každé období obsahuje vlastnost ukazující na trasu rozhraní API pro čtyři sady dat – BalanceSummary, UsageDetails, poplatky za tržišti a ceník.


## <a name="api-response-codes"></a>Kódy odpovědí rozhraní API   
|Stavový kód odpovědi|Message|Popis|
|-|-|-|
|200| OK|Bez chyby|
|401| Neautorizováno| Klíč rozhraní API se nenašel, je neplatný, vypršela platnost atd.|
|404| Není dostupný| Koncový bod sestavy nebyl nalezen.|
|400| Chybný požadavek| Neplatné parametry – rozsahy dat, čísla EA atd.|
|500| Chyba serveru| Neočekávaná chyba při zpracování žádosti| 









