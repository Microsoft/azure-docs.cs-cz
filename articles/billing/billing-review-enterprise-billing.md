---
title: Kontrola fakturačních dat smlouvy Azure Enterprise pomocí rozhraní REST API | Microsoft Docs
description: Zjistěte, jak pomocí rozhraní Azure REST API zkontrolovat fakturační údaje smlouvy Enterprise.
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
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 47668169841cc2b180995808349487cefed02391
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754306"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Kontrola fakturace smlouvy Enterprise pomocí rozhraní REST API

Rozhraní API pro vytváření sestav Azure vám pomůžou zkontrolovat a spravovat náklady na Azure.

V tomto článku zjistíte, jak pomocí rozhraní Azure REST API načíst fakturační údaje související s fakturačními účty, odděleními nebo účty se smlouvou Enterprise (EA). 

## <a name="individual-account-billing"></a>Fakturace individuálního účtu

Získání podrobností o využití účtů v rámci oddělení:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Parametr `{billingAccountId}` je povinný a měl by obsahovat ID účtu.

Jsou vyžadovány následující hlavičky: 

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Content-Type:*|Povinná hodnota. Nastavte na `application/json`.|  
|*Authorization:*|Povinná hodnota. Nastavte na platný [klíč rozhraní API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) `Bearer`. |  

Tento příklad ukazuje synchronní volání, které vrátí podrobnosti pro aktuální fakturační období. Z důvodu zajištění lepšího výkonu synchronní volání vrací informace za poslední měsíc.  Můžete využít také [asynchronní volání rozhraní API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based), které vrátí data za 36 měsíců.


## <a name="response"></a>Odpověď  

V případě úspěšné odpovědi se vrátí stavový kód 200 (OK), který obsahuje seznam podrobných nákladů pro daný účet.

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

Tento příklad je zkrácený, úplný popis jednotlivých polí odpovědi a způsobu zpracování chyb najdete v části věnované [získání podrobností o využití pro fakturační účet](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist-legacy).

## <a name="department-billing"></a>Fakturace oddělení 

Získejte agregované podrobnosti o využití všech účtů v rámci oddělení. 

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Parametr `{departmentId}` je povinný a měl by obsahovat ID oddělení v registračním účtu.

Jsou vyžadovány následující hlavičky: 

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Content-Type:*|Povinná hodnota. Nastavte na `application/json`.|  
|*Authorization:*|Povinná hodnota. Nastavte na platný [klíč rozhraní API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) `Bearer`. |  

Tento příklad ukazuje synchronní volání, které vrátí podrobnosti pro aktuální fakturační období. Z důvodu zajištění lepšího výkonu synchronní volání vrací informace za poslední měsíc.  Můžete využít také [asynchronní volání rozhraní API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based), které vrátí data za 36 měsíců.

### <a name="response"></a>Odpověď  

V případě úspěšné odpovědi se vrátí stavový kód 200 (OK), který obsahuje seznam podrobných informací o využití a nákladů pro dané fakturační období a ID faktury daného oddělení.


Následující příklad ukazuje výstup rozhraní REST API pro oddělení `1234`.

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

Tento příklad je zkrácený, úplný popis jednotlivých polí odpovědi a způsobu zpracování chyb najdete v části věnované [získání podrobností o využití pro oddělení](/rest/api/consumption/usagedetails/list#departmentusagedetailslist-legacy).

## <a name="enrollment-account-billing"></a>Fakturace registračního účtu

Získejte agregované podrobnosti o využití registračního účtu.

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Parametr `{enrollmentAccountId}` je povinný a měl by obsahovat ID registračního účtu.

Jsou vyžadovány následující hlavičky: 

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Content-Type:*|Povinná hodnota. Nastavte na `application/json`.|  
|*Authorization:*|Povinná hodnota. Nastavte na platný [klíč rozhraní API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) `Bearer`. |  

Tento příklad ukazuje synchronní volání, které vrátí podrobnosti pro aktuální fakturační období. Z důvodu zajištění lepšího výkonu synchronní volání vrací informace za poslední měsíc.  Můžete využít také [asynchronní volání rozhraní API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based), které vrátí data za 36 měsíců.

### <a name="response"></a>Odpověď  

V případě úspěšné odpovědi se vrátí stavový kód 200 (OK), který obsahuje seznam podrobných informací o využití a nákladů pro dané fakturační období a ID faktury daného oddělení.

Následující příklad ukazuje výstup rozhraní REST API pro smlouvu Enterprise `1234`.

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

Tento příklad je zkrácený, úplný popis jednotlivých polí odpovědi a způsobu zpracování chyb najdete v části věnované [získání podrobností o využití pro registrační účet](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist-legacy).

## <a name="next-steps"></a>Další kroky 
- Projděte si [Přehled služby Enterprise Reporting](https://docs.microsoft.com/azure/billing/billing-enterprise-api).
- Prozkoumejte [Rozhraní REST API podnikové fakturace](https://docs.microsoft.com/rest/api/billing/).   
- [Začínáme s Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
