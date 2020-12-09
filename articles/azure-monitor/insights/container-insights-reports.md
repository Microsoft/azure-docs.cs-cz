---
title: Sestavy v Azure Monitor pro kontejnery
description: Popisuje sestavy, které jsou k dispozici pro analýzu dat shromažďovaných Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 3cc2f8fb9bfaa278ce06b4a8cd6d379397b7129a
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96907489"
---
# <a name="reports-in-azure-monitor-for-containers"></a>Sestavy v Azure Monitor pro kontejnery
Sestavy v Azure Monitor pro kontejnery jsou doporučeny pro předem připravené [sešity Azure](../platform/workbooks-overview.md). Tento článek popisuje různé dostupné sestavy a přístup k nim.

## <a name="viewing-reports"></a>Zobrazení sestav
Z nabídky **Azure monitor** v Azure Portal vyberte **kontejnery**. V části **monitorování** vyberte **přehledy** , zvolte konkrétní cluster a pak vyberte stránku **sestavy (Preview)** . 

[![Stránka sestavy](media/container-insights-reports/reports-page.png)](media/container-insights-reports/reports-page.png#lightbox)

## <a name="create-a-custom-workbook"></a>Vytvoření vlastního sešitu
Pokud chcete vytvořit vlastní sešit založený na některém z těchto sešitů, vyberte rozevírací seznam **Zobrazit sešity** a potom v dolní části rozevírací nabídky klikněte **na AKS Gallery** . Další informace o sešitech a používání šablon sešitu najdete v tématu [Azure monitor sešity](../platform/workbooks-overview.md) .

[![Galerie AKS](media/container-insights-reports/aks-gallery.png)](media/container-insights-reports/aks-gallery.png#lightbox)

## <a name="node-workbooks"></a>Sešity uzlů

- **Kapacita disku**: grafy interaktivních použití disku pro každý disk prezentovaný uzlu v kontejneru podle následujících perspektiv:

    - Využití disku v procentech pro všechny disky.
    - Volné místo na disku pro všechny disky.
    - Mřížka zobrazující disk jednotlivých uzlů, její procento využitého místa, trend procenta využitého místa, volné místo na disku (GiB) a trend volného místa na disku (GiB). Po výběru řádku v tabulce se pod řádkem zobrazí procentuální hodnota využitého místa a volné místo na disku (GiB).

- **Disk v/** v: grafy využití interaktivního disku pro každý disk prezentovaný uzlu v kontejneru podle následujících perspektiv:

    - Počet vstupně-výstupních operací na disku shrnutých na všech discích, a to čtením bajtů za sekundu, zápisem bajtů/s a čtením a zápisem v bajtech/s.
    - Osm grafů výkonu ukazuje klíčové ukazatele výkonu, které umožňují měřit a identifikovat kritické body v/v disku.

- **GPU**: interaktivní grafy použití GPU pro každý uzel clusteru Kubernetes s podporou GPU.

## <a name="resource-monitoring-workbooks"></a>Sešity pro monitorování prostředků

- **Nasazení**: stav nasazení & vodorovně pod autoscaleer (hPa), včetně vlastního hPa. 
  
- **Podrobnosti o úlohách**: interaktivní grafy znázorňující statistiku výkonu úloh pro obor názvů. Obsahuje několik karet:

  - Přehled využití CPU a paměti nástrojem POD.
  - Stav POD/kontejner zobrazený POD trendem restartování, trendem restartování kontejneru a stavem kontejneru pro lusky
  - Kubernetes události zobrazující Souhrn událostí pro kontroler.

- **Kubelet**: obsahuje dvě mřížky, které znázorňují klíčové statistiky uzlu:

    - Přehled podle mřížky uzlů shrnuje celkovou operaci, celkový počet chyb a úspěšné operace podle procenta a trendu pro každý uzel.
    - Přehled podle souhrnu typu operací pro každou operaci: celková operace, celkový počet chyb a úspěšné operace podle procenta a trendu.
## <a name="billing-workbooks"></a>Fakturační sešity

- **Využití dat**: pomáhá vizualizovat zdroj dat bez nutnosti sestavovat vlastní knihovnu dotazů z toho, co sdílíme v naší dokumentaci. V tomto sešitu jsou grafy, pomocí kterých můžete zobrazit fakturovatelná data z těchto perspektiv jako:

  - Celková fakturovatelná data ingestovaná v GB podle řešení
  - Fakturovatelné data ingestovaná protokoly kontejnerů (protokoly aplikací)
  - Fakturovatelné protokoly kontejneru data ingestovaná podle oboru názvů Kubernetes
  - Fakturovatelné protokoly kontejnerů data ingestovaná oddělená názvem clusteru
  - Fakturovatelné data protokolu kontejneru podle položky logsource
  - Fakturovatelné diagnostická data ingestovaná v protokolech diagnostických hlavních uzlů

## <a name="networking-workbooks"></a>Síťové sešity

- **Konfigurace npm**: monitorování konfigurací vaší sítě, které jsou nakonfigurované pomocí Správce síťových zásad (npm).

  - Souhrnné informace o celkové složitosti konfigurace.
  - Zásady, pravidla a počty nastavené v průběhu času, což umožňuje získat přehled o vztahu mezi třemi a přidáním dimenze času pro ladění konfigurace.
  - Počet položek ve všech IPSets a každé IPSet.
  - Nejhorší a střední případový výkon na uzel pro přidání součástí do konfigurace sítě.

- **Síť**: interaktivní grafy využití sítě pro každý síťový adaptér uzlu a Mřížka představuje klíčové ukazatele výkonu, které vám pomůžou změřit výkon síťových adaptérů.



## <a name="next-steps"></a>Další kroky

- Podrobnosti o sešitech v Azure Monitor najdete v tématu [Azure monitor sešity](../platform/workbooks-overview.md) .
