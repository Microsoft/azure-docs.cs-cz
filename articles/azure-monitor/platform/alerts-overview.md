---
title: Přehled služby Výstrahy a oznámení monitorování v Azure
description: Přehled výstrah v Azure. Výstrahy, upozornění classic, rozhraní upozornění.
author: rboucher
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: cf01ddd53741a77df61e3dc1946eb836640034d0
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53345988"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Přehled výstrah v Microsoft Azure 

Tento článek popisuje, jaké výstrahy jsou jejich výhody a jak je začít používat.  


## <a name="what-are-alerts-in-microsoft-azure"></a>Co jsou upozornění v Microsoft Azure?
Proaktivně upozornění se zobrazí důležitých podmínky se nacházejí ve vašich dat z monitorování. Umožňují vám identifikovat a vyřešit problémy dříve, než uživatelé vašeho systému Všimněte si, že je. 

Tento článek popisuje jednotné prostředí výstrah ve službě Azure Monitor, který nyní obsahuje Log Analytics a Application Insights. [Předchozí zkušenosti upozornění](alerts-overview.md) a typy výstrah, se nazývají **klasických upozornění**. Tento starší prostředí a starší typu výstrahy můžete zobrazit kliknutím na **zobrazit upozornění classic** v horní části stránky upozornění. 

## <a name="overview"></a>Přehled

Následující diagram znázorňuje tok výstrah. 

![Tok výstrah](media/alerts-overview/Azure-Monitor-Alerts.svg)

Pravidla upozornění jsou odděleny od výstrahy a akce, která se provede, když se aktivuje upozornění. 

**Pravidlo výstrahy** -pravidlo upozornění zachycuje cíle a kritéria pro generování výstrah. Pravidlo výstrahy může být v povolené nebo zakázané. Upozornění aktivovaly jenom při povolené. 

Jsou klíčové atributy pravidlo výstrahy:

**Cílový prostředek** – definuje rozsah a signály pro výstrahy k dispozici. Cíl může být libovolné prostředky Azure. Příklad cíle: virtuální počítač, účet úložiště, škálovací sadu virtuálních počítačů, pracovní prostor Log Analytics nebo prostředek Application Insights. Pro některé prostředky (jako jsou virtuální počítače), můžete zadat více prostředků jako cíl pravidla upozornění.

**Signál** – jsou, protože ho vygeneroval cílového prostředku signál a může být z několika typů. Metriky, aktivity protokolu, Application Insights a Log.

**Kritéria** – kritéria je kombinací signál a logiky použít na cílový prostředek. Příklady: 
   - Procento CPU > 70 %
   - Doba odezvy serveru > 4 ms 
   - Počet výsledků dotazu protokolu > 100

**Název výstrahy** – konkrétní název pravidla upozornění, která uživatel nakonfiguroval

**Popis výstrahy** – Popis upozornění pravidla nakonfigurovaná uživatelem

**Závažnost** – závažnost výstrahy, když je splněna kritéria zadaná v pravidle výstrahy. Závažnost může být v rozsahu od 0 do 4.

**Akce** – určité akce provede, když se aktivuje upozornění. Další informace najdete v tématu [skupiny akcí](../../azure-monitor/platform/action-groups.md).

## <a name="what-you-can-alert-on"></a>Může upozornit na

Můžete upozornění na metriky a protokoly, jak je popsáno v [monitorování zdroje dat](../../monitoring-and-diagnostics/monitoring-data-sources.md). Patří mezi ně jsou mimo jiné tyto:
- Hodnoty metrik
- Dotazy prohledávání protokolu
- Události protokolu aktivit
- Stav základní platformy Azure
- Testy dostupnosti webu

## <a name="manage-alerts"></a>Správa upozornění
Stav výstrahy k určení, kde je v procesu překladu, který můžete nastavit. Když se splní kritéria zadaná v pravidle výstrahy, vytvoření nebo aktivuje výstrahu, je ve stavu *nový*. Po potvrzení oznámení a při zavření, můžete změnit stav. Všechny změny stavu jsou uloženy v historii upozornění.

