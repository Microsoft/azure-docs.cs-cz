---
title: Přehled upozorňování a monitorování oznámení v Azure
description: Přehled upozorňování v Azure. Výstrahy, klasické výstrahy a rozhraní výstrah.
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 01/28/2018
ms.openlocfilehash: 9796b41d0df091bc6eb23ba525a79da278e00bbd
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802306"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Přehled výstrah v Microsoft Azure 

Tento článek popisuje, co jsou výstrahy, jejich výhody a jak je začít používat.  




## <a name="what-are-alerts-in-microsoft-azure"></a>Co jsou výstrahy v Microsoft Azure?
Výstrahy proaktivně upozorňují na skutečnost, že jsou ve vašich datech monitorování zjištěny důležité podmínky. Umožňují identifikovat a řešit problémy předtím, než si ji uživatelé vašeho systému všimnete. 

Tento článek popisuje jednotné prostředí výstrah v Azure Monitor, které zahrnuje výstrahy, které byly dříve spravovány pomocí Log Analytics a Application Insights. [Předchozí](alerts-classic.overview.md) výstrahy a typy výstrah se nazývají *klasické výstrahy*. Toto starší prostředí a starší typ výstrahy můžete zobrazit tak, že v horní části stránky výstrah vyberete **Zobrazit klasické výstrahy** . 

## <a name="overview"></a>Přehled

Následující diagram znázorňuje tok výstrah. 

![Diagram toku výstrah](media/alerts-overview/Azure-Monitor-Alerts.svg)

Pravidla výstrah jsou oddělená od výstrah a akce prováděné při aktivování výstrahy. Pravidlo výstrahy zachycuje cíl a kritéria pro upozorňování. Pravidlo výstrahy může být v povoleném nebo zakázaném stavu. Výstrahy se aktivují jenom v případě, že jsou povolené. 

Níže jsou uvedené klíčové atributy pravidla výstrahy:

**Cílový prostředek**: definuje rozsah a signály, které jsou k dispozici pro upozorňování. Cílem může být libovolný prostředek Azure. Příklady cílů: virtuální počítač, účet úložiště, sada škálování virtuálního počítače, Log Analytics pracovní prostor nebo prostředek Application Insights. U určitých prostředků (jako jsou virtuální počítače) můžete zadat více prostředků jako cíl pravidla výstrahy.

**Signál**: vysílá cílovým prostředkem. Signály můžou být z následujících typů: metrika, protokol aktivit, Application Insights a protokol.

**Kritéria**: kombinace signálu a logiky použitá u cílového prostředku. Příklady: 
   - Procento využití CPU > 70%
   - Doba odezvy serveru > 4 MS 
   - Počet výsledků dotazu protokolu > 100

**Název výstrahy**: konkrétní název pro pravidlo výstrahy nakonfigurované uživatelem.

**Popis výstrahy**: Popis pravidla výstrahy nakonfigurovaného uživatelem.

**Závažnost**: Závažnost výstrahy po splnění kritérií zadaných v pravidle výstrahy. Závažnost může být v rozsahu od 0 do 4.
   - Závažnost 0 = kritický
   - Závažnost 1 = chyba
   - Závažnost 2 = upozornění
   - Závažnost 3 = informativní
   - Závažnost 4 = verbose 

**Action (akce**): konkrétní akce provedená při vyvolání výstrahy. Další informace najdete v tématu [skupiny akcí](../../azure-monitor/platform/action-groups.md).

## <a name="what-you-can-alert-on"></a>K čemu můžete upozornit

Můžete upozornit na metriky a protokoly, jak je popsáno v tématu [monitorování zdrojů dat](../../azure-monitor/platform/data-sources-reference.md). Patří mezi ně, ale nejsou omezeny na:
- Hodnoty metriky
- Dotazy na hledání v protokolu
- Události protokolu aktivit
- Stav základní platformy Azure
- Testy pro dostupnost webu

Dříve byly Azure Monitor metriky, Application Insights, Log Analytics a Service Health samostatné možnosti upozorňování. V průběhu času Azure vylepšil a kombinuje jak uživatelské rozhraní, tak i různé metody upozorňování. Tato konsolidace se pořád zpracovává. V důsledku toho stále existují některé možnosti upozorňování, které ještě nejsou v novém systému upozornění.  

