---
title: Řešení potíží s chybami omezení v Azure | Dokumenty společnosti Microsoft
description: Omezení chyb, opakování a backoff v Azure Compute.
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
ms.openlocfilehash: f5fbd80fc9a8e519cf8f49ab16d7e747c6a8171b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045359"
---
# <a name="troubleshooting-api-throttling-errors"></a>Řešení chyb způsobených omezováním rozhraní API 

Požadavky na výpočetní prostředky Azure mohou být omezeny na základě předplatného a na základě oblasti, které vám pomohou s celkovým výkonem služby. Zajistíme, aby všechna volání zprostředkovatele výpočetních prostředků Azure (CRP), který spravuje prostředky v rámci oboru názvů Microsoft.Compute, nepřekročila maximální povolenou míru požadavků rozhraní API. Tento dokument popisuje omezení rozhraní API, podrobnosti o řešení problémů omezení a doporučené postupy, aby se zabránilo omezení.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Omezení pomocí Správce prostředků Azure vs poskytovatelé prostředků  

Jako přední dveře k Azure, Azure Resource Manager provádí ověřování a ověřování prvního řádu a omezení všech příchozích požadavků rozhraní API. Zde jsou [popsány](https://docs.microsoft.com/azure/azure-resource-manager/management/request-limits-and-throttling)limity rychlosti volání Azure Resource Manager a související hlavičky HTTP diagnostiky .
 
Když klient rozhraní API Azure získá chybu omezení, stav HTTP je 429 příliš mnoho požadavků. Chcete-li pochopit, pokud omezení požadavku provádí Správce prostředků Azure nebo základní `x-ms-ratelimit-remaining-subscription-reads` poskytovatel prostředků, `x-ms-ratelimit-remaining-subscription-writes` jako je CRP, zkontrolujte pro požadavky GET a odpovědi pro požadavky bez GET. Pokud se počet zbývajících volání blíží 0, bylo dosaženo obecného limitu volání předplatného definovaného službou Azure Resource Manager. Aktivity všech klientů předplatného se počítají společně. V opačném případě omezení pochází od cílového poskytovatele prostředků (ten, který je určen `/providers/<RP>` segmentem url požadavku). 

## <a name="call-rate-informational-response-headers"></a>Hlavičky odpovědí informační sazby volání 

| Hlavička                            | Formát hodnoty                           | Příklad                               | Popis                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-ms-ratelimit-remaining-resource |```<source RP>/<policy or bucket>;<count>```| Microsoft.Compute/HighCostGet3Min;159 | Počet volání zbývajícírozhraní ROZHRANÍ API pro zásady omezení pokrývající kontejner prostředků nebo skupinu operací včetně cíle tohoto požadavku                                                                   |
| x-ms-request-charge               | ```<count>```                             | 1                                     | Počet volání počítá "účtovány" pro tento požadavek HTTP na limit příslušné zásady. To je nejvíce typicky 1. Dávkové požadavky, například pro škálování škálovací sady virtuálních strojů, můžete účtovat více počítá. |


Všimněte si, že požadavek rozhraní API může být podroben více zásad omezení. Pro každou zásadu bude existovat samostatná `x-ms-ratelimit-remaining-resource` hlavička. 

Zde je ukázková odpověď na odstranění požadavku na škálovací sadu virtuálních strojů.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>Podrobnosti chyby omezení

Stav 429 HTTP se běžně používá k odmítnutí požadavku, protože je dosaženo limitu rychlosti volání. Typická odpověď na chybu omezení od zprostředkovatele výpočetních prostředků bude vypadat jako následující příklad (zobrazí se pouze příslušná záhlaví):

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

Zásada se zbývajícím počtem volání 0 je ta, kvůli které je vrácena chyba omezení. V tomto případě, který je `HighCostGet30Min`. Celkový formát těla odezvy je obecný formát chybrozhraní API Azure Resource Manager (conformant s OData). Hlavní kód chyby , `OperationNotAllowed`je jeden zprostředkovatel výpočetních prostředků používá k hlášení chyb omezení (mimo jiné typy chyb klienta). Vlastnost `message` vnitřní error (s) obsahuje serializované JSON struktury s podrobnostmi omezení porušení.

Jak je znázorněno výše, každá `Retry-After` chyba omezení zahrnuje záhlaví, které poskytuje minimální počet sekund, které by měl klient čekat před opakováním požadavku. 

## <a name="api-call-rate-and-throttling-error-analyzer"></a>Analyzátor chyb volání rozhraní API a omezení
Pro rozhraní API poskytovatele výpočetních prostředků je k dispozici verze preview funkce řešení potíží. Tyto rutiny prostředí PowerShell poskytují statistické údaje o míře požadavků rozhraní API za časový interval na operaci a omezení porušení na operaci skupiny (zásady):
-   [Export-AzLogAnalyticRequestRateByInterval](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticrequestratebyinterval)
-   [Export-AzLogAnalyticŠkrtdRequest](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticthrottledrequest)

Statistiky volání rozhraní API může poskytnout velký přehled o chování klienta předplatného a umožňují snadnou identifikaci modelů volání, které způsobují omezení.

Omezení analyzátoru v současné době je, že nepočítá požadavky na typy prostředků disku a snímku (na podporu spravovaných disků). Vzhledem k tomu, že shromažďuje data z telemetrie CRP, také nemůže pomoci při identifikaci chyb omezení z ARM. Ale ty lze snadno identifikovat na základě výrazných hlaviček odpovědí ARM, jak bylo popsáno dříve.

Rutiny prostředí PowerShell používají rozhraní API služby REST, které lze snadno volat přímo klienty (i když zatím bez formální podpory). Chcete-li zobrazit formát požadavku HTTP, spusťte rutiny s přepínačem -Debug nebo špehovat jejich spuštění pomocí Fiddleru.


## <a name="best-practices"></a>Osvědčené postupy 

- Neopakujte chyby rozhraní API služby Azure bezpodmínečně nebo okamžitě. Běžný výskyt je pro kód klienta se dostat do smyčky rychlé opakování při výskytu chyby, která není možné opakovat. Opakování nakonec vyčerpá povolený limit volání pro skupinu cílové operace a ovlivní ostatní klienty předplatného. 
- V případech automatizace rozhraní API s velkým objemem zvažte implementaci proaktivního samoomezení na straně klienta, když počet volání k dispozici pro cílovou skupinu operací klesne pod určitou nízkou prahovou hodnotu. 
- Při sledování asynchronních operací respektujte rady záhlaví Opakování po. 
- Pokud kód klienta potřebuje informace o konkrétním virtuálním počítači, dotaz, který virtuální počítač přímo namísto výpisu všech virtuálních počítače v obsahující skupiny prostředků nebo celé předplatné a pak výběr emitovaného virtuálního počítače na straně klienta. 
- Pokud klientský kód potřebuje virtuální počítače, disky a snímky z konkrétního umístění Azure, použijte formulář dotazu na základě umístění namísto `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` dotazování všech virtuálních počítačů předplatného a filtrování podle umístění na straně klienta: dotaz na místní koncové body zprostředkovatele výpočetních prostředků. 
-   Při vytváření nebo aktualizaci prostředků rozhraní API zejména virtuálních počítačů a škálovací sady virtuálních strojů, je mnohem efektivnější sledovat `provisioningState`vrácenou asynchronní operaci k dokončení než dotazování na adresu URL prostředku samotné (na základě ).

## <a name="next-steps"></a>Další kroky

Další informace o opakování pokyny pro jiné služby v Azure, najdete v [tématu opakování pokyny pro konkrétní služby](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)
