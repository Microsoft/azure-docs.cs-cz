---
title: Poradce při potížích s metrikami Azure Monitoru
description: Řešení problémů s vytvářením, přizpůsobením nebo interpretací metrických grafů
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: e1ad4e53596b8228bdef5beb18aa250a9512c49f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659658"
---
# <a name="troubleshooting-metrics-charts"></a>Řešení potíží s grafy metrik

Tento článek použijte, pokud narazíte na problémy s vytvářením, přizpůsobením nebo interpretací grafů v průzkumníku metrik Azure. Pokud s metrikami tečujete, přečtěte si informace o [tom, jak začít s průzkumníkem metrik](metrics-getting-started.md) a [pokročilými funkcemi průzkumníka metrik](metrics-charts.md). Můžete také zobrazit [příklady](metric-chart-samples.md) nakonfigurovaných grafů metrik.

## <a name="cant-find-your-resource-to-select-it"></a>Nelze najít zdroj, který by ho mohl vybrat.

Po kliknutí na tlačítko **Vybrat prostředek** se v dialogovém okně pro výběr prostředků nezobrazí váš prostředek.

**Řešení:** Průzkumník metrik vyžaduje, abyste před výpisem dostupných prostředků vybrali předplatná a skupiny prostředků. Pokud se váš prostředek nezobrazí:

1. Ujistěte se, že jste v rozevírací nabídce **Předplatné** vybrali správné předplatné. Pokud se vaše předplatné nezobrazí, klikněte na **nastavení Adresář a předplatné** a přidejte předplatné s vaším prostředkem.

1. Ujistěte se, že jste vybrali správnou skupinu prostředků.
    > [!WARNING]
    > Kvůli zajištění nejlepšího výkonu po prvním otevření Průzkumníku metrik rozevírací nabídka **Skupina prostředků** neobsahuje žádné předem vybrané skupiny prostředků. Musíte vybrat alespoň jednu skupinu a teprve pak se zobrazí prostředky.

## <a name="chart-shows-no-data"></a>Graf nezobrazuje žádná data

Někdy grafy mohou po výběru správných prostředků a metrik zobrazovat žádná data. Toto chování může být způsobeno několika následujícími důvody:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>Ve vašem předplatném není zaregistrovaný poskytovatel prostředků Microsoft.Insights

Abyste mohli procházet metriky, ve vašem předplatném musí být zaregistrovaný poskytovatel prostředků *Microsoft.Insights*. Většinou se registruje automaticky (tj. po nakonfigurování pravidla upozornění, přizpůsobení nastavení diagnostiky pro jakýkoli prostředek nebo nakonfigurování pravidla automatického škálování). Pokud poskytovatel prostředků Microsoft.Insights není registrovaný, musíte ho ručně zaregistrovat podle následujících kroků popsaných v [Azure zprostředkovatelů a typů prostředků](../../azure-resource-manager/management/resource-providers-and-types.md).

**Řešení:** Otevřete **předplatná**, kartu **Zprostředkovatelé prostředků** a ověřte, že *microsoft.insights* je registrovaný pro vaše předplatné.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Nemáte dostatečná přístupová práva ke svému prostředku

