---
title: Přehledy skupin prostředků Azure Monitor | Dokumenty společnosti Microsoft
description: Seznamte se se stavem a výkonem distribuovaných aplikací a služeb na úrovni skupiny prostředků pomocí Azure Monitoru
ms.subservice: ''
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 6d97e40bf2bf2298fb53609621db8ff2c6f1038f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663534"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Monitorování skupin prostředků pomocí Azure Monitoru (preview)

Moderní aplikace jsou často složité a vysoce distribuované s mnoha diskrétními součástmi, které spolupracují na poskytování služby. Azure Monitor rozpozná tuto složitost a poskytuje přehledy monitorování pro skupiny prostředků. To usnadňuje třídění a diagnostiku problémů, se kterými se jednotlivé prostředky setkávají, a&mdash;zároveň&mdash;nabízí kontext, pokud jde o stav a výkon skupiny prostředků a aplikace jako celku.

## <a name="access-insights-for-resource-groups"></a>Přístup k přehledům pro skupiny prostředků

1. Na levém navigačním panelu vyberte **skupiny prostředků.**
2. Vyberte jednu ze skupin prostředků, kterou chcete prozkoumat. (Pokud máte velký počet skupin prostředků filtrování podle předplatného může být někdy užitečné.)
3. Pokud chcete získat přístup k přehledům pro skupinu prostředků, klikněte v levé nabídce libovolné skupiny prostředků na **Přehledy.**

