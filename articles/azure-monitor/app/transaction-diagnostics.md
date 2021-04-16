---
title: Diagnostika transakcí Azure Application Insights | Microsoft Docs
description: Application Insights komplexní diagnostiku transakcí
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.openlocfilehash: 60365079c295e154ff0a38277c9ccdec35157e6e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481391"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Sjednocená Diagnostika transakcí mezi komponentami

Sjednocené diagnostické prostředí automaticky koreluje telemetrie na straně serveru ze všech Application Insights monitorovaných komponent do jednoho zobrazení. Nezáleží na tom, jestli máte více prostředků s oddělenými klíči instrumentace. Application Insights detekuje základní vztah a umožňuje snadnou diagnostikovat komponentu, závislost nebo výjimku aplikace, které způsobily zpomalení nebo selhání transakce.

## <a name="what-is-a-component"></a>Co je komponenta?

Komponenty jsou nezávisle nasaditelné části distribuované aplikace nebo mikroslužeb. Vývojáři a provozní týmy mají viditelnost na úrovni kódu nebo přístup k telemetrie generované těmito komponentami aplikací.

* Komponenty se liší od "pozorovaných" vnějších závislostí, jako je například SQL, EventHub atd. který tým nebo organizace nemusí mít přístup k (kód nebo telemetrie).
* Komponenty běží na jakémkoli počtu instancí serveru/role/kontejneru.
* Komponenty mohou být odděleny Application Insights klíče instrumentace (i když se odběry liší) nebo různé role, které se hlásí do jediného klíče instrumentace Application Insights. Nové prostředí zobrazuje podrobnosti napříč všemi komponentami bez ohledu na to, jak byly nastaveny.

