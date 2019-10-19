---
title: Řešení pro správu v doporučených postupech Azure | Microsoft Docs
description: ''
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: 28ae01fe28b1b2d6af95567e529c7c9ae17920e4
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553929"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Osvědčené postupy pro vytváření řešení pro správu v Azure (Preview)
> [!NOTE]
> Toto je předběžná dokumentace k vytváření řešení pro správu v Azure, která jsou momentálně ve verzi Preview. Jakékoli schéma popsané níže se může změnit.  

Tento článek poskytuje osvědčené postupy pro [Vytvoření souboru řešení pro správu](solutions-solution-file.md) v Azure.  Tyto informace se aktualizují, protože se identifikují další osvědčené postupy.

## <a name="data-sources"></a>Zdroje dat
- Zdroje dat lze [konfigurovat pomocí šablony Správce prostředků](../../azure-monitor/platform/template-workspace-configuration.md), ale neměly by být zahrnuty do souboru řešení.  Důvodem je, že konfigurace zdrojů dat není momentálně idempotentnía, což znamená, že vaše řešení by mohlo přepsat existující konfiguraci v pracovním prostoru uživatele.<br><br>Vaše řešení může například vyžadovat upozornění a chybové události z protokolu událostí aplikace.  Pokud tento údaj zadáte jako zdroj dat ve vašem řešení, riskujete odebrání událostí informací, pokud má uživatel ve svém pracovním prostoru nakonfigurovaný.  Pokud jste zahrnuli všechny události, můžete v pracovním prostoru uživatele shromažďovat nadměrné informační události.

- Pokud vaše řešení vyžaduje data z jednoho ze standardních zdrojů dat, měli byste ho definovat jako předpoklad.  Stav v dokumentaci, že zákazník musí nakonfigurovat zdroj dat sami.  
- Přidáním [ověřovací zprávy toku dat](../../azure-monitor/platform/view-designer-tiles.md) do libovolného zobrazení ve vašem řešení můžete uživateli dát pokyn ke zdrojům dat, které je potřeba nakonfigurovat pro shromažďování požadovaných dat.  Tato zpráva se zobrazí na dlaždici zobrazení, pokud se požadovaná data nenašly.


## <a name="runbooks"></a>Runbooky
- Přidejte [plán automatizace](../../automation/automation-schedules.md) pro jednotlivé sady Runbook ve vašem řešení, které je třeba spustit podle plánu.
- Zahrňte do svého řešení [modul IngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) , který budou používat Runbooky pro zápis dat do úložiště Log Analytics.  Nakonfigurujte řešení tak, aby [odkazovalo](solutions-solution-file.md#solution-resource) na tento prostředek, aby zůstalo v případě odebrání řešení.  To umožňuje více řešení sdílet modul.
- Použijte [proměnné pro automatizaci](../../automation/automation-schedules.md) k poskytnutí hodnot řešení, které uživatelé mohou chtít později změnit.  I v případě, že je řešení nakonfigurované tak, aby obsahovalo proměnnou, je možné, že je tato hodnota stále změněna.

## <a name="views"></a>Zobrazení
- Všechna řešení by měla obsahovat jedno zobrazení, které se zobrazí na portálu uživatele.  Zobrazení může obsahovat několik [částí vizualizace](../../azure-monitor/platform/view-designer-parts.md) k ilustraci různých sad dat.
- Přidáním [ověřovací zprávy toku dat](../../azure-monitor/platform/view-designer-tiles.md) do libovolného zobrazení ve vašem řešení můžete uživateli dát pokyn ke zdrojům dat, které je potřeba nakonfigurovat pro shromažďování požadovaných dat.
- Nakonfigurujte řešení tak, aby [obsahovalo](solutions-solution-file.md#solution-resource) zobrazení, které se odebere, pokud se odebere řešení.

## <a name="alerts"></a>Výstrahy
- Definujte seznam příjemců jako parametr v souboru řešení, aby ho uživatel mohl při instalaci řešení definovat.
- Nakonfigurujte řešení tak, aby [odkazovalo](solutions-solution-file.md#solution-resource) na pravidla výstrah, aby uživatel mohl změnit jejich konfiguraci.  Můžou chtít udělat změny, jako je například změna seznamu příjemců, Změna prahové hodnoty výstrahy nebo vypnutí pravidla výstrahy. 


## <a name="next-steps"></a>Další kroky
* Projděte si základní proces [navrhování a sestavování řešení pro správu](solutions-creating.md).
* Přečtěte si, jak [vytvořit soubor řešení](solutions-solution-file.md).
* [Přidejte uložená hledání a výstrahy](solutions-resources-searches-alerts.md) do řešení pro správu.
* [Přidejte zobrazení](solutions-resources-views.md) do řešení pro správu.
* [Přidejte Runbooky Automation a další prostředky](solutions-resources-automation.md) do řešení pro správu.

