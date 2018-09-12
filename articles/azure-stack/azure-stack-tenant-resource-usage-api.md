---
title: Rozhraní API využití prostředků tenanta | Dokumentace Microsoftu
description: Odkaz pro rozhraní API, využití prostředků, který načíst informace o použití služby Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: ab5dad550e590cd70f54ad5c8d4727d0f6370190
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379708"
---
# <a name="tenant-resource-usage-api"></a>Rozhraní API využití prostředků tenanta

Tenanta slouží k zobrazení dat o využití prostředků tenanta vlastní rozhraní API pro klienty. Toto rozhraní API je konzistentní s Azure Usage API (aktuálně v soukromém náhledu).

Můžete použít rutinu prostředí Windows PowerShell **Get-UsageAggregates** zobrazíte data o využití, jako jsou v Azure.

## <a name="api-call"></a>Volání rozhraní API
### <a name="request"></a>Žádost
Požadavek získá podrobnosti o využití pro požadované předplatné a pro požadovaný časový rámec. Není k dispozici není datová část požadavku.

| **– Metoda** | **Identifikátor URI žádosti** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version= 2015-06-01-preview & continuationToken = {hodnota tokenu} |

### <a name="arguments"></a>Argumenty
| **Argument** | **Popis** |
| --- | --- |
| *armendpoint* |Azure Resource Manageru koncový bod vašeho prostředí Azure Stack. Vytváření služby Azure Stack je, že název koncového bodu Azure Resource Manageru je ve formátu `https://management.{domain-name}`. Například pro development kit, název domény je local.azurestack.external, pak je koncový bod Resource Manageru `https://management.local.azurestack.external`. |
| *subId* |ID předplatného uživatele, který provádí volání. Toto rozhraní API pouze pro dotaz můžete použít pro použití v rámci jednoho předplatného. Poskytovatelé mohou použít rozhraní API využití prostředků poskytovatele za účelem využití dotazu pro všechny tenanty. |
| *reportedStartTime* |Spuštění dotazu. Hodnota pro *data a času* by měl být ve standardu UTC a na začátek hodiny, například 13:00. Pro denní agregace nastavte tuto hodnotu na půlnoc UTC. Formát je *uvozeny řídicími znaky* ISO 8601, například 2015-06-16T18 % 3a53 % 3a11 % 2b00 % 3a00Z, kde je dvojtečka převeden na % 3a a navíc je převeden na % 2b tak, aby se identifikátor URI popisný. |
| *reportedEndTime* |Konec dotazu. Omezení, které se vztahují *reportedStartTime* platí také pro tento argument. Hodnota pro *reportedEndTime* nemůže být v budoucnu. |
| *aggregationGranularity* |Volitelný parametr, který má dva jednotlivých možných hodnot: dnů a hodin. Jak navrhnout hodnoty jednoho vrací data v denní členitosti a druhý je hodinové řešení. Denní možnost je výchozí hodnota. |
| *api-version* |Verze protokolu, který se používá k této žádosti. Je nutné použít 2015-06-01-preview. |
| *continuationToken* |Načíst token z posledního volání rozhraní API využití zprostředkovatele. Tento token je nutné, když odpověď je větší než 1 000 řádků a funguje jako záložku v průběhu. Pokud není k dispozici, jsou data načtena od začátku dne nebo předaný hodinách, v závislosti na členitosti. |

### <a name="response"></a>Odpověď
Získejte /subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 a reportedEndTime = 2015-06-01T00 % 3a00 % 3a00 % 2b00 % 3a00 & aggregationGranularity = denně & verze api-version = 1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregate/sub1-meterID1",
"name": "sub1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

…
```

### <a name="response-details"></a>Podrobnosti o odpovědi
| **Argument** | **Popis** |
| --- | --- |
| *ID* |Jedinečné ID agregace využití |
| *Jméno* |Název agregace využití |
| *type* |Definice prostředků |
| *ID předplatného* |Identifikátor předplatného Azure uživatele |
| *usageStartTime* |Čas UTC počáteční čas využití kontejneru, do které patří tato agregace využití |
| *usageEndTime* |Čas ukončení UTC využití kontejneru, do které patří tato agregace využití |
| *instanceData* |Páry klíč hodnota Podrobnosti instance (v novém formátu):<br>  *resourceUri*: plně kvalifikované ID prostředku, včetně skupin prostředků a název instance <br>  *umístění*: oblast, ve kterém byla tato služba spuštěna <br>  *značky*: značky prostředku, které uživatel zadá <br>  *additionalInfo*: více podrobností o prostředku, který spotřebovával, například typ obrázku nebo verze operačního systému |
| *Množství* |Množství spotřeby prostředků, ke které došlo v tohoto časového rámce |
| *MeterId* |Jedinečné ID prostředku, který spotřebovával (také nazývané *ResourceID*) |


## <a name="next-steps"></a>Další postup
[Rozhraní API využití prostředků poskytovatele](azure-stack-provider-resource-api.md)

[Nejčastější dotazy souvisí s využitím](azure-stack-usage-related-faq.md)

