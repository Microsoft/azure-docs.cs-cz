---
title: Kontrola fakturačních dat předplatného Azure pomocí REST API | Microsoft Docs
description: Naučte se používat rozhraní Azure REST API ke kontrole podrobností fakturace předplatného.
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
ms.openlocfilehash: 8cfa429b18fb282f5c1f85d2fd1637704653b855
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443050"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Kontrola fakturace předplatného pomocí rozhraní REST API

Rozhraní API pro vytváření sestav Azure vám pomůžou zkontrolovat a spravovat náklady na Azure.

Filtry vám pomůžou přizpůsobit výsledky, aby vyhovovaly vašim potřebám.

Tady se naučíte, jak použít REST API k vrácení podrobností fakturace předplatného pro daný rozsah dat.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>Žádost o sestavení

`{subscriptionID}` Parametr je povinný a identifikuje cílové předplatné.

Parametr je povinný a určuje aktuální [fakturační období.](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) `{billingPeriod}`

V `${startDate}` tomto `${endDate}` příkladu jsou vyžadovány parametry a, ale volitelné pro koncový bod. Určují rozsah kalendářních dat jako řetězce ve formátu yyyy-MM-DD (příklady: `'20180501'` a `'20180615'`).

Jsou vyžadovány následující hlavičky:

|Hlavička žádosti|Popis|
|--------------------|-----------------|
|*Content-Type:*|Povinný parametr. Nastavte na `application/json`.|
|*Authorization:*|Povinný parametr. Nastavte na platný `Bearer` [přístupový token](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

## <a name="response"></a>Odpověď

Pro úspěšnou odpověď se vrátí stavový kód 200 (OK), který obsahuje seznam podrobných nákladů na váš účet.

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
        "usageQuantity": "${usageQuantity}",
        "billableQuantity": "${billableQuantity}",
        "pretaxCost": "${cost}",
        "meterId": "${meterID}",
        "meterDetails": "${meterDetails}"
      }
    }
  ],
  "nextLink": "${nextLinkURL}"
}
```

Každá položka v **hodnotě** představuje podrobnosti týkající se použití služby:

|Vlastnost Response|Popis|
|----------------|----------|
|**subscriptionGuid** | Globálně jedinečné ID pro předplatné |
|**startDate** | Datum, kdy bylo zahájeno používání. |
|**endDate** | Datum, kdy bylo dokončeno použití. |
|**useageQuantity** | Použité množství. |
|**billableQuantity** | Množství se ve skutečnosti účtuje. |
|**pretaxCost** | Fakturované náklady před platnými daněmi. |
|**meterDetails** | Podrobné informace o použití. |
|**nextLink**| Při nastavení určuje adresu URL pro další stránku podrobností. Prázdné, pokud je stránka poslední. |

Tento příklad je zkrácený; Úplný popis každého pole odpovědi najdete v části [Podrobnosti o využití seznamu](https://docs.microsoft.com/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod) .

Jiné stavové kódy označují chybové stavy. V těchto případech objekt odpovědi vysvětluje, proč se žádost nezdařila.

``` json
{
  "error": [
    {
      "code": "Error type.",
      "message": "Error response describing why the operation failed."
    }
  ]
}
```

## <a name="next-steps"></a>Další postup
- [Přehled nástroje Enterprise Reporting](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Prozkoumat [REST API fakturace v podniku](https://docs.microsoft.com/rest/api/billing/)
- [Začínáme s Azure REST API](https://docs.microsoft.com/rest/api/azure/)
