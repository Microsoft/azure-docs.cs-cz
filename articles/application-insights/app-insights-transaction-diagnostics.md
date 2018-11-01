---
title: Diagnostika transakcí Azure Application Insights | Dokumentace Microsoftu
description: Diagnostika transakcí začátku do konce Application Insights
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 5b52fd41fc3e37078bbddc721c0c54af2c430b43
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419227"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Diagnostika jednotné transakcí mezi komponentami

Diagnostika jednotné prostředí automaticky koreluje telemetrie na straně serveru z pro všechny součásti Application Insights monitoruje do jediného zobrazení. Nebude vadit, když máte více zdrojů pomocí samostatných instrumentačních klíčů. Application Insights zjistí základní vztah a umožňuje snadno diagnostikovat aplikace komponentu, závislost nebo výjimku, která způsobila transakce zpomalení nebo selhání.

## <a name="what-is-a-component"></a>Co je komponenta?

Součásti jsou umožňují nezávislé nasazení částí aplikace distribuovat/mikroslužeb. Vývojáři provozní týmy a týmy mají viditelnost na úrovni kódu nebo přístup k telemetrii generovanou těmito součásti aplikace.

* Komponenty se liší od "zjištěnou" externí závislosti, jako je SQL, atd centra událostí, které tým nebo organizace nemusí mít přístup k (kód nebo telemetrické údaje).
* Komponenty jsou spuštěny na libovolný počet instancí role/server/kontejner.
* Součástí může být samostatný instrumentačních klíčů Application Insights (i v případě, že předplatná se liší) nebo různé role, generování sestav na jeden Instrumentační klíč Application Insights. Nové prostředí obsahuje podrobnosti pro všechny součásti, bez ohledu na to, jak jsou již byly vytvořeny.

