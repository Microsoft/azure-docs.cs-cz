---
title: Kontrola fakturačních údajů předplatného Azure pomocí rozhraní REST API | Dokumentace Microsoftu
description: Zjistěte, jak použít rozhraní Azure REST API ke kontrole fakturační detaily předplatného.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: erikre
ms.openlocfilehash: a4e5307a151439dde5ac41cb5b1bbb80f43ad71c
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112746"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Zkontrolujte fakturace předplatného pomocí rozhraní REST API

Rozhraní API pro generování sestav pomoc Azure prohlížet a spravovat náklady na Azure.  

Pomáhají přizpůsobení výsledků podle svých potřeb.

Zde zjistíte, jak použít rozhraní REST API, který vrátí fakturační detaily předplatného pro daný rozsah.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>Žádost o sestavení  

`{subscriptionID}` Parametr je povinný a identifikuje cílové předplatné.

`{billingPeriod}` Parametr je povinný a určuje aktuální [fakturačního období](https://docs.microsoft.com/rest/api/billing/billingperiods/get#billingperiod).

`${startDate}` a `${endDate}` parametry jsou povinné pro účely tohoto příkladu, ale volitelný pro koncový bod.  Určí rozsah dat jako řetězce ve formátu RRRR-MM-DD (příklady: `'20180501'` a `'20180615'`). 

Vyžadují se následující hlavičky: 

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Typ obsahu:*|Povinná hodnota. Nastavte na `application/json`.|  
|*Autorizace:*|Povinná hodnota. Nastaven na platné `Bearer` [přístupový token](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |  

## <a name="response"></a>Odpověď  

Pro úspěšné odpovědi, který obsahuje seznam podrobné ceny pro váš účet se vrátí stavový kód 200 (OK).

``` json
{
  "value": [
    {
      "id": "/subscriptions/{$subscriptionID}/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/{$detailsID}",
      "name": "{$detailsID}",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}",
        "invoiceId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/invoices/${invoiceID}",
        "usageStart": "${startDate}}",
        "usageEnd": "${endDate}",
        "currency": "USD",
        "usageQuantity": ${usageQuantity},
        "billableQuantity": ${billableQuantity},
        "pretaxCost": ${cost},
        "meterId": "${meterID}",
        "meterDetails": ${meterDetails}
      }
    }
    ],
    "nextLink": "${nextLinkURL}"
} 
```  

Každá položka v **hodnotu** představuje podrobnosti týkající se používání služeb:

|Vlastnost Response.|Popis|
|----------------|----------|
|**subscriptionGuid** | Globálně jedinečné ID předplatného. | 
|**startDate** | Data použití spuštěna. |
|**endDate** | Datum ukončení používání. |
|**useageQuantity** | Množství. | 
|**billableQuantity** | Ve skutečnosti účtuje množství. |
|**pretaxCost** | Náklady před příslušné daně. | 
|**meterDetails** | Podrobné informace o použití. |
|**nextLink**| Při nastavení, určuje adresu URL pro další "stránky" podrobnosti. Prázdný, když je stránka poslední z nich. |  
||
  
Tento příklad je zkrácený; Zobrazit [vypsat podrobnosti o použití](https://docs.microsoft.com/rest/api/consumption/usagedetails/listbybillingperiod#usagedetailslistresult) úplný popis každého pole odpovědi. 

Jiné stavové kódy označují chybové stavy. V těchto případech objektu odpovědi vysvětluje, proč žádost selhala.

``` json
{  
  "error": [  
    { "code": "Error type." 
      "message": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="next-steps"></a>Další postup 
- Kontrola [Enterprise Přehled vytváření sestav](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Prozkoumat [Enterprise rozhraní REST API pro fakturaci](https://docs.microsoft.com/rest/api/billing/)   
- [Začínáme s Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
