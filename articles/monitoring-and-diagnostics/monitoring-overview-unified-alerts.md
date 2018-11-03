---
title: Sjednocené výstrah ve službě Azure Monitor
description: Popis jednotné upozornění v Azure, které vám pomohou se správou výstrah a upozornění pravidel napříč službami Azure.
author: manishsm-msft
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: mamit
ms.component: alerts
ms.openlocfilehash: 30b2d60868702c6113612668b8e4cf9975aa2c40
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50962193"
---
# <a name="unified-alerts-in-azure-monitor"></a>Sjednocené výstrah ve službě Azure Monitor

## <a name="overview"></a>Přehled

> [!NOTE]
>  Novém jednotném výstrah rozhraní, který vám umožní spravovat výstrahy z více předplatných a zavádí upozornění stavů a inteligentní skupin je momentálně dostupná ve verzi public preview. Viz poslední část tohoto článku pro popis této rozšířeného prostředí a proces pro jeho povolení.


Tento článek popisuje jednotné prostředí výstrah ve službě Azure Monitor. [Předchozí zkušenosti upozornění](monitoring-overview-alerts.md) je k dispozici **upozornění (klasická)** v nabídce Azure Monitor. 

## <a name="features-of-the-unified-alert-experience"></a>Funkce sjednocené prostředí pro výstrahy

Jednotné prostředí má následující výhody prostřednictvím klasického prostředí:

-   **Lepší systému oznámení**: [skupiny akcí](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) jsou pojmenované skupiny oznámení a akce, které lze znovu použít ve více výstrah. 
- **Sjednocené prostředí pro tvorbu**: upozornění a pravidla upozornění metrik, protokolů a protokolů aktivit Azure Monitor, Log Analytics a Application Insights se dají spravovat na jednom místě. 
- **Zobrazení aktivuje upozornění Log Analytics na portálu Azure portal**: upozornění Log Analytics se teď dají Zobrazit výstrahy z jiných zdrojů na webu Azure Portal. Dříve byly výstrahy z jiných zdrojů samostatné portálu.
- **Oddělení aktivovaná upozornění a pravidla upozornění**: pravidla upozornění se teď rozlišují z výstrah. Pravidlo výstrahy je definice podmínku, která aktivuje výstrahu. Výstraha je instance jeho spuštění pravidla upozornění.
- **Lepší workflow**: jednotné prostředí pro tvorbu výstrahy vás provede procesem konfigurace pravidla výstrahy.
 
Upozornění na metriku mají následující vylepšení klasického upozornění metrik:

-   **Vylepšené latence**: upozornění na metriku můžete spustit tak často, jak každou minutu. Klasické metriky upozornění vždy spustit s frekvencí každých 5 minut. Upozornění protokolů ještě déle než minutu z důvodu čas potřebný k ingestování protokoly zpoždění. 
-   **Podpora pro vícerozměrné metriky**: může upozornit na jednodimenzionální metriky, což znamená, že můžete sledovat konkrétní instanci metriky.
-   **Větší kontrolu nad metriky podmínky**: můžete definovat bohatší pravidel upozornění, které podporují monitorování maximální minimální, průměrné a celkové hodnoty metriky.
-   **Kombinované monitorování několika metrik**: můžete monitorovat až dvě metriky pomocí jednoho pravidla. Výstraha se aktivuje, pokud obě metriky porušení jejich příslušné prahové hodnoty pro zadané časové období.
-   **Metriky na základě protokolů** (omezené veřejné verzi preview): některé protokolu data, která se data přesunou do Log Analytics můžete nyní extrahovat a převést na metrik Azure monitoru a potom zobrazí upozornění na stejně jako jiné metriky. 


## <a name="alert-rules"></a>Pravidla upozornění
Prostředí upozornění jednotné používá následující koncepty pro oddělení pravidla upozornění z výstrah při sjednocujícího prostředí pro vytváření obsahu napříč různé typy výstrah.

