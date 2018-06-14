---
title: Nové hledání protokolu log Analytics nejčastější dotazy | Microsoft Docs
description: Tento článek obsahuje nejčastější dotazy týkající se upgradu analýzy protokolů pro nové dotazovací jazyk.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: 6dfee26d7585c8ec295a1f0ea1bd0bc14a34cc5a
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2018
ms.locfileid: "27993865"
---
# <a name="log-analytics-new-log-search-faq-and-known-issues"></a>Nejčastější dotazy k vyhledávání a známé problémy protokolu nové analýzy protokolů

Tento článek obsahuje nejčastější dotazy a známé problémy týkající se upgradu [analýzy protokolů pro nové dotazovací jazyk](log-analytics-log-search-upgrade.md).  Měli byste si přečíst prostřednictvím celý článek před provedením rozhodnutí o upgrade vašeho pracovního prostoru.


## <a name="alerts"></a>Výstrahy

### <a name="question-i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>Otázka: je nutné spoustu pravidla výstrah. Je třeba je znovu vytvořit v požadovaném jazyce po upgradu?  
Ne, vaše pravidla výstrah jsou automaticky převedena na nový jazyk vyhledávání během upgradu.  

### <a name="question-i-have-alert-rules-with-webhook-and-runbook-actions-will-these-continue-to-work-when-i-upgrade"></a>Otázka: je nutné pravidla výstrah se akce webhooku a sady runbook. Budou tyto fungovat při upgradu?

Ne, existují některé změny v webhooku a runbook akce, které může být nutné provést změny v tom, jak při zpracování datové části. Jsme provedli tyto změny standardizovat různými formáty výstup a snížit velikost datové části. Podrobnosti o tyto formáty jsou v [přidat akce do pravidla výstrah v analýzy protokolů](log-analytics-alerts-actions.md).


## <a name="computer-groups"></a>Skupiny počítačů

### <a name="question-im-getting-errors-when-trying-to-use-computer-groups--has-their-syntax-changed"></a>Otázka: zobrazuje chyby při pokusu o použití skupiny počítačů.  Změnila se jejich syntaxi?
Ano, syntaxe pro používání počítače skupin změny upgradován pracovního prostoru.  V tématu [skupiny počítačů v analýzy protokolů protokolu hledání](log-analytics-computer-groups.md) podrobnosti.


## <a name="dashboards"></a>Řídicí panely

### <a name="question-can-i-still-use-dashboards-in-an-upgraded-workspace"></a>Otázka: Použití řídicích panelů v upgradované pracovního prostoru?
V upgradu, jsme se od proces depracating **vlastní řídicí panel**.  Můžete nadále používat všechny dlaždice, které jste přidali k řídicímu panelu, než byl upgradován pracovního prostoru, ale nelze upravit tyto dlaždice nebo přidat nové.  Můžete vytvořit a upravit zobrazení s [Návrhář zobrazení](log-analytics-view-designer.md), který má širší funkci nastavit a také vytvořit řídicí panely na portálu Azure.


## <a name="log-searches"></a>Protokol hledání

### <a name="question-i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>Otázka: mimo pracovní prostor upgradovaný I mají uložená hledání. Můžete převést je na nový jazyk vyhledávání automaticky?
Můžete na stránce vyhledávání protokolu převáděcí nástroj jazyk převést každé z nich.  Neexistuje žádná metoda automaticky převést více hledání bez nutnosti upgradu pracovním prostoru.

