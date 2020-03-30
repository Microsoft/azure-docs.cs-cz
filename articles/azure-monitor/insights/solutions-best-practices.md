---
title: Řešení pro správu v doporučených postupech Azure | Dokumenty společnosti Microsoft
description: ''
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: 837fb87d73698961ec1550b122840563d1707f8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663194"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Doporučené postupy pro vytváření řešení pro správu v Azure (Preview)
> [!NOTE]
> Toto je předběžná dokumentace pro vytváření řešení pro správu v Azure, které jsou aktuálně ve verzi preview. Jakékoli schéma popsané níže se může změnit.  

Tento článek obsahuje osvědčené postupy pro [vytvoření souboru řešení pro správu](solutions-solution-file.md) v Azure.  Tyto informace budou aktualizovány, protože jsou určeny další osvědčené postupy.

## <a name="data-sources"></a>Zdroje dat
- Zdroje dat lze [nakonfigurovat pomocí šablony Správce prostředků](../../azure-monitor/platform/template-workspace-configuration.md), ale neměly by být zahrnuty do souboru řešení.  Důvodem je, že konfigurace zdrojů dat není aktuálně idempotentní, což znamená, že vaše řešení může přepsat existující konfiguraci v pracovním prostoru uživatele.<br><br>Vaše řešení může například vyžadovat události upozornění a chyby z protokolu událostí aplikace.  Pokud toto zadáte jako zdroj dat ve vašem řešení, riskujete odebrání informačních událostí, pokud uživatel měl tento nakonfigurovaný ve svém pracovním prostoru.  Pokud jste zahrnuli všechny události, pak je možné, že shromažďování nadměrné informace události v pracovním prostoru uživatele.

- Pokud vaše řešení vyžaduje data z jednoho ze standardních zdrojů dat, měli byste to definovat jako předpoklad.  Uveďte v dokumentaci, že zákazník musí nakonfigurovat zdroj dat na vlastní pěst.  
- Přidejte zprávu [ověření toku dat](../../azure-monitor/platform/view-designer-tiles.md) do všech zobrazení ve vašem řešení, abyste uživatele poučili o zdrojích dat, které je třeba nakonfigurovat pro shromažďování požadovaných dat.  Tato zpráva se zobrazí na dlaždici zobrazení, pokud nejsou nalezena požadovaná data.


## <a name="runbooks"></a>Runbooky
- Přidejte [plán automatizace](../../automation/automation-schedules.md) pro každou runbook ve vašem řešení, které je potřeba spustit podle plánu.
- Zahrňte [modul IngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) do svého řešení, který bude používat sady Runbook, které zapisují data do úložiště Log Analytics.  Nakonfigurujte řešení tak, aby [odkazovalo na](solutions-solution-file.md#solution-resource) tento prostředek, aby zůstalo, pokud je řešení odebráno.  To umožňuje více řešení pro sdílení modulu.
- Pomocí [proměnných automatizace](../../automation/automation-schedules.md) můžete poskytnout hodnoty řešení, které mohou uživatelé chtít později změnit.  I v případě, že řešení je nakonfigurován tak, aby obsahoval proměnnou, je hodnota stále změnit.

## <a name="views"></a>Zobrazení
- Všechna řešení by měla obsahovat jedno zobrazení, které se zobrazí na portálu uživatele.  Zobrazení může obsahovat více [vizualizačních částí](../../azure-monitor/platform/view-designer-parts.md) pro ilustraci různých sad dat.
- Přidejte zprávu [ověření toku dat](../../azure-monitor/platform/view-designer-tiles.md) do všech zobrazení ve vašem řešení, abyste uživatele poučili o zdrojích dat, které je třeba nakonfigurovat pro shromažďování požadovaných dat.
- Nakonfigurujte řešení tak, aby [obsahovalo](solutions-solution-file.md#solution-resource) zobrazení tak, aby bylo odebráno, pokud je řešení odebráno.

## <a name="alerts"></a>Výstrahy
- Definujte seznam příjemců jako parametr v souboru řešení, aby je uživatel mohl definovat při instalaci řešení.
- Nakonfigurujte řešení [tak,](solutions-solution-file.md#solution-resource) aby uživatel mohl změnit svou konfiguraci.  Mohou chtít provést změny, jako je například úprava seznamu příjemců, změna prahové hodnoty výstrahy nebo zakázání pravidla výstrahy. 


## <a name="next-steps"></a>Další kroky
* Projděte si základní proces [navrhování a vytváření řešení pro správu](solutions-creating.md).
* Přečtěte [si,](solutions-solution-file.md)jak vytvořit soubor řešení .
* [Přidejte uložená hledání a výstrahy](solutions-resources-searches-alerts.md) do svého řešení pro správu.
* [Přidejte zobrazení](solutions-resources-views.md) do řešení správy.
* Přidejte do svého řešení pro správu [runbooky automation a další prostředky.](solutions-resources-automation.md)

