---
title: Přehled sešitů Azure Monitor
description: Zjednodušení složitých sestav pomocí předem sestavených a vlastních parametrizovaných sešitů
author: mrbullwinkle
manager: carmonm
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 29e675f3ae35df9211f58d45ad8450566d67a588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658230"
---
# <a name="azure-monitor-workbooks"></a>Sešity Azure Monitor

Sešity poskytují flexibilní plátno pro analýzu dat a vytváření bohatých vizuálních sestav v rámci portálu Azure. Umožňují vám využít více zdrojů dat z celého Azure a kombinovat je do jednotných interaktivních prostředí. 

## <a name="data-sources"></a>Zdroje dat

Sešity můžou v Azure zadávat dotazy na data z více zdrojů. Autoři sešitů mohou tato data transformovat tak, aby poskytovala přehled o dostupnosti, výkonu, využití a celkovém stavu základních součástí. Například analýza protokolů výkonu z virtuálních počítačů k identifikaci instancí s vysokou pamětí nebo s nedostatkem paměti a zobrazení výsledků jako mřížky v interaktivní sestavě.
  
Skutečnou silou sešitů je však schopnost kombinovat data z různorodých zdrojů v rámci jedné sestavy. To umožňuje vytváření složených zobrazení prostředků nebo spojení mezi prostředky umožňující bohatší data a přehledy, které by jinak nebylo možné.

Sešity jsou aktuálně kompatibilní s následujícími zdroji dat:

