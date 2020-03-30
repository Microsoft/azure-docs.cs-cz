---
title: Diagnostika transakcí přehledů aplikací Azure | Dokumenty společnosti Microsoft
description: Diagnostika transakcí mezi koncovými aplikacemi
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.openlocfilehash: 39c4c51a1bc84e06efac3674b1ee5b487f9e6729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671133"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Jednotná diagnostika transakcí mezi komponentami

Jednotné diagnostické prostředí automaticky koreluje telemetrickou na straně serveru ze všech monitorovaných součástí Application Insights do jednoho zobrazení. Nezáleží na tom, zda máte více prostředků se samostatnými instrumentačními klíči. Application Insights detekuje základní vztah a umožňuje snadno diagnostikovat součást aplikace, závislost nebo výjimku, která způsobila zpomalení nebo selhání transakce.

## <a name="what-is-a-component"></a>Co je komponenta?

Součásti jsou nezávisle nasaditelné části aplikace distribuovaných nebo mikroslužeb. Vývojáři a provozní týmy mají viditelnost na úrovni kódu nebo přístup k telemetrii generované těmito součástmi aplikace.

* Součásti se liší od "pozorovaných" externích závislostí, jako je SQL, EventHub atd., ke kterým váš tým/organizace nemusí mít přístup (kód nebo telemetrie).
* Součásti jsou spuštěny v libovolném počtu instancí serveru/role/kontejneru.
* Součásti mohou být samostatné klíče instrumentace Application Insights (i když se předplatná liší) nebo různé role, které se hlásí do jednoho klíče instrumentace Application Insights. Nové prostředí zobrazuje podrobnosti ve všech součástech bez ohledu na to, jak byly nastaveny.

> [!NOTE]
> * **Chybí odkazy na související položky?** Všechny související telemetrie jsou v [horní](#cross-component-transaction-chart) a [dolní](#all-telemetry-with-this-operation-id) části levé strany. 

## <a name="transaction-diagnostics-experience"></a>Zkušenosti s diagnostikou transakcí
Toto zobrazení má čtyři klíčové části: seznam výsledků, graf transakcí mezi komponentami, seznam časových sekvencí všech telemetrických dat souvisejících s touto operací a podokno podrobností pro libovolnou vybranou položku telemetrie vlevo.

![Klíčové části](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Graf transakcí mezi komponentami

Tento graf poskytuje časovou osu s vodorovnými pruhy pro dobu trvání požadavků a závislostí mezi součástmi. Všechny výjimky, které jsou shromažďovány jsou také označeny na časové ose.

* Horní řádek v tomto grafu představuje vstupní bod, příchozí požadavek na první součást volanou v této transakci. Doba trvání je celková doba, po kterou byla transakce dokončena.
* Všechna volání externích závislostí jsou jednoduché řádky bez sbalitelné, s ikonami představujícími typ závislosti.
* Volání jiných součástí jsou sbalitelné řádky. Každý řádek odpovídá konkrétní operaci vyvolána na komponentu.
* Ve výchozím nastavení se vybraný požadavek, závislost nebo výjimka zobrazí na pravé straně.
* Vyberte libovolný řádek, chcete-li zobrazit jeho [podrobnosti vpravo](#details-of-the-selected-telemetry). 

> [!NOTE]
> Volání jiných součástí mají dva řádky: jeden řádek představuje odchozí volání (závislost) z volající součásti a druhý řádek odpovídá příchozí požadavek na volané součásti. Úvodní ikona a odlišný styl pruhů doby trvání pomáhají rozlišovat mezi nimi.

## <a name="all-telemetry-with-this-operation-id"></a>Veškerá telemetrie s tímto ID operace

Tato část zobrazuje zobrazení plochého seznamu v časové posloupnosti všech telemetrických dat souvisejících s touto transakcí. Zobrazuje také vlastní události a trasování, které nejsou zobrazeny v grafu transakce. Tento seznam můžete filtrovat do telemetrie generované určitou komponentou nebo voláním. Můžete vybrat libovolnou položku telemetrie v tomto seznamu a zobrazit odpovídající [podrobnosti vpravo](#details-of-the-selected-telemetry).

![Časová posloupnost všech telemetrických](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Podrobnosti o vybrané telemetrii

Toto skládací podokno zobrazuje podrobnosti libovolné vybrané položky z grafu transakcí nebo seznamu. "Zobrazit vše" uvádí všechny standardní atributy, které jsou shromažďovány. Všechny vlastní atributy jsou samostatně uvedeny pod standardní sadou. Klikněte na "..." pod okno trasování zásobníku získat možnost zkopírovat trasování. "Otevřít trasování profileru" nebo "Otevřít snímek ladění" zobrazuje diagnostiku na úrovni kódu v odpovídajících podoknech podrobností.

![Podrobnosti o výjimce](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Výsledky hledání

Toto skládací podokno zobrazuje další výsledky, které splňují kritéria filtru. Klikněte na jakýkoli výsledek aktualizovat příslušné údaje 3 výše uvedené oddíly. Snažíme se najít vzorky, které jsou s největší pravděpodobností mít podrobnosti k dispozici ze všech složek, i když odběr vzorků je v platnosti v některéz nich. Ty jsou zobrazeny jako "navrhované" vzorky.

![Výsledky hledání](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Ladicí program profileru a snímku

[Application Insights profiler](../../azure-monitor/app/profiler.md) nebo [snímek ladicí program](snapshot-debugger.md) pomoc s diagnostikou na úrovni kódu výkonu a selhání. Díky této zkušenosti můžete zobrazit trasování profiler nebo snímky z libovolné součásti s jedním kliknutím.

Pokud jste nemohli dostat Profiler pracovní, obraťte se **na\@serviceprofilerhelp microsoft.com**

Pokud jste nemohli dostat snímek ladicí program pracovní, obraťte se **na snapshothelp\@microsoft.com**

![Integrace profileru](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>Nejčastější dotazy

*V grafu se zobrazuje jedna součást a ostatní se zobrazují pouze jako externí závislosti bez podrobností o tom, co se v těchto součástech stalo.*

Možné důvody:

* Jsou ostatní součásti instrumentované pomocí Application Insights?
* Používají nejnovější stabilní application insights SDK?
* Pokud jsou tyto součásti samostatné prostředky Application Insights, máte požadovaný přístup k jejich telemetrii?

Pokud máte přístup a komponenty jsou instrumentované s nejnovějšími sadami SDK Application Insights, dejte nám vědět prostřednictvím kanálu zpětné vazby vpravém horním rohu.

*Pro závislosti se zobrazují duplicitní řádky. Očekává se to?*

V tomto okamžiku zobrazujeme volání odchozí závislosti oddělené od příchozího požadavku. Dvě volání obvykle vypadají stejně s pouze hodnota trvání se liší z důvodu sítě zpáteční. Úvodní ikona a odlišný styl pruhů doby trvání pomáhají rozlišovat mezi nimi. Je tato prezentace dat matoucí? Sunás prosím uveďte svůj názor!

*A co hodiny zkosení v různých instancích komponent?*

Časové osy jsou upraveny pro zkosení hodin v transakčním grafu. Přesná časová razítka můžete zobrazit v podokně podrobností nebo pomocí služby Analytics.

*Proč nové prostředí chybí většina souvisejících položek dotazů?*

Toto chování je úmyslné. Všechny související položky napříč všemi součástmi jsou již k dispozici na levé straně (horní a dolní části). Nové prostředí má dvě související položky, které nepokrývá levá strana: všechny telemetrie z pěti minut před a po této události a časové osy uživatele.
