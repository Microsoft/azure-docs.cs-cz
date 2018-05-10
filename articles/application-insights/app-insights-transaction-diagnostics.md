---
title: Azure Application Insights transakce Diagnostics | Microsoft Docs
description: Rozhraní Application Insights začátku do konce transakce diagnostics
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/19/2018
ms.author: mbullwin;sdash
ms.openlocfilehash: 7a4e4f74c02358fc117e0a66977ee3f0aef5b1dd
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Jednotná transakce mezi komponenty diagnostiky

*Toto prostředí je aktuálně ve verzi preview a nahradí existující okna diagnostiky pro žádosti na straně serveru, závislosti a výjimek.*

Ve verzi preview zavádí nové prostředí jednotná diagnostiky, které automaticky korelaci telemetrických dat na straně serveru z pro všechny součásti Application Insights monitorovat do jednoho zobrazení. Pokud máte několik prostředků s klíčů instrumentace samostatné nezáleží. Application Insights zjistí základní relace a umožňuje snadno diagnostikovat součást aplikace, závislostí nebo výjimka, která způsobila, že transakce zpomalení nebo selhání.

## <a name="what-is-a-component"></a>Co je součástí?

Součásti jsou nezávisle nasadit částí aplikace distribuované nebo mikroslužeb. Operace a vývojáři týmy mají viditelnost na úrovni kódu nebo o přístup k telemetrii vygenerovanou tyto součásti aplikace.

* Komponenty se liší od "zjištěnou" externí závislosti, jako je například SQL, EventHub atd, které váš tým nebo organizace nemusí mít přístup k (kód nebo telemetrie).
* Součásti spustit na libovolný počet instancí serveru, role nebo kontejnerů.
* Součástí může být samostatné klíčů instrumentace Application Insights (i v případě předplatných se liší) nebo různé role zprávy jeden klíč instrumentace Application Insights. Nové prostředí obsahuje podrobnosti pro všechny součásti, bez ohledu na to, jak se již byly vytvořeny.