> [!NOTE]
> * **Chybějící odkazy na související položky?** Jsou všechny související telemetrii [horní](#cross-component-transaction-chart) a [dolní](#all-telemetry-with-this-Operation-Id) oddíly na levé straně. 

## <a name="transaction-diagnostics-experience"></a>Diagnostika transakcí prostředí
Toto zobrazení obsahuje čtyři klíčové části: výsledky seznamu, graf transakcí mezi komponentami, časový průběh seznam všechny telemetrické údaje vztahující se k této operaci a v podokně podrobností pro všechny položky vybrané telemetrie na levé straně.

![Klíčové části](media/app-insights-transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Graf transakcí mezi komponentami

Tento graf obsahuje časovou osu s vodorovné pruhy po dobu trvání požadavků a závislostí mezi komponentami. Všechny výjimky, které se shromažďují jsou také označené na časové ose.

* Horní řádek v tomto grafu představuje vstupní bod, příchozí požadavek na první komponenta volá se v této transakci. Doba trvání je celkovou dobu potřebnou k dokončení transakce.
* Všechna volání do externí závislosti jsou jednoduché-sbalitelné řádky s ikonami představující typ závislosti.
* Volání součásti jsou sbalitelné řádků. Každý řádek odpovídá konkrétní operace se vyvolala na komponentu.
* Ve výchozím nastavení, požadavek, závislost nebo výjimku, který jste vybrali zobrazí na pravé straně.
* Vyberte libovolný řádek zobrazíte jeho [podrobnosti na pravé straně](#details-of-the-selected-telemetry). 

> [!NOTE]
Volání na jiné komponenty obsahuje dva řádky: odchozích volání (závislosti) z komponenty volající představuje jeden řádek a druhý řádek odpovídá příchozí žádosti názvem komponenty. Přední ikonu a různé styly pruhy doba trvání pomoci rozlišit mezi nimi.

## <a name="all-telemetry-with-this-operation-id"></a>Veškerá telemetrie s tímto Id operace

Tato část uvádí seznam bez stromové struktury v čase posloupnost veškerá telemetrická data týkající se této transakce. Také ukazuje vlastní události a trasování, které nejsou zobrazeny v grafu transakce. Je tento seznam můžete filtrovat na telemetrii generovanou konkrétní součást a volání. Můžete vybrat všechny položky telemetrie v tomto seznamu chcete zobrazit odpovídající [podrobnosti na pravé straně](#details-of-the-selected-telemetry).

![Čas posloupnost veškerou telemetrii](media/app-insights-transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Podrobnosti vybrané telemetrických dat

Tento sbalitelné podokně se zobrazí podrobnosti vybrané položky z grafu transakce, nebo v seznamu. "Zobrazit všechny" jsou uvedeny všechny standardní atributy, které byly shromážděny. Žádné vlastní atributy jsou uvedeny samostatně standardní sady. Klikněte na "..." níže okno trasování zásobníku získat možnost kopírování trasování. "Trasování profileru otevřít" nebo "Otevřít snímek ladění" zobrazuje úrovně diagnostiky kódu v odpovídající podokna podrobností.

![Podrobnosti o výjimce](media/app-insights-transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Výsledky hledání

V tomto podokně sbalitelné zobrazuje výsledky, které splňují kritéria filtru. Klikněte na libovolný výsledek aktualizace příslušné podrobnosti o 3 oddíly uvedené výše. Snažíme se najít ukázky, které bývají nejčastějším obsahují podrobné informace ze všech komponent k dispozici i v případě, že v žádném z nich je aktivní vzorkování. Tyto aspekty znázorňuje "doporučené" vzorků.

![Výsledky hledání](media/app-insights-transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Profiler a snapshot debugger

[Application Insights profileru](app-insights-profiler.md) nebo [snapshot debuggeru](app-insights-snapshot-debugger.md) pomoct s diagnostikou úroveň kódu systému problémy s výkonem a selhání. Pomocí této možnosti můžete zobrazit trasování profileru nebo klikněte na tlačítko snímky u každé komponenty s jedním.

Pokud Profiler práce nelze získat, obraťte se prosím **serviceprofilerhelp@microsoft.com**

Pokud nelze získat Snapshot Debugger práce, obraťte se prosím **snapshothelp@microsoft.com**

![Profiler integrace](media/app-insights-transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>Nejčastější dotazy

*V grafu se zobrazuje jedinou komponentu a ostatní jsou zobrazeny pouze jako externí závislosti bez všech podrobností o co se stalo v rámci těchto komponent.*

Možné důvody:

* Jsou ostatní součásti neinstrumentují službou Application Insights?
* Používají se nejnovější stabilní Application Insights SDK?
* Pokud tyto součásti jsou samostatný prostředek Application Insights, máte požadovaný přístup k jejich telemetrie?

Pokud máte přístup a komponenty jsou vybaveny nejnovější sady SDK služby Application Insights, dejte nám vědět prostřednictvím kanálu horní pravé zpětnou vazbu.

*Zobrazila se duplicitní řádky pro závislosti. Je to očekávání?*

V tuto chvíli jsme se zobrazují volání odchozí závislost odděleně od příchozího požadavku. Obvykle dvě volání vypadají stejně s pouze hodnotu doby trvání odlišné z důvodu síťové odezvy. Přední ikonu a různé styly pruhy doba trvání pomoci rozlišit mezi nimi. Tato prezentace dat je matoucí? Sdělte nám svůj názor!

*A co zkosí hodiny napříč instancemi různé součásti?*

Časové osy jsou upraveny pro nepřesnostem v grafu transakce. Zobrazí se přesný časová razítka v podokně podrobností nebo pomocí Analytics.

*Proč je nové prostředí chybí většinu dotazů související položky?*

Toto chování je úmyslné. Všechny související položky pro všechny součásti, jsou už k dispozici na levé straně (horní a dolní části). Nové prostředí má dva související položky, které nepokrývá na levé straně: veškerá telemetrie 5 minut před a po této události a časová osa uživatele.