> [!NOTE]
> * **Chybí odkazy související položky?** Všechny související telemetrie jsou v [horních](#cross-component-transaction-chart) a [dolních](#all-telemetry-with-this-operation-id) částech levé strany. 

## <a name="transaction-diagnostics-experience"></a>Prostředí pro diagnostiku transakcí
Toto zobrazení obsahuje čtyři klíčové části: seznam výsledků, graf transakcí mezi komponentami, seznam časových sekvencí všech telemetrie souvisejících s touto operací a podokno podrobností pro všechny vybrané položky telemetrie na levé straně.

![Klíčové části](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Graf transakcí mezi komponentami

Tento graf poskytuje časovou osu s vodorovnými pruhy po dobu trvání požadavků a závislostí napříč komponentami. Všechny shromažďované výjimky jsou také označeny na časové ose.

* Horní řádek v tomto grafu představuje vstupní bod, příchozí požadavek na první komponentu volanou v této transakci. Doba trvání je celková doba potřebná k dokončení transakce.
* Všechna volání vnějších závislostí jsou jednoduché nesbalitelné řádky s ikonami, které představují typ závislosti.
* Volání jiných komponent jsou sbalitelnými řádky. Každý řádek odpovídá konkrétní operaci vyvolanou v součásti.
* Ve výchozím nastavení se na pravé straně zobrazí požadavek, závislost nebo výjimka, kterou jste vybrali.
* Výběrem libovolného řádku zobrazíte jeho [Podrobnosti na pravé straně](#details-of-the-selected-telemetry). 

> [!NOTE]
> Volání jiných komponent mají dva řádky: jeden řádek představuje odchozí volání (závislost) ze součásti volajícího a druhý řádek odpovídá příchozímu požadavku na volané komponentě. Úvodní ikona a různé styly pruhů trvání mezi nimi můžou rozlišovat.

## <a name="all-telemetry-with-this-operation-id"></a>Veškerá telemetrie s tímto ID operace

Tato část ukazuje zobrazení plochého seznamu v časovém intervalu pro všechny telemetrie týkající se této transakce. Zobrazuje také vlastní události a trasování, které nejsou zobrazeny v transakčním grafu. Tento seznam můžete filtrovat na telemetrii vygenerovanou konkrétní součástí nebo voláním. Můžete vybrat libovolnou položku telemetrie v tomto seznamu, abyste viděli odpovídající [Podrobnosti na pravé straně](#details-of-the-selected-telemetry).

![Časová sekvence všech telemetrie](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Podrobnosti vybrané telemetrie

V tomto sbalitelném podokně se zobrazuje podrobnosti libovolné vybrané položky z transakčního grafu nebo seznamu. "Zobrazit vše" obsahuje seznam všech shromažďovaných standardních atributů. Všechny vlastní atributy jsou uvedeny samostatně pod standardní sadou. Klikněte na "..." pod oknem trasování zásobníku, abyste získali možnost zkopírovat trasování. "Otevřením trasování profileru" nebo "otevřít snímek ladění" se v příslušných podoknech podrobností zobrazí Diagnostika na úrovni kódu.

![Podrobnosti výjimky](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Výsledky hledání

V tomto sbalitelném podokně se zobrazují další výsledky, které splňují kritéria filtru. Kliknutím na libovolný výsledek aktualizujete příslušné podrobnosti o 3 výše uvedených částech. Pokusíme se najít ukázky, u kterých jsou pravděpodobně podrobnosti dostupné ze všech komponent, i když se vzorkování projeví v některém z nich. Ty jsou uvedené jako "navrhované" ukázky.

![Výsledky hledání](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Profiler a Snapshot Debugger

[Application Insights Profiler](./profiler.md) nebo [program Snapshot Debugger](snapshot-debugger.md) vám pomůžou diagnostikovat problémy s výkonem a selháním na úrovni kódu. Díky tomuto prostředí můžete zobrazit trasování a snímky profileru z jakékoli komponenty jediným kliknutím.

Pokud nemůžete získat funkční profil, kontaktujte prosím **serviceprofilerhelp \@ Microsoft.com**

Pokud se vám nepovedlo Snapshot Debugger pracovat, kontaktujte prosím **snapshothelp \@ Microsoft.com**

![Integrace profileru](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>Časté otázky

*Zobrazuje se v grafu jedna komponenta a ostatní se zobrazují jenom jako externí závislosti bez jakýchkoli podrobností o tom, co se v těchto součástech stalo.*

Potenciální důvody:

* Jsou ostatní komponenty instrumentované pomocí Application Insights?
* Používají nejnovější stabilní sadu Application Insights SDK?
* Pokud jsou tyto komponenty oddělené Application Insights prostředky, máte požadovaný [přístup](resources-roles-access-control.md) , pokud máte přístup a komponenty jsou instrumentované s nejnovějšími Application Insights SDK, dejte nám vědět prostřednictvím horního kanálu zpětné vazby.

*Zobrazuje se pro závislosti duplicitní řádky. Je to očekávané?*

V tuto chvíli se zobrazuje volání odchozí závislosti oddělené od příchozího požadavku. Obvykle jsou dvě volání shodná s pouze hodnotou trvání odlišnou vzhledem k síťovému zpoždění. Úvodní ikona a různé styly pruhů trvání mezi nimi můžou rozlišovat. Je tato prezentace dat matoucí? Sdělte nám svůj názor!

*Kolik hodin se v různých instancích komponent mění?*

Časové osy se upravují pro časově naklonění hodin v transakčním grafu. Přesná časová razítka můžete zobrazit v podokně podrobností nebo pomocí analýzy.

*Proč v novém prostředí chybí většina dotazů souvisejících položek?*

Toto chování je úmyslné. Všechny související položky napříč všemi komponentami jsou již na levé straně (v horních a dolních částech) k dispozici. Nové prostředí obsahuje dvě související položky, které levá strana nepokrývá: všechny telemetrie od pěti minut před a po této události a časovou osu uživatele.

*V prostředí diagnostiky transakcí se při použití Application Insights JavaScript SDK zobrazuje více událostí, než se očekávalo. Existuje způsob, jak zobrazit méně událostí na transakci?*

Prostředí pro diagnostiku transakcí zobrazuje veškerou telemetrii v rámci [jedné operace](correlation.md#data-model-for-telemetry-correlation) , která sdílí [ID operace](data-model-context.md#operation-id). Ve výchozím nastavení Application Insights SDK pro JavaScript vytvoří novou operaci pro každé jedinečné zobrazení stránky. V aplikaci s jednou stránkou (SPA) bude vygenerována pouze jedna událost zobrazení stránky a pro všechny generované telemetrie bude použito ID jediné operace, což může způsobit, že bude mnoho událostí koreluje se stejnou operací. V těchto scénářích můžete pomocí automatického sledování směrování automaticky vytvořit nové operace pro navigaci v aplikaci s jednou stránkou. Je nutné zapnout [enableAutoRouteTracking](javascript.md#single-page-applications) , aby zobrazení stránky bylo vygenerováno při každé aktualizaci trasy adresy URL (zobrazení logických stránek). Pokud chcete ručně aktualizovat ID operace, můžete to provést voláním `appInsights.properties.context.telemetryTrace.traceID = Microsoft.ApplicationInsights.Telemetry.Util.generateW3CId()` . Ruční aktivací události PageView dojde také k resetování ID operace.
