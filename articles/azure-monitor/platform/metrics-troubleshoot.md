---
title: Řešení potíží s grafy metrik Azure monitoru
description: Řešení potíží s vytváření, přizpůsobení nebo interpretace grafy metrik
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: cff1bbefc3c54f7f9c02c646bd9eef528fe28c73
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939576"
---
# <a name="troubleshooting-metrics-charts"></a>Řešení potíží s grafy metrik

Pokud narazíte na problémy s vytváření, přizpůsobení nebo interpretaci grafů v Průzkumníku metrik Azure, pomocí tohoto článku. Pokud jste ještě na metriky, přečtěte si o [Začínáme s Průzkumníkem metrik](metrics-getting-started.md) a [pokročilé funkce Průzkumníka metrik](metrics-charts.md). Můžete také zobrazit [příklady](metric-chart-samples.md) nakonfigurované metriky grafů.

## <a name="cant-find-your-resource-to-select-it"></a>Vyberte váš prostředek nejde najít.

Jste kliknuli **vyberte prostředek** tlačítko, ale nevidíte váš prostředek v dialogovém okně pro výběr prostředků.

**Řešení:** Průzkumník metrik vyžaduje, abyste před Výpis dostupných prostředků vyberte předplatné a skupiny prostředků. Pokud se váš prostředek:

1. Ujistěte se, že jste vybrali správné předplatné **předplatné** rozevíracího seznamu. Pokud vaše předplatné není uvedené, klikněte na **adresář a předplatné nastavení** a přidejte předplatné s vaším prostředkem.

1. Ujistěte se, že jste vybrali správný prostředek skupiny.
    > [!WARNING]
    > Pro zajištění nejlepšího výkonu, při prvním otevření Průzkumníku metrik **skupiny prostředků** rozevírací seznam nemá žádné skupiny prostředků. předem vybraná. Předtím, než se zobrazí všechny prostředky, je třeba vybrat alespoň jednu skupinu.

## <a name="chart-shows-no-data"></a>Graf zobrazuje žádná data

Někdy může grafy zobrazovat žádná data po výběru správné prostředky a metriky. Toto chování může být způsobeno několika z následujících důvodů:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>Pro vaše předplatné není registrováno poskytovatele prostředků Microsoft.Insights.

Zkoumání metrik vyžaduje *Microsoft.Insights* ve vašem předplatném zaregistrovaný poskytovatel prostředků. V mnoha případech je zapsáno automaticky (to znamená, po konfiguraci pravidla upozornění, přizpůsobení nastavení diagnostiky pro prostředek nebo konfigurace pravidla automatického škálování). Pokud není zaregistrovaný poskytovatel prostředků Microsoft.Insights, budete muset ručně zaregistrovat ji pomocí kroků popsaných v [poskytovatelé a typy prostředků Azure](../../azure-resource-manager/resource-manager-supported-services.md).

**Řešení:** Otevřít **předplatná**, **poskytovatelů prostředků** kartě a ověřte, že *Microsoft.Insights* je registrován pro vaše předplatné.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Nemáte dostatečná přístupová práva k prostředku

