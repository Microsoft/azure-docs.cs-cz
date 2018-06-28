---
title: Rozhraní API pro využití prostředků klienta | Microsoft Docs
description: Referenční dokumentace pro využití prostředků rozhraní API, která načíst informace o využití protokolů Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: b9d7c7ee-e906-4978-92a3-a2c52df16c36
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 8472d8ce733c07641a7fa6d53aeb6909cd709990
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048386"
---
# <a name="tenant-resource-usage-api"></a>Využití prostředků rozhraní API klienta

Klienta můžete použít rozhraní API klienta k zobrazení dat o využití prostředků klienta vlastní. Toto rozhraní API je konzistentní s využití rozhraní API služby Azure (aktuálně v soukromém náhledu).

Můžete použít rutinu prostředí Windows PowerShell **Get-UsageAggregates** získat data o využití jako v Azure.

## <a name="api-call"></a>Volání rozhraní API
### <a name="request"></a>Žádost
Požadavek získá spotřeba podrobnosti pro požadované předplatné a pro požadovaný časový rámec. Neexistuje žádný text žádosti.

| **– Metoda** | **Identifikátor URI požadavku** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version= 2015-06-01-preview & continuationToken = {{hodnota tokenu} |

### <a name="arguments"></a>Argumenty
| **Argument** | **Popis** |
| --- | --- |
| *Armendpoint* |Azure Resource Manager koncový bod vašeho prostředí Azure zásobníku. Konvence zásobník Azure je, že název koncového bodu Azure Resource Manager je ve formátu `https://management.{domain-name}`. Například pro development kit je název domény local.azurestack.external a pak je koncový bod správce prostředků `https://management.local.azurestack.external`. |
| *subId* |ID předplatného uživatele, který je uskutečněním hovoru. Toto rozhraní API jenom pro dotaz můžete použít pro použití v rámci jednoho předplatného. Zprostředkovatelé můžete použít rozhraní API poskytovatele prostředků využití dotazu využití pro všechny klienty. |
| *reportedStartTime* |Spuštění dotazu. Hodnota *data a času* by měl být ve formátu UTC a na začátku hodinu, například 13:00. Pro denní agregace nastavte tuto hodnotu půlnoci času UTC. Formát je *uvozený* ISO 8601, například 2015-06-16T18 % 3a53 % 3a11 % 2b00 % 3a00Z, kde je k % 3a uvozené dvojtečkou a plus je uvozena k % 2b tak, aby se identifikátor URI popisný. |
| *reportedEndTime* |Koncový čas dotazu. Omezení, která se týkají *reportedStartTime* platí také pro tento argument. Hodnota *reportedEndTime* nemůže být v budoucnu. |
| *aggregationGranularity* |Volitelný parametr, který má dvě hodnoty diskrétní potenciální: dnů a hodin. Jako hodnoty naznačují, jeden vrátí data ve dnech, a druhá je hodinové řešení. Denní možnost je výchozí. |
| *api-version* |Verze protokolu, který slouží k vytvoření této žádosti. Je nutné použít 2015-06-01-preview. |
| *continuationToken* |Token načíst z posledního volání poskytovatele rozhraní API využití. Tento token je potřeba, když je větší než 1 000 řádků odpověď a funguje jako záložku v průběhu. Pokud není přítomný, od začátku den jsou načtena data nebo předaná hodinu, podle členitost. |

### <a name="response"></a>Odpověď
ZÍSKAT /subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 & reportedEndTime = 2015-06-01T00 % 3a00 % 3a00 % 2b00 % 3a00 & aggregationGranularity = denně & verze api-version = 1.0

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
| *ID* |Jedinečné ID využití agregace |
| *Jméno* |Název využití agregace |
| *type* |Definice prostředků |
| *ID předplatného* |Identifikátor předplatného Azure uživatele |
| *usageStartTime* |Počáteční čas využití sady, do které patří tato agregace využití UTC |
| *usageEndTime* |Koncový čas UTC využití sady, do které patří tato agregace využití |
| *instanceData* |Páry klíč hodnota podrobností instance (v novém formátu):<br>  *resourceUri*: plně kvalifikovaný ID prostředku, včetně skupiny prostředků a název instance <br>  *umístění*: oblast, ve které byla tato služba spuštěna <br>  *značky*: značky prostředku, které určuje uživatele <br>  *additionalInfo*: více podrobností o prostředku, který byl zpracován, například typ verzí nebo bitovou kopii operačního systému |
| *množství* |Množství spotřeby prostředků, které došlo v této časového rámce |
| *meterId* |Jedinečné ID pro prostředek, která se spotřebovala (také nazývané *ResourceID*) |


## <a name="next-steps"></a>Další postup
[Rozhraní API využití prostředků poskytovatele](azure-stack-provider-resource-api.md)

[Nejčastější dotazy souvisí s využitím](azure-stack-usage-related-faq.md)

