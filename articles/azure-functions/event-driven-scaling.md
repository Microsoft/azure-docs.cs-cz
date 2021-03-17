---
title: Škálování založené na událostech v Azure Functions
description: Vysvětluje chování plánu spotřeby a aplikací funkcí plánu Premium.
ms.date: 10/29/2020
ms.topic: conceptual
ms.service: azure-functions
ms.openlocfilehash: 8aca1ab6629f95ef9162e1247434bd3189d5a7d2
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937648"
---
# <a name="event-driven-scaling-in-azure-functions"></a>Škálování založené na událostech v Azure Functions

V plánech spotřeby a Premium Azure Functions škáluje prostředky procesoru a paměti přidáním dalších instancí hosta Functions. Počet instancí se určuje podle počtu událostí, které aktivují funkci. 

Každá instance hostitele Functions v plánu spotřeby je omezená na 1,5 GB paměti a jeden procesor.  Instance hostitele je celá aplikace Function App, což znamená, že všechny funkce v rámci aplikace Function App sdílejí prostředky v rámci instance a škálují ve stejnou dobu. Aplikace Function App, které sdílejí stejné škálování plánu spotřeby, nezávisle.  V plánu Premium určuje velikost plánu dostupnou paměť a procesor pro všechny aplikace v tomto plánu na této instanci.  

Soubory s kódem funkce jsou uložené ve sdílených složkách služby soubory Azure na hlavním účtu úložiště funkce. Když odstraníte hlavní účet úložiště aplikace Function App, soubory s kódem funkce se odstraní a nelze je obnovit.

## <a name="runtime-scaling"></a>Škálování za běhu

Azure Functions používá komponentu s názvem *kontroler škálování* pro monitorování míry událostí a určení, zda se má horizontální navýšení nebo škálování škálovat. Kontroler škálování používá heuristiky pro každý typ triggeru. Pokud například používáte Trigger služby Azure Queue Storage, škáluje se podle délky fronty a stáří nejstarší zprávy fronty.

Jednotka škálování pro Azure Functions je aplikace Function App. Při horizontálním navýšení kapacity aplikace Function App se přidělí další prostředky pro spuštění více instancí Azure Functionsho hostitele. V opačném případě dojde k omezení požadavků na výpočetní výkon, protože řadič škálování odebere instance hostitele funkcí. Počet instancí je nakonec "škálovat" na nulu, pokud v aplikaci Function App nejsou spuštěny žádné funkce.

![Škálování událostí monitorování řadiče a vytváření instancí](./media/functions-scale/central-listener.png)

## <a name="cold-start"></a>Studený start

Až bude aplikace Function App po určitou dobu nečinná, může tato platforma škálovat počet instancí, na kterých vaše aplikace běží, na nulu. Další požadavek má přidanou latenci škálování od nuly po jeden. Tato latence se označuje jako _studená spuštění_. Počet závislostí, které vyžaduje aplikace Function App, může mít vliv na počáteční čas. Studená Start je více problémů při synchronních operacích, například triggerech HTTP, které musí vracet odpověď. Pokud mají tyto funkce vliv na studená spuštění, zvažte spuštění v plánu Premium nebo ve vyhrazeném plánu s povoleným nastavením **vždy zapnuto** .   

## <a name="understanding-scaling-behaviors"></a>Principy chování škálování

Škálování se může u různých faktorů lišit a škáluje se různě na základě zvoleného triggeru a jazyka. Existuje několik složitými rozhraními chování škálování, která je potřeba znát:

* **Maximální počet instancí:** Jedna aplikace Function App se škáluje maximálně na 200 instancí. Jedna instance může zpracovávat více než jednu zprávu nebo požádat současně, takže neexistuje nastavený limit počtu souběžných spuštění.  Můžete [zadat nižší maximum](#limit-scale-out) pro omezení škálování podle potřeby.
* **Nová frekvence instancí:** Pro aktivační události HTTP se přidělují nové instance, a to nejvíce jednou za sekundu. U triggerů bez protokolu HTTP se přidělují nové instance, a to nejvíce každých 30 sekund. Škálování je rychlejší při provozu v [plánu Premium](functions-premium-plan.md).
* **Efektivita škálování:** Pro aktivační události Service Bus použijte pro nejúčinnější škálování _Spravovat_ práva k prostředkům. U oprávnění k _naslouchání_ není škálování tak přesné, protože délka fronty se nedá použít k informování rozhodnutí o škálování. Další informace o nastavení práv v zásadách přístupu Service Bus najdete v tématu [zásady autorizace sdíleného přístupu](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies). Aktivační události centra událostí najdete v [návodu k škálování](functions-bindings-event-hubs-trigger.md#scaling) v referenčním článku. 

## <a name="limit-scale-out"></a>Omezení horizontálního navýšení kapacity

Možná budete chtít omezit maximální počet instancí, které aplikace používala k horizontálnímu navýšení kapacity.  Toto je nejběžnější pro případy, kdy komponenta pro příjem dat, jako je databáze, má omezené propustnost.  Ve výchozím nastavení plán spotřeby funguje škálování na až 200 instancí a funkce plánu Premium se škálují až na až 100 instancí.  Úpravou hodnoty můžete určit nižší maximum pro konkrétní aplikaci `functionAppScaleLimit` .  `functionAppScaleLimit`Lze nastavit na `0` nebo `null` pro neomezenou nebo platnou hodnotu mezi `1` a maximum.

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <RESOURCE_GROUP> -n <FUNCTION_APP-NAME>/config/web --set properties.functionAppScaleLimit=<SCALE_LIMIT>
```

## <a name="best-practices-and-patterns-for-scalable-apps"></a>Osvědčené postupy a vzory pro škálovatelné aplikace

Existuje mnoho aspektů aplikace Function App, které mají dopad na to, jak se škáluje, včetně konfigurace hostitele, běhového prostředí a efektivity prostředků.  Další informace najdete v [části věnované škálovatelnosti v článku věnovaném důležitým](functions-best-practices.md#scalability-best-practices)informacím o výkonu. Měli byste taky vědět, jak se připojení chovají, jak se vaše aplikace Function škáluje. Další informace najdete v tématu [Správa připojení v Azure Functions](manage-connections.md).

Další informace o škálování v Pythonu a Node.js Azure Functions najdete v tématu [Příručka pro vývojáře v Pythonu – škálování a souběžnost](functions-reference-python.md#scaling-and-performance) a [Azure Functions Node.js příručka pro vývojáře – škálování a souběžnost](functions-reference-node.md#scaling-and-concurrency).

## <a name="billing-model"></a>Model fakturace

Fakturace pro různé plány je podrobně popsána na [stránce s cenami Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Použití je agregované na úrovni aplikace funkcí a počítá se pouze v době, kdy je spuštěn kód funkce. Níže jsou uvedené jednotky pro fakturaci:

* **Spotřeba prostředků v GB-s (GB-s)**. Vypočítáno jako kombinace velikosti paměti a doby provádění pro všechny funkce v rámci aplikace Function App. 
* **Spuštění**. Počítá se pokaždé, když se funkce spustí v reakci na Trigger události.

Užitečné dotazy a informace o tom, jak pochopit vyúčtování spotřeby, najdete [na stránce s nejčastějšími dotazy k fakturaci](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="next-steps"></a>Další kroky

+ [Azure Functions možností hostování](functions-scale.md)

