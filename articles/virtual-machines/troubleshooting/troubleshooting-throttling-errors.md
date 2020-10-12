---
title: Řešení chyb omezování v Azure | Microsoft Docs
description: Omezení chyb, opakování a omezení rychlosti ve službě Azure Compute.
services: virtual-machines
documentationcenter: ''
author: changov
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: changov
ms.reviewer: vashan, rajraj
ms.openlocfilehash: b1cc8a43423ecd33218948aaa001fc34877eac60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074283"
---
# <a name="troubleshooting-api-throttling-errors"></a>Řešení chyb způsobených omezováním rozhraní API 

Požadavky na výpočetní výkon Azure můžou být omezené na základě předplatného a pro jednotlivé oblasti, které vám pomůžou s celkovým výkonem služby. Zajišťujeme všechna volání poskytovatele prostředků Azure COMPUTE (CRP), který spravuje prostředky pod oborem názvů Microsoft. COMPUTE nepřekračuje maximální povolenou rychlost požadavků rozhraní API. Tento dokument popisuje omezování rozhraní API, podrobnosti o tom, jak řešit problémy s omezením, a osvědčené postupy, abyste se vyhnuli omezením.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Omezování pomocí Azure Resource Manager zprostředkovatelů prostředků vs  

Jako přední dveře do Azure Azure Resource Manager provádí ověřování a první pořadí ověřování a omezování všech příchozích požadavků rozhraní API. Omezení četnosti volání Azure Resource Manager a související hlavičky protokolu HTTP pro diagnostickou odezvu jsou popsány [zde](../../azure-resource-manager/management/request-limits-and-throttling.md).
 
Když klient rozhraní API Azure získá chybu omezování, stav HTTP je 429 příliš mnoho požadavků. Pokud chcete zjistit, jestli je omezování požadavků prováděné Azure Resource Manager nebo podkladovým poskytovatelem prostředků, jako je CRP, prozkoumejte žádosti o `x-ms-ratelimit-remaining-subscription-reads` získání požadavků a `x-ms-ratelimit-remaining-subscription-writes` odpovědí pro požadavky bez získání. Pokud se zbývající počet volání blíží 0, bylo dosaženo obecného limitu volání pro předplatné definovaného Azure Resource Manager. Aktivity všech klientů předplatného se počítají dohromady. V opačném případě omezování přichází z cílového poskytovatele prostředků (ten adresovaná `/providers/<RP>` segmentem adresy URL požadavku). 

## <a name="call-rate-informational-response-headers"></a>Hlavičky informativních odpovědí v kurzu volání 

| Záhlaví                            | Formát hodnoty                           | Příklad                               | Description                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-MS-ratelimit-zbývající – prostředek |```<source RP>/<policy or bucket>;<count>```| Microsoft. COMPUTE/HighCostGet3Min; 159 | Zbývající počet volání rozhraní API pro zásady omezování zahrnující kontejner prostředků nebo skupinu operací včetně cíle této žádosti                                                                   |
| x-MS-Request-poplatek               | ```<count>```                             | 1                                     | Počet volání, která se účtují za tento požadavek HTTP, směrem k příslušnému limitu zásad. Většinou se jedná o 1. Požadavky na dávky, například pro škálování sady škálování virtuálních počítačů, můžou účtovat víc počtů. |


Všimněte si, že požadavek rozhraní API může podléhat více zásadám omezování. `x-ms-ratelimit-remaining-resource`Pro každou zásadu bude existovat samostatná hlavička. 

Tady je ukázková odpověď na požadavek na odstranění sady škálování virtuálního počítače.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>Podrobnosti o chybě omezování

Stav HTTP 429 se běžně používá k zamítnutí požadavku, protože bylo dosaženo limitu frekvence volání. Typická odezva na chybu omezení od poskytovatele výpočetních prostředků bude vypadat jako v následujícím příkladu (zobrazují se pouze relevantní hlavičky):

```
HTTP/1.1 429 Too Many Requests
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet3Min;46
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet30Min;0
Retry-After: 1200
Content-Type: application/json; charset=utf-8
{
  "code": "OperationNotAllowed",
  "message": "The server rejected the request because too many requests have been received for this subscription.",
  "details": [
    {
      "code": "TooManyRequests",
      "target": "HighCostGet30Min",
      "message": "{\"operationGroup\":\"HighCostGet30Min\",\"startTime\":\"2018-06-29T19:54:21.0914017+00:00\",\"endTime\":\"2018-06-29T20:14:21.0914017+00:00\",\"allowedRequestCount\":800,\"measuredRequestCount\":1238}"
    }
  ]
}

```