> [!NOTE]
> * **Chybí odkazy související položku?** Všechny telemetrická data týkající se žádosti na straně serveru, závislosti a výjimky jsou [horní](#cross-component-transaction-chart) a [dolní](#all-telemetry-related-to-the-selected-component-operation) části na levé straně. 
> * [Horní](#cross-component-transaction-chart) části korelaci transakce pro všechny součásti. Nejlepších výsledků dosáhnete zajistěte, aby že všechny součásti jsou vybaveny s nejnovější stabilní sady SDK Application Insights. Pokud existují různé prostředky Application Insights, ujistěte se, že máte příslušná oprávnění k zobrazení jejich telemetrie.
> * [Dolní](#all-telemetry-related-to-the-selected-component-operation) části jsou zobrazeny na levé straně **všechny** včetně trasování a události související s telemetrií na žádost z vybrané součásti.

## <a name="enable-transaction-diagnostics-experience"></a>Povolení možnosti diagnostiky transakce
Povolit "Unified podrobnosti: E2E transakce diagnostiky" z [seznamu verze Preview](app-insights-previews.md)

![Povolit náhled](media/app-insights-e2eTxn-diagnostics/previews.png)

Tato předběžná verze je aktuálně dostupné pro žádosti na straně serveru, závislosti a výjimek. Dostanete na nové prostředí z **výsledky hledání**, **výkonu**, nebo **selhání** rychlou kontrolu prostředí. Ve verzi preview nahrazuje odpovídající okna classic podrobnosti.

![Vzorků výkonu](media/app-insights-e2eTxn-diagnostics/performanceSamplesClickThrough.png)

## <a name="transaction-diagnostics-experience"></a>Transakce diagnostiky prostředí
Toto zobrazení má tři částí klíče: transakce mezi komponenty graf, čas pořadí seznam všech telemetrie z určité součásti operaci a v podokně podrobností pro libovolnou položku vybrané telemetrii na levé straně.

![Klíčovými částmi](media/app-insights-e2eTxn-diagnostics/3partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Mezi součásti transakce grafu

Tento graf poskytuje časovou osu s vodorovné pruhy po dobu trvání požadavky a závislosti v rámci komponenty. Jakékoli výjimky, které byly shromážděny jsou označeny také na časové ose.

* Horní řádek na tento graf představuje vstupní bod příchozí žádost o první součást volat v této transakci. Doba trvání je celkový čas potřebný k dokončení transakce.
* Volání externí závislosti jsou jednoduché-sbalitelné řádky s ikony představující typ závislosti.
* Volání na jiné součásti jsou sbalitelné řádků. Každý řádek odpovídá konkrétní operaci volá na komponentu.
* Ve výchozím nastavení požadavku, závislostí nebo výjimka, ke které jste zpočátku vybrali se zobrazí v grafu.
* Vyberte libovolný řádek zobrazíte jeho [podrobnosti na pravé straně](#details-of-the-selected-telemetry). 

> [!NOTE]
Volání na jiné součásti mají dva řádky: jeden řádek představuje odchozí volání (závislostí) z komponenty volajícího a druhý řádek odpovídá příchozí požadavek na komponentu názvem. Úvodní ikonu a odlišné stylů pruhů trvání pomůže rozlišit mezi nimi.

## <a name="all-telemetry-related-to-the-selected-component-operation"></a>Všechny telemetrická data související s vybranou operaci součásti

Všechny vybraný řádek v grafu mezi komponenty transakce se týká operace vyvolané v jednotlivých součástí. Tato operace vybrané součásti se projeví v názvu v dolní části. Otevřete v této části zobrazíte ploché čas posloupnost všechny telemetrická data týkající se této konkrétní operaci. Můžete vybrat libovolnou položku telemetrie v tomto seznamu zobrazíte odpovídající [podrobnosti na pravé straně](#details-of-the-selected-telemetry).

![Čas pořadí všechny telemetrie](media/app-insights-e2eTxn-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Podrobnosti vybrané telemetrie

V tomto podokně zobrazí podrobnosti vybraných položek ze dvou oddílech na levé straně. "Zobrazit všechny" uvádí všechny standardní atributy, které byly shromážděny. Žádné vlastní atributy jsou uvedené samostatně pod standardní sady. Kliknutím na "otevřete profileru trasování" nebo "otevřete ladění snímek" pro úrovně diagnostiky kódu v odpovídající podokna podrobností.

![Detail výjimky](media/app-insights-e2eTxn-diagnostics/exceptiondetail.png)

## <a name="profiler-and-snapshot-debugger"></a>Ladicí program profileru a snímků

[Application Insights profileru](app-insights-profiler.md) nebo [ladicí program snímku](app-insights-snapshot-debugger.md) pomohou s diagnostikou kód úroveň výkonu a selhání problémy. Pomocí této možnosti můžete zobrazit profileru trasování nebo klikněte na tlačítko snímky z libovolné součásti se jeden.

Pokud nelze získat profileru práce, kontaktujte prosím **serviceprofilerhelp@microsoft.com**

Pokud nelze získat ladicí program snímku práce, kontaktujte prosím **snapshothelp@microsoft.com**

![Integrace ladicí program](media/app-insights-e2eTxn-diagnostics/debugSnapshot.png)

## <a name="faq"></a>Nejčastější dotazy

*V tématu jedinou komponentu na graf a ostatní se zobrazuje pouze jako vnější závislosti bez žádné podrobnosti co se stalo v rámci těchto součástí.*

Možné důvody:

* Jsou ostatní součásti vybaveny s Application Insights?
* Používají se nejnovější stabilní Application Insights SDK?
* Pokud tyto součásti jsou samostatné prostředky Application Insights, máte požadovaný přístup k jejich telemetrie?

Pokud máte přístup a součásti jsou vybaveny s nejnovější sadách Application Insights SDK, dejte nám vědět prostřednictvím kanálu horní pravé zpětnou vazbu.

*Je nutné pouze externí závislosti. Měli zajímají tato verze preview?*

Ano. Nové prostředí kombinuje všechny související serverové telemetrie do jednoho zobrazení. Starší oken podrobností budou nahrazeny toto prostředí v budoucnu, takže vyzkoušejte ji a sdělte nám svůj názor. Zde je, jak vypadá pro transakci jedinou komponentu:

![Jedna součást prostředí](media/app-insights-e2eTxn-diagnostics/singleComponent.png)

*Zobrazuje, duplicitní řádky pro závislosti. Je to očekávané?*

V tuto chvíli jsme zobrazují volání odchozí závislostí odděleně od příchozí žádosti. Obvykle dva volání vypadají stejně s pouze hodnota doby trvání probíhá jiný kvůli sítě odezvy. Úvodní ikonu a odlišné stylů pruhů trvání pomůže rozlišit mezi nimi. Tato prezentace dat je matoucí? Sdělte nám svůj názor!

*Co se chystáte hodiny zkosí napříč instancemi různé součásti?*

Časové osy jsou přizpůsobené pro zkosí hodiny v grafu transakce. Můžete zobrazit přesný časová razítka v podokně podrobností nebo pomocí analýzy.

*Proč na nové prostředí chybí většinu dotazů související položky?*

Toto chování je úmyslné. Všechny související položky pro všechny součásti, jsou již k dispozici na levé straně (horní a dolní části). Nové prostředí má dvě související položky, které na levé straně nezahrnuje: všechny telemetrie z pěti minut před a po této události a časový horizont uživatele.

*Proč na nové prostředí nemá funkci I kontakt máte rádi v starší okna?*

Sdělte nám svůj názor! Chceme adres vašim požadavkům, než toto prostředí je všeobecně dostupná, po kterém starší zobrazení se již nepoužívá. Teď můžete zakázat ve verzi preview, přejdete na starší okna.

## <a name="known-issues"></a>Známé problémy

* Selhání ukázky z aplikace mapy propojit starší okna podrobností.
* Na základě autocluster insights ve výsledcích hledání propojit starší okna podrobností.
* Integraci pracovních položek není k dispozici v nové prostředí.
