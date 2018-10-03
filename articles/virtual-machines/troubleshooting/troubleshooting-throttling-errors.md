---
title: Řešení potíží, omezování chyby v Azure | Dokumentace Microsoftu
description: Omezení chyby, opakovaných pokusů a omezení rychlosti v Azure Compute.
services: virtual-machines
documentationcenter: ''
author: changov
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.devlang: na
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: vashan, rajraj, changov
ms.openlocfilehash: 7a1c283820b1ddef0c85899d9b56b6dcc3ea4b95
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2018
ms.locfileid: "48043131"
---
# <a name="troubleshooting-api-throttling-errors"></a>Řešení potíží s chybami omezení rozhraní API 

Požadavky na výpočetní prostředky Azure může omezit na předplatné a na základě jednotlivých oblastech, abychom vám pomohli s celkový výkon služby. Zajišťujeme, že všechna volání do Azure poskytovateli prostředků Compute (CRP), který spravuje prostředky v rámci oboru názvů Microsoft.Compute nedošlo k překročení maximální povolené frekvence požadavků rozhraní API. Tento dokument popisuje rozhraní API, omezování, podrobnosti o tom, jak řešit omezování problémy a osvědčené postupy, aby omezené.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Omezení využití sítě pomocí Azure Resource Manageru vs poskytovatelů prostředků  

Jako přední dveře do Azure Azure Resource Manageru nepodporuje ověřování a prvního řádu ověření a omezování všechny příchozí žádosti rozhraní API. Omezení přenosové rychlosti volání služby Azure Resource Manageru a hlavičky související diagnostických odpovědi protokolu HTTP jsou popsány [tady](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-request-limits).
 
Když klienta aplikace Azure API získá omezení chybu, je stav protokolu HTTP 429 příliš mnoho požadavků. Vysvětlení, pokud omezování žádostí probíhá v Azure Resource Manageru nebo základní poskytovatele prostředků jako CRP, zkontrolujte `x-ms-ratelimit-remaining-subscription-reads` pro požadavky GET a `x-ms-ratelimit-remaining-subscription-writes` hlavičky odpovědi pro požadavky bez GET. Pokud zbývající počet volání se blíží 0, bylo dosaženo limitu předplatného obecné volání definované pomocí Azure Resource Manageru. Aktivity všichni klienti předplatného započítávají dohromady. V opačném případě omezování pochází z poskytovatele prostředků cílového (ten odkazovaného `/providers/<RP>` segment adresy URL požadavku). 

## <a name="call-rate-informational-response-headers"></a>Hlavičky odpovědi informační frekvence volání 

| Záhlaví                            | Formát hodnoty                           | Příklad:                               | Popis                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-ms-ratelimit – zbývající prostředků |```<source RP>/<policy or bucket>;<count>```| Microsoft.Compute/HighCostGet3Min;159 | Zbývající počet volání rozhraní API pro zásady omezování pokrývající kontejneru nebo operace skupinu prostředků včetně cíl této žádosti                                                                   |
| x-ms žádost poplatek               | ```<count>   ```                             | 1                                     | Počet volání vrátí "účtovat" pro tento požadavek HTTP směrem k příslušné zásady omezení. Toto je nejčastěji 1. Požadavky služby batch, například škálování škálovací sady virtuálních počítačů může účtovat více počty. |


Všimněte si, že požadavek na rozhraní API může být podroben více omezení zásad. Bude samostatný `x-ms-ratelimit-remaining-resource` záhlaví pro každou zásadu. 

Tady je ukázková odpověď odstranit požadavek škálovací sady virtuálních počítačů.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

##<a name="throttling-error-details"></a>Omezení šířky pásma podrobnosti o chybě

Stav protokolu HTTP 429 se běžně používá chcete odmítnout žádost, protože je dosaženo omezení četnosti volání. Typická omezení chybová odpověď z poskytovatele výpočetních prostředků bude vypadat jako následující příklad (jsou zobrazeny pouze relevantní záhlaví):

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

Zásady se zbývající počet volání 0 je to omezení chyba je vrácena. V tomto případě je `HighCostGet30Min`. Celkový formát těla odpovědi je obecný formát Chyba rozhraní API Azure Resource Manageru (splňovala podmínky shody s protokolem OData). Kód chyby hlavní `OperationNotAllowed`, je ten poskytovatele výpočetních prostředků používá oznamuje omezování chyby (mimo jiné druhy chyby klienta). `message` Vlastnost vnitřní chyby obsahuje serializovaná strukturu JSON s podrobnostmi o porušení omezení.

Jak je znázorněno výše, zahrnuje každé chybě omezení `Retry-After` hlavičky, která poskytuje minimální počet sekund, klient čekat před opakováním žádosti. 

## <a name="best-practices"></a>Osvědčené postupy 

- Bezpodmínečně a/nebo okamžitě opakování chyby rozhraní API služby Azure. Běžné výskyt je pro klientský kód rychle dostat do smyčce rychlé opakování, když dojde k nějaké chybě, která není možné opakovat. Opakované pokusy se nakonec vyčerpání limitu povolených volání pro operaci cílové skupiny a mít vliv na ostatní klienti předplatného. 
- V případech, vysoký počet rozhraní API automatizace zvažte implementaci aktivní na straně klienta svým omezování, když počet dostupných volání pro cílovou skupinu operace klesne pod některé nízké prahové hodnoty. 
- Při sledování asynchronních operací se dodržovat pomocné parametry hlavičkou Retry-After. 
- Pokud klientský kód potřebuje informace o konkrétní virtuální počítač, dotaz tohoto virtuálního počítače přímo, bez výpis všech virtuálních počítačů v příslušnou skupinu prostředků nebo celé předplatné a pak vybrat potřebné virtuálního počítače na straně klienta. 
- Pokud klientský kód potřebuje virtuální počítače, disky nebo snímky z určitého umístění Azure, založená na poloze formulář dotazu použít namísto dotaz na odběr všechny virtuální počítače a následného filtrování podle polohy na straně klienta: `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` dotazu na místní poskytovatele výpočetních prostředků Koncové body. 
-   Při vytváření nebo aktualizaci rozhraní API prostředky zejména, virtuální počítače a škálovací sady virtuálních počítačů, je mnohem efektivnější než dotazování na adrese URL prostředku, samotný sledovat vrácený asynchronní operace do konce (na základě `provisioningState`).

## <a name="next-steps"></a>Další postup

Další informace o pokyny k opakování pro ostatní služby v Azure najdete v tématu [pokyny pro opakování pro určité služby](https://docs.microsoft.com/en-us/azure/architecture/best-practices/retry-service-specific)
