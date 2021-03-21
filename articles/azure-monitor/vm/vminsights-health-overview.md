---
title: Stav hosta pro virtuální počítače Insights (Preview)
description: Přehled funkce Health Insights v rámci virtuálních počítačů, včetně toho, jak můžete zobrazit stav virtuálních počítačů a přijímat výstrahy, když se virtuální počítač stává stavem není v pořádku.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/27/2020
ms.openlocfilehash: 2922ca4068531c45e6acad0ce54aa96624c6238e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102052122"
---
# <a name="vm-insights-guest-health-preview"></a>Stav hosta pro virtuální počítače Insights (Preview)
Stav hosta pro virtuální počítače Insights umožňuje zobrazit stav virtuálních počítačů na základě sady měření výkonu, které jsou v pravidelných intervalech odebírány z hostovaného operačního systému. Můžete rychle zjistit stav všech virtuálních počítačů v rámci předplatného nebo skupiny prostředků, přejít k podrobnostem o podrobném stavu konkrétního virtuálního počítače nebo ho aktivně upozornit, když se virtuální počítač neobjeví v pořádku. 

## <a name="enable-virtual-machine-health"></a>Povolit stav virtuálního počítače
Podrobnosti o povolení funkce stavu hosta a virtuálních počítačů pro registraci najdete v tématu [Povolení stavu hosta služby VM Insights (Preview)](vminsights-health-enable.md) .

## <a name="pricing"></a>Ceny
Pro funkci stavu hosta se neúčtují žádné přímé náklady, ale v pracovním prostoru Log Analytics existují náklady na ingestování a ukládání dat o stavu. Všechna data jsou uložená v tabulce *HealthStateChangeEvent* . Podrobnosti o cenových modelech a nákladech najdete v tématu [Správa využití a nákladů pomocí protokolů Azure monitor](../logs/manage-cost-storage.md) .

## <a name="view-virtual-machine-health"></a>Zobrazit stav virtuálního počítače
Sloupec **stavu virtuálního počítače hosta** na **stránce Začínáme** poskytuje rychlý přehled o stavu každého virtuálního počítače v konkrétním předplatném nebo skupině prostředků. Aktuální stav každého virtuálního počítače se zobrazí, zatímco ikony pro každou skupinu zobrazují počet virtuálních počítačů, které jsou aktuálně v jednotlivých stavech v dané skupině.

