---
title: Řešení potíží s Azure Monitor grafy metrik
description: Řešení potíží s vytvářením, přizpůsobením nebo interpretací grafů metrik
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: d62c4b79fcb86080649c542e34b81d3213978604
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610403"
---
# <a name="troubleshooting-metrics-charts"></a>Řešení potíží s grafy metrik

Tento článek použijte, pokud narazíte na problémy s vytvářením, přizpůsobením nebo interpretací grafů v Průzkumníkovi metrik Azure. Pokud s metrikami začínáte, přečtěte si informace o tom, jak [začít s Průzkumníkem metrik a s](metrics-getting-started.md) [pokročilými funkcemi v Průzkumníkovi metrik](../essentials/metrics-charts.md). Můžete také zobrazit [Příklady](../essentials/metric-chart-samples.md) konfigurovaných grafů metriky.

## <a name="chart-shows-no-data"></a>Graf nezobrazuje žádná data

V některých případech se může stát, že grafy po výběru správných prostředků a metrik nezobrazí žádná data. Toto chování může být způsobeno několika z následujících důvodů:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>Ve vašem předplatném není zaregistrovaný poskytovatel prostředků Microsoft.Insights

Abyste mohli procházet metriky, ve vašem předplatném musí být zaregistrovaný poskytovatel prostředků *Microsoft.Insights*. Většinou se registruje automaticky (tj. po nakonfigurování pravidla upozornění, přizpůsobení nastavení diagnostiky pro jakýkoli prostředek nebo nakonfigurování pravidla automatického škálování). Pokud poskytovatel prostředků Microsoft. Insights není zaregistrovaný, musíte ho ručně zaregistrovat pomocí následujících kroků popsaných v tématu [poskytovatelé a typy prostředků Azure](../../azure-resource-manager/management/resource-providers-and-types.md).

**Řešení:** Otevřete **předplatné**, kartu **poskytovatelé prostředků** a ověřte, jestli je pro vaše předplatné zaregistrované *Microsoft. Insights* .

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Nemáte dostatečná přístupová práva ke svému prostředku

