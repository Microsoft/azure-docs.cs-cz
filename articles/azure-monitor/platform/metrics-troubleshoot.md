---
title: Řešení potíží s Azure Monitor grafy metrik
description: Řešení potíží s vytvářením, přizpůsobením nebo interpretací grafů metrik
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: d31b046bf02893affff84069ee92b3bd7735b904
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243224"
---
# <a name="troubleshooting-metrics-charts"></a>Řešení potíží s grafy metrik

Tento článek použijte, pokud narazíte na problémy s vytvářením, přizpůsobením nebo interpretací grafů v Průzkumníkovi metrik Azure. Pokud s metrikami začínáte, přečtěte si informace o tom, jak [začít s Průzkumníkem metrik a s](metrics-getting-started.md) [pokročilými funkcemi v Průzkumníkovi metrik](metrics-charts.md). Můžete také zobrazit [Příklady](metric-chart-samples.md) konfigurovaných grafů metriky.

## <a name="cant-find-your-resource-to-select-it"></a>Nepovedlo se najít prostředek pro jeho výběr.

Kliknuli jste na tlačítko **Vybrat prostředek** , ale prostředek se v dialogovém okně pro výběr prostředku nezobrazuje.

**Řešení:** Průzkumník metrik vyžaduje před výpisem dostupných prostředků výběr předplatných a skupin prostředků. Pokud se prostředek nezobrazuje:

1. Ujistěte se, že jste v rozevíracím seznamu **předplatné** vybrali správné předplatné. Pokud vaše předplatné není uvedené, klikněte na **Nastavení adresáře a předplatného** a přidejte předplatné s vaším prostředkem.

1. Ujistěte se, že jste vybrali správnou skupinu prostředků.
    > [!WARNING]
    > Nejlepšího výkonu dosáhnete, když při prvním otevření Průzkumníka metrik v rozevíracím seznamu **Skupina prostředků** nejsou žádné předem vybrané skupiny prostředků. Než budete moci zobrazit všechny prostředky, je nutné vybrat alespoň jednu skupinu.

## <a name="chart-shows-no-data"></a>Graf nezobrazuje žádná data

V některých případech se může stát, že grafy po výběru správných prostředků a metrik nezobrazí žádná data. Toto chování může být způsobeno několika z následujících důvodů:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>Poskytovatel prostředků Microsoft. Insights není pro vaše předplatné registrovaný.

Zkoumání metrik vyžaduje poskytovatele prostředků *Microsoft. Insights* zaregistrovaný ve vašem předplatném. V mnoha případech je zaregistrováno automaticky (tj. po konfiguraci pravidla výstrahy, přizpůsobení nastavení diagnostiky pro libovolný prostředek nebo konfigurace pravidla automatického škálování). Pokud poskytovatel prostředků Microsoft. Insights není zaregistrovaný, musíte ho ručně zaregistrovat pomocí následujících kroků popsaných v tématu [poskytovatelé a typy prostředků Azure](../../azure-resource-manager/resource-manager-supported-services.md).

**Řešení:** Otevřete **předplatné**, kartu **poskytovatelé prostředků** a ověřte, jestli je pro vaše předplatné zaregistrované *Microsoft. Insights* .

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Nemáte dostatečná přístupová práva k vašemu prostředku.