Zásada s zbývajícím počtem volání 0 je ta, která je z důvodu, že se vrací chyba omezování. V tomto případě je to `HighCostGet30Min` . Celkový formát těla odpovědi je obecný formát chyby Azure Resource Manager rozhraní API (v souladu s OData). Hlavní kód chyby `OperationNotAllowed` je jeden zprostředkovatel výpočetních prostředků, který používá k nahlášení chyb omezení (mezi další typy chyb klienta). `message`Vlastnost vnitřních chyb obsahuje serializovanou strukturu JSON s podrobnostmi o porušení omezení.

Jak je uvedeno výše, každá chyba omezování zahrnuje `Retry-After` hlavičku, která poskytuje minimální dobu v sekundách, po kterou musí klient čekat, než bude požadavek opakovat. 

## <a name="api-call-rate-and-throttling-error-analyzer"></a>Frekvence volání rozhraní API a analyzátor chyb omezování
Pro rozhraní API poskytovatele výpočetních prostředků je k dispozici verze Preview funkce řešení potíží. Tyto rutiny PowerShellu poskytují statistiky o počtu požadavků rozhraní API na časový interval na operaci a porušení omezení pro skupinu operací (Policy):
-   [Export – AzLogAnalyticRequestRateByInterval](/powershell/module/az.compute/export-azloganalyticrequestratebyinterval)
-   [Export – AzLogAnalyticThrottledRequest](/powershell/module/az.compute/export-azloganalyticthrottledrequest)

Statistika volání rozhraní API může poskytovat skvělé informace o chování klientů předplatného a povolit snadnou identifikaci vzorů volání, které způsobují omezení.

Časový limit analyzátoru v době, kdy tento čas nepočítá požadavky na typy prostředků disku a snímku (v podpoře spravovaných disků). Vzhledem k tomu, že shromažďuje data z telemetrie CRP, nemůže také pomáhat při identifikaci chyb omezení od ARM. Tyto prvky je ale možné snadno identifikovat na základě hlaviček odpovědí rozlišujícího ARM, jak je popsáno výše.

Rutiny PowerShellu používají rozhraní REST API, které můžou klienti snadno volat (i když ještě nemáte oficiální podporu). Pokud chcete zobrazit formát požadavku HTTP, spusťte rutiny s přepínačem-Debug nebo Snoop na jejich spuštění pomocí Fiddler.


## <a name="best-practices"></a>Osvědčené postupy 

- Neprovádějte nepodmíněné chyby rozhraní API služby Azure Service API a okamžitě nebo hned znovu. Běžným výskytem je, aby se kód klienta dostal do rychlé smyčky opakování při výskytu chyby, která není znovu schopná. Opakované pokusy budou nakonec vyčerpat povolený limit volání pro skupinu cílových operací a ovlivnit ostatní klienty daného předplatného. 
- V případech automatizace rozhraní API ve velkém rozsahu zvažte implementaci proaktivní omezování na straně klienta, když počet dostupných volání pro skupinu cílových operací klesne pod určitou nízkou prahovou hodnotu. 
- Při sledování asynchronních operací respektuje Retry-After tipů hlaviček. 
- Pokud kód klienta potřebuje informace o konkrétním virtuálním počítači, Dotazujte ho přímo místo na výpis všech virtuálních počítačů v nadřazené skupině prostředků nebo na celé předplatné a pak na straně klienta vyberte potřebný virtuální počítač. 
- Pokud klientský kód potřebuje virtuální počítače, disky a snímky z konkrétního umístění Azure, místo dotazování na všechny virtuální počítače předplatného a filtrování podle umístění na straně klienta: `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` dotaz na výpočetní koncové body poskytovatele prostředků Compute. 
-   Když vytváříte nebo aktualizujete prostředky rozhraní API na základě těchto virtuálních počítačů a virtuálních počítačů, je mnohem efektivnější sledovat vrácenou asynchronní operaci do dokončení, než dotazování na samotné adrese URL prostředku (na základě `provisioningState` ).

## <a name="next-steps"></a>Další kroky

Další informace o pokynech pro opakování pro jiné služby v Azure najdete v [pokynech k opakování pro konkrétní služby](/azure/architecture/best-practices/retry-service-specific) .
