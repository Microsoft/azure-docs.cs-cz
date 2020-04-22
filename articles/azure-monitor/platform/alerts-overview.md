---
title: Přehled monitorování výstrah a oznámení v Azure
description: Přehled upozorňování v Azure. Výstrahy, klasické výstrahy a rozhraní výstrah.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 01/28/2018
ms.openlocfilehash: e02c23623062f5cb3e4c597b0bb257b30aa9f44a
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769772"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Přehled upozornění v Microsoft Azure 

Tento článek popisuje, co výstrahy jsou, jejich výhody a jak začít používat.  

## <a name="what-are-alerts-in-microsoft-azure"></a>Co jsou výstrahy v Microsoft Azure?
Výstrahy vás aktivně upozorní, když jsou v datech monitorování nalezeny důležité podmínky. Umožňují identifikovat a řešit problémy dříve, než si jich uživatelé systému všimnou. 

Tento článek popisuje jednotné prostředí výstrah v Azure Monitoru, který zahrnuje výstrahy, které byly dříve spravované Log Analytics a Application Insights. [Předchozí prostředí výstrah](alerts-classic.overview.md) a typy výstrah se nazývají klasické *výstrahy*. Toto starší prostředí a starší typ výstrah můžete zobrazit výběrem **možnosti Zobrazit klasické výstrahy** v horní části stránky výstrahy. 

## <a name="overview"></a>Přehled

Níže uvedený diagram představuje tok výstrah. 

![Diagram toku výstrah](media/alerts-overview/Azure-Monitor-Alerts.svg)

Pravidla výstrah jsou oddělena od výstrah a akcí prováděných při vyvolání výstrahy. Pravidlo výstrahy zachycuje cíl a kritéria pro výstrahy. Pravidlo výstrahy může být v povoleném nebo zakázaném stavu. Výstrahy se smí smí pouze v případě, že jsou povoleny. 

Níže jsou uvedeny klíčové atributy pravidla výstrahy:

**Cílový zdroj**: Definuje rozsah a signály, které jsou k dispozici pro výstrahy. Cíl může být libovolný prostředek Azure. Příklady cílů: virtuální počítač, účet úložiště, škálovací sada virtuálního počítače, pracovní prostor Log Analytics nebo prostředek Application Insights. Pro určité prostředky (například virtuální počítače) můžete jako cíl pravidla výstrahy zadat více prostředků.

**Signál**: Vyzařované cílovým zdrojem. Signály mohou být následujících typů: metrika, protokol aktivit, přehledy aplikací a protokol.

**Kritéria**: Kombinace signálu a logiky použité na cílový prostředek. Příklady: 

- Procento > procesoru 70%
- Doba odezvy serveru > 4 ms 
- Počet výsledků dotazu protokolu > 100

**Název výstrahy**: Specifický název pravidla výstrahy nakonfigurovaného uživatelem.

**Popis výstrahy**: Popis pravidla výstrahy nakonfigurovaného uživatelem.

**Závažnost**: Závažnost výstrahy po splnění kritérií zadaných v pravidle výstrahy. Závažnost může být v rozsahu od 0 do 4.

- Sev 0 = Kritický
- Sev 1 = Chyba
- Sev 2 = Upozornění
- Sev 3 = Informační
- Sev 4 = Podrobné 

**Akce**: Konkrétní akce přijatá při vypalování výstrahy. Další informace naleznete [v tématu Skupiny akcí](../../azure-monitor/platform/action-groups.md).

## <a name="what-you-can-alert-on"></a>Na co můžete upozornit

Můžete upozornit na metriky a protokoly, jak je popsáno ve [zdrojích dat monitorování](../../azure-monitor/platform/data-sources.md). Patří mezi ně, ale nejsou omezeny na:

- Hodnoty metriky
- Vyhledávací dotazy na protokoly
- Události protokolu aktivit
- Stav základní platformy Azure
- Testy dostupnosti webů

Dříve metriky Azure Monitor, Application Insights, Log Analytics a Service Health měly samostatné možnosti upozornění. V průběhu času Azure vylepšil a zkombinoval uživatelské rozhraní i různé metody upozorňování. Tato konsolidace je stále v procesu. V důsledku toho stále existují některé možnosti upozornění, které ještě nejsou v novém systému výstrah.  