| Položka | Definice |
|:---|:---|
| Pravidlo výstrahy | Definice podmínky, aby se vytvořila výstraha. Pravidlo upozornění se skládá z _cílový prostředek_, _signál_, _kritéria_, a _logiky_. Pravidlo výstrahy je aktivní, pokud je pouze _povolené_ stavu.
| Cílový prostředek | Definuje konkrétní prostředky a signály, které jsou k dispozici pro generování výstrah. Cíl může být libovolné prostředky Azure.<br><br>Příklady: virtuální počítač, účet úložiště, škálovací sadu virtuálních počítačů, pracovní prostor Log Analytics, prostředek služby Application Insights |
| Signál | Zdroj dat, protože ho vygeneroval cílový prostředek. Typy podporované signálů, které jsou *metrika*, *protokolu aktivit*, *Application Insights*, a *protokolu*. |
| Kritéria | Kombinace _signál_ a _logiky_ použity na cílový prostředek.<br><br>Příklady: Procento využití procesoru > 70 %, doba odezvy serveru > 4 ms, počet výsledků protokolu dotaz > 100 a tak dále |
| Logika | Uživatelem definované logiku a ověřte, zda je signál, který se v rámci očekávání/hodnoty rozsahu. |
| Akce | Akce provádět, když se aktivuje upozornění. Více akcí může dojít, když se aktivuje upozornění. Tyto výstrahy podporují skupiny akcí.<br><br>Příklady: e-mailem na e-mailovou adresu, volání adresy URL webhooku |
| Stav monitorování | Určuje, zda byl vyřešen podmínku, vytvoří upozornění na metriku. Pravidla upozornění na metriky ukázkový konkrétní metriky v pravidelných intervalech. Při splnění kritérií pro pravidlo výstrahy, pak nová výstraha se vytvoří s podmínkou "aktivována."  Když metrika se definuje tak, znovu, pokud je stále splněna kritéria, pak nic se nestane.  V případě, že nejsou splněna kritéria, pak podmínka výstrahy se změní na "přeložit." Další výstrahu při příštím při splnění kritéria, se vytvoří s podmínkou "aktivována." |


## <a name="alert-pages"></a>Upozornění stránky
Sjednocené výstrahy poskytují jednotné místo, kde můžete zobrazit a spravovat všechna upozornění Azure. Následující části popisují funkce každou jednotlivou stránku jednotné prostředí.

