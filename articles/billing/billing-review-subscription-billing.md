---
title: Zkontrolujte data fakturace předplatného Azure pomocí rozhraní REST API | Microsoft Docs
description: Naučte se používat rozhraní API REST služby Azure, chcete-li zkontrolovat podrobnosti fakturace předplatného.
services: billing
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: alleonar
ms.openlocfilehash: cc29d1f613af67604d50654be794cc90080098bb
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063786"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Zkontrolujte fakturace předplatného pomocí rozhraní REST API

Zkontrolujte a spravovat náklady na Azure Azure Nápověda rozhraní API pro vytváření sestav.  

Filtry pomáhají přizpůsobení výsledků podle svých potřeb.

Zde můžete naučit se používat rozhraní REST API vrátit podrobnosti fakturace předplatného pro dané časové období.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>Vytvoření žádosti  

`{subscriptionID}` Parametr je vyžadován a identifikuje cílové předplatné.

`{billingPeriod}` Parametr je vyžadován a určuje aktuální [fakturační období](https://docs.microsoft.com/rest/api/billing/billingperiods/get#billingperiod).

`${startDate}` a `${endDate}` parametry jsou požadovány pro tento příklad, ale volitelné pro koncový bod.  Určí rozsah dat jako řetězce ve formátu RRRR-MM-DD (příklady: `'20180501'` a `'20180615'`). 

Vyžadují se následující hlavičky: 

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Content-Type:*|Povinná hodnota. Nastavte na `application/json`.|  
|*Autorizace:*|Povinná hodnota. Nastavte na platnou `Bearer` [přístupový token](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |  

## <a name="response"></a>Odpověď  

Pro úspěšné odpovědi, který obsahuje seznam podrobné náklady pro váš účet je vrátit stavovým kódem 200 (OK).

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

Každá položka v **hodnota** představuje podrobnosti týkající se použití služby:

|Vlastnost Response.|Popis|
|----------------|----------|
|**subscriptionGuid** | Globálně jedinečné ID pro předplatné. | 
|**Počátečním** | Datum použití spuštěna. |
|**Koncové datum** | Datum použití skončila. |
|**useageQuantity** | Množství použít. | 
|**billableQuantity** | Množství ve skutečnosti účtují. |
|**pretaxCost** | Před příslušné daně náklady. | 
|**meterDetails** | Podrobné informace o použití. |
|**nextLink**| Pokud nastavíte, určuje adresu URL pro další "stránky" podrobnosti. Prázdné, pokud stránka je poslední. |  
||
  
V tomto příkladu je zkratka; v tématu [seznamu podrobnosti o použití](https://docs.microsoft.com/rest/api/consumption/usagedetails/listbybillingperiod#usagedetailslistresult) úplný popis každého pole odpovědi. 

Další stavové kódy označují chybový stav. V těchto případech objektu odpovědi vysvětluje, proč zpracování žádosti se nezdařilo.

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
- Zkontrolujte [Enterprise Přehled vytváření sestav](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Prozkoumat [Enterprise fakturace rozhraní REST API](https://docs.microsoft.com/rest/api/billing/)   
- [Začínáme s Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
