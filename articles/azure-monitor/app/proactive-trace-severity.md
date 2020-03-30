---
title: Degradace v poměru závažnosti trasování – Přehledy aplikací Azure
description: Sledujte trasování aplikací pomocí Azure Application Insights pro neobvyklé vzory v telemetrii trasování pomocí inteligentní detekce .
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 30bdd30ac9c49bb79a3c48bae8149ec761756dd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671677"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Degradace v poměru závažnosti trasování (náhled)

Stopy jsou široce používány v aplikacích, protože pomáhají vyprávět příběh o tom, co se děje v zákulisí. Když se něco pokazí, stopy poskytují zásadní přehled o sledu událostí vedoucích k nežádoucímu stavu. Zatímco stopy jsou obecně nestrukturované, je tu jedna věc, která může konkrétně naučit od nich - jejich úroveň závažnosti. V ustáleném stavu aplikace bychom očekávali, že poměr mezi "dobré" stopy (*Info* a *Verbose*) a "špatné" stopy (*Upozornění*, *Chyba*a *Kritické*) zůstat stabilní. Předpokládá se, že "špatné" stopy se mohou do určité míry do určité míry provádět z důvodu libovolného počtu důvodů (například přechodné síťové problémy). Ale když skutečný problém začne růst, obvykle se projevuje jako zvýšení relativního podílu "špatné" stopy vs "dobré" stopy. Application Insights Smart Detection automaticky analyzuje trasování zaznamenané vaší aplikací a může vás varovat před neobvyklými vzory v závažnosti telemetrie trasování.

Tato funkce nevyžaduje žádné speciální nastavení, kromě konfigurace protokolování trasování pro vaši aplikaci (viz postup konfigurace posluchače protokolu trasování pro [rozhraní .NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) nebo [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs)). Je aktivní, když vaše aplikace generuje dostatek telemetrie výjimek.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Kdy dostanu tento typ inteligentního oznámení o detekci?
Tento typ oznámení můžete získat, pokud poměr mezi "dobré" trasování (trasování protokolované s úrovní *Info* nebo *Verbose)* a "chybné" trasování (trasování zaznamenané s úrovní *upozornění*, *chyba*nebo *fatální*) je ponižující v určitý den, ve srovnání s směrným plánem vypočtené za předchozích sedm dní.

## <a name="does-my-app-definitely-have-a-problem"></a>Má moje aplikace určitě nějaký problém?
Ne, oznámení neznamená, že vaše aplikace má určitě problém. Přestože degradace v poměru mezi "dobré" a "špatné" stopy může znamenat problém aplikace, tato změna poměru může být benigní. Zvýšení může být například způsobeno nový tok v aplikaci emitující více "chybné" trasování než existující toky).

## <a name="how-do-i-fix-it"></a>Jak ho mám opravit?
Oznámení obsahují diagnostické informace pro podporu v procesu diagnostiky:
1. **Třídění.** Oznámení ukazuje, kolik operací jsou ovlivněny. To vám může pomoci přiřadit prioritu problému.
2. **Rozsah.** Je problém ovlivňuje veškerý provoz, nebo jen nějaký provoz? Tyto informace lze získat z oznámení.
3. **Diagnostikovat.** Související položky a sestavy, které odkazují na podpůrné informace, můžete použít k další diagnostice problému.