| **Zdroj monitorování** | **Typ signálu**  | **Popis** | 
|-------------|----------------|-------------|
| Stav služby | Protokol aktivit  | Nepodporuje se. Viz téma [vytváření výstrah protokolu aktivit u oznámení služby](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).  |
| Application Insights | Testy dostupnosti webu | Nepodporuje se. Podívejte se na téma [výstrahy webového testu](../../azure-monitor/app/monitor-web-app-availability.md). K dispozici pro všechny weby, které jsou instrumentované pro posílání dat Application Insights. Dostanou oznámení, pokud je dostupnost nebo odezva webu nižší než očekávání. |

## <a name="manage-alerts"></a>Správa upozornění
Můžete nastavit stav výstrahy, chcete-li určit, kde se nachází v procesu řešení. Když jsou splněna kritéria zadaná v pravidle výstrahy, je vytvořena nebo aktivována výstraha a stav *nového*. Stav můžete změnit, když potvrdíte výstrahu a při jejím zavření. Všechny změny stavu jsou uloženy v historii výstrahy.

Podporovány jsou následující stavy upozornění.

| Stav | Popis |
|:---|:---|
| Novinka | Problém byl právě zjištěn a ještě nebyl revidován. |
| Potvrzené | Správce zkontroloval výstrahu a začal na ní pracovat. |
| ukončit | Problém byl vyřešen. Jakmile je výstraha zavřena, můžete ji znovu otevřít změnou na jiný stav. |

