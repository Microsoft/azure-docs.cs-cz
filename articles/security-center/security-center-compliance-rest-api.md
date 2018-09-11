---
title: Sledování dodržování předpisů Azure Security Center pomocí rozhraní REST API | Dokumentace Microsoftu
description: Zobrazit výsledky kontroly dodržování předpisů automatizované pomocí REST API služby Azure Security Center.
services: security-center
documentationcenter: na
author: rloutlaw
manager: angerobe
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rloutlaw
ms.openlocfilehash: 3f07928897df01f5d654fa6a6bffce14290b24e4
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44295306"
---
# <a name="review-security-center-compliance-results-using-the-azure-rest-apis"></a>Zobrazení výsledků dodržování předpisů Security Center pomocí rozhraní Azure REST API

V tomto článku zjistíte, jak načíst informace o dodržování předpisů zabezpečení pro seznam předplatných, pomocí rozhraní REST API služby Azure.

## <a name="review-compliance-for-each-subscription"></a>Kontrola dodržování předpisů pro každé předplatné

Následujícím příkladu získá informace o zabezpečení pro vyhodnocení elastických pro danou dodržování předpisů a předplatné [získat dodržování předpisů](/rest/api/securitycenter/compliances/get) operace.

```http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/compliances/{complianceName}?api-version=2017-08-01-preview
```

`{complianceName}` Parametr je povinný a měl by obsahovat název posouzené dodržování předpisů pro `{subscription-id}`. Výstup bude obsahovat výsledky posouzení, jako například:

```json
{
...
  "properties": {
    "assessmentResult": [
      {
        "segmentType": "Compliant",
        "percentage": 77.777777777777786
      }
    ],
    "resourceCount": 18,
    "assessmentTimestampUtcDate": "2018-01-01T00:00:00Z"
  }
}
```

## <a name="review-compliance-for-multiple-subscriptions"></a>Kontrola dodržování předpisů pro více předplatných

K získání dat pro více předplatných, proveďte následující volání nejdřív Získejte seznam předplatných pomocí [do seznamu](/rest/api/resources/subscriptions/list) operace. Výše uvedené invoke [získat dodržování předpisů](/rest/api/securitycenter/compliances/get) pro každý vrácený ID předplatného.

Volání rozhraní API je:

```http
GET https://management.azure.com/subscriptions?api-version=2016-06-01
```

Která vrací pole s hodnotami, jako je následující. Použijte subscriptionId hodnoty volání nahoře můžete zkontrolovat informace o dodržování předpisů pro všechna předplatná.

```json
"value": [
    {
      "id": "/subscriptions/{subscription-id}",
      "subscriptionId": "{subscription-id}",
      "displayName": "Demo subscription",
      ...
    }
```






