---
title: Projděte si fakturační data registrace do Azure Enterprise pomocí REST API | Microsoft Docs
description: Naučte se používat rozhraní Azure REST API ke kontrole fakturačních informací o registraci v podniku.
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
ms.author: banders
ms.openlocfilehash: 25d9b48696dc2a83ea0ba77c1be2c7aad7627fff
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443151"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Kontrola fakturace podnikového zápisu pomocí rozhraní REST API

Rozhraní API pro vytváření sestav Azure vám pomůžou zkontrolovat a spravovat náklady na Azure.

V tomto článku se dozvíte, jak načíst fakturační informace spojené s fakturačními účty, oddělení nebo účty pro zápis smluv Enterprise (EA) pomocí rozhraní Azure REST API. 

## <a name="individual-account-billing"></a>Fakturace jednotlivých účtů

Získání podrobností o využití účtů v oddělení:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{billingAccountId}` Parametr je povinný a měl by obsahovat ID účtu.

Jsou vyžadovány následující hlavičky: 

|Hlavička žádosti|Popis|  
|--------------------|-----------------|  
|*Content-Type:*|Povinný parametr. Nastavte na `application/json`.|  
|*Authorization:*|Povinný parametr. Nastavte na platný `Bearer` [klíč rozhraní API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Tento příklad ukazuje synchronní volání, které vrací podrobnosti pro aktuální fakturační cyklus. Z důvodu výkonu synchronní volání vrátí informace za poslední měsíc.  Můžete také volat [rozhraní API asynchronně](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) , aby vracelo data po dobu 36 měsíců.


## <a name="response"></a>Odpověď  

Pro úspěšnou odpověď se vrátí stavový kód 200 (OK), který obsahuje seznam podrobných nákladů na účet.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/BillingAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        ...
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        ...
      }
    }
  ]
}
```  

Tento příklad je zkrácený; Úplný popis každého pole odpovědi a zpracování chyb najdete v tématu [Získání podrobností o využití pro fakturační účet](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist) .

## <a name="department-billing"></a>Fakturace oddělení 

Získejte podrobnosti o využití agregované pro všechny účty v oddělení. 

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{departmentId}` Parametr je povinný a měl by obsahovat ID oddělení v registračním účtu.

Jsou vyžadovány následující hlavičky: 

|Hlavička žádosti|Popis|  
|--------------------|-----------------|  
|*Content-Type:*|Povinný parametr. Nastavte na `application/json`.|  
|*Authorization:*|Povinný parametr. Nastavte na platný `Bearer` [klíč rozhraní API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Tento příklad ukazuje synchronní volání, které vrací podrobnosti pro aktuální fakturační cyklus. Z důvodu výkonu synchronní volání vrátí informace za poslední měsíc.  Můžete také volat [rozhraní API asynchronně](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) , aby vracelo data po dobu 36 měsíců.

### <a name="response"></a>Odpověď  

Pro úspěšnou odpověď se vrátí stavový kód 200 (OK), který obsahuje seznam podrobných informací o využití a nákladů na dané fakturační období a ID faktury pro dané oddělení.


Následující příklad ukazuje výstup REST API pro oddělení `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```  

Tento příklad je zkrácený; Úplný popis každého pole odpovědi a zpracování chyb najdete v tématu [Získání podrobností o využití pro oddělení](/rest/api/consumption/usagedetails/list#departmentusagedetailslist) .

## <a name="enrollment-account-billing"></a>Fakturace účtu registrace

Získat podrobnosti o využití agregované pro registrační účet.

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{enrollmentAccountId}` Parametr je povinný a měl by obsahovat ID účtu pro zápis.

Jsou vyžadovány následující hlavičky: 

|Hlavička žádosti|Popis|  
|--------------------|-----------------|  
|*Content-Type:*|Povinný parametr. Nastavte na `application/json`.|  
|*Authorization:*|Povinný parametr. Nastavte na platný `Bearer` [klíč rozhraní API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Tento příklad ukazuje synchronní volání, které vrací podrobnosti pro aktuální fakturační cyklus. Z důvodu výkonu synchronní volání vrátí informace za poslední měsíc.  Můžete také volat [rozhraní API asynchronně](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) , aby vracelo data po dobu 36 měsíců.

### <a name="response"></a>Odpověď  

Pro úspěšnou odpověď se vrátí stavový kód 200 (OK), který obsahuje seznam podrobných informací o využití a nákladů na dané fakturační období a ID faktury pro dané oddělení.

Následující příklad ukazuje výstup REST API pro registraci `1234`v podniku.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        ....
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
``` 

Tento příklad je zkrácený; Úplný popis každého pole odpovědi a zpracování chyb najdete v tématu [Získání podrobností o využití pro registrační účet](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist) .

## <a name="next-steps"></a>Další kroky 
- [Přehled nástroje Enterprise Reporting](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Prozkoumat [REST API fakturace v podniku](https://docs.microsoft.com/rest/api/billing/)   
- [Začínáme s Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