* [Protokoly](workbooks-data-sources.md#logs)
* [Metriky](workbooks-data-sources.md#metrics)
* [Azure Resource Graph](workbooks-data-sources.md#azure-resource-graph)
* [Upozornění (Preview)](workbooks-data-sources.md#alerts-preview)
* [Zdraví pracovního vytížení (preview)](workbooks-data-sources.md#workload-health-preview)
* [Azure Resource Health (preview)](workbooks-data-sources.md#azure-resource-health)
* [Azure Data Explorer (preview)](workbooks-data-sources.md#azure-data-explorer-preview)

## <a name="visualizations"></a>Vizualizace

Sešity poskytují bohatou sadu funkcí pro vizualizaci dat. Podrobné příklady jednotlivých typů vizualizací naleznete v následujících ukázkových odkazech:

* [Text](workbooks-visualizations.md#text)
* [Grafy](workbooks-visualizations.md#charts)
* [Mřížky](workbooks-visualizations.md#grids)
* [Dlaždice](workbooks-visualizations.md#tiles)
* [Stromy](workbooks-visualizations.md#trees)
* [Grafy](workbooks-visualizations.md#graphs)

![Příklad vizualizací sešitu](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>Začínáme

Chcete-li prozkoumat prostředí sešitů, nejprve přejděte na službu Azure Monitor. To lze provést zadáním **monitoru** do vyhledávacího pole na webu Azure Portal.

Pak vyberte **Sešity (náhled).**

![Snímek obrazovky s tlačítkem Náhled se šetamí zvýrazněným červeným rámečkem](./media/workbooks-overview/workbooks-preview.png)

### <a name="gallery"></a>Galerie

Tím přejdete do galerie sešitů:

![Snímek obrazovky se zobrazením galerie sešitů Azure Monitoru](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>Sešity versus šablony sešitů

_Sešit_ můžete zobrazit zeleně a řadu _šablon sešitů_ fialově. Šablony slouží jako kurátorské sestavy, které jsou určeny pro flexibilní opakované použití více uživateli a týmy. Otevřením šablony vytvoříte přechodný sešit naplněný obsahem šablony. 

Můžete upravit parametry sešitu založeného na šabloně a provést analýzu bez obav z porušení budoucího prostředí pro vytváření sestav pro kolegy. Pokud otevřete šablonu, proveďte některé úpravy a pak vyberete ikonu uložit, uložíte šablonu jako sešit, který by pak zobrazoval zeleně a nechal původní šablonu nedotčenou. 

Pod kapotou se šablony také liší od uložených sešitů. Uložením sešitu se vytvoří přidružený prostředek Azure Resource Manager, zatímco přechodný sešit vytvořený při pouhém otevření šablony nemá žádný jedinečný prostředek s ním přidružený. Další informace o spravovitu řízení přístupu v sešitech naleznete v [článku o řízení přístupu k sešitům](workbooks-access-control.md).

### <a name="exploring-a-workbook-template"></a>Procházení šablony sešitu

Výběrem **možnosti Analýza selhání aplikace** zobrazíte jednu z výchozích šablon sešitu aplikace.

![Snímek obrazovky se šablonou analýzy selhání aplikace](./media/workbooks-overview/failure-analysis.png)

Jak již bylo uvedeno dříve, otevření šablony vytvoří dočasný sešit, se kterým budete moci pracovat. Ve výchozím nastavení se sešit otevře v režimu čtení, který zobrazuje pouze informace pro zamýšlené prostředí analýzy, které bylo vytvořeno původním autorem šablony.

V případě tohoto konkrétního sešitu je prostředí interaktivní. Můžete upravit předplatné, cílené aplikace a časový rozsah dat, která chcete zobrazit. Jakmile provedete tyto výběry mřížka požadavků HTTP je také interaktivní, takže výběrem jednotlivého řádku se změní, jaká data jsou vykreslena ve dvou grafech v dolní části sestavy.

### <a name="editing-mode"></a>Režim úprav

Chcete-li pochopit, jak je tato šablona sešitu sestavena, je třeba zaměnit režim úprav výběrem **možnosti Upravit**. 

![Snímek obrazovky se šablonou analýzy selhání aplikace](./media/workbooks-overview/edit.png)

Po přepnutí do režimu úprav si všimnete, že se vpravo, které odpovídají jednotlivým aspektům sešitu, zobrazí řada polí **pro úpravy.**

![Snímek obrazovky s tlačítkem Upravit](./media/workbooks-overview/edit-mode.png)

Pokud vybereme tlačítko upravit okamžitě pod mřížkou dat požadavku, vidíme, že tato část našeho sešitu se skládá z dotazu Kusto proti datům z prostředku Application Insights.

![Snímek obrazovky s podkladovým dotazem Kusto](./media/workbooks-overview/kusto.png)

Kliknutím na ostatní tlačítka **pro úpravy** vpravo zobrazíte řadu základních součástí, které tvoří sešity, jako [jsou textová pole](workbooks-visualizations.md#text)založená na značkách , prvky [uživatelského](workbooks-parameters.md) rozhraní pro výběr parametrů a další [typy grafů/vizualizací](workbooks-visualizations.md). 

Prozkoumání předem sestavených šablon v režimu úprav a jejich úprava podle vašich potřeb a uložení vlastního sešitu je vynikající způsob, jak začít se učit, co je možné v sešitech Azure Monitoru.

## <a name="pinning-visualizations"></a>Připnutí vizualizací

Kroky textu, dotazu a metrik v sešitu lze připnout pomocí tlačítka pin u těchto položek, když je sešit v režimu pin, nebo pokud autor sešitu povolil nastavení pro tento prvek, aby se ikona špendlíku zviditelnila. 

Chcete-li získat přístup k režimu připnutí, klepněte na **tlačítko Upravit,** předejte režim úprav a v horní liště vyberte modrou ikonu špendlíku. Nad *polem Pro úpravy* jednotlivých částí sešitu se pak na pravé straně obrazovky zobrazí ikona jednotlivých kolíků.

![Zkušenosti s připnutím](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> Stav sešitu se uloží v době pinu a připnuté sešity na řídicím panelu se při změně podkladového sešitu neaktualizují. Chcete-li aktualizovat připnutý díl sešitu, budete muset tuto část odstranit a znovu připnout.

## <a name="dashboard-time-ranges"></a>Časové rozsahy řídicího panelu

Připnuté části dotazu sešitu budou respektovat časový rozsah řídicího panelu, pokud je připnutá položka nakonfigurována pro použití parametru *Časový rozsah.* Hodnota časového rozsahu řídicího panelu bude použita jako hodnota parametru časového rozsahu a jakákoli změna časového rozsahu řídicího panelu způsobí aktualizaci připnuté položky. Pokud připnutá část používá časový rozsah řídicího panelu, zobrazí se titulky připnuté aktualizace součásti, které zobrazí časový rozsah řídicího panelu při každé změně časového rozsahu. 

Připnuté součásti sešitu používající parametr časového rozsahu se navíc automaticky aktualizují rychlostí určenou časovým rozsahem řídicího panelu. Čas posledního spuštění dotazu se zobrazí v podtitulech připnuté části.

Pokud má připnutý krok explicitně nastavený časový rozsah (nepoužívá parametr časového rozsahu), bude tento časový rozsah vždy použit pro řídicí panel bez ohledu na nastavení řídicího panelu. Podtitul připnuté části nezobrazí časový rozsah řídicího panelu a dotaz se na řídicím panelu automaticky neaktualizuje. V titulku se zobrazí poslední spuštění dotazu.

> [!NOTE]
> Dotazy používající zdroj *slučovacích* dat nejsou aktuálně podporovány při připnutí na řídicí panely.

## <a name="sharing-workbook-templates"></a>Sdílení šablon sešitů

Jakmile začnete vytvářet vlastní šablony sešitu, můžete je chtít sdílet s širší komunitou. Další informace a další šablony, které nejsou součástí výchozího zobrazení galerie Azure Monitoru, najdete v našem [úložišti GitHub](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md). Pokud chcete procházet existující sešity, navštivte [knihovnu sešitů](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) na GitHubu.

## <a name="next-step"></a>Další krok

* [Začínáte](workbooks-visualizations.md) se učit další informace o sešitech, mnoho bohatých možností vizualizací.
* [Řízení](workbooks-access-control.md) a sdílení přístupu k prostředkům sešitu
