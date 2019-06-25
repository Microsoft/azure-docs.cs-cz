---
title: Upozornění protokolů ve službě Azure Monitor
description: Aktivační událost e-mailů, oznámení, volání adresy URL webů (webhooky), nebo automatizace při splnění zadané podmínky analytického dotazu pro Azure Alerts.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 5/31/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: ae35c735cffeb8cd85af1f32bb2d14ede6dc6b69
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427414"
---
# <a name="log-alerts-in-azure-monitor"></a>Upozornění protokolů ve službě Azure Monitor

Tento článek obsahuje podrobnosti o upozornění protokolů jsou jedním z typů výstrah, které jsou podporovány v rámci [Azure Alerts](../../azure-monitor/platform/alerts-overview.md) a umožnit uživatelům použít analytické platformy Azure jako základ pro generování výstrah.

Upozornění protokolu se skládá z prohledávání protokolů pravidel vytvořených pro [protokoly Azure monitoru](../../azure-monitor/learn/tutorial-viewdata.md) nebo [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events). Další informace o jejich využití najdete v tématu [vytvoření upozornění protokolu v Azure](../../azure-monitor/platform/alerts-log.md)

> [!NOTE]
> Data z oblíbených protokolů [protokoly Azure monitoru](../../azure-monitor/learn/tutorial-viewdata.md) je teď taky dostupná na platformě metriky ve službě Azure Monitor. Pro zobrazení podrobností o [upozornění metriky pro protokoly](../../azure-monitor/platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Hledání pravidel upozornění protokolů – definice a typy

Pravidla prohledávání protokolu vytváří služba Azure Alerts pro automatické spouštění zadaných dotazů na protokoly v pravidelných intervalech.  Pokud výsledky dotazu na protokol splňují konkrétní kritéria, vytvoří se záznam upozornění. Pravidlo potom může automaticky spustit jednu nebo více akcí pomocí [skupin akcí](../../azure-monitor/platform/action-groups.md). [Azure Monitoring Přispěvatel](../../azure-monitor/platform/roles-permissions-security.md) role pro vytváření, úpravu a aktualizaci upozornění protokolů se může vyžadovat; spolu s přístup & dotazu provádění práva pro cíle analytics v pravidlo upozornění nebo dotaz na upozornění. V případě, že vytváření uživatel nemá přístup k všechny cíle analytics v pravidlo upozornění nebo dotaz na upozornění – vytvoření pravidla může selhat nebo pravidel upozornění protokolů se spustí pomocí částečných výsledků.

Pravidla vyhledávání protokolů jsou definovány následující podrobnosti:

- **Protokolování dotazu**.  Dotaz, který se spustí pokaždé, když se pravidlo upozornění aktivuje.  Vrácené tímto dotazem záznamy slouží k určení, jestli je výstraha aktivovat. Analytický dotaz je možné pro konkrétní pracovní prostor Log Analytics nebo aplikaci Application Insights a dokonce i pracovat nad více [více prostředky Log Analytics a Application Insights](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) zadaný uživatel má přístup stejně jako práva na všechny dotazy prostředky. 
    > [!IMPORTANT]
    > [dotaz napříč prostředky](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) podpora v upozornění protokolu pro Application Insights a upozornění protokolů pro [Log Analytics nakonfigurované pomocí rozhraní API scheduledQueryRules](../../azure-monitor/platform/alerts-log-api-switch.md) pouze.

    Některé analytické příkazy a kombinace nejsou kompatibilní s využitím v upozornění protokolu; pro další podrobnosti zobrazíte [protokolu dotazy výstrah ve službě Azure Monitor](../../azure-monitor/platform/alerts-log-query.md).

- **Časové období**.  Určuje časový rozsah dotazu. Dotaz vrátí pouze záznamy vytvořené v tomto rozsahu před aktuálním časem. Časové období omezuje data načtena dotaz protokolu, aby se zabránilo zneužití a připojení, vyřešíte nepřetržitou jakýkoli příkaz času (například před) používaných v dotazu protokolu. <br>*Například toto časové období je nastavený na 60 minut a spuštění dotazu v 13:15, se vrátí pouze záznamy vytvořené mezi 12:15 PM a 1:15 PM do spuštění protokolu dotazu. Když teď dotaz protokolu používá čas příkazu, jako jsou před (7d), protokolu by spustí dotaz pouze pro data mezi 12:15 PM a 1:15 PM - jakoby dat existuje pouze posledních 60 minut. A ne po dobu sedmi dní dat, jak je uvedeno v protokolu dotazu.*

- **Frekvence**.  Určuje, jak často se má spustit dotaz. Může být libovolná hodnota mezi 5 minutami a 24 hodinami. By měla být větší nebo menší než toto časové období.  Pokud je hodnota větší než časové období, riskujete záznamy se vynechalo.<br>*Představte si třeba po dobu 30 minut a frekvenci 60 minut.  Pokud je dotaz spuštěn v 1:00, vrátí záznamy 12:30 až 1:00 PM.  Při příštím spuštění dotazu by je 2:00, kdy vracel záznamy 1:30 až 2:00.  Záznamy, které vytvoří až 1:00, 1:30 by nikdy být vyhodnocena.*

- **Prahová hodnota**.  K určení, zda má být vytvořena výstraha se vyhodnocují výsledky prohledávání protokolů.  Prahová hodnota se liší pro různé typy pravidel upozornění na prohledávání protokolu.

Pravidla vyhledávání protokolů už to pro [protokoly Azure monitoru](../../azure-monitor/learn/tutorial-viewdata.md) nebo [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events), mohou být dvou typů. Každý z těchto typů je podrobně popsány v následující části.

- **[Počet výsledků](#number-of-results-alert-rules)** . Jediná výstraha vytvořena, když počet záznamů vrácených prohledávání protokolů překročí zadaný počet.
- **[Metriky měření](#metric-measurement-alert-rules)** .  Upozornění bylo vytvořeno pro každý objekt ve výsledcích prohledávání protokolů s hodnotami, které překročí zadanou prahovou hodnotu.

Rozdíly mezi typy pravidla upozornění jsou následující.

- *Počet výsledků* pravidla upozornění vždy vytvoří jednu výstrahu, nějakou *měření metriky* pravidlo výstrahy vytvoří upozornění pro každý objekt, který překračuje prahovou hodnotu.
- *Počet výsledků* pravidla upozornění vytvořit upozornění, když je prahová hodnota překročena jednorázově. *Metriky měření* pravidla upozornění může vytvořit upozornění, pokud je překročena prahová hodnota počtu dobu v konkrétním časovém intervalu.

### <a name="number-of-results-alert-rules"></a>Počet pravidel upozornění výsledky

**Počet výsledků** pravidla upozornění vytvoření jedinou výstrahu, když počet záznamů vrácených dotazem vyhledávání překročí zadanou prahovou hodnotu. Tento typ upozornění pravidla je ideální pro práci s událostmi, jako jsou protokoly událostí Windows, Syslog, odpovědi webové aplikace a vlastní protokoly.  Chcete vytvořit upozornění, když se vytvoří událost konkrétní chyby nebo při vytváření více chybové události v konkrétním časovém období.

**Prahová hodnota**: Prahová hodnota pro počet pravidel upozornění výsledků je větší nebo menší než jednu konkrétní hodnotu.  Pokud počet záznamů vrácených prohledávání protokolů, které splňují tato kritéria, vytvoří se výstraha.

Výstrahy v rámci jediné události, nastavte počet výsledků na hodnotu větší než 0 a zkontrolujte výskyt jedné události, který byl vytvořen od posledního spuštění dotazu. Některé aplikace může zaznamenat občasné chybu, která by neměla být nutně vygenerovat výstrahu.  Aplikace může například opakujte proces, který vytvoří událost chyby a potom příště se úspěšně provedla.  V takovém případě nemusí chtít vytvořit výstrahu, pokud více událostí jsou vytvořené v konkrétním časovém období.  

V některých případech můžete chtít vytvořit výstrahu chybí událost.  Například může proces protokolu běžné události k označení, že funguje správně.  Pokud není některá z těchto událostí protokolu v konkrétním časovém období, je třeba vytvořit výstrahu.  V takovém případě můžete nastavit mez na **menší než 1**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Příklad upozornění protokolu typu počet záznamů

Představte si třeba situaci, ve kterém chcete vědět, když vaše webové aplikace poskytuje odpověď pro uživatele, kteří kód 500 (to znamená) vnitřní chyba serveru. Vytvoříte pravidlo upozornění s následujícími podrobnostmi:  

- **Dotaz:** požadavky | kde kód výsledku == "500"<br>
- **Časové období:** 30 minut<br>
- **Četnosti upozornění:** pěti minut<br>
- **Prahová hodnota:** Větší než 0.<br>

Výstraha by spusťte dotaz každých 5 minut, 30 minut dat – vás pod rouškou u jakéhokoli záznamu, kde byl výsledný kód 500. Pokud ještě jeden takový záznam najde, aktivuje se upozornění a aktivuje akci nakonfigurovanou.

### <a name="metric-measurement-alert-rules"></a>Pravidla upozornění metriky měření

**Metriky měření** pravidla upozornění vytvořit upozornění pro každý objekt v dotazu s hodnotou, která překročí zadanou prahovou hodnotu a zadaný aktivační podmínka. Na rozdíl od **počet výsledků** pravidla, upozornění **měření metriky** pravidla upozornění fungovat, pokud výsledek analytics poskytuje časové řady. Mají odlišné následující rozdíly proti **počet výsledků** pravidla upozornění.

- **Agregační funkce**: Určuje, která se provede výpočet a potenciálně číselná pole agregovat.  Například **count()** vrátí počet záznamů v dotazu **avg(CounterValue)** vrací průměr pole CounterValue v intervalu. V dotazu je agregační funkce musí být s názvem/volá: AggregatedValue a poskytnout číselnou hodnotu. 

- **Pole Seskupit**: Pro každou instanci tohoto pole se vytvoří záznam s agregovanou hodnotu a pro každý může být vygenerována výstraha.  Například pokud chcete generovat výstrahy pro každý počítač, můžete využít **počítačem**. V případě více skupin polí zadaná v oznámení dotazu se uživatel může určit, jaké pole se má použít k řazení výsledků pomocí **agregační na** parametr (metricColumn)

    > [!NOTE]
    > *Agregace na* (metricColumn) možnost je dostupná pro upozornění protokolu typ měření metriky pro Application Insights a upozornění protokolů pro [Log Analytics nakonfigurované pomocí rozhraní API scheduledQueryRules](../../azure-monitor/platform/alerts-log-api-switch.md) pouze.

- **Interval**:  Definuje časový interval, po kterou se data agregují.  Například, pokud jste zadali **pět minut**, by se vytvořily záznam pro každou instanci tohoto pole Skupina v 5minutových intervalech agregován v časovém období zadaný pro oznámení.

    > [!NOTE]
    > Funkce Bin musí použít v dotazu k určení intervalu. Jak bin() může vést k nerovnost časové intervaly – výstraha se automaticky převede bin příkaz bin_at příkazu s odpovídajícím časem za běhu, zajistit výsledky s pevnému bodu. Typ metriky měření upozornění protokolu je navržen pro práci s dotazy s až tři výskyty bin() příkaz
    
- **Prahová hodnota**: Prahová hodnota pro pravidla upozornění metriky měření je definován tak, že agregované hodnoty a celou řadu.  Pokud libovolný datový bod v prohledávání protokolu překročí tuto hodnotu, se považuje za porušení zabezpečení.  Pokud počet narušení v pro libovolný objekt ve výsledcích překročí zadanou hodnotu, je vytvořit výstrahu pro daný objekt.

Chybná konfigurace *agregační na* nebo *metricColumn* volba může způsobit pravidla upozornění na misfire. Další informace najdete v tématu [řešení potíží při měření metriky pravidlo výstrahy je nesprávný](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect).

#### <a name="example-of-metric-measurement-type-log-alert"></a>Příklad upozornění protokolu typ měření metriky

Představte si třeba situaci, kde chcete výstrahu Pokud jakýkoli počítač překročí 90 % třikrát více než 30 minut využití procesoru.  Vytvoříte pravidlo upozornění s následujícími podrobnostmi:  

- **Dotaz:** Perf | kde ObjectName == "Procesor pro" a hodnota CounterName == "čas procesoru v %" | summarize AggregatedValue = avg(CounterValue) podle bin (TimeGenerated, 5m), počítač<br>
- **Časové období:** 30 minut<br>
- **Četnosti upozornění:** pěti minut<br>
- **Alert Logic - podmínku a prahovou hodnotu:** Větší než 90<br>
- **Pole skupiny (agregaci na):** Computer
- **Aktivovat upozornění na základě:** Celkový počet poruší větší než 2<br>

Dotaz by vytvořit průměrnou hodnotu pro každý počítač v intervalech 5 minut.  Tento dotaz se spustí každých 5 minut, než se data shromážděná za předchozí 30 minut. Protože je pole Skupina (agregaci v), zvolené úložiště se sloupcovou strukturou "Computer" - pro různé hodnoty "Computer" je rozdělit AggregatedValue a průměrné využití procesoru pro každý počítač, je určen pro bin dobu 5 minut.  (Řekněme) tři počítače, bude výsledkem vzorku dotazu pro níže.


|TimeGenerated [UTC] |Computer  |AggregatedValue  |
|---------|---------|---------|
|20xx-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

Pokud výsledek dotazu bude zobrazena, se zobrazí jako.

![Ukázkové výsledky dotazu](media/alerts-unified-log/metrics-measurement-sample-graph.png)

V tomto příkladu vidíme v přihrádkách 5 minut pro všechny tři počítače - průměrné využití procesoru jako vypočítanou pro 5 minut. Prahová hodnota 90 porušovány podle srv01 pouze jednou v 1:25 bin. Ve srovnání s srv02 překročí 90 prahovou hodnotu na 1:10, 1:15 a 1:25 přihrádky; zatímco srv03 překročí 90 prahovou hodnotu na 1:10, 1:15, 1:20 a 1:30.
Protože je nakonfigurované upozornění tak aktivační událost vycházející celkový počet porušení jsou větší než dvě, vidíme, že srv02 a srv03 pouze splňují kritéria. Samostatné výstrahy proto by se vytvořily pro srv02 a srv03 vzhledem k tomu, u nichž nebyla dodržena 90 % prahové hodnoty dvakrát napříč více času přihrádky.  Pokud *aktivovat upozornění na základě:* parametr místo toho se nakonfigurovalo pro *průběžné porušení* možnost, pak by se aktivuje výstrahu **pouze** pro srv03, protože to u nichž nebyla dodržena Prahová hodnota pro tři po sobě jdoucích přihrádkách z 1:10 1:20. A **není** pro srv02, protože u nichž nebyla dodržena prahová hodnota pro dva přihrádky po sobě jdoucích z 1:10 do 1:15.

## <a name="log-search-alert-rule---firing-and-state"></a>Hledání pravidel upozornění protokolů – stav a jeho spuštění

Pravidel upozornění protokolů vyhledávání funguje na logiku zabezpečuje pomocí vhodné uživatelem závislosti na konfiguraci a vlastní analýzy dotazech. Od logiku monitorování včetně přesné podmínky nebo důvod, proč by měly aktivovat pravidlo upozornění, je zapouzdřena v dotazu analytics – které se můžou lišit v jednotlivých pravidel upozornění protokolů. Upozornění Azure má omezené informace o konkrétní základní příčiny (nebo) scénář právě vyhodnocuje, když je dosažená nebo Překročená prahová hodnota podmínky pravidel upozornění protokolů hledání. Proto upozornění protokolů se označují jako bez stavu. MSMQEvent_Arrived; pravidel upozornění protokolů se zachovat, tak dlouho, dokud se vyskytl výstražný stav splníte tak, že výsledek vlastního analytického dotazu, které jsou k dispozici. Bez upozornění každý získávání převedl, jako je uvnitř dotazu analytics uživatelem zadané maskované logiky přesnou příčinu selhání monitorování. Bylo aktuálně žádný mechanismus pro výstrahy monitorování Azure k potvrzením odvození se vyřešit hlavní příčinu.

Umožňuje nám stejným způsobem pracovat s praktické příklad naleznete v tématu. Předpokládejme máme pravidel upozornění protokolů volá *Contoso upozornění protokolu*, protože podle konfigurace v [ukázkové chatovací aplikaci obsažené upozornění protokolu typu Number výsledků](#example-of-number-of-records-type-log-alert) – kde vlastní výstrahy dotaz byl navržen k vyhledání 500 Kód výsledku v protokolech.

- Výsledek hledání v protokolu: 05: 00 provedení Contoso upozornění protokolu pomocí výstrah služby Azure, vrátil nulovou záznamy s kódem výsledku s 500. Protože nula je pod prahovou hodnotou, což není aktivováno upozornění.
- Na další iteraci: 10: 00 provedení Contoso upozornění protokolu pomocí výstrah služby Azure výsledek hledání v protokolu ve formě pět záznamů s kódem výsledku 500. Protože pět překročí prahovou hodnotu a výstraha se aktivuje pomocí přidružené akce se aktivují.
- Výsledek hledání v protokolu v 1:15 PM provedení Contoso upozornění protokolu pomocí výstrah služby Azure, k dispozici dva záznamy s kódem výsledku 500. Protože dvě překročí prahovou hodnotu a výstraha se aktivuje pomocí přidružené akce se aktivují.
- Nyní na další iteraci: 20: 00 provedení Contoso upozornění protokolu Azure výstraha poskytuje výsledek hledání v protokolu znovu žádné záznamy s kódem výsledku 500. Protože nula je pod prahovou hodnotou, což není aktivováno upozornění.

Ale v případě výše uvedené v 1:15 PM - upozornění Azure nemůže určit, že základní problémy zaznamenáno v 1:10 přetrvávají a pokud je net nové chyby. Uživatelem zadaný dotaz může být zohlední starší záznamy - jistotu upozornění v Azure. Od logiku pro výstrahy, je zapouzdřena v upozornění dotazu - to dva záznamy s kódem 500 výsledku zaznamenáno v 1:15 PM může nebo nemusí být už jednou vyskytl: 10: 00. Proto k pečlivého upozornění, když se zpracovává Contoso upozornění protokolu v 13:15, nakonfigurované akce se znovu aktivuje. Nyní: 20: 00 Když žádné záznamy se zobrazují s kódem výsledku 500 - upozornění Azure nemůže být jistotu, že teď je vyřešen příčiny 500 výsledný kód zaznamenáno v 1:10 PM a 1:15 PM a Azure Monitor výstrah můžete bez obav odvodit 500 – Chyba problémy se neprovede ze stejného důvodu znovu s. Proto bude Contoso upozornění protokolu nebyl změněn na vyřešený v řídicím panelu Azure výstrahy a/nebo oznámení odeslaných s informacemi o tom vyřešení upozornění. Místo toho můžete uživatele, kdo rozumí použití prvků přesné podmínky nebo důvod pro logiku vložený do dotazu analytics [označit výstrahy jako uzavřenou](alerts-managing-alert-states.md) podle potřeby.

## <a name="pricing-and-billing-of-log-alerts"></a>Ceny a fakturace protokol výstrah

Ceny za upozornění protokolů použít je uvedeno v [ceny služby Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) stránky. V Azure účtuje poplatky za upozornění protokolů jsou reprezentovány jako typ `microsoft.insights/scheduledqueryrules` pomocí:

- Upozornění protokolů Application insights, zobrazí se přesný název výstrahy spolu s skupinu prostředků a vlastnosti výstrahy
- Upozornění protokolů v Log Analytics, zobrazí se přesný název výstrahy spolu s skupinu prostředků a vlastnosti výstrahy; Při použití [scheduledQueryRules rozhraní API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

[Starší verze rozhraní API pro analýzu protokolů](../../azure-monitor/platform/api-alerts.md) má akce výstrah a plány jako součást Log Analytics uložené hledání a není správné [prostředků Azure](../../azure-resource-manager/resource-group-overview.md). Proto k povolení fakturace pro tyto starší verze protokolu výstrahy vytvořené pro Log Analytics pomocí webu Azure Portal **bez** [přepnutí na nové rozhraní API](../../azure-monitor/platform/alerts-log-api-switch.md) nebo prostřednictvím [starší verze rozhraní API pro analýzu protokolů](../../azure-monitor/platform/api-alerts.md) - pravidla upozornění skryté pseudo jsou vytvořeny na `microsoft.insights/scheduledqueryrules` fakturace v Azure. Pravidla upozornění skryté pseudo vytvořená pro fakturaci na `microsoft.insights/scheduledqueryrules` uvedeno jako `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` společně se skupinu prostředků a vlastnosti výstrahy.

> [!NOTE]
> Pokud platné znaky, jako `<, >, %, &, \, ?, /` jsou k dispozici, nahradí se `_` v pravidle výstrahy skryté pseudo název a proto také ve službě Azure fakturovat.

Odebrat prostředky skryté scheduleQueryRules vytvořené pro účely fakturace pomocí pravidel upozornění [starší verze rozhraní API pro analýzu protokolů](api-alerts.md), uživatel provádět následující:

- Buď uživatel může [přepnout předvoleb rozhraní API pro pravidla upozornění v pracovním prostoru Log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md) a bez ztráty jejich upozornění pravidel nebo monitorování Přesun do Azure Resource Manageru kompatibilní [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). A tím eliminuje nutnost pseudo skrytá pravidla výstrah pro účely fakturace.
- Nebo pokud uživatel nechce přepnout předvoleb rozhraní API, uživatel bude muset **odstranit** původní plán a akce upozornění pomocí [starší verze rozhraní API pro analýzu protokolů](api-alerts.md) nebo odstranit v [webu Azure portal původní pravidel upozornění protokolů](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal)

Kromě toho pro skryté scheduleQueryRules prostředky vytvořené pro účely fakturace pomocí pravidel upozornění [starší verze rozhraní API pro analýzu protokolů](api-alerts.md), všechny operace úprav jako PUT se nezdaří. Jako `microsoft.insights/scheduledqueryrules` typ pseudo pravidla jsou za účelem fakturace pravidla upozornění vytvořené pomocí [starší verze rozhraní API pro analýzu protokolů](api-alerts.md). Každá změna pravidla upozornění by mělo být provedeno pomocí [starší verze rozhraní API pro analýzu protokolů](api-alerts.md) (nebo) uživatel může [přepnout předvoleb rozhraní API pro pravidla upozornění](../../azure-monitor/platform/alerts-log-api-switch.md) používat [scheduledQueryRules rozhraní API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) Místo toho.

## <a name="next-steps"></a>Další postup

* Další informace o [vytváření v protokolu upozornění v Azure](../../azure-monitor/platform/alerts-log.md).
* Vysvětlení [webhooky v protokolu upozornění v Azure](alerts-log-webhook.md).
* Další informace o [upozornění Azure](../../azure-monitor/platform/alerts-overview.md).
* Další informace o [Application Insights](../../azure-monitor/app/analytics.md).
* Další informace o [Log Analytics](../../azure-monitor/log-query/log-query-overview.md).