### <a name="alerts-overview-page"></a>Stránku přehled výstrah
**Výstrahy** agregovaný souhrn všech aktivovaná upozornění se zobrazí stránka s přehledem, a celkový počet povolená pravidla upozornění. Změna předplatná nebo parametry filtru aktualizuje agregací a výstrahy vyvolané seznamu.

 ![Přehled výstrah](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### <a name="alert-rules-management"></a>Správa pravidel výstrah
**Pravidla** je jediné stránce pro správu všechna pravidla výstrah ve vašich předplatných Azure. Obsahuje seznam všech pravidel upozornění a může být řazeny podle cílové prostředky, skupiny prostředků, název pravidla nebo stav. Pravidla výstrah můžete také upravit, povolit nebo zakázat z této stránky.

 ![pravidla výstrah](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Vytvoření pravidla upozornění
Výstrahy se můžou vytvořit konzistentním způsobem bez ohledu na to služba monitorování nebo signalizuje, že typ. Všechny výstrahy vyvolané a související podrobnosti jsou dostupné na jedné stránce.
 
Vytvořit nové pravidlo upozornění s následující tři kroky:
1. Vyberte si _cílové_ výstrahy.
1. Vyberte _signál_ z dostupné signály pro cíl.
1. Zadejte _logiky_ použít data z tohoto signálu.
 
Tato zjednodušený proces vytváření už vyžaduje, abyste znali monitorování zdroje nebo signály, které jsou podporovány před výběrem prostředku Azure. Seznam dostupné signály se automaticky filtruje podle cílový prostředek, který jste vybrali, a provede vás těmito možnostmi definování logiky pravidla výstrahy.

Další informace o tom, jak vytvořit pravidla výstrah ve službě [vytvoření, zobrazení a Správa výstrah pomocí Azure monitoru](alert-metric.md).

Výstrahy jsou dostupné v rámci několika služeb monitorování v Azure. Informace, jak a kdy se má používat každý z těchto služeb najdete v tématu [aplikací a prostředků Monitoring Azure](../azure-monitor/overview.md). Následující tabulka obsahuje seznam typů pravidel upozornění, které jsou dostupné v rámci Azure. Také uvádí, co v současné době nepodporuje jednotné prostředí upozornění.

| **Zdroj monitorování** | **Typ signálu**  | **Popis** | 
|-------------|----------------|-------------|
| Azure Monitor | Metrika  | Zkratka [upozornění na metriku téměř v reálném čase](monitoring-near-real-time-metric-alerts.md), podpora, vyhodnocování podmínek metriky často jednou za minutu a povolit pro více metrik a vícerozměrné metriky pravidla. Seznam podporovaných typů prostředků je k dispozici v [novějších upozornění metrik pro služby Azure na webu Azure Portal](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported).<br>[Klasické metriky upozornění](monitoring-overview-alerts.md) nejsou podporovány v nové prostředí upozornění. Najdete je v části upozornění (klasická) na webu Azure Portal. Upozornění classic podporovat některé metriky typy, které dosud nebyla přesunuta na novější upozornění. Úplný seznam najdete v tématu [podporované metriky](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics). |
| Log Analytics | Logs  | Dostávat oznámení nebo spouštět automatizované akce, když vyhledávací dotaz log splňuje určitá kritéria. Upozornění v Log Analytics jsou [kopírovaná do nového prostředí](monitoring-alerts-extend.md). A [Náhled *Log Analytics zaznamená jako metriky* ](monitoring-alerts-extend-tool.md) je k dispozici. Verze preview umožňuje provést určité typy protokolů a jejich převedení na metriky, kde vám může potom upozornit na nich s použitím nové prostředí pro upozorňování. Verzi preview je užitečné, pokud máte protokoly mimo Azure, které chcete získat společně s nativní metrikami Azure monitoru. |
| Protokoly aktivit | Protokol aktivit | Obsahuje záznamy o všech vytvářet, aktualizovat a odstranit některé akce, které byly vytvořeny podle vybraného cíle. |
| Stav služby | Protokol aktivit  | Nepodporuje jednotné výstrahy. Zobrazit [vytvoření upozornění protokolu aktivit pro oznámení služby](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Application Insights | Logs  | Obsahuje protokoly s podrobnostmi o výkonu vaší aplikace. Pomocí dotazu analytics můžete definovat podmínky pro akce, které mají být provedeny v závislosti na aplikační data. |
| Application Insights | Metrika | Nepodporuje jednotné výstrahy. Zobrazit [upozornění na metriku](../application-insights/app-insights-alerts.md). |
| Application Insights | Testy dostupnosti webu | Nepodporuje jednotné výstrahy.  Zobrazit [upozornění webových testů](../application-insights/app-insights-monitor-web-app-availability.md). K dispozici pro všechny weby, které je instrumentováno pro odesílání dat do Application Insights. Když dostupnosti nebo rychlost odezvy webu je nižší než očekávání, dostanete oznámení. |

## <a name="enhanced-unified-alerts-public-preview"></a>Rozšířené jednotné výstrahy (public preview)

Vylepšené výstrahy jednotné prostředí byla vydána ve verzi public preview pro Azure Monitor 1. června 2018. Toto prostředí je založena na výhody [unified výstrahy](#overview), které byly vydány v březnu 2018 a které umožňují spravovat a agregovat jednotlivá upozornění a změnit stav výstrahy. Tato část popisuje nové funkce a jak se zorientovat nové výstrahy stránky na webu Azure Portal.

### <a name="enhanced-unified-alerts"></a>Rozšířené jednotné výstrahy

Nové prostředí obsahuje následující funkce, které nejsou k dispozici v klasickém jednotné prostředí:

- **Zobrazit výstrahy napříč předplatnými**: Nyní můžete zobrazit a spravovat jednotlivé instance dané výstrahy napříč několika předplatnými v rámci jednoho zobrazení.
- **Správa stavu výstrah**: Teď máte stavu, který označuje, zda jsou jste byla potvrzena jako zavřené výstrahy.
- **Uspořádání výstrahy se inteligentní skupinami**: inteligentní skupiny automaticky seskupení souvisejících výstrah, takže je můžete spravovat jako sadu, a ne samostatně.

### <a name="enable-enhanced-unified-alerts"></a>Povolit rozšířené jednotné výstrahy
Povolte nové prostředí upozornění jednotné výběrem pruhu v horní části stránky s upozorněními. Tento proces vytvoří upozornění úložiště, které zahrnuje v posledních 30 dnech aktivovaná upozornění napříč podporované služby. Po povolení nové prostředí můžete vzájemně přepínat mezi novém i starém prostředí tak, že vyberete tento banner.

> [!NOTE]
>  Může trvat několik minut, než se nové prostředí, aby byl zpočátku povolen.

![Banner](media/monitoring-overview-unified-alerts/opt-in-banner.png)

Všechna předplatná, ke kterým máte přístup k proběhne, když povolíte nové prostředí. I když je povoleno celé předplatné, můžete ji zobrazit pouze uživatelé, kteří zvolí nové prostředí. Jiní uživatelé s přístupem k předplatnému musí toto prostředí povolit samostatně.

Povolení nové prostředí upozornění nemá vliv na konfiguraci skupiny akcí nebo oznámení v pravidel upozornění. Změní jen způsob zobrazení a správa instancí aktivovaná upozornění na webu Azure Portal.

### <a name="smart-groups"></a>Inteligentní skupiny
Inteligentní skupiny snížit šum, neboť umožňuje spravovat související výstrahy jako celek, nikoli jako jednotlivé výstrahy. Můžete zobrazit podrobnosti o inteligentní skupiny a nastavit stav podobně jako na tom, jak s výstrahami. Každá výstraha je členem skupiny inteligentní jeden a pouze jeden.

Inteligentní skupiny jsou automaticky vytvořené využitím strojového učení kombinování souvisejících výstrah, které představují jeden problém. Když se vytvoří výstrahu, algoritmus ji přidá do novou inteligentní skupinu nebo existující inteligentní skupinu na základě informací, jako je například historické vzory, podobně jako vlastnosti a podobné struktury. 

V současné době algoritmus uvažuje pouze výstrahy ze stejné služby monitorování v rámci předplatného. Inteligentní skupiny můžete snížit až 99 % rušivé výstrahy prostřednictvím tohoto sloučení. Můžete prohlédnout příčinu výstrahy byly součástí skupiny na stránce podrobností inteligentní skupinu.

Název inteligentní skupiny je název své první výstraha. Nelze vytvořit ani přejmenovat inteligentní skupinu.


### <a name="alert-states"></a>Stavy výstrah
Rozšířené jednotné výstrahy přinášejí koncept stav výstrahy. Stav výstrahy k určení, kde je v procesu překladu, který můžete nastavit. Když se vytvoří výstrahu, je ve stavu *nový*. Po potvrzení oznámení a při zavření, můžete změnit stav. Všechny změny stavu jsou uloženy v historii upozornění.

Jsou podporovány následující stavy výstrah.

| Stav | Popis |
|:---|:---|
| Nová | Problém právě byl zjištěn a není zatím Nezkontrolováno. |
| Potvrzeno | Správce zkontroluje upozornění a začnete pracovat na ní. |
| Zavřeno | Tento problém byl vyřešen. Po zavření výstrahy, můžete ho znovu otevřít tak, že změníte do jiného stavu. |

Stav výstrahy se liší od stav monitorování. Pravidla upozornění na metriky můžete nastavit upozornění na podmínku _přeložit_ při chybová podmínka není splněna. Stav upozornění je nastavena podle uživatele a je nezávislý na stav monitorování. I když systém lze nastavit stav monitorování na "vyřešit", stav výstrahy se změnit, dokud ji uživatel nezmění.

#### <a name="change-the-state-of-an-alert-or-smart-group"></a>Změna stavu výstrah nebo inteligentní skupinu
Můžete změnit stav individuální výstrahu nebo nastavením stavu inteligentní skupiny společné správě více výstrah.

Změnit stav upozornění tak, že vyberete **změnit stav upozornění** v zobrazení podrobností pro výstrahu. Nebo změny stavu pro inteligentní skupiny tak, že vyberete **změnit stav inteligentní skupiny** v jeho podrobné zobrazení. Změňte stav z více položek najednou nejprve jejich zvolením v zobrazení seznamu a následným výběrem **změnit stav** v horní části stránky. 

V obou případech platí vyberte z rozevírací nabídky nový stav. Zadejte volitelný komentář. Pokud chcete změnit jednu položku, máte také možnost použít stejné změny na všech výstrah ve skupině inteligentní.

![Změnit stav](media/monitoring-overview-unified-alerts/change-tate.png)

### <a name="alerts-page"></a>Stránky s upozorněními
Výchozí stránky s upozorněními poskytuje souhrn výstrah, které jsou vytvořeny v konkrétním časovém intervalu. Zobrazí celkový počet výstrah pro každý závažnost s sloupce, který určuje celkový počet výstrah v jednotlivých stavech závažnost v každé. Vyberte některou z závažnosti otevřete [všechny výstrahy](#all-alerts-page) stránka se vyfiltruje podle tohoto závažnosti.

![Stránky s upozorněními](media/monitoring-overview-unified-alerts/alerts-page.png)

Toto zobrazení můžete filtrovat výběrem hodnoty v rozevíracích nabídek v horní části stránky.

| Sloupec | Popis |
|:---|:---|
| Předplatné | Vyberte až o pěti předplatných Azure. Pouze výstrahy ve vybraných předplatných se nastavují v zobrazení. |
| Skupina prostředků | Výběr jedné skupiny prostředků. V zobrazení jsou zahrnuty pouze výstrahy s cíli ve vybrané skupině prostředků. |
| Časové rozmezí | Pouze výstrahy vyvolané v rámci vybrané časové období se nastavují v zobrazení. Podporované hodnoty jsou uplynulou hodinu, posledních 24 hodin, posledních 7 dní a posledních 30 dní. |

Vyberte následující hodnoty v horní části stránky s upozorněními na otevřením jiné stránky.

| Hodnota | Popis |
|:---|:---|
| Celkový počet upozornění | Celkový počet upozornění, která odpovídají vybraným kritériím. Vyberte tuto hodnotu a otevřete zobrazení všech výstrah se žádný filtr. |
| Inteligentní skupiny | Celkový počet inteligentní skupiny, které byly vytvořeny z výstrah, které odpovídají vybraným kritériím. Vyberte tuto hodnotu a otevřete seznam inteligentní skupin v zobrazení všech výstrah.
| Celkový počet pravidel upozornění | Celkový počet pravidel upozornění v vybrané předplatné a skupinu prostředků. Vyberte tuto hodnotu a otevřete zobrazení pravidla filtrování ve vybraném předplatném a skupině prostředků.


### <a name="all-alerts-page"></a>Všechny stránky s upozorněními 
Pomocí stránky pro všechny výstrahy zobrazíte seznam výstrah, které byly vytvořeny v rámci zvoleného časového období. Můžete zobrazit seznam jednotlivých výstrah nebo seznam inteligentní skupin, které obsahují upozornění. Klikněte na banner v horní části stránky a přepínání mezi zobrazeními.

![Všechny stránky s upozorněními](media/monitoring-overview-unified-alerts/all-alerts-page.png)

Zobrazení můžete filtrovat výběrem následující hodnoty v rozevíracích nabídek v horní části stránky.

| Sloupec | Popis |
|:---|:---|
| Předplatné | Vyberte až o pěti předplatných Azure. Pouze výstrahy ve vybraných předplatných se nastavují v zobrazení. |
| Skupina prostředků | Výběr jedné skupiny prostředků. V zobrazení jsou zahrnuty pouze výstrahy s cíli ve vybrané skupině prostředků. |
| Typ prostředku | Vyberte jeden nebo více typů prostředků. Pouze výstrahy s cíli vybraného typu jsou zahrnuty v zobrazení. Tento sloupec je k dispozici pouze po zadal skupinu prostředků. |
| Prostředek | Vyberte prostředek. V zobrazení jsou zahrnuty pouze výstrahy s tento prostředek jako cíl. Tento sloupec je k dispozici pouze po byl zadán typ prostředku. |
| Severity | Vyberte závažnost výstrahy, nebo vyberte *všechny* zahrnout všechny závažnosti výstrahy. |
| Stav monitorování | Vybrat podmínku monitorování, nebo vyberte *všechny* zahrnout upozornění na podmínky. |
| Stav upozornění | Vyberte výstrahy stavu nebo *všechny* zahrnout výstrahy stavy. |
| Monitorovat službu | Vyberte službu, nebo vyberte *všechny* zahrnout všechny služby. Jsou zahrnuty pouze výstrahy vytvořené pravidly, které používají službu jako cíl. |
| Časové rozmezí | Pouze výstrahy vyvolané v rámci vybrané časové období se nastavují v zobrazení. Podporované hodnoty jsou uplynulou hodinu, posledních 24 hodin, posledních 7 dní a posledních 30 dní. |

Vyberte **sloupce** v horní části stránky a vybrat sloupce, které chcete zobrazit. 

### <a name="alert-detail-page"></a>Stránky podrobností výstrahy
Když vyberete výstrahu, zobrazí se stránka podrobností výstrahy. Poskytuje podrobnosti upozornění a umožňuje změnit její stav.

![Podrobnosti upozornění](media/monitoring-overview-unified-alerts/alert-detail.png)

Na stránce podrobností výstrahy obsahuje následující části.

| Sekce | Popis |
|:---|:---|
| Základy | Zobrazí vlastnosti a další důležité informace o výstraze. |
| Historie | Uvádí všechny akce podniknuté upozornění a všechny změny provedené na upozornění. Toto je aktuálně omezená na změny stavu. |
| Inteligentní skupiny | Informace o skupině inteligentní výstraha je součástí. *Počet výstrah* odkazuje na počet výstrah, které jsou součástí inteligentní skupinu. To zahrnuje další výstrahy ve stejné skupině inteligentní, které se vytvořily v posledních 30 dní.  To je bez ohledu na filtr času v seznamu stránky s upozorněními. Výběrem výstrahy zobrazíte její podrobnosti. |
| Další podrobnosti | Zobrazí další kontextové informace pro výstrahy, což je obvykle specifický pro typ zdroje, která upozornění vytvořila. |


### <a name="smart-group-detail-page"></a>Stránka s podrobnostmi inteligentní skupiny
Když vyberete inteligentní skupinu, zobrazí se stránka podrobností inteligentní skupinu. Poskytuje podrobnosti o inteligentní skupiny, včetně odůvodnění, která byla použita k vytvoření skupiny a umožňuje změnit její stav.
 
![Inteligentní skupiny podrobností](media/monitoring-overview-unified-alerts/smart-group-detail.png)


Stránka s podrobnostmi inteligentní skupiny obsahuje následující části.

| Sekce | Popis |
|:---|:---|
| Výstrahy | Zobrazí seznam jednotlivých výstrah, které jsou součástí inteligentní skupinu. Vyberte výstrahu a otevřete její stránku podrobností výstrahy. |
| Historie | Obsahuje všechny akce podniknuté inteligentní skupinou a změnách, které se provedly. Toto je aktuálně omezená na změny stavu a výstrahy členství. |

## <a name="next-steps"></a>Další postup
- [Naučte se používat nové prostředí upozornění k vytvoření, zobrazení a Správa výstrah](alert-metric.md)
- [Další informace o upozornění protokolů v prostředí upozornění](monitor-alerts-unified-log.md)
- [Další informace o upozorněních metrik v prostředí upozornění](monitoring-near-real-time-metric-alerts.md)
- [Další informace o upozornění protokolu aktivit v prostředí upozornění](alert-activity-log.md)
