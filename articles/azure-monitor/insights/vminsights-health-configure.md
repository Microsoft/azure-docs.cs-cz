---
title: Konfigurace monitorování v Azure Monitor pro virtuální počítače stav hosta (Preview)
description: Popisuje, jak upravit výchozí monitorování pro Azure Monitor pro virtuální počítače stav hosta (Preview) pomocí Azure Portal.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/14/2020
ms.openlocfilehash: 427bdec2b5e5ab14d566375d5ad8f9da9dc3e81b
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505592"
---
# <a name="configure-monitoring-in-azure-monitor-for-vms-guest-health-preview"></a>Konfigurace monitorování v Azure Monitor pro virtuální počítače stav hosta (Preview)
Azure Monitor pro virtuální počítače stav hosta umožňuje zobrazit stav virtuálního počítače podle definice sady měření výkonu, které jsou odebírány v pravidelných intervalech. Tento článek popisuje, jak můžete upravit výchozí monitorování pomocí Azure Portal. Popisuje také základní koncepty monitorování požadovaných ke [konfiguraci monitorování pomocí pravidla shromažďování dat](vminsights-health-configure-dcr.md).

## <a name="open-monitor-configuration"></a>Otevřít konfiguraci monitorování
Otevřete okno Konfigurace monitorování Azure Portal tím, že vyberete monitor a pak kartu **Konfigurace** .

[![Konfigurace podrobností monitorování](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)

## <a name="enable-or-disable-monitors"></a>Povolit nebo zakázat monitory
Monitor jednotky i agregované monitory mají nastavení **stavu monitorování stavu** , které umožňuje povolit nebo zakázat monitorování. Když je povolené monitorování, zobrazí se jeho stav a použije se k nastavení stavu virtuálního počítače. Pokud je monitorování zakázané, jeho stav se nepočítá a nepoužívá se k nastavení stavu virtuálního počítače.

| Nastavení | Popis |
|:---|:---|
| Povoleno | Monitorování je povoleno bez ohledu na nastavení jeho nadřazeného objektu. |
| Zakázáno | Monitorování je zakázáno bez ohledu na nastavení jeho nadřazeného prvku. |
| Stejné jako nadřazené | Monitor bude povolen nebo zakázán v závislosti na nastavení jeho nadřazeného prvku. |

Pokud je monitorování zakázané, zobrazí se všechna kritéria, jak je znázorněno v následujícím příkladu.

![Zakázané monitorování](media/vminsights-health-configure/disabled-monitor.png)


> [!NOTE]
> Pokud je nadřazené monitorování zakázané, pak se zakázané i podřízené monitory. Pokud povolíte podřízené monitorování explicitně, bude povolen i nadřazený, ale jeho stav konfigurace zůstane stejný. V takovém případě se v nadřazeném monitorování zobrazí následující zpráva.
>
> *Stav nakonfigurovaného monitorování je nepovolený, ale stav se neodráží. Důvodem je to, že se buď našíří nakonfigurované změny, nebo některé z jejích podřízených monitorování byly explicitně povoleny.*

## <a name="enable-or-disable-virtual-machine"></a>Povolit nebo zakázat virtuální počítač
Monitorování virtuálního počítače můžete zakázat pro dočasné zastavení všech monitorování. Monitorování virtuálního počítače můžete zakázat například při provádění údržby.

| Nastavení | Popis |
|:---|:---|
| Povoleno  | Zobrazí se stav počítače. |
| Zakázáno | Stav počítače se zobrazuje jako **zakázaný**. Výstrahy nejsou vytvořeny. |

## <a name="health-state-logic"></a>Logika stavu
Logika stavu pro monitor jednotky definuje kritéria pro nastavení jeho stavu. Můžete určit, kolik stavů monitorování má, a prahovou hodnotu pro to, jak se počítá jednotlivé stavy.

![Ukázková kritéria stavu](media/vminsights-health-configure/sample-health-criteria.png)

Agregované monitory neurčují žádnou logiku stavu. Jejich stav je nastaven pomocí monitorování jednotek podle jejich souhrnu stavu.

Monitorování jednotek má každý ze dvou nebo tří stavů. Každá z nich bude mít vždy dobrý stav a volitelně také stav varování, kritický stav nebo obojí. Upozornění a kritická stav každé z nich vyžadují jedinečná kritéria, která definují, kdy je monitorování nastaveno na tento stav. Dobrý stav nemá kritéria, protože tento stav je nastaven, když kritéria pro ostatní stavy nejsou splněna.

V následujícím příkladu je využití procesoru nastaveno na následující stav:

- Kritická, pokud je větší než 90%.
- Upozornění, pokud je větší než nebo rovno 80%.
- V pořádku, pokud je v 80%. To se předpokládá, protože se jedná o podmínku, kterou neplatí ani jedna z ostatních podmínek.

## <a name="next-steps"></a>Další kroky

- [Nakonfigurujte monitorování ve velkém měřítku pomocí pravidel shromažďování dat.](vminsights-health-configure-dcr.md)