| **Zdroj monitoru** | **Typ signálu**  | **Popis** |
|-------------|----------------|-------------|
| Stav služeb | Protokol aktivit  | Není podporováno. Viz [Vytváření upozornění protokolu aktivit u oznámení o službách](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).  |
| Application Insights | Testy dostupnosti webu | Není podporováno. Viz [Výstrahy webových testů](../../azure-monitor/app/monitor-web-app-availability.md). K dispozici pro všechny webové stránky, které jsou instrumentované pro odesílání dat do Application Insights. Dostávat oznámení, pokud dostupnost nebo odezva webu je pod očekávání. |

## <a name="manage-alerts"></a>Správa upozornění
Můžete nastavit stav výstrahy a určit, kde se nachází v procesu řešení. Pokud jsou splněna kritéria zadaná v pravidle výstrahy, je vytvořena nebo aktivována výstraha a má stav *Nový*. Stav můžete změnit, když potvrdíte výstrahu a zavřete ji. Všechny změny stavu jsou uloženy v historii výstrahy.

Jsou podporovány následující stavy výstrah.

| Stav | Popis |
|:---|:---|
| Nová | Problém byl právě zjištěn a dosud nebyl přezkoumán. |
| Potvrzeno | Správce výstrahu zkontroloval a začal na ní pracovat. |
| Zavřeno | Problém byl vyřešen. Po zavření výstrahy ji můžete znovu otevřít změnou do jiného stavu. |

