---
title: Azure Event Hubs – výjimky rozhraní .NET
description: Tento článek poskytuje seznam výjimek zasílání zpráv Azure Event Hubs .NET a navrhovaných akcí.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 09/23/2020
ms.author: shvija
ms.openlocfilehash: 6a1d7c969d31033ae9d00d212cc8f1a45abbeda1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91344573"
---
# <a name="eventhubsexception---net"></a>EventHubsException – .NET
**EventHubsException** se aktivuje, když operace specifická pro Event Hubs způsobila problém, včetně obou chyb v rámci služby a specifických pro klienta. 

## <a name="exception-information"></a>Informace o výjimce
Tato výjimka zahrnuje následující kontextové informace, které vám pomůžou pochopit kontext chyby a její relativní závažnost. 

- - **Přechodný**: Určuje, zda je výjimka považována za obnovitelnou nebo ne. V případě, že byl považován za přechodný, již byly aplikovány příslušné zásady opakování a opakované pokusy byly neúspěšné.
- **Důvod**: poskytuje sadu známých důvodů k selhání, které usnadňuje kategorizaci a objasnění hlavní příčiny. Tyto důvody jsou určeny k tomu, aby při kontrole textu zprávy o výjimce nemohly být ideálním řešením použití filtrování výjimek a jiné logiky. Některé z důvodů selhání při selhání:
    - **Klient zavřeno**: nastane, když klient centra událostí, který už je uzavřený nebo vyřazený. Doporučujeme zkontrolovat kód aplikace, abyste zajistili, že objekty z klientské knihovny Event Hubs jsou vytvořeny a uzavřeny v určeném oboru.
    - **Časový limit služby**: označuje, že služba Event Hubs nereagovala na operaci v očekávaném časovém intervalu. K tomuto problému může dojít v důsledku přechodného síťového problému nebo problému se službou. Služba Event Hubs možná nebo nemusí úspěšně dokončit požadavek. stav není známý. Doporučuje se ověřit aktuální stav a v případě potřeby opakovat akci.
    - **Překročená kvóta**: udává, že existuje příliš mnoho aktivních operací čtení pro jednu skupinu příjemců. Toto omezení závisí na úrovni oboru názvů Event Hubs a může být nutné přesunout do vyšší úrovně. Alternativou by bylo vytvořit další skupiny příjemců a zajistit, aby byl počet čtení klienta pro každou skupinu v rámci limitu. Další informace najdete v tématu [kvóty a omezení pro Azure Event Hubs](event-hubs-quotas.md).
    - **Překročení velikosti zprávy**: data události jako maximální velikost povolená pro jednotlivé události i pro dávku událostí. Zahrnuje data události a veškerá přidružená metadata a režijní náklady na systém. Chcete-li tuto chybu vyřešit, snižte počet událostí odeslaných v dávce nebo zmenšete velikost dat obsažených ve zprávě. Vzhledem k tomu, že se limity velikosti můžou změnit, přečtěte si téma [kvóty a limity pro Azure Event Hubs](event-hubs-quotas.md) pro konkrétní věci.
    - **Uživatel byl odpojen**: klient pro spotřebitele byl odpojen službou centra událostí z instance centra událostí. Obvykle k tomu dochází, když příjemce s vyšší úrovní vlastníku uplatňuje vlastnictví v rámci párování oddílů a skupin příjemců.
    - **Prostředek nebyl nalezen**: Služba Event Hubs nemohla najít prostředek, jako je například centrum událostí, skupina uživatelů nebo oddíl. Mohla být odstraněna nebo došlo k potížím se službou Event Hubs.

## <a name="handling-exceptions"></a>Zpracování výjimek
Můžete reagovat na konkrétní důvod selhání pro **EventHubException**  několika způsoby. Jedním ze způsobů je použít klauzuli filtru výjimky jako součást bloku catch.

```csharp
try
{
    // Read events using the consumer client
}
catch (EventHubsException ex) where 
    (ex.Reason == EventHubsException.FailureReason.ConsumerDisconnected)
{
    // Take action based on a consumer being disconnected
}
```

## <a name="next-steps"></a>Další kroky
Existují další výjimky, které jsou popsány v [článku starší verze](event-hubs-messaging-exceptions.md). Některé z nich platí jenom pro starší verze klientské knihovny Event Hubs .NET.