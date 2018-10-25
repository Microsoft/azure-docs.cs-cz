---
title: Skupina prostředků Azure monitoru Insights | Dokumentace Microsoftu
description: Pochopení stavu a výkonu vašich distribuovaných aplikací a služeb na úrovni skupiny prostředků pomocí Azure monitoru
services: azure-monitor
author: NumberByColors
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: 4b24ab406cc9cdbc3ce03ee203ec034843d5630a
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986419"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Sledování skupiny prostředků pomocí Azure monitoru (preview)

Moderní aplikace jsou často složité a vysoce distribuované mnoho samostatných částí spolupracují na poskytování služby. Uvědomili si tato složitost, Azure Monitor poskytuje monitorování přehledy pro skupiny prostředků. To usnadňuje posuzujte a Diagnostikujte problémy jednotlivé prostředky dojde při nabízení kontext v souvislosti s stav a výkon skupiny prostředků&mdash;a vaše aplikace&mdash;jako celek.

## <a name="access-insights-for-resource-groups"></a>Získat přehled pro skupiny prostředků

1. Vyberte **skupiny prostředků** v levém navigačním panelu.
2. Vyberte si jednu ze svých skupin prostředků, které chcete prozkoumat. (Pokud máte velký počet skupin prostředků filtrování podle předplatného může v některých případech být užitečné.)
3. Přehledy pro skupinu prostředků, klikněte na tlačítko **Insights** v levé nabídce libovolná skupina prostředků.