V Azure se přístup ke metrikám řídí [řízením přístupu na základě role Azure (RBAC)](../../role-based-access-control/overview.md). Abyste mohli procházet metriky prostředků, musíte být členem role [Čtenář monitorování](../../role-based-access-control/built-in-roles.md#monitoring-reader), [Přispěvatel monitorování](../../role-based-access-control/built-in-roles.md#monitoring-contributor) nebo [Přispěvatel](../../role-based-access-control/built-in-roles.md#contributor).

**Řešení:** Ujistěte se, že máte dostatečná oprávnění pro prostředek, ze kterého prozkoumáte metriky.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>Váš prostředek ve vybraném časovém rozsahu negeneroval metriky

Některé prostředky negenerují metriky neustále. Azure například neshromažďuje metriky zastavených virtuálních počítačů. Další prostředky můžou generovat metriky pouze za určitých podmínek. Například metrika znázorňující dobu zpracování transakce vyžaduje alespoň jednu transakci. Pokud ve vybraném časovém rozsahu nedojde k žádným transakcím, graf bude přirozeně prázdný. Kromě toho, i když se většina metrik v Azure shromažďuje každou minutu, existují metriky, které se shromažďují méně často. Další podrobnosti o metrikách, které chcete procházet, najdete v dokumentaci k metrikám.

**Řešení:** Změňte čas grafu na širší rozsah. Můžete začít od "posledních 30 dnů" s použitím většího časového rozlišení (nebo se spoléhat na možnost automatické členitosti v časovém intervalu).

### <a name="you-picked-a-time-range-greater-than-30-days"></a>Vybrali jste časový rozsah delší než 30 dnů

[Většina metrik v Azure se ukládá po dobu 93 dnů](../essentials/data-platform-metrics.md#retention-of-metrics). V každém grafu však můžete zadávat dotazy na data maximálně za 30 dnů. Toto omezení neplatí pro [metriky založené na protokolech](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

**Řešení:** Pokud se zobrazí prázdný graf nebo se v grafu zobrazí pouze část dat metriky, ověřte, zda rozdíl mezi počátečním a koncovým datem v nástroji pro výběr času nepřekročí 30 dní.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Všechny hodnoty metrik byly mimo vymezený rozsah osy Y

[Vymezením hranic osy Y grafu](../essentials/metrics-charts.md#locking-the-range-of-the-y-axis) můžete neúmyslně způsobit, že se čára grafu nezobrazí v zobrazované oblasti grafu. Pokud je například osa Y vymezená rozsahem od 0 % do 50 % a metrika má konstantní hodnotu 100 %, čára se vždy vykreslí mimo viditelnou oblast a graf bude vypadat prázdně.

**Řešení:** Ověřte, že hranice osy y grafu nejsou uzamčené mimo rozsah hodnot metriky. Pokud jsou hranice osy Y vymezené, můžete je dočasně resetovat a tím zajistit, aby hodnoty metrik nespadaly mimo rozsah grafu. Vymezení rozsahu osy Y s automatickými intervaly se nedoporučuje u grafů s agregacemi **součtu**, **minima** a **maxima**, protože při změně velikosti okna prohlížeče nebo změně rozlišení obrazovky se intervaly změní, a tím se změní i hodnoty těchto agregací. Po přepnutí intervalů může zobrazovaná oblast grafu zůstat prázdná.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Prohlížíte se na metriku hostovaného operačního systému, ale nepovolili jste diagnostické rozšíření Azure.

Abyste mohli shromažďovat metriky **hostitelského operačního systému**, musíte nakonfigurovat rozšíření Azure Diagnostics nebo ho povolit na panelu **Nastavení diagnostiky** pro váš prostředek.

**Řešení:** Pokud je povolené rozšíření Azure Diagnostics, ale stále nemůžete zobrazit metriky, postupujte podle kroků popsaných v [Průvodci odstraňováním potíží s rozšířením Azure Diagnostics](../agents/diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal). Další informace najdete v tématu řešení potíží s [oborem názvů a metrikami hostovaného operačního systému](#cannot-pick-guest-os-namespace-and-metrics) .

## <a name="error-retrieving-data-message-on-dashboard"></a>Chybová zpráva při načítání dat na řídicím panelu

K tomuto problému může dojít v případě, že se řídicí panel vytvořil s použitím metriky, která se později označila za zastaralou a odebrala se z Azure. Pokud chcete ověřit, že se jedná o tento případ, otevřete kartu **metriky** prostředku a zkontrolujte dostupné metriky ve výběru metriky. Pokud se metrika nezobrazí, znamená to, že se odebrala z Azure. Když se metrika označí za zastaralou, obvykle existuje nová lepší metrika, která poskytuje podobný pohled na stav prostředku.

**Řešení:** Aktualizujte dlaždici, která selhala, výběrem alternativní metriky pro váš graf na řídicím panelu. Můžete si [projít seznam dostupných metrik pro služby Azure](../platform/metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>V grafu se zobrazuje přerušovaná čára

Grafy metrik Azure používají čárkovaný styl čáry k označení toho, že mezi dvěma známými datovými body zrnitosti chybí hodnota (označovaná také jako hodnota null). Například pokud v selektoru jste si vybrali členitou časovou přesnost 1 minuty, ale metrika byla nahlášena v 07:26, 07:27, 07:29 a 07:30 (Všimněte si, že se v části sekunda a třetí datové body objeví minutová mezera), pak se přerušovaná čára připojí 07:27 a 07:29 a plná čára připojí všechny ostatní datové body. Přerušovaná čára klesne dolů na nulu, pokud metrika používá agregaci **Count** a **Sum** . U agregací **AVG**, **min** nebo **Max** se přerušovaná čára spojí dva nejbližší známé datové body. Pokud data navíc chybí na pravé nebo levé straně grafu, přerušovaná čára se protáhne směrem k chybějícímu datovému bodu.
  ![Snímek obrazovky, který ukazuje, jak data chybějí na pravé straně grafu nebo v levé části grafu, se odvádí na směr chybějícího datového bodu.](./media/metrics-troubleshoot/dashed-line.png)

**Řešení:** Toto chování je záměrné. Je to užitečné při identifikaci chybějících datových bodů. Spojnicový graf je nadřízenou volbou pro vizualizaci trendů metrik s vysokou hustotou, ale může být obtížné ji interpretovat pro metriky s zhuštěnými hodnotami, zejména v případě, že se v souvislosti s časovým intervalem jsou důležité hodnoty. Přerušovaná čára usnadňuje čtení těchto grafů, ale pokud je váš graf stále nejasný, zvažte zobrazení metrik pomocí jiného typu grafu. Například rozptýlený graf pro stejnou metriku jasně zobrazuje každé časové intervaly tím, že pouze vizualizuje tečku, pokud existuje hodnota a přeskočí datový bod úplně, když hodnota chybí: ![ snímek obrazovky, který zvýrazní možnost nabídky bodový graf.](./media/metrics-troubleshoot/scatter-plot.png)

   > [!NOTE]
   > Pokud stále dáváte přednost zobrazení metrik pomocí spojnicového grafu, může vám s posuzováním časových intervalů pomoct, když najedete myší na graf, protože se zvýrazní datový bod, který se nachází pod ukazatelem myši.

## <a name="chart-shows-unexpected-drop-in-values"></a>V grafu se zobrazuje neočekávaný pokles hodnot

V řadě případů je zdánlivý pokles hodnot metrik způsobený nesprávným výkladem dat zobrazených v grafu. Pokud graf ukazuje data za poslední minuty, může vás zmást pokles součtů nebo počtů, protože platforma Azure ještě nepřijala nebo nezpracovala nejnovější datové body metrik. V závislosti na konkrétní službě se může latence zpracování metrik pohybovat v rozsahu několika minut. V grafech zobrazujících nedávný časový rozsah s členitou úrovní 1 nebo 5 minut se může poznamenat, že hodnota za posledních několik minut bude poznatelný: ![ snímek obrazovky, který zobrazuje jednu z hodnot za posledních několik minut.](./media/metrics-troubleshoot/unexpected-dip.png)

**Řešení:** Toto chování je záměrné. Věříme, že je přínosné zobrazovat data ihned po přijetí, a to i v případě, že jde o *částečná* nebo *neúplná* data. Díky tomu můžete rychleji dojít k důležitému závěru a rovnou spustit šetření. Když například u metriky, která ukazuje počet selhání, uvidíte částečnou hodnotu X, budete vědět, že v dané minutě došlo minimálně k X selháním. Rovnou můžete začít s šetřením problému a nemusíte čekat, až se zobrazí přesný počet selhání, ke kterým došlo v dané minutě, což nemusí být tak důležité. Jakmile obdržíme kompletní sadu dat, graf se aktualizuje. V tu dobu se však můžou zobrazit také nové neúplné datové body pro poslední minuty.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>Nejde vybrat obor názvů a metriky hostovaného operačního systému.

Virtuální počítače a škálovací sady virtuálních počítačů mají dvě kategorie metrik: Metriky **hostitele virtuálního počítače**, které shromažďuje hostitelské prostředí Azure, a metriky **hostovaného operačního systému (Classic)** , které shromažďuje [agent monitorování](../agents/agents-overview.md) spuštěný na virtuálních počítačích. Agenta monitorování můžete nainstalovat povolením [rozšíření Azure Diagnostics](../agents/diagnostics-extension-overview.md).

Metriky hostovaného operačního systému se ve výchozím nastavení ukládají do účtu služby Azure Storage, který vyberete na kartě **Nastavení diagnostiky** vašeho prostředku. Pokud se metriky hostovaného operačního systému neshromáždí nebo pokud k nim Průzkumník metrik nemá přístup, zobrazí se pouze obor názvů metrik **hostitele virtuálního počítače**:

![obrázek metriky](./media/metrics-troubleshoot/vm.png)

**Řešení:** Pokud nevidíte obor názvů a metriky oborů **hostovaného operačního systému (Classic)** v Průzkumníkovi metrik:

1. Ověřte, že je povolené [rozšíření Azure Diagnostics](../agents/diagnostics-extension-overview.md) a že je nakonfigurované pro shromažďování metrik.
    > [!WARNING]
    > K odesílání metrik **hostovaného operačního systému** do účtu úložiště není možné použít [agenta Log Analytics](../agents/agents-overview.md#log-analytics-agent) (označuje se také jako Microsoft Monitoring Agent nebo MMA).

1. Ujistěte se, že je [pro vaše předplatné zaregistrovaný](#microsoftinsights-resource-provider-isnt-registered-for-your-subscription)poskytovatel prostředků **Microsoft. Insights** .

1. Ověřte, že účet úložiště není chráněný bránou firewall. Azure Portal potřebuje přístup k účtu úložiště, aby mohl načítat data metrik a vykreslovat grafy.

1. Pomocí [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) ověříte, že se do účtu úložiště zanášejí metriky. Pokud se metriky neshromažďují, postupujte podle [Průvodce odstraňováním potíží s rozšířením Azure Diagnostics](../agents/diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>Další kroky

* [Další informace o tom, jak začít s Průzkumníkem metrik](metrics-getting-started.md)
* [Další informace o rozšířených funkcích Průzkumníka metrik](../essentials/metrics-charts.md)
* [Seznam dostupných metrik pro služby Azure](../platform/metrics-supported.md)
* [Příklady nakonfigurovaných grafů](../essentials/metric-chart-samples.md)