*Stav výstrahy* je jiný a nezávislý na *stavu monitorování*. Stav výstrahy je nastaven uživatelem. Stav monitorování je nastaven systémem. Při vyvolání výstrahy je nastavena podmínka monitorování výstrahy na *vypálení*. Pokud se základní stav, který způsobil, že výstraha byla vypalována, je podmínka monitorování nastavena na *vyřešenou*. Stav výstrahy se nezmění, dokud jej uživatel nezmění. Přečtěte [si, jak změnit stav výstrah a inteligentních skupin](https://aka.ms/managing-alert-smart-group-states).

## <a name="smart-groups"></a>Inteligentní skupiny 

Inteligentní skupiny jsou agregace výstrah založených na algoritmech strojového učení, které mohou pomoci snížit výstražný šum a pomoc při řešení potíží. [Přečtěte si další informace o inteligentních skupinách](https://aka.ms/smart-groups) a [o tom, jak spravovat inteligentní skupiny](https://aka.ms/managing-smart-groups).


## <a name="alerts-experience"></a>Prostředí upozornění 
Výchozí stránka Výstrahy obsahuje souhrn výstrah, které jsou vytvořeny v určitém časovém rozsahu. Zobrazuje celkový počet výstrah pro každou závažnost se sloupci, které identifikují celkový počet výstrah v každém stavu pro každou závažnost. Vyberte některou z závažnosti, chcete-li otevřít stránku [Všechny výstrahy](#all-alerts-page) filtrované podle této závažnosti.

Případně můžete [programově výčet instancí výstrah generovaných na vaše odběry pomocí rest API](#manage-your-alert-instances-programmatically).

> [!NOTE]
   >  Přístup k výstrahy generované v posledních 30 dnech.

Nezobrazuje ani nesleduje klasická upozornění. Můžete změnit odběry nebo parametry filtru a aktualizovat stránku. 

![Snímek obrazovky se stránkou Upozornění](media/alerts-overview/alerts-page.png)

Toto zobrazení můžete filtrovat výběrem hodnot v rozevíracích nabídkách v horní části stránky.

| Sloupec | Popis |
|:---|:---|
| Předplatné | Vyberte předplatná Azure, pro které chcete zobrazit výstrahy. Volitelně můžete vybrat všechna předplatná. Do zobrazení jsou zahrnuta pouze výstrahy, ke kterým máte přístup ve vybraných předplatných. |
| Skupina prostředků | Vyberte jednu skupinu prostředků. Do zobrazení jsou zahrnuty pouze výstrahy s cíli ve vybrané skupině prostředků. |
| Časové rozmezí | Do zobrazení jsou zahrnuty pouze výstrahy vyvolané ve vybraném časovém rozsahu. Podporované hodnoty jsou poslední hodina, posledních 24 hodin, posledních 7 dní a posledních 30 dní. |

V horní části stránky Výstrahy vyberte následující hodnoty a otevřete další stránku:

| Hodnota | Popis |
|:---|:---|
| Celkový počet výstrah | Celkový počet výstrah, které odpovídají vybraným kritériím. Tuto hodnotu vyberte, chcete-li otevřít zobrazení Všechny výstrahy bez filtru. |
| Inteligentní skupiny | Celkový počet inteligentních skupin, které byly vytvořeny z výstrah, které odpovídají vybraným kritériím. Tuto hodnotu vyberte, chcete-li otevřít seznam inteligentních skupin v zobrazení Všechny výstrahy.
| Pravidla výstrah celkem | Celkový počet pravidel výstrah ve vybraném předplatném a skupině prostředků. Tuto hodnotu vyberte, chcete-li otevřít zobrazení pravidel filtrované ve vybraném předplatném a skupině prostředků.


## <a name="manage-alert-rules"></a>Správa pravidel výstrah
Chcete-li zobrazit stránku **Pravidla,** vyberte **spravovat pravidla výstrah**. Stránka Pravidla je jediné místo pro správu všech pravidel výstrah v rámci předplatných Azure. Obsahuje všechna pravidla výstrah a lze je seřadit podle cílových zdrojů, skupin prostředků, názvu pravidla nebo stavu. Na této stránce můžete také upravit, povolit nebo zakázat pravidla výstrah.  

 ![Snímek obrazovky se stránkou Pravidla](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Vytvoření pravidla upozornění
Pravidla výstrah můžete vytvářet konzistentním způsobem bez ohledu na monitorovací službu nebo typ signálu.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tflw]

 
Tady je postup, jak vytvořit nové pravidlo výstrahy:
1. Vyberte _cíl_ pro výstrahu.
1. Vyberte _signál_ z dostupných signálů pro cíl.
1. Zadejte _logiku,_ která má být použita pro data ze signálu.
 
Tento zjednodušený proces vytváření již nevyžaduje, abyste znali zdroj monitorování nebo signály, které jsou podporované před výběrem prostředku Azure. Seznam dostupných signálů je automaticky filtrován na základě vybraného cílového prostředku. Také na základě tohoto cíle, budete vedeni prostřednictvím definování logiky pravidla výstrahy automaticky.  

Další informace o tom, jak vytvořit pravidla výstrah, najdete v [možnostech Vytváření, zobrazení a správa výstrah pomocí Azure Monitoru](../../azure-monitor/platform/alerts-metric.md).

Výstrahy jsou k dispozici v několika monitorovacích službách Azure. Informace o tom, jak a kdy používat jednotlivé z těchto služeb, naleznete [v tématu Monitorování aplikací a prostředků Azure](../../azure-monitor/overview.md). 


## <a name="all-alerts-page"></a>Stránka Všechny výstrahy 
Chcete-li zobrazit stránku **Všechna upozornění,** vyberte **možnost Celkový počet výstrah**. Zde si můžete prohlédnout seznam výstrah vytvořených ve vybraném čase. Můžete zobrazit seznam jednotlivých výstrah nebo seznam inteligentních skupin, které výstrahy obsahují. Vyberte nápis v horní části stránky a přepínejte mezi zobrazeními.

![Snímek obrazovky se stránkou Všechny výstrahy](media/alerts-overview/all-alerts-page.png)

Zobrazení můžete filtrovat výběrem následujících hodnot v rozevíracích nabídkách v horní části stránky:

| Sloupec | Popis |
|:---|:---|
| Předplatné | Vyberte předplatná Azure, pro které chcete zobrazit výstrahy. Volitelně můžete vybrat všechna předplatná. Do zobrazení jsou zahrnuta pouze výstrahy, ke kterým máte přístup ve vybraných předplatných. |
| Skupina prostředků | Vyberte jednu skupinu prostředků. Do zobrazení jsou zahrnuty pouze výstrahy s cíli ve vybrané skupině prostředků. |
| Typ prostředku | Vyberte jeden nebo více typů prostředků. Do zobrazení jsou zahrnuty pouze výstrahy s cíli vybraného typu. Tento sloupec je k dispozici pouze po zadání skupiny prostředků. |
| Prostředek | Vyberte zdroj. Do zobrazení jsou zahrnuty pouze výstrahy s tímto prostředkem jako cílem. Tento sloupec je k dispozici pouze po zadání typu prostředku. |
| Severity | Vyberte závažnost výstrahy nebo vyberte **Vše,** chcete-li zahrnout výstrahy všech závažností. |
| Stav monitorování | Vyberte podmínku monitorování nebo vyberte **možnost Vše,** chcete-li zahrnout výstrahy všech podmínek. |
| Stav výstrahy | Vyberte stav výstrahy nebo vyberte **Vše,** chcete-li zahrnout výstrahy všech stavů. |
| Monitorovat službu | Vyberte službu nebo vyberte **Vše,** chcete-li zahrnout všechny služby. Jsou zahrnuty pouze výstrahy vytvořené pravidly, které používají službu jako cíl. |
| Časové rozmezí | Do zobrazení jsou zahrnuty pouze výstrahy vyvolané ve vybraném časovém rozsahu. Podporované hodnoty jsou poslední hodina, posledních 24 hodin, posledních 7 dní a posledních 30 dní. |

Vyberte **Sloupce** v horní části stránky a vyberte sloupce, které chcete zobrazit. 

## <a name="alert-details-page"></a>Stránka podrobností výstrahy
Když vyberete výstrahu, tato stránka obsahuje podrobnosti o výstraze a umožňuje změnit její stav.

![Snímek obrazovky se stránkou Podrobnosti výstrahy](media/alerts-overview/alert-detail2.png)

Stránka Podrobnosti výstrahy obsahuje následující části:

| Sekce | Popis |
|:---|:---|
| Souhrn | Zobrazí vlastnosti a další důležité informace o výstraze. |
| Historie | Zobrazí seznam jednotlivých akcí provedených výstrahou a všech změn provedených ve výstraze. V současné době omezena na změny stavu. |
| Diagnostika | Informace o inteligentní skupině, ve které je výstraha zahrnuta. *Počet výstrah* odkazuje na počet výstrah, které jsou zahrnuty v inteligentní skupině. Zahrnuje další výstrahy ve stejné inteligentní skupině, které byly vytvořeny v posledních 30 dnech, bez ohledu na časový filtr na stránce seznamu výstrah. Vyberte výstrahu, chcete-li zobrazit její podrobnosti. |

## <a name="role-based-access-control-rbac-for-your-alert-instances"></a>Řízení přístupu na základě rolí (RBAC) pro instance výstrah

Spotřeba a správa instancí výstrah vyžaduje, aby uživatel měl předdefinované role RBAC [buď monitorování přispěvatele](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) nebo [sledování čtečky](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader). Tyto role jsou podporované v libovolném oboru Azure Resource Manager, od úrovně předplatného až po podrobná přiřazení na úrovni prostředků. Například pokud uživatel má pouze monitorování přístupu přispěvatele pro virtuální počítač `ContosoVM1`, `ContosoVM1`tento uživatel může využívat a spravovat pouze výstrahy generované na .

## <a name="manage-your-alert-instances-programmatically"></a>Programová správa instancí výstrah

Můžete chtít dotaz programově pro výstrahy generované proti vašemu předplatnému. To může být vytvoření vlastních zobrazení mimo portál Azure nebo analýzu výstrah k identifikaci vzorců a trendů.

Můžete dotaz na výstrahy generované proti vaše odběry buď pomocí [rozhraní REST API pro správu výstrah](https://aka.ms/alert-management-api) nebo pomocí grafu prostředků [Azure](../../governance/resource-graph/overview.md) a rozhraní REST API [pro prostředky](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources).

Rozhraní REST ROZHRANÍ API pro prostředky grafu prostředků umožňuje dotaz na instance výstrah y ve velkém měřítku. To se doporučuje, pokud je třeba spravovat výstrahy generované v mnoha předplatných. 

Následující ukázkový požadavek na rozhraní REST API grafu prostředků vrátí počet výstrah v rámci jednoho předplatného:

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "AlertsManagementResources | where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()"
}
```

Výsledek tohoto dotazu na graf prostředků můžete také zobrazit na portálu pomocí Průzkumníka prostředků Azure: [portal.azure.com](https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AlertsManagementResources%20%7C%20where%20type%20%3D~%20%27Microsoft.AlertsManagement%2Falerts%27%20%7C%20summarize%20count())

Můžete zadat dotaz na výstrahy pro jejich [základní](alerts-common-schema-definitions.md#essentials) pole.

Pomocí [rozhraní REST API pro správu výstrah](https://aka.ms/alert-management-api) získáte další informace o konkrétních výstrahách, včetně polí [kontextu výstrah.](alerts-common-schema-definitions.md#alert-context)

## <a name="next-steps"></a>Další kroky

- [Další informace o inteligentních skupinách](https://aka.ms/smart-groups)
- [Informace o skupinách akcí](../../azure-monitor/platform/action-groups.md)
- [Správa instancí výstrah v Azure](https://aka.ms/managing-alert-instances)
- [Správa inteligentních skupin](https://aka.ms/managing-smart-groups)
- [Další informace o cenách upozornění Azure](https://azure.microsoft.com/pricing/details/monitor/)