### <a name="question-why-are-my-query-results-not-sorted"></a>Otázka: Proč nejsou Moje výsledky dotazu seřazeny?
Výsledky nejsou ve výchozím nastavení v nové dotazovací jazyk seřazeny.  Použití [řazení operátor](https://go.microsoft.com/fwlink/?linkid=856079) seřadit výsledky podle jednoho nebo více vlastností.

### <a name="question-where-did-the-metrics-view-go-after-i-upgraded"></a>Otázka: Kde zobrazení metriky? po upgradu
Zobrazení metriky přiřadil grafické reprezentace údaje o výkonu z hledání protokolů.  Toto zobrazení je již k dispozici po upgradu.  Můžete použít [vykreslení operátor](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) formát výstupu z dotazu v timechart.

### <a name="question-where-did-minify-go-after-i-upgraded"></a>Otázka: Kde minifikaci přejděte po upgradu?
Minifikaci je funkce, která poskytuje souhrnné zobrazení výsledků hledání.  Po upgradu se už Minify možnost se zobrazí na portálu hledání protokolů.  Podobně jako funkce s použitím jazyka nové vyhledávání můžete získat [snížit](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/reduce-operator) nebo [autocluster_v2](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/evaluate-operator/autocluster). 

    Event
    | where TimeGenerated > ago(10h)
    | reduce by RenderedDescription

    Event
    | where TimeGenerated > ago(10h)
    | evaluate autocluster_v2()



## <a name="log-search-api"></a>Rozhraní API pro prohledávání protokolů

### <a name="question-does-the-log-search-api-get-updated-after-i-upgrade"></a>Otázka: Rozhraní API pro vyhledávání protokolu aktualizovat po upgradu?
Starší verze [rozhraní API pro vyhledávání protokolu](log-analytics-log-search-api.md) nebude fungovat, pokud jste upgradovali pracovního prostoru.  V tématu [REST API služby Azure Log Analytics](https://dev.loganalytics.io/) podrobnosti o nové rozhraní API.


## <a name="portals"></a>Portály

### <a name="question-should-i-use-the-new-advanced-analytics-portal-or-keep-using-the-log-search-portal"></a>Otázka: Mám použít nový portál pro pokročilé analýzy nebo zachovat pomocí portálu hledání protokolů?
Uvidíte porovnání dva portály v [portály pro vytváření a úpravy dotazů protokolu v Azure Log Analytics](log-analytics-log-search-portals.md).  Každá má odlišné výhody, abyste si mohli vybrat nejvhodnější pro vaše požadavky.  Je běžné psát dotazy na portálu pokročilé analýzy a vložit je na jiných místech, například Návrhář zobrazení.  Měli byste si přečíst o [problémy vzít v úvahu](log-analytics-log-search-portals.md#advanced-analytics-portal) při učinit.


### <a name="question--after-upgrade-i-get-an-error-trying-to-run-queries-and-am-also-seeing-errors-in-my-views"></a>Otázka: Po provedení upgradu, I dojde k chybě při pokusu o spuštění dotazů a také se zobrazují chyby v mé zobrazení.

Váš prohlížeč vyžaduje přístup ke spouštění dotazů analýzy protokolů po upgradu na následující adresy.  Pokud váš prohlížeč je přístup k portálu Azure přes bránu firewall, musíte povolit přístup pro tyto adresy.

| URI | IP adresa | Porty |
|:---|:---|:---|
| portal.loganalytics.io | Dynamická | 80,443 |
| api.loganalytics.io    | Dynamická | 80,443 |
| docs.loganalytics.io   | Dynamická | 80,443 |



## <a name="power-bi"></a>Power BI

### <a name="question-does-anything-change-with-powerbi-integration"></a>Otázka: Nic mění s integrací PowerBI?
Ano.  Jakmile pracovního prostoru, byl upgradován pak proces pro export dat analýzy protokolů do Power BI přestane fungovat.  Všechny existující plány, které jste vytvořili před upgradem bude zakázán.  

Po upgradu, analýzy protokolů Azure používá stejnou platformu jako Application Insights a použít stejný postup exportování dotazů analýzy protokolů pro Power BI jako [proces exportu dotazy Application Insights do Power BI](../application-insights/app-insights-export-power-bi.md#export-analytics-queries).  Export do Power BI přímo teď zavolá koncový bod rozhraní API. To umožňuje získat až 500 000 řádků nebo 64,000,000 bajtů dat, exportu dlouho dotazy a přizpůsobit časový limit dotazu (výchozí hodnota časového limitu je 3 minut a maximální časový limit je 10 minut).

## <a name="powershell-cmdlets"></a>Rutiny prostředí PowerShell

### <a name="question-does-the-log-search-powershell-cmdlet-get-updated-after-i-upgrade"></a>Otázka: Rutiny prostředí PowerShell vyhledávání protokolu aktualizovat po upgradu?
[Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/Get-AzureRmOperationalInsightsSearchResults) přestanou po dokončení upgradu všech pracovních prostorů.  Použití [rutinu Invoke-LogAnalyticsQuery](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets) k vyhledávání protokolu v upgradované pracovních prostorů.




## <a name="resource-manager-templates"></a>Šablony Resource Manageru

### <a name="question-can-i-create-an-upgraded-workspace-with-a-resource-manager-template"></a>Otázka: Je možné vytvořit pracovní prostor služby upgradovaný pomocí šablony Resource Manageru?
Ano.  Musíte použít verzi rozhraní API 2017-03-15-preview a zahrnout **funkce** oddílu v šabloně jako v následujícím příkladu.

    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "apiVersion": "2017-03-15-preview",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceRegion')]",
            "properties": {
                "sku": {
                    "name": "Free"
                },
                "features": {
                    "legacy": 0,
                    "searchVersion": 1
                }
            }
        }
    ],



## <a name="solutions"></a>Řešení

### <a name="question-will-my-solutions-continue-to-work"></a>Otázka: Moje řešení budou fungovat?
Všechna řešení budou dále fungovat v upgradované prostoru, i když jejich výkonu bude zvýšit, pokud se převedou na nový dotazovací jazyk.  Existují známé problémy s některé existující řešení, které jsou popsané v této části.

### <a name="known-issue-perspectives-in-application-insights-connector"></a>Známý problém: perspektivy v konektoru služby Application Insights
Perspektivy v [řešení Application Insights konektor](log-analytics-app-insights-connector.md) již nejsou podporovány v konektoru řešení Application Insights.  Návrhář zobrazení můžete vytvořit vlastní zobrazení s daty Application Insights.

### <a name="known-issue-backup-solution"></a>Známý problém: řešení pro zálohování
Řešení zálohování nemusí shromažďování dat, pokud byla nainstalována před upgradem pracovního prostoru. Odinstalujte řešení a potom nainstalujte nejnovější verzi.  Novou verzi řešení nepodporuje classic trezory Backup, takže je nutné také upgradovat na trezory služeb zotavení nadále používat řešení.

## <a name="upgrade-process"></a>Proces upgradu

### <a name="question-i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>Otázka: je nutné několik pracovních prostorů. Můžete upgradovat všechny pracovní prostory ve stejnou dobu?  
Ne.  Upgrade platí pokaždé, když do jednoho pracovního prostoru. Aktuálně neexistuje žádný způsob upgradu mnoho pracovních prostorů najednou. Upozorňujeme, že také se týká jiných uživatelů upgradovaný pracovního prostoru.  

### <a name="question-will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>Otázka: Existující data protokolu, které jsou shromážděny v pracovního prostoru se změní-li I upgrade?  
Ne. Data protokolu, který je k dispozici pro hledání prostoru nemá vliv upgrade. Uložená hledání, výstrahy a zobrazení budou převedeny na nový jazyk vyhledávání automaticky.  

### <a name="question-what-happens-if-i-dont-upgrade-my-workspace"></a>Otázka: Co se stane, když nemáte upgradu pracovního prostoru?  
V nadcházejících měsících bude zastaralá hledání starších protokolů. Pracovní prostory, které nejsou upgradovány do té doby budou automaticky aktualizovány.

### <a name="question-can-i-revert-back-after-i-upgrade"></a>Otázka: Můžete vrátit zpět po upgradu?
Před zveřejněním může vrátit pracovního prostoru po upgradu.  Teď, když nový jazyk dosáhl obecné dostupnosti, tato funkce byla odebrána jako jsme počáteční vyřadit z provozu starší verze platformy.


## <a name="views"></a>Zobrazení

### <a name="question-how-do-i-create-a-new-view-with-view-designer"></a>Otázka: Jak lze vytvořit nové zobrazení pomocí návrháře zobrazení?
Před upgradem můžete vytvořit nové zobrazení pomocí návrháře zobrazit z dlaždice na řídicím panelu hlavní na portálu OMS.  Pracovní prostor upgradován, odeberou se tato dlaždice.  Můžete vytvořit nové zobrazení pomocí návrháře zobrazení na portálu OMS kliknutím na tlačítko v levé nabídce + zelená.  Budete pokračovat, chcete-li vytvořit nové zobrazení pomocí portálu Azure klikněte na dlaždici Návrhář zobrazení.


## <a name="next-steps"></a>Další postup

- Další informace o [upgrade na novou pracovní prostor analýzy protokolů dotazu jazyka](log-analytics-log-search-upgrade.md).