V Azure se přístup k metrikám řídí [řízením přístupu na základě role (RBAC)](../../role-based-access-control/overview.md). Abyste mohli prozkoumat metriky pro libovolný prostředek, musíte být členem [čtecího modulu pro monitorování](../../role-based-access-control/built-in-roles.md#monitoring-reader), [přispěvatelem monitorování](../../role-based-access-control/built-in-roles.md#monitoring-contributor)nebo [přispěvatelem](../../role-based-access-control/built-in-roles.md#contributor) .

**Řešení:** Ujistěte se, že máte dostatečná oprávnění pro prostředek, ze kterého prozkoumáte metriky.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>Váš prostředek negeneroval metriky během vybraného časového rozsahu.

Některé prostředky stále negenerují své metriky. Azure například nebude shromažďovat metriky pro zastavené virtuální počítače. Jiné prostředky mohou vygenerovat své metriky pouze v případě, že dojde k nějaké situaci. Například metrika ukazující čas zpracování transakce vyžaduje alespoň jednu transakci. Pokud ve vybraném časovém rozsahu neexistovaly žádné transakce, graf bude přirozeně prázdný. I když je většina metrik v Azure shromažďována každou minutu, některé z nich jsou shromažďovány méně často. Další podrobnosti o metrikě, kterou se snažíte prozkoumat, najdete v dokumentaci metriky.

**Řešení:** Změňte čas grafu na širší rozsah. Můžete začít od "posledních 30 dnů" s použitím většího časového rozlišení (nebo se spoléhat na možnost automatické členitosti v časovém intervalu).

### <a name="you-picked-a-time-range-greater-than-30-days"></a>Vybrali jste časový rozsah delší než 30 dní.

[Většina metrik v Azure se ukládá po dobu 93 dnů](data-platform-metrics.md#retention-of-metrics). Můžete ale zadávat dotazy jenom na data o více než 30 dnech na jakémkoli jednom grafu. Toto omezení se nevztahuje na [metriky založené na protokolech](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

**Řešení:** Pokud se zobrazí prázdný graf nebo se v grafu zobrazí pouze část dat metriky, ověřte, zda rozdíl mezi počátečním a koncovým datem v nástroji pro výběr času nepřekročí 30 dní.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Všechny hodnoty metrik byly mimo rozsah uzamčené osy y.

Tím, že [zamknete hranice grafu osy y](metrics-charts.md#lock-boundaries-of-chart-y-axis), můžete neúmyslně nastavit oblast zobrazení grafu, aby nezobrazovala spojnici grafu. Pokud je například osa y pevně nastavená na rozsah mezi 0 a 50% a metrika má konstantní hodnotu 100%, řádek se vždycky vykreslí mimo viditelnou oblast, takže se graf zobrazí jako prázdný.

**Řešení:** Ověřte, že hranice osy y grafu nejsou uzamčené mimo rozsah hodnot metriky. Pokud jsou hranice osy y zamčené, můžete je dočasně resetovat, aby se zajistilo, že hodnoty metrik nespadají mimo rozsah grafu. Uzamykání rozsahu osy y se nedoporučuje s automatickou členitost grafů s agregací **Sum**, **min**a **Max** , protože jejich hodnoty se změní na členitost změnou velikosti okna prohlížeče nebo při přechodu na jedno rozlišení obrazovky. do druhé. Přepínání členitosti může zůstat prázdný prostor zobrazení grafu.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Prohlížíte se na metriku hostovaného operačního systému, ale nepovolili jste diagnostické rozšíření Azure.

Kolekce metrik **operačního systému hosta** vyžaduje konfiguraci rozšíření Azure Diagnostics nebo jeho povolení pomocí panelu **nastavení diagnostiky** pro váš prostředek.

**Řešení:** Pokud je povolené rozšíření Azure Diagnostics, ale stále nemůžete zobrazit metriky, postupujte podle kroků popsaných v [Průvodci odstraňováním potíží s rozšířením Azure Diagnostics](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal). Další informace najdete v tématu řešení potíží s [oborem názvů a metrikami hostovaného operačního systému](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics) .

## <a name="error-retrieving-data-message-on-dashboard"></a>Chybová zpráva při načítání dat na řídicím panelu

K tomuto problému může dojít, když byl řídicí panel vytvořen pomocí metriky, která byla později vystaralá a odebrána z Azure. Pokud chcete ověřit, že se jedná o tento případ, otevřete kartu **metriky** prostředku a zkontrolujte dostupné metriky ve výběru metriky. Pokud není metrika zobrazená, byla metrika odebrána z Azure. Když je metrika zastaralá, je k dispozici lepší nová metrika, která nabízí podobnou perspektivu týkající se stavu prostředků.

**Řešení:** Aktualizujte dlaždici, která selhala, výběrem alternativní metriky pro váš graf na řídicím panelu. Můžete [si prohlédnout seznam dostupných metrik pro služby Azure](metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>Graf znázorňuje čárkovanou čáru

Grafy metrik Azure používají čárkovaný styl čáry k označení toho, že mezi dvěma známými datovými body zrnitosti chybí hodnota (označovaná také jako hodnota null). Například pokud v selektoru jste vybrali členitou časovou prodlevu 1 minutu, ale metrika byla nahlášena v 07:26, 07:27, 07:29 a 07:30 (Všimněte si, že se v průběhu druhého a třetího datového bodu objevila mezera), pak se přerušovaná čára připojí 07:27 a 07:29 a připojení k pevné čáře všechny ostatní datové body. Přerušovaná čára klesne dolů na nulu, pokud metrika používá agregaci **Count** a **Sum** . U agregací **AVG**, **min** nebo **Max** se přerušovaná čára spojí dva nejbližší známé datové body. Také, pokud data chybějí na pravé straně grafu nebo na levé straně grafu, je přerušovaná čára rozšířena směrem k směru chybějícího datového bodu.
  ![metric obrázek @ no__t-1

**Řešení:** Toto chování je záměrné. Je vhodný pro identifikaci chybějících datových bodů. Spojnicový graf je nadřízenou volbou pro vizualizaci trendů metrik s vysokou hustotou, ale může být obtížné ji interpretovat pro metriky s zhuštěnými hodnotami, zejména v případě, že se v souvislosti s časovým intervalem jsou důležité hodnoty. Přerušovaná čára usnadňuje čtení těchto grafů, ale pokud je váš graf stále nejasný, zvažte zobrazení metriky s jiným typem grafu. Například rozptýlený graf pro stejnou metriku jasně zobrazuje každé časové intervaly tím, že vizualizuje tečku, pokud existuje hodnota a přeskočí datový bod úplně, když hodnota chybí: ![metric image @ no__t-1

   > [!NOTE]
   > Pokud pro svou metriku stále upřednostňujete spojnicový graf, přesunutím myši na graf může pomáhat s vyhodnocením časového rozlišení tím, že zvýrazníte datový bod na místě ukazatele myši.

## <a name="chart-shows-unexpected-drop-in-values"></a>Graf zobrazuje neočekávané hodnoty zrušení v hodnotách

V mnoha případech je vnímaná odkládací hodnota metriky nesrozumitelnější data zobrazená v grafu. V případě, že se v grafu zobrazují nejaktuálnější minuty, můžete být v omylu započítány na základě součtů nebo počtů, protože poslední datové body metriky ještě nebyly od Azure přijaty nebo zpracovány. V závislosti na službě může být latence zpracování metrik v rozsahu několika minut. V grafech zobrazujících nedávný časový rozsah s členitou přesností 1 nebo 5 minut se může poznamenat, že hodnota za posledních několik minut bude podrobná: ![metric image @ no__t-1.

**Řešení:** Toto chování je záměrné. Věříme, že se data zobrazují hned po obdržení dat, která jsou výhodná, i když jsou data *částečná* nebo *neúplná*. To vám umožní udělat si důležitý závěr dřív a zahájit šetření hned. Například u metriky, která zobrazuje počet selhání, se zobrazí částečná hodnota X, která vám oznamuje, že v dané minutě došlo k nejméně × selhání. Můžete zahájit šetření problému hned a nečekat, než se zobrazí přesný počet chyb, ke kterým došlo v této minutě, což nemusí být důležité. Graf se po obdržení celé sady dat aktualizuje, ale v tuto chvíli může zobrazit nové nedokončené datové body z novějších minut.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>Nejde vybrat obor názvů a metriky hostovaného operačního systému.

Virtuální počítače a služba Virtual Machine Scale Sets mají dvě kategorie metrik: metriky **hostitele virtuálních počítačů** shromažďované hostitelským prostředím Azure a metriky **hostovaného operačního systému (Classic)** shromažďované [agentem monitorování. ](agents-overview.md)běží na virtuálních počítačích. Agenta monitorování nainstalujete povolením [diagnostického rozšíření Azure](diagnostics-extension-overview.md).

Ve výchozím nastavení se metriky hostovaného operačního systému ukládají v Azure Storage účtu, který vybíráte z karty **nastavení diagnostiky** vašeho prostředku. Pokud nejsou shromažďovány metriky hostovaného operačního systému nebo k nim Průzkumník metrik nemůže získat přístup, zobrazí se pouze obor názvů metriky **hostitele virtuálního počítače** :

![Obrázek metriky](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**Řešení:** Pokud nevidíte obor názvů a metriky oborů **hostovaného operačního systému (Classic)** v Průzkumníkovi metrik:

1. Potvrďte, že je pro shromažďování metrik zapnuté a nakonfigurované [diagnostické rozšíření Azure](diagnostics-extension-overview.md) .
    > [!WARNING]
    > [Agenta Log Analytics](agents-overview.md#log-analytics-agent) (označovaného také jako Microsoft Monitoring Agent nebo "MMA") nemůžete použít k odeslání **hostovaného operačního systému** do účtu úložiště.

1. Ujistěte se, že je [pro vaše předplatné zaregistrovaný](metrics-troubleshoot.md#microsoftinsights-resource-provider-isnt-registered-for-your-subscription)poskytovatel prostředků **Microsoft. Insights** .

1. Ověřte, jestli není účet úložiště chráněný bránou firewall. Azure Portal potřebuje přístup k účtu úložiště, aby bylo možné načíst data metrik a sekreslit grafy.

1. Pomocí [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) ověřte, zda jsou metriky předávány do účtu úložiště. Pokud nejsou metriky shromažďovány, postupujte podle pokynů pro [řešení potíží s rozšířením Azure Diagnostics](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>Další kroky

* [Další informace o tom, jak začít s Průzkumníkem metrik](metrics-getting-started.md)
* [Další informace o rozšířených funkcích Průzkumníka metrik](metrics-charts.md)
* [Zobrazit seznam dostupných metrik pro služby Azure](metrics-supported.md)
* [Zobrazit příklady konfigurovaných grafů](metric-chart-samples.md)