V Azure se přístup k metrikám řídí prostřednictvím [řízení přístupu na základě role](../../role-based-access-control/overview.md). Abyste mohli procházet metriky prostředků, musíte být členem role [Čtenář monitorování](../../role-based-access-control/built-in-roles.md#monitoring-reader), [Přispěvatel monitorování](../../role-based-access-control/built-in-roles.md#monitoring-contributor) nebo [Přispěvatel](../../role-based-access-control/built-in-roles.md#contributor).

**Řešení:** Ujistěte se, že máte dostatečná oprávnění pro prostředek, ze kterého zkoumáte metriky.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>Váš prostředek ve vybraném časovém rozsahu negeneroval metriky

Některé prostředky negenerují metriky neustále. Azure například neshromažďuje metriky zastavených virtuálních počítačů. Další prostředky můžou generovat metriky pouze za určitých podmínek. Například metrika znázorňující dobu zpracování transakce vyžaduje alespoň jednu transakci. Pokud ve vybraném časovém rozsahu nedojde k žádným transakcím, graf bude přirozeně prázdný. Kromě toho, i když se většina metrik v Azure shromažďuje každou minutu, existují metriky, které se shromažďují méně často. Další podrobnosti o metrikách, které chcete procházet, najdete v dokumentaci k metrikám.

**Řešení:** Změňte čas grafu na širší rozsah. Můžete začít od "Posledních 30 dnů" pomocí větší čas rozlišovací schopnost (nebo se spoléhat na možnost "Automatické časové rozlišovací schopnost").

### <a name="you-picked-a-time-range-greater-than-30-days"></a>Vybrali jste časový rozsah delší než 30 dnů

[Většina metrik v Azure se ukládá po dobu 93 dnů](data-platform-metrics.md#retention-of-metrics). V každém grafu však můžete zadávat dotazy na data maximálně za 30 dnů. Toto omezení neplatí pro [metriky založené na protokolech](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

**Řešení:** Pokud se zobrazí prázdný graf nebo graf zobrazuje pouze část dat metriky, ověřte, zda rozdíl mezi počátečním a koncovým datem ve výběru času nepřesahuje 30denní interval.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Všechny hodnoty metrik byly mimo vymezený rozsah osy Y

[Vymezením hranic osy Y grafu](metrics-charts.md#lock-boundaries-of-chart-y-axis) můžete neúmyslně způsobit, že se čára grafu nezobrazí v zobrazované oblasti grafu. Pokud je například osa Y vymezená rozsahem od 0 % do 50 % a metrika má konstantní hodnotu 100 %, čára se vždy vykreslí mimo viditelnou oblast a graf bude vypadat prázdně.

**Řešení:** Ověřte, zda hranice osy y grafu nejsou uzamčeny mimo rozsah hodnot metriky. Pokud jsou hranice osy Y vymezené, můžete je dočasně resetovat a tím zajistit, aby hodnoty metrik nespadaly mimo rozsah grafu. Vymezení rozsahu osy Y s automatickými intervaly se nedoporučuje u grafů s agregacemi **součtu**, **minima** a **maxima**, protože při změně velikosti okna prohlížeče nebo změně rozlišení obrazovky se intervaly změní, a tím se změní i hodnoty těchto agregací. Po přepnutí intervalů může zobrazovaná oblast grafu zůstat prázdná.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Díváte se na metriku hostovaného operačního systému, ale nepovolili jste rozšíření Diagnostika Azure

Abyste mohli shromažďovat metriky **hostitelského operačního systému**, musíte nakonfigurovat rozšíření Azure Diagnostics nebo ho povolit na panelu **Nastavení diagnostiky** pro váš prostředek.

**Řešení:** Pokud je rozšíření Diagnostika Azure povoleno, ale stále se vám nedaří zobrazit metriky, postupujte podle pokynů uvedených v [průvodci odstraňováním potíží s rozšířením Diagnostika Azure](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal). Podívejte se také na kroky řešení potíží pro [nevybrat obor názvů hostovaného operačního systému a metriky](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics)

## <a name="error-retrieving-data-message-on-dashboard"></a>Zpráva Chyba při načítání dat na řídicím panelu

K tomuto problému může dojít v případě, že se řídicí panel vytvořil s použitím metriky, která se později označila za zastaralou a odebrala se z Azure. Pokud chcete ověřit, jestli se jedná o tento případ, otevřete kartu **Metriky** vašeho prostředku a zkontrolujte dostupné metriky ve výběru metrik. Pokud se metrika nezobrazí, znamená to, že se odebrala z Azure. Když se metrika označí za zastaralou, obvykle existuje nová lepší metrika, která poskytuje podobný pohled na stav prostředku.

**Řešení:** Aktualizujte dlaždici se selháním výběrem alternativní metriky pro graf na řídicím panelu. Můžete si [projít seznam dostupných metrik pro služby Azure](metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>Graf zobrazuje přerušovanou čáru

Azure metriky grafy použít styl přerušované čáry k označení, že je chybějící hodnota (označovaná také jako "nulová hodnota") mezi dvěma známými body dat časového období. Pokud jste například v voliči času vybrali rozlišovací schopnost "1 minuta", ale metrika byla hlášena v 07:26, 07:27, 07:29 a 07:30 (všimněte si minutové mezery mezi druhým a třetím datovým bodem), pak se přerušovaná čára připojí v 7:27 a 07:29 a připojí se pevná čára všechny ostatní datové body. Přerušovaná čára klesne na nulu, když metrika používá **počet** a **součet** agregace. U agregací **avg**, **min** nebo **max** spojuje přerušovaná čára dva nejbližší známé datové body. Pokud data navíc chybí na pravé nebo levé straně grafu, přerušovaná čára se protáhne směrem k chybějícímu datovému bodu.
  ![obrázek metriky](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**Řešení:** Toto chování je záměrné. Je to užitečné při identifikaci chybějících datových bodů. Spojnicový graf je vynikající volbou pro vizualizaci trendů metrik s vysokou hustotou, ale může být obtížné interpretovat metriky s řídkými hodnotami, zejména pokud je důležité korelace hodnot s časovým zrnitkem. Přerušovaná čára usnadňuje čtení těchto grafů, ale pokud je váš graf stále nejasný, zvažte zobrazení metrik pomocí jiného typu grafu. Například bodový graf s rozptýleným vykreslením pro stejnou metriku jasně zobrazuje pokaždé zrnitost tak, že vizualizuje ![tečku pouze v případě, že je hodnota, a úplně přeskočí datový bod, když hodnota chybí: obrázek metriky](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > Pokud stále dáváte přednost zobrazení metrik pomocí spojnicového grafu, může vám s posuzováním časových intervalů pomoct, když najedete myší na graf, protože se zvýrazní datový bod, který se nachází pod ukazatelem myši.

## <a name="chart-shows-unexpected-drop-in-values"></a>Graf zobrazuje neočekávaný pokles hodnot

V řadě případů je zdánlivý pokles hodnot metrik způsobený nesprávným výkladem dat zobrazených v grafu. Pokud graf ukazuje data za poslední minuty, může vás zmást pokles součtů nebo počtů, protože platforma Azure ještě nepřijala nebo nezpracovala nejnovější datové body metrik. V závislosti na konkrétní službě se může latence zpracování metrik pohybovat v rozsahu několika minut. U grafů zobrazujících poslední časový rozsah s rozlišovací schopností 1 nebo 5 minut se znatelnější projeví pokles hodnoty za posledních několik minut: ![metrický obrázek](./media/metrics-troubleshoot/drop-in-values.png)

**Řešení:** Toto chování je záměrné. Věříme, že je přínosné zobrazovat data ihned po přijetí, a to i v případě, že jde o *částečná* nebo *neúplná* data. Díky tomu můžete rychleji dojít k důležitému závěru a rovnou spustit šetření. Když například u metriky, která ukazuje počet selhání, uvidíte částečnou hodnotu X, budete vědět, že v dané minutě došlo minimálně k X selháním. Rovnou můžete začít s šetřením problému a nemusíte čekat, až se zobrazí přesný počet selhání, ke kterým došlo v dané minutě, což nemusí být tak důležité. Jakmile obdržíme kompletní sadu dat, graf se aktualizuje. V tu dobu se však můžou zobrazit také nové neúplné datové body pro poslední minuty.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>Nelze vybrat obor názvů hostovaného operačního serveru a metriky

Virtuální počítače a škálovací sady virtuálních počítačů mají dvě kategorie metrik: metriky **hostitele virtuálních počítačů,** které jsou shromažďovány v hostitelském prostředí Azure, a metriky **hostovaného operačního systému (klasické),** které shromažďuje [agent monitorování](agents-overview.md) spuštěný na vašich virtuálních počítačích. Agenta monitorování můžete nainstalovat povolením [rozšíření Azure Diagnostics](diagnostics-extension-overview.md).

Metriky hostovaného operačního systému se ve výchozím nastavení ukládají do účtu služby Azure Storage, který vyberete na kartě **Nastavení diagnostiky** vašeho prostředku. Pokud se metriky hostovaného operačního systému neshromáždí nebo pokud k nim Průzkumník metrik nemá přístup, zobrazí se pouze obor názvů metrik **hostitele virtuálního počítače**:

![obrázek metriky](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**Řešení:** Pokud v průzkumníku metrik nevidíte obor názvů **hostovaného operačního systému (klasického):**

1. Ověřte, že je povolené [rozšíření Azure Diagnostics](diagnostics-extension-overview.md) a že je nakonfigurované pro shromažďování metrik.
    > [!WARNING]
    > K odesílání metrik **hostovaného operačního systému** do účtu úložiště není možné použít [agenta Log Analytics](agents-overview.md#log-analytics-agent) (označuje se také jako Microsoft Monitoring Agent nebo MMA).

1. Ujistěte se, že je pro [vaše předplatné zaregistrován](metrics-troubleshoot.md#microsoftinsights-resource-provider-isnt-registered-for-your-subscription)poskytovatel prostředků **Microsoft.Insights** .

1. Ověřte, že účet úložiště není chráněný bránou firewall. Azure Portal potřebuje přístup k účtu úložiště, aby mohl načítat data metrik a vykreslovat grafy.

1. Pomocí [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) ověřte, že se metriky přenášejí do účtu úložiště. Pokud se metriky neshromažďují, postupujte podle [Průvodce odstraňováním potíží s rozšířením Azure Diagnostics](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>Další kroky

* [Informace o tom, jak začít s Průzkumníkem metrik](metrics-getting-started.md)
* [Informace o pokročilých funkcích Průzkumníka metrik](metrics-charts.md)
* [Seznam dostupných metrik pro služby Azure](metrics-supported.md)
* [Příklady nakonfigurovaných grafů](metric-chart-samples.md)
