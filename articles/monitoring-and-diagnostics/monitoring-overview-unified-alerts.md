---
title: Unified výstrah v monitorování Azure | Microsoft Docs
description: Popis jednotná výstrah v Azure, které vám umožní spravovat výstrahy a pravidla výstrah napříč službami Azure.
author: manishsm-msft
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: mamit,bwren
ms.custom: ''
ms.openlocfilehash: 699dff42846ee1f9d42980feca55d8a79e2514e3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839839"
---
# <a name="unified-alerts-in-azure-monitor"></a>Jednotná výstrah v monitorování Azure

## <a name="overview"></a>Přehled

> [!NOTE]
>  V novém jednotném výstrah rozhraní, které vám umožní spravovat výstrahy z více předplatných a zavádí výstraha stavy a inteligentní skupiny je teď dostupné ve verzi public preview. Najdete v článku [poslední části tohoto článku](#enhanced-unified-alerts-experience-public-preview) popis tohoto rozšířeného prostředí a proces zapnuta.


Tento článek popisuje jednotném rozhraní výstrah v monitorování Azure. [Předchozí výstrahy zkušenosti](monitoring-overview-alerts.md) má k dispozici **výstrahy (klasické)** možnost v nabídce monitorování Azure. 

## <a name="features-of-the-unified-alert-experience"></a>Funkce jednotném rozhraní výstrah

Jednotné prostředí má následující výhody přes classic prostředí:

-   **Lepší oznámení systému**: Unified výstrahy použití [skupiny akcí]( https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups), které jsou s názvem skupiny oznámení a akce, které lze znovu použít ve více výstrah. 
- **Unified zážitky** – můžete spravovat výstrahy a výstrahu pravidel pro metriky, protokoly a aktivity protokolu napříč Azure monitorování, analýzy protokolů a Application Insights na jednom místě. 
- **Zobrazení aktivováno analýzy protokolů výstrahy na portálu Azure** -Zobrazit výstrahy z analýzy protokolů s další výstrahy z jiných zdrojů na portálu Azure. Dříve byly tyto samostatné portálu.
- **Oddělení Fired výstrahy a pravidla výstrah** -výstrah, pravidla rozlišují teď z výstrah. Pravidlo výstrahy je definice podmínku, která aktivuje výstrahu. Výstraha je instance pálení pravidlo výstrahy.
- **Lepší pracovního postupu** -sjednocené výstraha zážitky vás provede procesem konfigurace pravidla výstrahy.
 
Metriky výstrahy mají následující vylepšení classic metriky výstrahy:

-   **Vylepšené latence**: metriky výstrahy můžete spustit často každou minutu. Classic metriky výstrahy vždy spustit v intervalu 5 minut. Výstrahy protokolu ještě delší než jedna minuta kvůli době je potřeba k ingestování protokoly zpoždění. 
-   **Podpora pro multidimenzionální metriky**: můžete výstrahy na dimenzí metriky, které umožňují monitorovat konkrétní instanci metriku.
-   **Větší kontrolu nad metriky podmínky**: můžete definovat bohatší pravidla výstrah, které podporují monitorování maximální, minimální, průměrná a celkové hodnoty metrik.
-   **Kombinaci monitorování více metrik**: můžete sledovat až dvě metriky s jedním pravidlem. Výstraha se spustí, pokud obě metriky narušit jejich příslušné prahové hodnoty pro zadané časové období.
-   **Metriky z protokolů** (verze public preview omezené): některé protokolu dat přejdete do analýzy protokolů můžete nyní extrahována a převést do Azure monitorování metriky a potom zobrazí upozornění na stejně jako jiné metriky. 


## <a name="alert-rules"></a>Pravidla upozornění
Jednotná výstrahy prostředí používá následující koncepty pro oddělení pravidla výstrah z výstrah při sjednocujícího prostředí pro vytváření napříč různé typy výstrah.

| Položka | Definice |
|:---|:---|
| Pravidlo výstrahy | Definice podmínku, která má být vytvořena výstraha. Tvořený _cíle prostředků_, _signál_, _kritéria_, a _logiku_. Pravidlo výstrahy je aktivní, pokud se nachází v pouze _povoleno_ stavu.
| Cílový prostředek | Definuje specifické prostředky a nevydá signál k dispozici pro výstrahy. Cílem může být jakýmikoli prostředky Azure.<br>Příklady: virtuální počítač, účet úložiště, škálovací sadu virtuálních počítačů, pracovní prostor analýzy protokolů prostředek Application Insights |
| Signál | Zdroj dat vysílaných cílový prostředek. Typy podporované signál *metrika*, *protokol aktivit*, *Application Insights*, a *protokolu*. |
| Kritéria | Kombinace _signál_ a _logiku_ použita na cílový prostředek.<br>Příklady: Procento využití procesoru > 70 %, doba odezvy serveru > 4 ms, počet výsledků protokolu dotaz > 100 atd. |
| Logiky | Uživatelem definované logiku zkontrolujte, jestli je signál v rámci očekává hodnoty nebo rozsahu. |
| Akce | Akce k provedení při vyvolání výstrahy. Více akcí může dojít, když se aktivuje výstrahu. Tyto výstrahy podporovat skupiny akcí.<br>Příklady: e-mailem na e-mailovou adresu, volání URL webhooku se nenačetla. |
| Stav monitorování | Určuje, zda je podmínka vytvořený metriky výstraha následně vyřešit. Metriky pravidla výstrah ukázkové konkrétní metriky v pravidelných intervalech. Při splnění kritérií pro pravidlo výstrahy, se vytvoří nová výstraha s podmínkou Fired.  Když je metriku vzorkovat znovu, pokud stále splnění kritérií a nedojde k žádné akci.  Pokud ale není splněna kritéria, podmínka výstrahy se změní na hodnotu Vyřešeno. Při příštím spuštění se splní kritéria a potom jinou výstrahy vytvořeny s podmínkou Fired. |


## <a name="alert-pages"></a>Výstrahy stránky
Jednotné místo pro zobrazit a spravovat všechny výstrahy Azure poskytuje sjednocené výstraha. Následující části popisují funkce jednotlivých stránek jednotném rozhraní.

### <a name="alerts-overview-page"></a>Stránka Přehled výstrah
**Výstrahy** přehled stránka zobrazuje agregovaný shrnutím všechny aktivní výstrahy, a celkový počet povolené pravidla výstrah. Změna odběry nebo parametry filtrování aktualizací agregace a výstrahy aktivováno seznamu.

 ![Přehled výstrah](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### <a name="alert-rules-management"></a>Pravidla výstrah správy
**Pravidla** je jediné stránce Spravovat všechny výstrahy pravidla ve vašich předplatných Azure. Zobrazí seznam všech pravidel výstrah a lze seřadit podle cílové prostředky, skupiny prostředků, název pravidla nebo stavu. Pravidla výstrah může být také upravená a povolit nebo zakázat z této stránky.

 ![pravidla výstrah](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## <a name="creating-an-alert-rule"></a>Vytvoření pravidla výstrahy
Výstrahy můžete vytvořené konzistentním způsobem bez ohledu na to, služba monitorování nebo signál typu. Všechny aktivováno výstrahy a související podrobnosti jsou dostupné v jediné stránce.
 
Můžete vytvořit nové pravidlo výstrahy se následující tři kroky:
1. Vyberte _cíl_ pro výstrahu.
1. Vyberte _signál_ z dostupných signály pro cíl.
1. Zadejte _logiku_ má být použita k datům z signál.
 
Tento proces zjednodušené vytváření už vyžaduje, aby uživatel vědět monitorování zdroje nebo signály podporované před výběrem prostředek služby Azure. Seznam signálů k dispozici jsou filtrovány automaticky podle vybrané cílový prostředek a provede vás procesem definování logice pravidla výstrahy.

Další informace o tom, jak vytvořit pravidla výstrah v [vytvořit, zobrazit a spravovat výstrah pomocí Azure monitorování](monitor-alerts-unified-usage.md).

Výstrahy jsou k dispozici v rámci několik Azure monitorování služeb. Informace o tom a při použití každé z těchto služeb najdete v tématu [monitorování Azure aplikacím a prostředkům](./monitoring-overview.md). Následující tabulka obsahuje seznam typů výstrah pravidel, které jsou k dispozici v Azure a co je aktuálně nepodporuje jednotném rozhraní výstrah.

| **Monitorování zdroje** | **Typ signálu**  | **Popis** | 
|-------------|----------------|-------------|
| Azure monitorování | Metrika  | Označuje taky jako [metriky výstrahy téměř v reálném čase](monitoring-near-real-time-metric-alerts.md), budou podporovat vyhodnocení metriky podmínky s četností až 1 minutu a povolit pro více metriky a komplexní metriky pravidla. Seznam typů podporovaných prostředků je k dispozici v [novější metriky výstrahy pro služby Azure na portálu Azure](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported).<br>[Classic metriky výstrahy](monitoring-overview-alerts.md) nejsou podporovány v nové výstrahy prostředí. Můžete je vyhledat podle výstrahy (klasické) na portálu Azure. Classic výstrahy podporovat některé typy metriky, které dosud nebyla přesunuta novější výstrah. Úplný seznam najdete v tématu [podporované metriky](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics). |
| Log Analytics | Logs  | Přijímat oznámení nebo spustit automatizované akce, pokud vyhledávací dotaz protokolu splňuje určitá kritéria. Výstrahy v analýzy protokolů jsou [kopírovány do nové prostředí](monitoring-alerts-extend.md). A [náhled z *analýzy protokolů zaznamená jako metriky* ](monitoring-alerts-extend-tool.md) je k dispozici. Verze preview umožňuje provést některé typy protokolů a jejich převedení na metriky, kde můžete lze pak výstraha na pomocí nové výstrahy prostředí. Ve verzi preview je užitečné, pokud máte protokoly mimo Azure, které chcete získat spolu s nativní metrik Azure monitorování. |
| Protokoly aktivit | Protokol aktivit | Obsahuje záznamy všech akcí vytvoření, aktualizace a odstranění vytvořené vybraný cílový. |
| Service Health | Protokol aktivit  | Nepodporované ve jednotná výstrahy. V tématu [vytvářet aktivity protokolu upozornění na oznámení o službách](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Application Insights | Logs  | Obsahuje protokoly výkonu podrobné informace o této aplikaci. Pomocí analýzy dotazu, můžete definovat podmínky pro akce, které mají být provedeny v závislosti na data aplikací. |
| Application Insights | Metrika | Nepodporované ve jednotná výstrahy. V tématu [metriky výstrahy]. (.. /Application-insights/App-insights-alerts.MD) |
| Application Insights | Testy dostupnosti webu | Nepodporované ve jednotná výstrahy.  V tématu [webového testu výstrahy](../application-insights/app-insights-monitor-web-app-availability.md). Chcete-li k dispozici na žádné web instrumentovány k odesílání dat do služby Application Insights. Přijímat oznámení v případě dostupnosti nebo odezvy webu je nižší než očekávání. |

## <a name="enhanced-unified-alerts-public-preview"></a>Rozšířené jednotná výstrahy (Public Preview)
> [!NOTE]
>  Funkce v této části je již brzy. Se nesmí objevit ještě ve vaší verzi portálu. 

Rozšířené výstrahy jednotné prostředí byla vydaná ve verzi public preview pro monitorování Azure na 1. června 2018. Toto prostředí je založena na výhody [unified výstrahy](#overview) vydané března 2018 a umožňuje spravovat a agregovat jednotlivé výstrahy kromě úpravy stav výstrahy. Tato část popisuje nové funkce a jak se orientovat nové výstrahy stránky na portálu Azure.

### <a name="features-enhanced-unified-alerts"></a>Funkce rozšířeného unified výstrahy

Nové rozhraní poskytuje následující funkce, které nejsou k dispozici jednotné přihlašování classic:

- **Zobrazit výstrahy ve předplatných** – teď můžete zobrazit a spravovat jednotlivé instance výstrah napříč více předplatných v rámci jednoho zobrazení.
- **Spravovat stav výstrahy** -výstrahy Teď máte stavu, která určuje, zda jeho byla potvrzena pro uzavřen.
- **Uspořádání výstrahy s inteligentní skupiny** -inteligentní skupiny automaticky seskupit výstrahy související s, aby je bylo možné spravovat jako sada místo jednotlivě.

### <a name="enable-enhanced-unified-alerts"></a>Povolit rozšířené jednotná výstrahy
Povolte novém jednotném rozhraní výstrah tak, že kliknete na informační zpráva v horní části stránky výstrah. Tento proces vytvoří výstrahy úložiště, která zahrnuje za posledních 30 dní aktivní výstrahy mezi podporované služby. Po povolení nové rozhraní můžete přepínat přepínat mezi novém i starém prostředí kliknutím na informační zprávě.

> [!NOTE]
>  To může trvat několik minut na nové prostředí na začátku povolit.

![Hlavička](media/monitoring-overview-unified-alerts/opt-in-banner.png)

Všechny odběry, které mají přístup k se zaregistruje, když povolíte nové prostředí. Přestože celé předplatné zapnutá, bude moct dál zobrazovat pouze uživatelé, kteří vybrané nové prostředí. Ostatní uživatelé s přístupem k předplatnému musíte povolit možnosti samostatně.

Povolení nové výstrahy prostředí nemá negativní vliv na konfiguraci skupiny akcí nebo oznámení v pravidlech vaší výstrahy. Pouze mění způsob, jak zobrazit a spravovat aktivní instance výstrahy na portálu Azure.

### <a name="smart-groups"></a>Inteligentní skupiny
Inteligentní skupiny snížení šumu tím, že se při správě související výstrahy jako na jednu jednotku spíš než správu jednotlivé výstrahy. Můžete zobrazit podrobnosti o inteligentní skupiny a nastavit stav podobná výstrahu. Každá výstraha je členem skupiny inteligentní pouze jeden.

Inteligentní skupiny se automaticky vytvoří pomocí strojového učení kombinování souvisejících výstrah, které představují jeden problém. Když se vytvoří výstrahu, algoritmus přidán ke skupině inteligentní nové nebo existující skupiny inteligentní podle informace jako historických vzory, podobnosti vlastností a podobnosti struktury. V současné době algoritmus uvažuje pouze výstrahy ze služby sledování v rámci předplatného. Inteligentní skupiny může snížit až 99 % výstrahy nepůsobily prostřednictvím Tato konsolidace. Můžete zobrazit z důvodu, že výstrahy byly součástí skupiny na stránce podrobností skupiny čipové.

Název inteligentní skupiny je název své první výstraha. Nelze vytvořit ani přejmenovat skupinu inteligentní.


### <a name="alert-states"></a>Stavů výstrah
Rozšířené jednotná výstrahy zavést koncept stav výstrahy. Stav výstrahy k určení, kde se nachází v procesu překladu, který můžete nastavit.  Když je vytvořen výstrahu, má stav *nový*. Stav můžete změnit, když jste potvrzené výstrahu a pokud jste zavřeli. Změny stavu jsou uloženy v historii výstrahy.

Jsou podporovány následující stavy výstrahy.

| Stav | Popis |
|:---|:---|
| Nová | Problém se právě byl zjištěn a ještě zkontrolovat. |
| Potvrzeno | Správce má přečetli upozornění a na něm spuštěna. |
| Zavřeno | Tento problém byl vyřešen. Po uzavření výstrahu, můžete ho znovu otevřít Moje změna ho na jiný stav. |

Stav výstrahy se liší od stav monitorování. Metriky pravidla výstrah můžete nastavit upozornění na stav systému _přeložit_ Pokud již není chybový stav splněna. Stav výstrahy je nastaven uživatelem a je nezávislý na stav monitorování. I když se systém může nastavit stav monitorování vyřešen, stav výstrahy není změnit, dokud uživatel změní ho.

#### <a name="changing-the-state-of-an-alert-or-smart-group"></a>Změna stavu výstrah nebo inteligentní skupinu
Můžete změnit stav individuální výstrahu nebo spravovat více výstrah, které jsou společně s nastavení stavu inteligentní skupiny.

Změnit stav výstrahy kliknutím na **změnit stav výstrahy** v zobrazení podrobností pro výstrahu nebo změna stavu pro skupinu inteligentní kliknutím **změnit stav inteligentní skupiny** v jeho podrobné zobrazení. Stav více položek najednou můžete změnit tak, že je vyberete v zobrazení seznamu a kliknete na **změny stavu** v horní části stránky. V obou případech vyberte nový stav z rozevíracího seznamu a volitelně zadejte komentář. Pokud měníte jednu položku, pak máte také možnost použít stejné změny pro všechny výstrahy, které jsou ve skupině inteligentní.

![Změnit stav](media/monitoring-overview-unified-alerts/change-tate.png)

### <a name="alerts-page"></a>stránka výstrah
Výchozí stránka výstrahy poskytuje souhrnné informace o výstrahách, které jsou vytvořené v rámci konkrétní časové okno. Zobrazuje celkový počet oznámení pro každou závažnost se sloupci identifikaci celkového počtu výstrah v každém stavu pro každý závažnosti. Klikněte na jakékoliv závažnosti otevřete [všechny výstrahy](#all-alerts-page) stránky filtrovaná podle této závažnosti.

![stránka výstrah](media/monitoring-overview-unified-alerts/alerts-page.png)

Toto zobrazení můžete filtrovat výběrem hodnoty v rozevíracích seznamů v horní části stránky.

| Sloupec | Popis |
|:---|:---|
| Předplatné | Vyberte až 5 předplatných Azure. V zobrazení jsou zahrnuty pouze výstrahy ve vybraných předplatných. |
| Skupina prostředků | Vyberte jedna skupina prostředků. V zobrazení jsou zahrnuty pouze výstrahy s cíli v s vybranou skupinou prostředků. |
| Časové rozmezí | V zobrazení budou zahrnuty pouze výstrahy aktivováno v rámci vybrané časové období. Podporované hodnoty jsou po hodině, za posledních 24 hodin, uplynulých 7 dní a posledních 30 dnů. |

Kliknutím na následující hodnoty v horní části stránky výstrahy otevřete jinou stránku.

| Hodnota | Popis |
|:---|:---|
| Celkový počet upozornění | Celkový počet upozornění, která odpovídají vybraným kritériím. Klikněte na tuto hodnotu k otevření zobrazení všechny výstrahy s žádný filtr. |
| Inteligentní skupiny | Celkový počet inteligentní skupiny vytvořené z výstrahy, které odpovídají vybraným kritériím. Klikněte na tuto hodnotu na seznam inteligentní skupiny otevřete v zobrazení všechny výstrahy.
| Celkový počet pravidel upozornění | Celkový počet pravidla výstrah ve vybrané skupině, předplatné a prostředků. Klikněte na tuto hodnotu k otevření zobrazení pravidla filtrováno podle vybraných předplatných a skupině prostředků.


### <a name="all-alerts-page"></a>Stránka všechny výstrahy 
Všechny výstrahy stránka umožňuje zobrazit seznam výstrah, které byly vytvořeny v rámci vybrané časové období. Můžete buď zobrazit seznam jednotlivé výstrahy nebo seznam inteligentní skupiny obsahující výstrahy. Klikněte na tlačítko hlavičky v horní části stránky pro přepínání mezi zobrazeními.

![Stránka všechny výstrahy](media/monitoring-overview-unified-alerts/all-alerts-page.png)

Můžete filtrovat zobrazení, vyberte následující hodnoty v rozevíracích seznamů v horní části stránky.

| Sloupec | Popis |
|:---|:---|
| Předplatné | Vyberte až 5 předplatných Azure. V zobrazení jsou zahrnuty pouze výstrahy ve vybraných předplatných. |
| Skupina prostředků | Vyberte jedna skupina prostředků. V zobrazení jsou zahrnuty pouze výstrahy s cíli v s vybranou skupinou prostředků. |
| Typ prostředku | Vyberte jeden nebo více typů prostředků. V zobrazení jsou zahrnuty pouze výstrahy s cíli vybraného typu. Tento sloupec je k dispozici pouze po zadal skupinu prostředků. |
| Prostředek | Vyberte prostředek. V zobrazení jsou zahrnuty pouze výstrahy s této prostředky jako cíl. Tento sloupec je k dispozici pouze po byl zadán typ prostředku. |
| Severity | Vyberte výstrahy závažnosti nebo *všechny* zahrnout všechny závažnosti výstrahy. |
| Stav monitorování | Vyberte stav monitorování nebo *všechny* zahrnout výstrahy podmínek. |
| Stav upozornění | Vyberte výstrahu, stavu nebo vybrat možnost *všechny* zahrnout výstrahy stavy. |
| Monitorovat službu | Vyberte službu nebo vyberte *všechny* zahrnout všechny služby. Pouze výstrahy vytvořené pomocí této služby jako cíl pravidla jsou zahrnuty. |
| Časové rozmezí | V zobrazení budou zahrnuty pouze výstrahy aktivováno v rámci vybrané časové období. Podporované hodnoty jsou po hodině, za posledních 24 hodin, uplynulých 7 dní a posledních 30 dnů. |

Klikněte na tlačítko **sloupce** v horní části stránky a vybrat sloupce, které chcete zobrazit. Můžete odebrat všechny sloupce kromě pro 

### <a name="alert-detail-page"></a>Stránka podrobností výstrahy
Když kliknete na výstrahu, zobrazí se stránka podrobností výstrahy. Poskytuje podrobnosti o výstraze a můžete změnit její stav.

![Podrobností výstrahy](media/monitoring-overview-unified-alerts/alert-detail.png)

Stránka podrobností výstrahy obsahuje následující části.

| Sekce | Popis |
|:---|:---|
| Základy | Zobrazí vlastnosti a další důležité informace o výstraze. |
| Historie | Uvádí každý akce výstraha a všechny změny výstrahy. Toto je aktuálně omezená na změny stavu. |
| Čipové skupiny | Informace o skupině inteligentní výstraha je součástí. **Počet výstrah** odkazuje na počet výstrah, které jsou součástí inteligentní skupiny. To zahrnuje další výstrahy, které jsou zahrnuté ve stejné skupině stejné inteligentní vytvořené v posledních 30 dnů.  To je bez ohledu na to filtr času na stránce Seznam výstrah. Klikněte na výstrahu zobrazíte její podrobnosti. |
| Další informace | Zobrazí další kontextové informace pro výstrahu, která je obvykle specifické pro daný typ zdroje, který vytvořili výstrahu. |


### <a name="smart-group-detail-page"></a>Stránka podrobností čipové skupiny
Když kliknete na skupinu inteligentní, zobrazí se stránka inteligentní podrobností skupiny. Poskytuje podrobnosti o skupině inteligentní, včetně reasoning použít k vytvoření skupiny a můžete změnit její stav.
 
![Podrobnosti skupiny čipové](media/monitoring-overview-unified-alerts/smart-group-detail.png)


Inteligentní podrobností skupiny stránka obsahuje následující části.

| Sekce | Popis |
|:---|:---|
| Výstrahy | Uvádí jednotlivé výstrahy, které jsou součástí inteligentní skupiny. Klikněte na příslušnou výstrahu otevřete stránku s jeho podrobnostmi výstrahy. |
| Historie | Uvádí každý akce inteligentní skupiny a všechny změny. Toto je aktuálně omezená na změny stavu a výstrahy členství změny. |

## <a name="next-steps"></a>Další postup
- [Další informace o použití nové výstrahy prostředí vytvořit, zobrazit a spravovat výstrahy](monitor-alerts-unified-usage.md)
- [Další informace o protokolu výstrahy v možnosti výstrahy](monitor-alerts-unified-log.md)
- [Další informace o metriky výstrahy v možnosti výstrahy](monitoring-near-real-time-metric-alerts.md)
- [Další informace o protokolu upozornění v možnosti výstrahy](monitoring-activity-log-alerts-new-experience.md)