Jsou podporovány následující stavy výstrah.

| Stav | Popis |
|:---|:---|
| Nová | Problém právě byl zjištěn a není zatím Nezkontrolováno. |
| Potvrzeno | Správce zkontroluje upozornění a začnete pracovat na ní. |
| Zavřeno | Tento problém byl vyřešen. Po zavření výstrahy, můžete ho znovu otevřít tak, že změníte do jiného stavu. |

**Upozornění stavu** je jiné a nezávislé **monitorovat stav**. Stav výstrahy je nastaven uživatelem. Stav monitorování se nastaví ho systém. Když se aktivuje upozornění, je nastavena podmínka výstrahy monitorování *aktivuje*. Pokud základní podmínku, která způsobila výstrahu, kterou chcete aktivovat vymaže stav monitorování nastavená na *přeložit*. Stav výstrahy se nezmění, dokud ji uživatel nezmění. Přečtěte si [změna stavu výstrah a inteligentní skupiny](https://aka.ms/managing-alert-smart-group-states).

## <a name="smart-groups"></a>Inteligentní skupiny 
Inteligentní skupiny jsou ve verzi preview. 

Inteligentní skupiny jsou agregace výstrah podle algoritmů strojového učení, které pomohou omezily rušivé výstrahy a pomoci při odstraňování poruch. [Další informace o skupinách inteligentní](https://aka.ms/smart-groups) a [jak spravovat vaše Chytré skupiny](https://aka.ms/managing-smart-groups).


## <a name="alerts-experience"></a>Prostředí upozornění 
Výchozí stránky s upozorněními poskytuje souhrn výstrah, které jsou vytvořeny v konkrétním časovém intervalu. Zobrazí celkový počet výstrah pro každý závažnost s sloupce, který určuje celkový počet výstrah v jednotlivých stavech závažnost v každé. Vyberte některou z závažnosti otevřete [všechny výstrahy](#all-alerts-page) stránka se vyfiltruje podle tohoto závažnosti.

Nelze zobrazit nebo sledovat starší [klasických upozornění](#classic-alerts). Můžete změnit předplatná nebo filtrovat parametry aktualizujte stránku. 

![Stránky s upozorněními](media/alerts-overview/alerts-page.png)

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


## <a name="manage-alert-rules"></a>Správa pravidel výstrah
Klikněte na **spravovat pravidla výstrah** zobrazíte **pravidla** stránky. **Pravidla** je na jednom místě pro správu všechna pravidla výstrah ve vašich předplatných Azure. Obsahuje seznam všech pravidel upozornění a může být řazeny podle cílové prostředky, skupiny prostředků, název pravidla nebo stav. Pravidla výstrah můžete také upravit, povolit nebo zakázat z této stránky.  

 ![pravidla výstrah](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Vytvoření pravidla upozornění
Výstrahy se můžou vytvořit konzistentním způsobem bez ohledu na to služba monitorování nebo signalizuje, že typ. Všechny výstrahy vyvolané a související podrobnosti jsou dostupné na jedné stránce.
 
Vytvořit nové pravidlo upozornění s následující tři kroky:
1. Vyberte si _cílové_ výstrahy.
1. Vyberte _signál_ z dostupné signály pro cíl.
1. Zadejte _logiky_ použít data z tohoto signálu.
 
Tato zjednodušený proces vytváření už vyžaduje, abyste znali monitorování zdroje nebo signály, které jsou podporovány před výběrem prostředku Azure. Seznam dostupné signály se automaticky filtruje podle cílový prostředek, který jste vybrali. Také v závislosti na, které cílí, projdete automaticky definování logiky pravidla výstrahy.  

Další informace o tom, jak vytvořit pravidla výstrah ve službě [vytvoření, zobrazení a Správa výstrah pomocí Azure monitoru](../../azure-monitor/platform/alerts-metric.md).

Výstrahy jsou dostupné v rámci několika služeb monitorování v Azure. Informace, jak a kdy se má používat každý z těchto služeb najdete v tématu [aplikací a prostředků Monitoring Azure](../../azure-monitor/overview.md). Následující tabulka obsahuje seznam typů pravidel upozornění, které jsou dostupné v rámci Azure. Také uvádí, co je aktuálně podporován ve které výstrahy prostředí.

Azure Monitor, Application Insights, Log Analytics a stavy služeb dříve musely samostatné možnosti výstrah. Azure přesčas, vylepšené a kombinaci uživatelského rozhraní a výstrahy různými způsoby. Tato konsolidace stále probíhá proces. V důsledku toho existují stále výstrah funkce není dosud v novém systému výstrah.  

| **Zdroj monitorování** | **Typ signálu**  | **Popis** | 
|-------------|----------------|-------------|
| Stav služby | Protokol aktivit  | Nepodporuje se. Zobrazit [vytvoření upozornění protokolu aktivit pro oznámení služby](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).  |
| Application Insights | Testy dostupnosti webu | Nepodporuje se. Zobrazit [upozornění webových testů](../../application-insights/app-insights-monitor-web-app-availability.md). K dispozici pro všechny weby, které je instrumentováno pro odesílání dat do Application Insights. Když dostupnosti nebo rychlost odezvy webu je nižší než očekávání, dostanete oznámení. |


## <a name="all-alerts-page"></a>Všechny stránky s upozorněními 
Klikněte na celkový počet upozornění zobrazíte všechny stránky s upozorněními. Zde můžete zobrazit seznam výstrah, které byly vytvořeny v rámci zvoleného časového období. Můžete zobrazit seznam jednotlivých výstrah nebo seznam inteligentní skupin, které obsahují upozornění. Klikněte na banner v horní části stránky a přepínání mezi zobrazeními.

![Všechny stránky s upozorněními](media/alerts-overview/all-alerts-page.png)

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

## <a name="alert-detail-page"></a>Stránky podrobností výstrahy
Když vyberete výstrahu, zobrazí se stránka podrobností výstrahy. Poskytuje podrobnosti upozornění a umožňuje změnit její stav.

![Podrobnosti upozornění](media/alerts-overview/alert-detail2.png)

Na stránce podrobností výstrahy obsahuje následující části.

| Sekce | Popis |
|:---|:---|
| Základy | Zobrazí vlastnosti a další důležité informace o výstraze. |
| Historie | Uvádí všechny akce podniknuté upozornění a všechny změny provedené na upozornění. Aktuálně se omezuje na změny stavu. |
| Inteligentní skupiny | Informace o skupině inteligentní výstraha je součástí. *Počet výstrah* odkazuje na počet výstrah, které jsou součástí inteligentní skupinu. Zahrnuje další výstrahy ve stejné skupině inteligentní, které se vytvořily za posledních 30 dnů bez ohledu na to filtr času v seznamu stránky s upozorněními. Výběrem výstrahy zobrazíte její podrobnosti. |
| Další podrobnosti | Zobrazí další kontextové informace pro výstrahy, což je obvykle specifický pro typ zdroje, která upozornění vytvořila. |


## <a name="classic-alerts"></a>Klasická upozornění 

Azure Monitor metrik a aktivit protokolu výstrahy funkce před. června 2018 se nazývá "Upozornění (klasická)". 

Další informace najdete v tématu [upozornění classic](./../../azure-monitor/platform/alerts-classic.overview.md)


## <a name="next-steps"></a>Další postup

- [Další informace o inteligentních skupiny](https://aka.ms/smart-groups)
- [Další informace o skupinách akcí](../../azure-monitor/platform/action-groups.md)
- [Správa vašich instancí upozornění v Azure](https://aka.ms/managing-alert-instances)
- [Správa inteligentní skupin](https://aka.ms/managing-smart-groups)