*Stav výstrahy* se liší a nezávisle na *stavu monitorování*. Stav výstrahy je nastaven uživatelem. Stav monitorování je nastaven systémem. Když se výstraha aktivuje, stav monitorování výstrahy se nastaví na *aktivováno*. Pokud je podkladová podmínka, která způsobila výstrahu, *vyhodnocena*jako nejasná, je stav monitorování nastaven na hodnotu Vyřešeno. Stav výstrahy se nezmění, dokud ji uživatel nezmění. Zjistěte, [Jak změnit stav výstrah a inteligentních skupin](https://aka.ms/managing-alert-smart-group-states).

## <a name="smart-groups"></a>Inteligentní skupiny 

Inteligentní skupiny jsou agregace výstrah na základě algoritmů strojového učení, které mohou pomoci při řešení potíží s omezením hluku výstrah a pomoci. [Přečtěte si další informace o inteligentních skupinách](https://aka.ms/smart-groups) a [o tom, jak spravovat vaše inteligentní skupiny](https://aka.ms/managing-smart-groups).


## <a name="alerts-experience"></a>Prostředí výstrah 
Stránka výchozí výstrahy poskytuje souhrn výstrah, které jsou vytvořeny v určitém časovém rozsahu. Zobrazuje celkový počet výstrah pro každou závažnost, se sloupci, které identifikují celkový počet výstrah v jednotlivých stavech pro každou závažnost. Výběrem libovolné závažnosti otevřete stránku [všechny výstrahy](#all-alerts-page) filtrované podle této závažnosti.

Případně můžete [pomocí rozhraní REST API programově vytvořit výčet instancí výstrah generovaných u vašich předplatných](#manage-your-alert-instances-programmatically).

> [!NOTE]
   >  Můžete přistupovat jenom k výstrahám generovaným za posledních 30 dní.

Nezobrazuje ani nesleduje klasické výstrahy. Chcete-li aktualizovat stránku, můžete změnit odběry nebo parametry filtru. 

![Snímek stránky s výstrahami](media/alerts-overview/alerts-page.png)

Toto zobrazení můžete filtrovat výběrem hodnot v rozevíracích nabídkách v horní části stránky.

| Column | Popis |
|:---|:---|
| Předplatné | Vyberte předplatná Azure, pro která chcete výstrahy zobrazit. Volitelně můžete zvolit, jestli chcete vybrat všechna Vaše předplatná. V zobrazení jsou zahrnuté jenom výstrahy, ke kterým máte přístup ve vybraných předplatných. |
| Skupina prostředků | Vyberte jednu skupinu prostředků. V zobrazení jsou zahrnuté jenom výstrahy s cíli ve vybrané skupině prostředků. |
| Časové rozmezí | V zobrazení jsou zahrnuty pouze výstrahy aktivované v rámci vybraného časového rozsahu. Podporovány jsou hodnoty za poslední hodinu, posledních 24 hodin, posledních 7 dní a posledních 30 dní. |

V horní části stránky výstrahy vyberte následující hodnoty a otevřete tak další stránku:

| Hodnota | Popis |
|:---|:---|
| Celkový počet upozornění | Celkový počet výstrah, které odpovídají vybraným kritériím. Výběrem této hodnoty otevřete zobrazení všechny výstrahy bez filtru. |
| Inteligentní skupiny | Celkový počet inteligentních skupin vytvořených z výstrah, které odpovídají vybraným kritériím. Výběrem této hodnoty otevřete seznam inteligentních skupin v zobrazení všechny výstrahy.
| Celkový počet pravidel upozornění | Celkový počet pravidel upozornění ve vybraném předplatném a skupině prostředků. Výběrem této hodnoty otevřete zobrazení pravidla filtrovaná ve vybraném předplatném a skupině prostředků.


## <a name="manage-alert-rules"></a>Správa pravidel výstrah
Chcete-li zobrazit stránku **pravidla** , vyberte možnost **Spravovat pravidla výstrah**. Stránka pravidla je jediné místo pro správu všech pravidel upozornění napříč předplatnými Azure. Uvádí všechna pravidla výstrah a je možné je seřadit na základě cílových prostředků, skupin prostředků, názvu pravidla nebo stavu. Na této stránce můžete také upravit, povolit nebo zakázat pravidla výstrah.  

 ![Snímek stránky s pravidly](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Vytvoření pravidla upozornění
Výstrahy můžete vytvářet konzistentním způsobem bez ohledu na službu monitorování nebo typ signálu. Všechny aktivované výstrahy a související podrobnosti jsou k dispozici na jedné stránce.
 
Tady je postup, jak vytvořit nové pravidlo upozornění:
1. Vyberte _cíl_ výstrahy.
1. Vyberte _signál_ z dostupných signálů pro cíl.
1. Zadejte _logiku_ , která se má použít pro data z signálu.
 
Tento zjednodušený proces vytváření již nevyžaduje, abyste před výběrem prostředku Azure znali zdroj nebo signály monitorování, které jsou podporované. Seznam dostupných signálů se automaticky filtruje podle cílového prostředku, který vyberete. Na základě tohoto cíle se také provedete tak, že automaticky definujete logiku pravidla výstrahy.  

Další informace o vytváření pravidel výstrah najdete v tématu [vytváření, zobrazení a správa výstrah pomocí Azure monitor](../../azure-monitor/platform/alerts-metric.md).

Výstrahy jsou k dispozici napříč několika službami monitorování Azure. Informace o tom, jak a kdy použít každou z těchto služeb, najdete v tématu [monitorování aplikací a prostředků Azure](../../azure-monitor/overview.md). 


## <a name="all-alerts-page"></a>Stránka všech výstrah 
Stránku **všechny výstrahy** zobrazíte tak, že vyberete **Celkový počet výstrah**. Tady si můžete prohlédnout seznam výstrah vytvořených v rámci vybraného času. Můžete si prohlédnout seznam individuálních výstrah nebo seznam inteligentních skupin, které obsahují výstrahy. Vyberte banner v horní části stránky pro přepínání mezi zobrazeními.

![Snímek stránky všech výstrah](media/alerts-overview/all-alerts-page.png)

Zobrazení můžete filtrovat výběrem následujících hodnot v rozevíracích nabídkách v horní části stránky:

| Column | Popis |
|:---|:---|
| Předplatné | Vyberte předplatná Azure, pro která chcete výstrahy zobrazit. Volitelně můžete zvolit, jestli chcete vybrat všechna Vaše předplatná. V zobrazení jsou zahrnuté jenom výstrahy, ke kterým máte přístup ve vybraných předplatných. |
| Skupina prostředků | Vyberte jednu skupinu prostředků. V zobrazení jsou zahrnuté jenom výstrahy s cíli ve vybrané skupině prostředků. |
| Typ prostředku | Vyberte jeden nebo více typů prostředků. V zobrazení jsou zahrnuté jenom výstrahy s cíli vybraného typu. Tento sloupec je k dispozici až po zadání skupiny prostředků. |
| Prostředek | Vyberte prostředek. V zobrazení jsou zahrnuty pouze výstrahy s tímto prostředkem jako cíl. Tento sloupec je k dispozici až po zadání typu prostředku. |
| Závažnost | Vyberte Závažnost výstrahy nebo vyberte možnost **vše** , pokud chcete zahrnout výstrahy všech závažnosti. |
| Podmínka monitorování | Vyberte podmínku monitorování nebo vyberte možnost **vše** , pokud chcete zahrnout výstrahy všech podmínek. |
| Stav výstrahy | Vyberte stav výstrahy nebo vyberte možnost **vše** , pokud chcete zahrnout výstrahy všech stavů. |
| Monitorovat službu | Vyberte službu nebo vyberte možnost **vše** , pokud chcete zahrnout všechny služby. Součástí jsou jenom výstrahy vytvořené pravidly, která používají službu jako cíl. |
| Časové rozmezí | V zobrazení jsou zahrnuty pouze výstrahy aktivované v rámci vybraného časového rozsahu. Podporovány jsou hodnoty za poslední hodinu, posledních 24 hodin, posledních 7 dní a posledních 30 dní. |

Vyberte **sloupce** v horní části stránky a vyberte sloupce, které chcete zobrazit. 

## <a name="alert-details-page"></a>Stránka podrobností výstrahy
Když vyberete výstrahu, Tato stránka poskytuje podrobnosti o výstraze a umožňuje změnit její stav.

![Snímek stránky s podrobnostmi výstrahy](media/alerts-overview/alert-detail2.png)

Stránka Podrobnosti výstrahy obsahuje následující části:

| Sekce | Popis |
|:---|:---|
| Souhrn | Zobrazí vlastnosti a další důležité informace o výstraze. |
| Historie | Zobrazí všechny akce podniknuté výstrahou a všechny změny provedené v upozornění. V současné době omezené na změny stavu. |
| Diagnostika | Informace o inteligentní skupině, do které je tato výstraha zahrnutá *Počet výstrah* odkazuje na počet výstrah, které jsou zahrnuty v této inteligentní skupině. Obsahuje další výstrahy ve stejné inteligentní skupině, které byly vytvořeny v posledních 30 dnech, bez ohledu na časový filtr na stránce seznam výstrah. Výběrem výstrahy zobrazíte její podrobnosti. |

## <a name="role-based-access-control-rbac-for-your-alert-instances"></a>Řízení přístupu na základě role (RBAC) pro instance upozornění

Spotřeba a Správa instancí výstrah vyžaduje, aby uživatel měl předdefinované role RBAC buď [monitorování přispěvatele](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) , nebo [čtečka monitorování](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader). Tyto role jsou podporované v jakémkoli oboru Azure Resource Manager, z úrovně předplatného až po podrobná přiřazení na úrovni prostředků. Pokud má například uživatel pouze oprávnění Přispěvatel monitorování pro virtuální počítač `ContosoVM1`, může tento uživatel využívat a spravovat pouze výstrahy vygenerované v `ContosoVM1`.

## <a name="manage-your-alert-instances-programmatically"></a>Spravujte instance výstrah programově

Pro výstrahy generované proti vašemu předplatnému můžete chtít dotazovat programově. Může se jednat o vytváření vlastních zobrazení mimo Azure Portal nebo k analýze výstrah pro identifikaci vzorců a trendů.

Pomocí [Alert Management REST API](https://aka.ms/alert-management-api) nebo pomocí [grafu prostředků Azure REST API pro výstrahy](https://docs.microsoft.com/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources)se můžete dotazovat na výstrahy vygenerované proti vašemu předplatnému.

[Graf prostředků Azure REST API pro výstrahy](https://docs.microsoft.com/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources)) umožňuje dotazovat se na instance výstrah ve velkém měřítku. To se doporučuje, když budete muset spravovat výstrahy vygenerované v mnoha předplatných. 

Následující vzorový požadavek na rozhraní API vrátí počet výstrah v rámci jednoho předplatného:

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()",
  "options": {
            "dataset":"alerts"
  }
}
```
Můžete zadat dotaz na výstrahy pro jejich [důležitá](alerts-common-schema-definitions.md#essentials) pole.

Pomocí [REST API Alert Management](https://aka.ms/alert-management-api) můžete získat další informace o konkrétních výstrahách, včetně jejich [kontextových polí výstrahy](alerts-common-schema-definitions.md#alert-context) .

## <a name="next-steps"></a>Další kroky

- [Další informace o inteligentních skupinách](https://aka.ms/smart-groups)
- [Další informace o skupinách akcí](../../azure-monitor/platform/action-groups.md)
- [Správa instancí upozornění v Azure](https://aka.ms/managing-alert-instances)
- [Správa inteligentních skupin](https://aka.ms/managing-smart-groups)






