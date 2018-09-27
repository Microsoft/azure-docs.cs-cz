---
title: Kontrola Azure podnikového zápisu dat pomocí rozhraní REST API pro fakturaci | Dokumentace Microsoftu
description: Zjistěte, jak použít rozhraní Azure REST API ke kontrole podnikové registrace fakturační údaje.
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
ms.author: erikre
ms.openlocfilehash: f4b5fccefb08597a13fcc91eda11bbf3eb122914
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394841"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Projděte si podnikové registrace fakturace pomocí rozhraní REST API

Rozhraní API pro generování sestav pomoc Azure prohlížet a spravovat náklady na Azure.

V tomto článku zjistíte, jak získat fakturační údaje související s fakturační účty, oddělení nebo enterprtise smlouvy (EA) registračních účtů pomocí rozhraní REST API služby Azure. 

## <a name="individual-account-billing"></a>Fakturace individuálního účtu

Pokud chcete získat podrobnosti o použití účtů v oddělení:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{billingAccountId}` Parametr je vyžadován a musí obsahovat ID účtu.

Vyžadují se následující hlavičky: 

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Typ obsahu:*|Povinná hodnota. Nastavte na `application/json`.|  
|*Autorizace:*|Povinná hodnota. Nastaven na platné `Bearer` [klíč rozhraní API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Tento příklad ukazuje synchronní volání, které vrátí podrobnosti pro aktuálního fakturačního cyklu. Synchronní volání z důvodů výkonu nevrátil informace pro poslední měsíc.  Můžete také volat [API asynchronně](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) vrátit data na 36 měsíců.


## <a name="response"></a>Odpověď  

Pro úspěšné odpovědi, který obsahuje seznam podrobné ceny pro tento účet se vrátí stavový kód 200 (OK).

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

Tento příklad je zkrácený; Zobrazit [získat podrobnosti o využití pro fakturační účet](/rest/api/consumption/usagedetails/listbybillingaccount) úplný popis každého pole odpovědi a zpracování chyb.

## <a name="department-billing"></a>Oddělení fakturace 

Získáte podrobnosti o použití agregované pro všechny účty v oddělení. 

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{departmentId}` Parametr je vyžadován a musí obsahovat ID pro oddělení v rámci registrace účtu.

Vyžadují se následující hlavičky: 

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Typ obsahu:*|Povinná hodnota. Nastavte na `application/json`.|  
|*Autorizace:*|Povinná hodnota. Nastaven na platné `Bearer` [klíč rozhraní API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Tento příklad ukazuje synchronní volání, které vrátí podrobnosti pro aktuálního fakturačního cyklu. Synchronní volání z důvodů výkonu nevrátil informace pro poslední měsíc.  Můžete také volat [API asynchronně](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) vrátit data na 36 měsíců.

### <a name="response"></a>Odpověď  

Pro úspěšné odpovědi, která obsahuje seznam informace podrobné informace o využití a nákladů pro daného fakturačního období a faktury ID pro oddělení se vrátí stavový kód 200 (OK).


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

Tento příklad je zkrácený; Zobrazit [získat podrobnosti o využití pro oddělení](/rest/api/consumption/usagedetails/listbydepartment) úplný popis každého pole odpovědi a zpracování chyb.

## <a name="enrollment-account-billing"></a>Fakturace účtu registrace

Získáte podrobnosti o použití zobrazují se pro účet pro zápis.

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{enrollmentAccountId}` Parametr je vyžadován a musí obsahovat ID pro účet pro zápis.

Vyžadují se následující hlavičky: 

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Typ obsahu:*|Povinná hodnota. Nastavte na `application/json`.|  
|*Autorizace:*|Povinná hodnota. Nastaven na platné `Bearer` [klíč rozhraní API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Tento příklad ukazuje synchronní volání, které vrátí podrobnosti pro aktuálního fakturačního cyklu. Synchronní volání z důvodů výkonu nevrátil informace pro poslední měsíc.  Můžete také volat [API asynchronně](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) vrátit data na 36 měsíců.

### <a name="response"></a>Odpověď  

Pro úspěšné odpovědi, která obsahuje seznam informace podrobné informace o využití a nákladů pro daného fakturačního období a faktury ID pro oddělení se vrátí stavový kód 200 (OK).

Následující příklad ukazuje výstup rozhraní REST API pro podnikového zápisu `1234`.

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

Tento příklad je zkrácený; Zobrazit [získat podrobnosti o využití pro účet pro zápis](/rest/api/consumption/usagedetails/listbyenrollmentaccount) úplný popis každého pole odpovědi a zpracování chyb.

## <a name="next-steps"></a>Další postup 
- Kontrola [Enterprise Přehled vytváření sestav](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Prozkoumat [Enterprise rozhraní REST API pro fakturaci](https://docs.microsoft.com/rest/api/billing/)   
- [Začínáme s Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