V Azure, se řídí přístup k metrikám [řízení přístupu na základě role (RBAC)](../../role-based-access-control/overview.md). Musíte být členem skupiny [Čtenář monitorování](../../role-based-access-control/built-in-roles.md#monitoring-reader), [Přispěvatel monitorování](../../role-based-access-control/built-in-roles.md#monitoring-contributor), nebo [Přispěvatel](../../role-based-access-control/built-in-roles.md#contributor) zkoumat metriky pro prostředek.

**Řešení:** Ujistěte se, že máte dostatečná oprávnění pro prostředek, ze kterého jsou zkoumání metrik.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>Váš prostředek nebyla během vybraného časového úseku generovat metriky

Některé prostředky negenerovat neustále jejich metriky. Například Azure nebude shromažďovat metriky pro zastavených virtuálních počítačů. Další prostředky mohou vysílat jejich metriky pouze v případě, že dojde k některé podmínky. Například metrika zobrazuje doba zpracování transakce vyžaduje aspoň jedna transakce. Pokud nebyly provedeny žádné transakce ve vybraném časovém rozsahu, graf přirozeně bude prázdný. Kromě toho Přestože většina metrik v Azure jsou shromažďovány každou minutu, platí, která jsou shromážděna méně často. V dokumentaci metriky zobrazíte další podrobnosti o metriku, která chcete prozkoumat.

**Řešení:** Používání nástroje většímu změníte čas grafu. Můžete začít z "Posledních 30 dní" pomocí větší granularitu (nebo spoléhat na možnost "Automatické časové intervaly").

### <a name="you-picked-a-time-range-greater-than-30-days"></a>Vybere časový rozsah, který je větší než 30 dní

[Většina metrik v Azure ukládají 93 dní](data-platform-metrics.md#retention-of-metrics). Nicméně dotaz lze použít pouze pro více než 30 dní, po který dat na libovolný graf jednoho. Toto omezení neplatí pro [metriky založené na protokolech](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

**Řešení:** Pokud se zobrazí prázdný graf nebo diagramu se zobrazí pouze část dat metriky, ověřte, že rozdíl mezi start-end data a v výběr času nepřekročí 30denní interval.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Všechny hodnoty metrik byly mimo rozsah uzamčen osy y

Podle [uzamčení hranice osa y grafu](metrics-charts.md#lock-boundaries-of-chart-y-axis), můžete neúmyslně vytvořit zobrazení oblasti grafu, není uveden čára grafu. Například pokud osy y je pevně nastavené na rozsah mezi 0 a 50 % a metriku má konstantní hodnotu 100 %, řádku vždy vykreslován mimo viditelná oblast, aby graf se zobrazí prázdné.

**Řešení:** Ověřte, že nejsou uzamčeny hranice osa y grafu mimo rozsah hodnoty metrik. Pokud jsou zamčené uvnitř hranic osy y, můžete dočasně obnovit je zajistit, aby metriky, které hodnoty není spadá mimo oblast grafu. Rozsah osy y uzamčení se nedoporučuje používat s automatickou členitosti u grafů s **součet**, **min**, a **maximální** agregace vzhledem k tomu, že jejich hodnoty se budou měnit s členitost změně velikosti okna prohlížeče nebo přechod z jedné obrazovky řešení do jiného. Přepínání členitosti může zanechat zobrazení oblasti grafu prázdný.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Před sebou metriku hostovaného operačního systému, ale neměli povolit rozšíření diagnostiky Azure

Kolekce **hostovaného operačního systému** metriky vyžaduje konfiguraci rozšíření diagnostiky Azure nebo povolte ho pomocí **nastavení diagnostiky** panel pro váš prostředek.

**Řešení:** Pokud je povolené rozšíření diagnostiky Azure, ale nemůžete zobrazit metriky, postupujte podle kroků uvedených v [Průvodce odstraňováním potíží rozšíření Azure Diagnostics](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal). Viz také kroky pro řešení potíží [nelze vybrat obor názvů hostovaný operační systém a metriky](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics)

## <a name="error-retrieving-data-message-on-dashboard"></a>Zpráva "Chyba při načítání dat" na řídicím panelu

Tento problém je běžný při řídicí panel byl vytvořen pomocí metriku, která se později zastaralé a odebrána z Azure. Chcete-li ověřit, že se jedná o tento případ, otevřete **metriky** kartu prostředku a zkontrolujte dostupné metriky v metriky pro výběr. Pokud metrika není zobrazený, metriku byl odebrán z Azure. Obvykle když metrika je zastaralá, je lepší novou metriku, která nabízí podobné perspektivou na stav prostředku.

**Řešení:** Aktualizujte dlaždici selhání výběrem alternativní metrik pro graf na řídicí panel. Je možné [prohlédněte si seznam dostupných metrik pro služby Azure](metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>Graf zobrazuje přerušované čáry

Metriky Azure grafy pomocí čárkovanou čáru naznačují, že chybí hodnota (označované také jako "null value") mezi dvěma známý časový interval datových bodů. Například, pokud v modulu pro výběr času vyberou "1" čas minutu, ale byla nahlášena metriku na 07:26, 07:27 07:29 a 07:30 (Poznámka: minuta propast mezi druhý a třetí datových bodů), bude na přerušovanou čáru připojení 07:27 a 07:29 a připojí plná čára Další datové body. Drops přerušovanou čáru na nulu, když se používá metrika **počet** a **součet** agregace. Pro **avg**, **min** nebo **maximální** agregace, přerušované čáry připojí dva nejbližší známé datové body. Také když na straně úplně vlevo nebo vpravo v grafu chybí data, přerušované čáry rozšíří směr chybí datový bod.
  ![metriky image](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**Řešení:** Toto chování je záměrné. To je užitečné pro identifikaci chybějící datové body. Spojnicový graf dokonalá volbou pro vizualizace trendů metrik s vysokou hustotou je ale může být obtížné interpretovat metriky hodnotami zhuštěný, zejména v případě, že hodnoty s časovým intervalem corelating je důležité. Přerušované čáry usnadňuje čtení těchto grafů, ale pokud graf je stále nejasné, vezměte v úvahu zobrazení metriky pomocí typu grafu. Například roztříštěný Krabicový diagram stejné metriky jasně ukazuje každý časový interval podle jen vizualizace tečku po hodnotu a přeskočení data bodu úplně když chybí hodnota: ![metriky image](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > Pokud stále chcete spojnicový graf vaše metriky, přesunutí myši nad grafem může pomoct s vyhodnocení časové intervaly zvýrazněním datový bod v umístění ukazatele myši.

## <a name="chart-shows-unexpected-drop-in-values"></a>Graf zobrazuje rozevírací neočekávané hodnoty

V mnoha případech je vnímaná prudkým snížením hodnoty metrik neporozumění daty zobrazenými v grafu. Může být omyl podle pokles součtů nebo vrátí, když tento graf zobrazuje nejnovější minut, protože poslední bod data metriky nebyly přijata nebo zpracována Azure ještě. V závislosti na službě zpracování metriky latence může být v rozsahu několika minut. Grafy znázorňující poslední časový rozsah s 1 nebo 5minutových intervalech, bude snadněji postřehnutelné pokles hodnoty za posledních několik minut: ![metriky image](./media/metrics-troubleshoot/drop-in-values.png)

**Řešení:** Toto chování je záměrné. Jsme přesvědčeni, že je vhodné zobrazení dat poté, co jsme ji přijmou, i v případě, že data jsou *částečné* nebo *neúplné*. To umožňuje provést až po důležité uzavření a spustit šetření okamžitě. Například pro metriku, který zobrazuje počet chyb, zobrazení částečné hodnoty X zjistíte, že došlo k nejméně X chyb v danou chvíli. Můžete začít okamžitě zkoumání problému, místo dočkat, až uvidíme přesný počet chyb, ke kterým došlo na tato minuta, který nemusí být jako důležité. Graf aktualizujeme, jakmile obdržíme celá sada dat, ale v tuto chvíli lze také zobrazit neúplné datové body novější minut.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>Nelze vybrat obor názvů hostovaný operační systém a metriky

Virtuální počítače a škálovací sady virtuálních počítačů mají dvě kategorie metriky: **Hostitel virtuálního počítače** metriky, které byly shromážděny sadou hostování prostředí Azure a **hostovaného operačního systému** metriky, které byly shromážděny sadou [agenta monitorování](agents-overview.md) běžící na virtuálních počítačích. Instalace agenta monitorování tím, že [diagnostické rozšíření Azure](diagnostics-extension-overview.md).

Ve výchozím nastavení, metriky hostovaného operačního systému se ukládají v účtu Azure Storage, který jste si můžete vybrat z **nastavení diagnostiky** kartu prostředku. Pokud se neshromažďují metriky hostovaného operačního systému nebo Průzkumníka metrik k nim nelze přistupovat, zobrazí se pouze **hostitele virtuálního počítače** metriky oboru názvů:

![metriky image](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**Řešení:** Pokud nevidíte **hostovaného operačního systému** obor názvů a metrik v Průzkumníku metrik:

1. Ujistěte se, že [diagnostické rozšíření Azure](diagnostics-extension-overview.md) je povolená a nakonfigurovaná tak, aby shromažďovat metriky.
    > [!WARNING]
    > Nemůžete použít [agenta Log Analytics](agents-overview.md#log-analytics-agent) (také označované jako agenta Microsoft Monitoring Agent, nebo "MMA") k odeslání **hostovaného operačního systému** do účtu úložiště.

1. Ověřte, že účet úložiště není chráněn bránou firewall.

1. Použití [Průzkumníka služby Azure storage](https://azure.microsoft.com/features/storage-explorer/) k ověření, že se metriky přenášejí do účtu úložiště. Pokud se neshromažďují metriky, postupujte podle [Průvodce odstraňováním potíží rozšíření Azure Diagnostics](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>Další postup

* [Další informace o zahájení práce s Průzkumníkem metrik](metrics-getting-started.md)
* [Další informace o pokročilých funkcích sady Průzkumník metrik](metrics-charts.md)
* [Podívejte se do seznamu dostupných metrik pro služby Azure](metrics-supported.md)
* [Podívejte se na příklady nakonfigurované grafů](metric-chart-samples.md)