![Snímek obrazovky s přehledem přehledů skupiny prostředků](./media/resource-group-insights/0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>Zdroje s aktivními výstrahami a zdravotními problémy

Stránka s přehledem ukazuje, kolik výstrah bylo aktivováno a jsou stále aktivní, spolu s aktuální azure resource health každého prostředku. Tyto informace vám společně mohou pomoci rychle rozpoznat všechny prostředky, se kterými dochází k potížím. Výstrahy vám pomohou zjistit problémy v kódu a způsob konfigurace infrastruktury. Povrchy Azure Resource Health mají problém se samotnou platformou Azure, která není specifická pro vaše jednotlivé aplikace.

![Snímek obrazovky s podoknem Stav prostředků Azure](./media/resource-group-insights/0002-overview.png)

### <a name="azure-resource-health"></a>Azure Resource Health

Pokud chcete zobrazit Stav prostředků Azure, zaškrtněte **políčko Zobrazit stav prostředků Azure** nad tabulkou. Tento sloupec je ve výchozím nastavení skrytý, aby se stránka rychle načetla.

![Snímek obrazovky s přidaným grafem stavu prostředků](./media/resource-group-insights/0003-overview.png)

Ve výchozím nastavení jsou prostředky seskupeny podle vrstvy aplikace a typu prostředku. **Vrstva aplikace** je jednoduchá kategorizace typů prostředků, která existuje pouze v kontextu stránky přehledu přehledu skupiny prostředků. Existují typy prostředků související s kódem aplikace, výpočetní infrastrukturou, sítí, úložištěm + databázemi. Nástroje pro správu získají vlastní vrstvy aplikací a každý jiný prostředek je kategorizován jako patřící do vrstvy **Jiné** aplikace. Toto seskupení vám pomůže zobrazit na první pohled, jaké subsystémy vaší aplikace jsou v pořádku a nejsou v pořádku.

## <a name="diagnose-issues-in-your-resource-group"></a>Diagnostika problémů ve skupině prostředků

Stránka Přehledy skupiny prostředků obsahuje několik dalších nástrojů s vymezenou skupinou, které vám pomohou diagnostikovat problémy

   |         |          |
   | ---------------- |:-----|
   | [**Výstrahy**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)      |  Zobrazujte, vytvářejte a spravujte výstrahy. |
   | [**Metriky**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) | Vizualizujte a prozkoumejte data založená na metrikách.    |
   | [**Protokoly aktivit**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) | Události na úrovni předplatného, ke kterým došlo v Azure.  |
   | [**Mapa aplikace**](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) | Procházejte topologii distribuované aplikace a identifikujte kritická místa výkonu nebo hotspoty selhání. |

## <a name="failures-and-performance"></a>Selhání a výkon

Co když jste si všimli, že vaše aplikace běží pomalu, nebo uživatelé hlásili chyby? Je to časově náročné prohledávat všechny vaše zdroje izolovat problémy.

Karty **Výkon** a **selhání** zjednodušují tento proces spojením diagnostických zobrazení výkonu a selhání pro mnoho běžných typů prostředků.

Většina typů prostředků otevře galerii šablon sešitu Azure Monitor. Každý sešit, který vytvoříte, lze přizpůsobit, uložit, sdílet s týmem a v budoucnu znovu použít k diagnostice podobných problémů.

### <a name="investigate-failures"></a>Prošetření selhání

Chcete-li otestovat kartu Selhání, vyberte **chyby** v části **Prozkoumat** v levé nabídce.

Po výběru se změní panel nabídek na levé straně a nabídne vám nové možnosti.

![Snímek obrazovky podokna Přehled selhání](./media/resource-group-insights/00004-failures.png)

Když je služba App Service vybrána, zobrazí se galerie šablon sešitu Azure Monitor.

![Snímek obrazovky s galerií sešitů aplikací](./media/resource-group-insights/0005-failure-insights-workbook.png)

Výběrem šablony pro Přehledy selhání se sešit otevřete.

![Snímek obrazovky se zprávou o selhání](./media/resource-group-insights/0006-failure-visual.png)

Můžete vybrat libovolný z řádků. Výběr se pak zobrazí v grafickém zobrazení podrobností.

![Snímek obrazovky s podrobnostmi o selhání](./media/resource-group-insights/0007-failure-details.png)

Sešity abstrahují obtížnou práci při vytváření vlastních sestav a vizualizací do snadno spotřebního formátu. Zatímco někteří uživatelé mohou chtít pouze upravit předem sestavené parametry, sešity jsou zcela přizpůsobitelné.

Pokud chcete zjistit, jak tento sešit interně funguje, vhorní části vyberte **Upravit.**

![Snímek obrazovky s možností dalších úprav](./media/resource-group-insights/0008-failure-edit.png)

V blízkosti různých prvků sešitu se zobrazí řada polí **Pro úpravy.** Pod provozní tabulkou vyberte pole **Upravit.**

![Snímek obrazovky s editačními poli](./media/resource-group-insights/0009-failure-edit-graph.png)

To odhaluje základní dotaz protokolu, který řídí vizualizaci tabulky.

 ![Snímek obrazovky okna dotazu protokolu](./media/resource-group-insights/0010-failure-edit-query.png)

Dotaz můžete upravit přímo. Nebo jej můžete použít jako referenci a půjčit si z něj při navrhování vlastního parametrizovaného sešitu.

### <a name="investigate-performance"></a>Prozkoumat výkon

Performance nabízí vlastní galerii sešitů. Pro službu App Service nabízí předem sestavený sešit Výkonu aplikací následující zobrazení:

 ![Snímek obrazovky se zobrazením výkonu](./media/resource-group-insights/0011-performance.png)

V takovém případě pokud vyberete upravit uvidíte, že tato sada vizualizací je napájená metriky Azure Monitor.

 ![Snímek obrazovky s zobrazením výkonu s metrikami Azure](./media/resource-group-insights/0012-performance-metrics.png)

## <a name="troubleshooting"></a>Řešení potíží

### <a name="enabling-access-to-alerts"></a>Povolení přístupu k výstrahám

Chcete-li zobrazit výstrahy ve službě Azure Monitor pro skupiny prostředků, někdo s rolí vlastníka nebo přispěvatele pro toto předplatné musí otevřít Azure Monitor pro skupiny prostředků pro všechny skupiny prostředků v předplatném. To umožní všem uživatelům s přístupem pro čtení zobrazit výstrahy ve službě Azure Monitor pro skupiny prostředků pro všechny skupiny prostředků v předplatném. Pokud máte roli vlastníka nebo přispěvatele, aktualizujte tuto stránku během několika minut.

Azure Monitor pro skupiny prostředků spoléhá na systém správy výstrah monitoru Azure pro načtení stavu výstrah. Správa výstrah není ve výchozím nastavení nakonfigurována pro každou skupinu prostředků a předplatné a může ji povolit pouze uživatel s rolí vlastníka nebo přispěvatele. To může být povoleno buď:
* Otevření Azure Monitor pro skupiny prostředků pro všechny skupiny prostředků v předplatném.
* Nebo tak, že přejdete na předplatné, klepněte na **položku Zprostředkovatelé prostředků**a potom klepněte na tlačítko **Registrovat pro výstrahy.Správa**.

## <a name="next-steps"></a>Další kroky

- [Sešity Azure Monitor](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)
- [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Výstrahy monitorování Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)
