---
title: Co se změnilo v Azure Log Analytics? | Dokumentace Microsoftu
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
ms.date: 11/29/2017
ms.author: bwren
ms.openlocfilehash: 017a1da233827f19489a99b234ee9009fd9f6fe3
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2017
ms.locfileid: "26039855"
---
# <a name="whats-changed-in-azure-log-analytics"></a>Co se změnilo v Azure Log Analytics?
Kromě dotazovací jazyk, samotné, jsou k dispozici několik vylepšení a změny, které byste měli vědět, když je pracovní prostor analýzy protokolů [upgradovat na nový dotazovací jazyk](log-analytics-log-search-new.md).  Tento článek obsahuje stručný popis změn mezi starší verze a upgradovali prostoru s odkazy na podrobná obsah pro každý. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Whats-changed-in-Azure-Log-Analytics/player]

V tématu [vyhledávání – nejčastější dotazy a známé problémy protokolu analýzy protokolů nové](log-analytics-log-search-faq.md) popis všech známých problémů s upgradu a odpovědi na běžné otázky.  

## <a name="query-language"></a>Dotazovací jazyk
Primární změny v upgradu analýzy protokolů je nový dotazovací jazyk, který má významná vylepšení přes předchozí jazyk.  

Můžete získat přímý porovnání běžných operací mezi starší verze jazyka a nového jazyka v [přechodu do Azure Log Analytics nové dotazovací jazyk](log-analytics-log-search-transition.md).  Kompletní dokumentaci o nový jazyk je k dispozici na [Azure Log Analytics Query Language](https://docs.loganalytics.io).


## <a name="computer-groups"></a>Skupiny počítačů
Metoda pro vytvoření skupiny počítače se nezměnila, ale nyní je nutné zadat jedinečný odkaz pro každou.  Skupiny počítačů, které jsou založené na protokolu hledání nutné použít syntaxi nového jazyka.

Není k dispozici nové syntaxe pro používání skupin počítačů do protokolů hledání.  Místo použití funkce $ComputerGroups, skupiny počítačů jsou nyní každý implementovaný jako funkce s jedinečný odkaz.  Alias se používá protokol hledání podobně jako jakékoli jiné funkce.  

Podrobnosti jsou dostupné na [skupiny počítačů v analýzy protokolů protokolu hledání](log-analytics-computer-groups.md).


## <a name="log-search-portals"></a>Portály pro prohledávání protokolů
Kromě portálu hledání protokolů vytvořit a spustit hledání protokolu můžete nyní portálu pokročilé analýzy, který poskytuje výrazné vylepšení úpravy funkce.

Stručný popis dva portály je k dispozici na [portály pro vytváření a úpravy dotazů protokolu v Azure Log Analytics](log-analytics-log-search-portals.md).  Kurz si můžete projít na nový portál na [Začínáme s portálu analýza](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal).

## <a name="alerts"></a>Výstrahy
Výstrahy fungovat stejně v upgradované pracovní prostory, ale dotaz, který spouští se musí být napsaný v požadovaném jazyce.  Všechny existující pravidla výstrah, které jste měli před upgradem budou automaticky převedeny na požadovaném jazyce.  Další informace získáte [vysvětlení výstrah v analýzy protokolů](log-analytics-alerts.md).

Formát datové části pro sady runbook a pomocí webhooků odeslaný výstrahy změnil.  Získat podrobnosti pro nový formát datové části v [přidat akce do pravidla výstrah v analýzy protokolů](log-analytics-alerts-actions.md).

## <a name="dashboards"></a>Řídicí panely
[Řídicí panely](log-analytics-dashboards.md) probíhá zastaralá.  Můžete nadále používat všechny dlaždice, které jste přidali k řídicímu panelu, než byl upgradován pracovního prostoru, ale nelze upravit tyto dlaždice nebo přidat nové.  Pomocí zobrazení návrhu můžete vytvořit vlastní zobrazení, která má širší funkce nastavené než řídicí panely.

Podrobnosti o Návrhář zobrazení jsou k dispozici na [Návrhář zobrazení použít k vytvoření vlastních zobrazení v analýzy protokolů](log-analytics-view-designer.md).

## <a name="power-bi"></a>Power BI
Proces exportu analýzy protokolů dat do Power BI se změnil pro upgradovaný pracovní prostory a všechny existující plány, které jste vytvořili před upgradem bude zakázán.  

Podrobnosti jsou dostupné na [analýzy protokolů exportovat data do Power BI](log-analytics-powerbi.md).

## <a name="powershell"></a>PowerShell
Get-AzureRmOperationalInsightsSearchResults pro spuštění z prostředí PowerShell protokolu hledání nebude fungovat se upgradovaný pracovního prostoru.  Invoke-LogAnalyticsQuery používejte pro tuto funkci s upgradovaný pracovního prostoru.

Podrobnosti jsou dostupné na [REST API Azure Log Analytics – rutiny prostředí PowerShell](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets).

## <a name="log-search-api"></a>Hledání protokolů rozhraní API
Hledání protokolů došlo ke změně rozhraní REST API a žádné řešení, které používají starší verzi je nutné aktualizovat na použití nové verze rozhraní API.   

Podrobnosti o nové verzi rozhraní API jsou k dispozici na [REST API služby Azure Log Analytics](https://dev.loganalytics.io/).

## <a name="next-steps"></a>Další kroky

- V tématu [vyhledávání – nejčastější dotazy a známé problémy protokolu analýzy protokolů nové](log-analytics-log-search-faq.md) popis všech známých problémů s upgradu a odpovědi na běžné otázky.