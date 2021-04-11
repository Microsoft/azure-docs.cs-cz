---
title: Kontrola fakturačních dat předplatného Azure s využitím rozhraní REST API
description: Naučte se používat rozhraní Azure REST API ke kontrole podrobných údajů fakturace předplatného. Výsledky si můžete přizpůsobit s využitím filtrů.
author: lleonard-msft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: article
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: f23beeb735c83cdf2d42a01ff684510dbd0d9feb
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058829"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Kontrola fakturace předplatného pomocí rozhraní REST API

Rozhraní API pro vytváření sestav Azure vám pomůžou zkontrolovat a spravovat náklady na Azure.

Filtry pomůžou přizpůsobit výsledky, aby vyhovovaly vašim potřebám.

Tady se naučíte, jak používat rozhraní REST API k vrácení podrobných údajů fakturace předplatného pro daný rozsah dat.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>Sestavení požadavku

Parametr `{subscriptionID}` je povinný a identifikuje cílové předplatné.

Parametr `{billingPeriod}` je povinný a určuje aktuální [fakturační období](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods).

Parametry `${startDate}` a `${endDate}` jsou v tomto příkladu povinné, ale pro koncový bod volitelné. Určují rozsah kalendářních dat jako řetězce ve formátu YYYY-MM-DD (příklady: `'20180501'` a `'20180615'`).

Jsou vyžadovány následující hlavičky:

|Hlavička požadavku|Popis|
|--------------------|-----------------|
|*Content-Type:*|Povinná hodnota. Nastavte na `application/json`.|
|*Authorization:*|Povinná hodnota. Nastavte na platný [přístupový token](/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer`. |

## <a name="response"></a>Odpověď

V případě úspěšné odpovědi se vrátí stavový kód 200 (OK), který obsahuje seznam podrobných nákladů pro váš účet.

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

Každá položka v **hodnotě** představuje podrobné informace o použití služby:

|Vlastnost odpovědi|Popis|
|----------------|----------|
|**subscriptionGuid** | Globálně jedinečné ID pro předplatné. |
|**startDate** | Datum, kdy bylo používání zahájeno. |
|**endDate** | Datum, kdy bylo používání ukončeno. |
|**useageQuantity** | Použité množství. |
|**billableQuantity** | Aktuálně účtované množství. |
|**pretaxCost** | Fakturované náklady před uplatněním daní. |
|**meterDetails** | Podrobné informace o použití. |
|**nextLink**| Při nastavení určuje adresu URL další stránky podrobností. Prázdné, pokud je stránka poslední. |

Tento příklad je zkrácený; úplný popis každého pole odpovědi najdete v části věnované [výpisu podrobností o využití](/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod-legacy).

Jiné stavové kódy označují chybové stavy. V těchto případech objekt odpovědi vysvětluje, proč se požadavek nezdařil.

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

## <a name="next-steps"></a>Další kroky
- Projděte si [Přehled služby Enterprise Reporting](./enterprise-api.md).
- Prozkoumejte [Rozhraní REST API podnikové fakturace](/rest/api/billing/).
- [Začínáme s Azure REST API](/rest/api/azure/)