![Snímek obrazovky stránky s přehledem insights skupiny prostředků](.\media\resource-group-insights\0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>Prostředky s aktivní výstrahy a problémy se stavem

Na stránce s přehledem ukazuje, kolik upozornění mají aktivován a jsou stále aktivní, spolu s aktuálním Azure Resource Health každého prostředku. Společně tyto informace vám umožňují rychle odhalovat případné problémy všechny prostředky, ke které dochází k problémům s. Výstrahy umožňují zjistit problémy v kódu a jak jste nakonfigurovali vaší infrastruktury. Azure Resource Health povrchy problém s platformě Azure jako takové, které nejsou specifické pro jednotlivé aplikace.

![Snímek obrazovky s Azure Resource Health podokno](.\media\resource-group-insights\0002-overview.png)

### <a name="azure-resource-health"></a>Azure Resource Health

Chcete-li zobrazit stav prostředku Azure, zkontrolujte **zobrazit Azure Resource Health** pole výše v tabulce. Ve výchozím nastavení stránka rychle načíst nápovědy je tento sloupec skrytý.

![Snímek obrazovky s grafem stavu prostředků přidán](.\media\resource-group-insights\0003-overview.png)

Ve výchozím nastavení prostředky jsou seskupené podle vrstvy aplikace a typ prostředku. **Vrstva aplikací** je jednoduché kategorizace typů prostředků, která existuje pouze v rámci kontextu stránka s přehledem insights skupiny prostředků. Existují typy prostředků související s kódem aplikace, výpočetní infrastruktura, sítě, úložiště a databáze. Nástroje pro správu získat své vlastní aplikace vrstvy a každý prostředek je zařazený do kategorie jako patřící do **jiných** vrstvy aplikace. Toto seskupení můžete zjistit na přehledem jaké subsystémy aplikace jsou v pořádku a není v pořádku.

## <a name="diagnose-issues-in-your-resource-group"></a>Diagnostikujte problémy ve vaší skupině prostředků

Poskytuje několik dalších nástrojů, které jsou při diagnostice problémů s rozsahem insights stránce skupiny prostředků

   |         |          |
   | ---------------- |:-----|
   | [**Výstrahy**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)      |  Zobrazit, vytvořit a spravovat upozornění. |
   | [**Metriky**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) | Vizualizovat a zkoumat metriky na základě data.    |
   | [**Protokoly aktivit**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) | Odběr událostí na úrovni, ke kterým došlo v Azure.  |
   | [**Mapa aplikace**](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) | Přejděte topologie distribuované aplikace, která bude identifikovat kritické body výkonu nebo aktivních bodů selhání. |

## <a name="failures-and-performance"></a>Chyby a výkonu

Co když jste si všimli vaše aplikace běží pomalu nebo uživatele informovali o chybách? Je časově prohledávat všechny vaše prostředky k izolování problémů.

**Výkonu** a **selhání** karty tento proces zjednodušit tím, že spojuje výkonu a selhání zobrazení diagnostiky pro mnoho běžných typů prostředků.

Většina typů prostředků se otevře šablony Galerie Azure Monitor sešitu. Je možné přizpůsobit každý sešit, který vytvoříte, uložené, sdílené s týmem a znovu používané v budoucnu k diagnostice podobné problémy.

### <a name="investigate-failures"></a>Prověřit chyby

K otestování na výběr karty selhání **selhání** pod **prošetření** v levé nabídce.

Na panelu nabídky na levé straně se změní po provedení výběru nabízí nové možnosti.

![Snímek obrazovky selhání podokně s přehledem](.\media\resource-group-insights\00004-failures.png)

Při výběru služby App Service, zobrazí se šablony Galerie Azure Monitor sešitu.

![Snímek obrazovky Galerie aplikací sešitu](.\media\resource-group-insights\0005-failure-insights-workbook.png)

Výběr šablony pro Insights selhání otevře sešit.

![Snímek obrazovky se sestava selhání](.\media\resource-group-insights\0006-failure-visual.png)

Můžete vybrat všechny řádky. Výběr se pak zobrazí v zobrazení grafické podrobnosti.

![Snímek obrazovky se podrobnosti o chybě](.\media\resource-group-insights\0007-failure-details.png)

Sešity abstrakci obtížné práci při vytváření vlastní sestavy a vizualizace do formátu snadné použití. Když někteří uživatelé mohou pouze chcete upravit předem připravených parametry, sešity jsou plně přizpůsobitelné.

Chcete-li získat přehled o tom interně funkci tento sešit, vyberte **upravit** v horním panelu.

![Snímek obrazovky s možností další úpravy](.\media\resource-group-insights\0008-failure-edit.png)

Řadu **upravit** téměř různé prvky sešitu se zobrazí pole. Vyberte **upravit** pole pod tabulkou operací.

![Snímek obrazovky textových polí](.\media\resource-group-insights\0009-failure-edit-graph.png)

Zobrazí se dotaz Log Analytics, která řídí vizualizaci s tabulkou.

 ![Snímek obrazovky okna dotazu log analytics](.\media\resource-group-insights\0010-failure-edit-query.png)

Můžete upravit dotaz přímo. Nebo můžete použít jako referenci a vypůjčit z něj při návrhu vlastní parametry sešitu.

### <a name="investigate-performance"></a>Vyšetřování výkonu

Výkon nabízí svůj vlastní galerie sešity. Pro službu App Service nabízí předem připravených sešitu výkonu aplikace následujícím způsobem:

 ![Snímek obrazovky zobrazení výkonu](.\media\resource-group-insights\0011-performance.png)

V takovém případě Pokud zvolíte možnost úpravy uvidíte, že tuto sadu vizualizace používá technologii Azure monitorování metrik.

 ![Snímek obrazovky zobrazení výkonu s metrikami Azure](.\media\resource-group-insights\0012-performance-metrics.png)

## <a name="troubleshooting"></a>Řešení potíží

### <a name="enabling-access-to-alerts"></a>Povolení přístupu k upozornění

Výstrahy zobrazíte ve službě Azure Monitor pro skupiny prostředků, někdo s roli vlastníka nebo přispěvatele pro tento odběr musí otevřete Azure Monitor pro skupiny prostředků pro libovolnou skupinu prostředků v předplatném. Tato možnost umožní všem uživatelům s oprávněním ke čtení, aby se zobrazovaly výstrahy ve službě Azure Monitor pro skupiny prostředků pro všechny skupiny prostředků v předplatném. Pokud budete mít roli vlastníka nebo přispěvatele, aktualizujte tuto stránku za pár minut.

Azure Monitor pro skupiny prostředků závisí na systému pro správu výstrah monitorování Azure načíst stav výstrahy. Pro každou skupinu prostředků a předplatném není ve výchozím nastavení nakonfigurovaná Správa výstrah a lze povolit pouze uživatelem s rolí přispěvatele nebo vlastníka. Může to být buď povoleno:
* Otevřete Azure Monitor pro skupiny prostředků pro libovolnou skupinu prostředků v předplatném.
* Nebo tak, že přejdete do předplatného, kliknutím na **poskytovatelů prostředků**, pak levým na **zaregistrujte Alerts.Management**.

## <a name="next-steps"></a>Další postup

- [Azure Monitor sešity](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)
- [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Azure Monitor výstrah](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)
