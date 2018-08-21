---
title: Zkontrolujte využití prostředků služby Azure pomocí rozhraní REST API | Dokumentace Microsoftu
description: Zjistěte, jak použít rozhraní Azure REST API ke kontrole využití prostředků služby Azure.
services: billing
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alleonar
ms.openlocfilehash: 1b7b1455413fb4886b317d468e6d278111c094b1
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "40225890"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Zkontrolujte využití prostředků Azure pomocí rozhraní REST API


Azure [Consumption API](https://docs.microsoft.com/rest/api/consumption/) nápovědy zkontrolujte data o využití a nákladů pro vaše prostředky Azure.

V tomto článku se dozvíte, jak načíst a agregovat informace o využití prostředků pro prostředky ve skupině prostředků Azure a jak pro filtrování výsledků na základě [značky Azure resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags).

## <a name="get-usage-for-a-resource-group"></a>Získání využití pro skupinu prostředků

Chcete-li získat využití prostředků pro výpočty, databáze a další prostředky ve skupině prostředků, použijte `usageDetails` operaci REST a filtrovat výsledky podle skupin prostředků.

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30&filter=properties/resourceGroup eq '{resource-group}]
Content-Type: application/json   
Authorization: Bearer
```

`{subscription-id}` Parametr je vyžadován a musí obsahovat ID předplatného, s přístupem k prostředku {resource-group} skupiny k roli Čtenář. 

Vyžadují se následující hlavičky: 

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Typ obsahu:*| Povinná hodnota. Nastavte na `application/json`. |  
|*Autorizace:*| Povinná hodnota. Nastaven na platné `Bearer` token. |

### <a name="response"></a>Odpověď  

Pro úspěšné odpovědi, který obsahuje seznam statistiky o využití pro každý prostředek Azure ve skupině prostředků s subscriptipon ID se vrátí stavový kód 200 (OK) `00000000-0000-0000-0000-000000000000`.

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        "meterId": "00000000-0000-0000-0000-000000000000",
        "partNumber": "Part Number 1",
        "resourceGuid": "00000000-0000-0000-0000-000000000000",
        "offerId": "Offer Id 1",
        "chargesBilledSeparately": true,
        "location": "EU West"
      }
    } ] }
```

## <a name="get-usage-for-tagged-resources"></a>Získat využití prostředků se značkou

Chcete-li získat využití prostředků pro prostředky v uspořádané podle značky, použijte `usageDetails` operaci REST a filtrovat výsledky pomocí názvu značky `$filter` parametr dotazu.

```http
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Consumption/usageDetails?$filter=tags eq 'tag1'&api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{subscription-id}` Parametr je vyžadován a musí obsahovat ID předplatného, které mají přístup k prostředkům příznakem.


### <a name="response"></a>Odpověď  

Pro úspěšné odpovědi, který obsahuje seznam statistiky o využití pro každý prostředek Azure ve skupině prostředků s subscriptipon ID se vrátí stavový kód 200 (OK) `00000000-0000-0000-0000-000000000000` a pár značek název služby key vault je `dev` a `tools`. 

Ukázkové odpovědi:

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "tags": {
        "dev": "tools"
      },
      "properties": {
        "billingPeriodId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        "meterId": "00000000-0000-0000-0000-000000000000",
        "partNumber": "Part Number 1",
        "resourceGuid": "00000000-0000-0000-0000-000000000000",
        "offerId": "Offer Id 1",
        "chargesBilledSeparately": true,
        "location": "EU West"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Další postup
- [Začínáme s Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