[![Stránka Začínáme se stavem virtuálního počítače hosta](media/vminsights-health-overview/get-started-page.png)](media/vminsights-health-overview/get-started-page.png#lightbox)


## <a name="monitors"></a>Monitory
Kliknutím na stav virtuálního počítače zobrazíte podrobný stav každého z jeho monitorování. Každé monitorování měří stav konkrétní součásti. Celkový stav virtuálního počítače je určen stavem jednotlivých monitorů. 

![Příklad monitorování](media/vminsights-health-overview/monitors.png)

V následující tabulce jsou uvedeny agregované a monitorované monitorování jednotek pro jednotlivé virtuální počítače. 

| Monitor | Typ | Description |
|:---|:---|:---|
| Využití procesoru | Jednotka | Procento využití procesoru. |
| Systémy souborů | Agregace | Agregovat stav všech systémů souborů na virtuálním počítači se systémem Linux. |
| Systém souborů  | Agregace | Stav každého jednotlivého systému souborů na virtuálním počítači se systémem Linux. Název monitorování je název systému souborů. |
| Volné místo | Jednotka | Procento volného místa v systému souborů. |
| Logické disky | Agregace | Agreguje stav všech logických disků na virtuálním počítači s Windows. |
| Logický disk  | Agregace | Stav každého jednotlivého logického disku na virtuálním počítači s Windows. Název monitoru je název disku. |
| Memory (Paměť) | Agregace | Agregovat stav paměti na virtuálním počítači. |
| Dostupná paměť | Jednotka | Dostupná megabajty na virtuálním počítači. |

## <a name="health-states"></a>Stav
Každé monitorování vyvzorkuje hodnoty v agentovi každou minutu a porovná vzorky hodnot s kritérii pro každý stav. Pokud jsou kritéria pro určitý stav pravdivá, bude monitorování nastaveno na tento stav. Pokud žádná z kritérií není pravdivá, je monitorování nastaveno na dobrý stav. Data se odesílají z agenta, aby se Azure Monitor každé tři minuty nebo hned, pokud dojde ke změně stavu.

Každé monitorování má lookback okno a analyzuje všechny vzorky shromážděné během této doby. Monitor může například mít lookback okno o velikosti 240 sekund, které hledá maximální hodnotu alespoň ze dvou vzorků hodnot, ale ne více než poslední 3. I když jsou hodnoty vzorkované v pravidelných intervalech, může se počet vzorků v konkrétním okně mírně lišit, pokud dojde k přerušení operace agenta.

Monitory mají v následující tabulce možné stavové stavy, které budou v jednom okamžiku pouze v jednom. Po inicializaci monitorování se spustí v dobrém stavu.

| Stav | Description |
|:---|:---|
| V pořádku  | Monitor aktuálně nepřekračuje varovnou nebo kritickou prahovou hodnotu. |
| Upozornění  | Monitorování překročilo prahovou hodnotu upozornění (Pokud je definována). |
| Kritické | Monitor překročil kritickou prahovou hodnotu (Pokud je definována). |
| Neznámý  | Nebylo shromážděno dostatek dat pro zjištění stavu. |
| Zakázáno | Monitorování je aktuálně zakázané. |
| Žádné     | Monitor je právě spuštěný a ještě není vyhodnocený nebo monitorovaný objekt už neexistuje. |



Existují dva typy monitorování, jak je znázorněno v následující tabulce.

| Monitor | Description |
|:---|:---|
| Monitorování jednotky | Měří určitý aspekt prostředku nebo aplikace. Může se jednat o kontrolu čítače výkonu za účelem zjištění výkonu nebo dostupnosti prostředku. |
| Souhrnné monitorování | Seskupuje více monitorování a poskytuje jeden souhrnný stav. Souhrnné monitorování může obsahovat jedno nebo více monitorování jednotky a další souhrnná monitorování. |


  
### <a name="health-rollup-policy"></a>Zásady souhrnu stavu
Stav virtuálního počítače je určen kumulativním stavem z každého z jeho jednotek a agregovaných monitorování. Každé agregované monitorování používá zásadu Shrnutí stavu nejhorší stav, kde stav agregovaného monitorování odpovídá stavu podřízeného monitorování s nejhorším stavem.  

V následujícím příkladu je monitorování **volného místa** v kritickém stavu, který role až do agregace pro svou instanci a následně do **systémů souborů**. I když je **využití procesoru** ve stavu varování, je virtuální počítač nastaven na kritický, protože se jedná o nejhorší stav pod ním. Pokud se volné místo vrátilo do stavu v pořádku, virtuální počítač se změní do stavu varování, protože využití CPU by se pak stalo monitorováním s nejhorším stavem.

![Příklad souhrnu stavu](media/vminsights-health-overview/health-rollup-example.png)


## <a name="monitor-details"></a>Podrobnosti monitorování
Vyberte monitorování a zobrazte jeho podrobnosti, které obsahují následující karty.

**Přehled** poskytuje popis monitorování, čas posledního vyhodnocení a vzorkování hodnot, které určují aktuální stav. Počet vzorků se může lišit v závislosti na definici monitorování a nestálosti hodnot.

[![Přehled monitorování podrobností](media/vminsights-health-overview/monitor-details-overview.png)](media/vminsights-health-overview/monitor-details-overview.png#lightbox)

**Historie** obsahuje historii změn stavu monitorování. Můžete rozbalit jakoukoli změnu stavu a zobrazit hodnoty vyhodnocené pro určení stavu. Klikněte na **Zobrazit konfiguraci** , která se použije k zobrazení konfigurace monitorování v době, kdy došlo ke změně stavu.



[![Historie monitorování podrobností](media/vminsights-health-overview/monitor-details-history.png)](media/vminsights-health-overview/monitor-details-history.png#lightbox)

### <a name="configuration"></a>Konfigurace
Zobrazení a úprava konfigurace monitorování pro vybraný virtuální počítač. Podrobnosti najdete v tématu [Konfigurace monitorování ve stavu hosta služby VM Insights (Preview)](vminsights-health-enable.md) .

[![Konfigurace podrobností monitorování](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)




## <a name="next-steps"></a>Další kroky

- [Povolit stav hosta v agentech VM Insights a integrovaných agentech.](vminsights-health-enable.md)
- [Nakonfigurujte monitorování pomocí Azure Portal.](vminsights-health-configure.md)
- [Nakonfigurujte monitorování pomocí pravidel shromažďování dat.](vminsights-health-configure-dcr.md)