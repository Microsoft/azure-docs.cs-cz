---
title: Rozhraní API využití prostředků poskytovatele | Dokumentace Microsoftu
description: Referenční dokumentace pro rozhraní API, využití prostředků, který načte informace o použití služby Azure Stack
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
ms.date: 08/24/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: aedaa729ec51d7b60b2c242239935f7b3e41794f
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918183"
---
# <a name="provider-resource-usage-api"></a>Rozhraní API využití prostředků poskytovatele
Termín *poskytovatele* platí pro správce služeb a žádné delegované poskytovatele. Operátoři Azure stacku a delegované poskytovatele. můžete použít rozhraní API využití zprostředkovatele zobrazíte využití svým klientům s přímým přístupem. Například jak je znázorněno v diagramu, P0 může volat zprostředkovatele rozhraní API využití informace o společnosti P1 a P2 pro použití s přímým přístupem a P1 může volat pro informace o využití na P3 a P4.

![Konceptuální model hierarchie zprostředkovatele](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>Reference k volání rozhraní API
### <a name="request"></a>Žádost
Požadavek získá podrobnosti o využití pro požadované předplatné a pro požadovaný časový rámec. Není k dispozici není datová část požadavku.

Použití těchto rozhraní API je poskytovatel rozhraní API, aby volající musí být přiřazenou roli vlastník, Přispěvatel nebo Čtenář v předplatném poskytovatele.

| **– Metoda** | **Identifikátor URI žádosti** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity} & subscriberId = {sub1.1} & verzi api-version = 2015-06-01-preview & continuationToken = {hodnota tokenu} |

### <a name="arguments"></a>Argumenty
| **Argument** | **Popis** |
| --- | --- |
| *armendpoint* |Azure Resource Manageru koncový bod vašeho prostředí Azure Stack. Vytváření služby Azure Stack je, že název koncového bodu Azure Resource Manageru je ve formátu `https://adminmanagement.{domain-name}`. Například pro development kit, pokud je název domény *local.azurestack.external*, pak je koncový bod Resource Manageru `https://adminmanagement.local.azurestack.external`. |
| *subId* |ID předplatného uživatele, který provádí volání. |
| *reportedStartTime* |Spuštění dotazu. Hodnota pro *data a času* by měl být koordinovaný univerzální čas (UTC) a na začátek hodiny, například 13:00. Pro denní agregace nastavte tuto hodnotu na půlnoc UTC. Formát je *uvozeny řídicími znaky* ISO 8601. Například *2015-06-16T18 % 3a53 % 3a11 % 2b00 % 3a00Z*, kde dvojtečka je převeden na *% 3a* a není uvozeno uvozovacím znakem plus na *% 2b* tak, aby se identifikátor URI popisný. |
| *reportedEndTime* |Konec dotazu. Omezení, které se vztahují *reportedStartTime* platí také pro tento argument. Hodnota pro *reportedEndTime* nemůže být v budoucnosti nebo aktuální datum. Pokud se jedná, výsledek je nastaven "zpracování není úplný." |
| *aggregationGranularity* |Volitelný parametr, který má dva jednotlivých možných hodnot: dnů a hodin. Jak navrhnout hodnoty jednoho vrací data v denní členitosti a druhý je hodinové řešení. Denní možnost je výchozí hodnota. |
| *subscriberId* |ID předplatného. Pokud chcete získat filtrovaná data, je potřeba ID předplatného tenanta přímé poskytovatele. Pokud není zadán žádný parametr ID předplatného, volání vrátí data o využití pro všechny poskytovatele tenanty s přímým přístupem. |
| *api-version* |Verze protokolu, který se používá k této žádosti. Tato hodnota nastavená na *2015-06-01-preview*. |
| *continuationToken* |Načíst token z posledního volání rozhraní API využití zprostředkovatele. Tento token je nutné, když odpověď je větší než 1 000 řádků a funguje jako záložku pro průběh. Pokud token není k dispozici, jsou data načtena od začátku dne nebo předaný hodinách, v závislosti na členitosti. |

### <a name="response"></a>Odpověď
Získejte /subscriptions/sub1/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 a reportedEndTime = 2015-06-01T00 % 3a00 % 3a00 % 2b00 % 3a00 & aggregationGranularity = denní & subscriberId = sub1.1 & verzi api-version = 1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1.1/providers/Microsoft.Commerce.Admin/UsageAggregate/sub1.1-

meterID1",
"name": "sub1.1-meterID1",
"type": "Microsoft.Commerce.Admin/UsageAggregate",

"properties": {
"subscriptionId":"sub1.1",
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
| *ID* |Jedinečný Identifikátor využití agregace. |
| *Jméno* |Název agregace využití. |
| *type* |Definice prostředků. |
| *ID předplatného* |Identifikátor předplatného uživatele Azure stacku. |
| *usageStartTime* |Čas UTC počáteční čas využití kontejneru, do které patří tato agregace využití.|
| *usageEndTime* |Čas ukončení UTC využití kontejneru, do které patří tato agregace využití. |
| *instanceData* |Páry klíč hodnota Podrobnosti instance (v novém formátu):<br> *resourceUri*: plně kvalifikované ID prostředku, který obsahuje skupiny prostředků a název instance. <br> *umístění*: oblast, ve kterém byla tato služba spuštěna. <br> *značky*: značky prostředku, která jsou určena podle uživatele. <br> *additionalInfo*: více podrobností o prostředku, který spotřebovával, například typ obrázku nebo verze operačního systému. |
| *Množství* |Množství spotřeby prostředků, ke které došlo v tomto časovém rámci. |
| *MeterId* |Jedinečné ID prostředku, který spotřebovával (také nazývané *ResourceID*). |


## <a name="retrieve-usage-information"></a>Načíst informace o využití

### <a name="powershell"></a>PowerShell

Ke generování dat o využití, byste měli mít prostředky, které jsou spuštěné a aktivně pomocí systému, například, aktivnímu virtuálnímu počítači a účet úložiště obsahující data atd. Pokud si nejste jistí, jestli nějaké prostředky spuštěné v Azure stacku Marketplace, nasazení virtuálního počítače (VM) a ověřte, virtuální počítač monitorování okno a ujistěte se, že běží. Pro zobrazení dat o využití, použijte následující rutiny prostředí PowerShell:

1. [Instalace Powershellu pro Azure Stack.](azure-stack-powershell-install.md)
2. [Konfigurace uživatele služby Azure Stack](user/azure-stack-powershell-configure-user.md) nebo [operátory Azure stacku](azure-stack-powershell-configure-admin.md) prostředí PowerShell 
3. Pro načtení dat o využití, použijte [Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) rutinu Powershellu:
```powershell
Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
```
### <a name="rest-api"></a>REST API

Volání služby Microsoft.Commerce.Admin můžete shromažďovat informace o využití pro odstraněné předplatná. 

**Veškeré využití tenanta pro vrácení odstraněných pro aktivní uživatelé:**

| **– Metoda** | **Identifikátor URI žádosti** |
| --- | --- |
| GET | https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&api-version= 2015-06-01-preview |

**Vrátit využití pro odstraněné nebo aktivní tenanta:**

| **– Metoda** | **Identifikátor URI žádosti** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&subscriberId={ id předplatitele} & verzi api-version = 2015-06-01-preview |


## <a name="next-steps"></a>Další postup
[Využití prostředků tenanta reference k rozhraní API](azure-stack-tenant-resource-usage-api.md)

[Nejčastější dotazy souvisí s využitím](azure-stack-usage-related-faq.md)
