---
title: Řešení pro správu v Azure osvědčené postupy | Microsoft Docs
description: ''
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: 155a7117f4c02bafcf66d0f7abca7dd97dc1236f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2018
ms.locfileid: "28924410"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Osvědčené postupy pro vytváření řešení pro správu v Azure (Preview)
> [!NOTE]
> Toto je předběžná dokumentace k vytvoření řešení pro správu v Azure, které jsou aktuálně ve verzi preview. Žádné schéma popsané níže se mohou změnit.  

Tento článek obsahuje osvědčené postupy pro [vytváření soubor řešení správy](operations-management-suite-solutions-solution-file.md) v Azure.  Tyto informace budou aktualizovány, jako jsou identifikovány další osvědčené postupy.

## <a name="data-sources"></a>Zdroje dat
- Zdroje dat mohou být [nakonfigurovat pomocí šablony Resource Manageru](../log-analytics/log-analytics-template-workspace-configuration.md), ale by neměly být obsažené v souboru řešení.  Důvodem je, že konfigurace zdroje dat není aktuálně idempotent, což znamená, že vaše řešení může přepsat existující konfiguraci v prostoru uživatele.<br><br>Řešení může například vyžadovat upozornění a chyb události z protokolu událostí aplikace.  Pokud toto určíte jako zdroj dat ve vašem řešení, riskujete odebrání – informační události, pokud uživatel měli nakonfigurovaný v jejich pracovním prostoru.  Pokud jste zahrnuli všechny události, pak vám může být shromažďování nadměrné informační události v prostoru uživatele.

- Pokud vaše řešení vyžaduje data z jedné standardní datových zdrojů, pak byste měli definovat to předpokladem je.  V dokumentaci k stavu, že zákazník musí nakonfigurujte zdroj dat na své vlastní.  
- Přidat [ověření toku dat](../log-analytics/log-analytics-view-designer-tiles.md) zpráva k zobrazením v řešení dáte pokyn, aby uživatel pro zdroje dat, které je potřeba nakonfigurovat pro požadovaná data, které se mají shromažďovat.  Tato zpráva se zobrazí na dlaždici zobrazení, když nebyla nalezena požadovaná data.


## <a name="runbooks"></a>Runbooky
- Přidat [plánování Automation](../automation/automation-schedules.md) pro každou sadu runbook ve vašem řešení, které je potřeba spustit podle plánu.
- Zahrnout [IngestionAPI modulu](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) ve vašem řešení, která má být používána zápis dat do úložiště analýzy protokolů sady runbook.  Nakonfigurujte řešení, aby [odkaz](operations-management-suite-solutions-solution-file.md#solution-resource) tento prostředek, že to zůstává, pokud je odebrán řešení.  To umožňuje více řešení sdílet modul.
- Použití [proměnné služeb automatizace](../automation/automation-schedules.md) zadat hodnoty do řešení, které mohou uživatelé chtít později změnit.  I v případě řešení je nakonfigurována tak, aby obsahovala proměnnou, jeho hodnota může být stále změněna.

## <a name="views"></a>Zobrazení
- Všechna řešení by měly obsahovat jedno zobrazení, které se zobrazí na portálu pro uživatele.  Zobrazení může obsahovat více [vizualizace částí](../log-analytics/log-analytics-view-designer-parts.md) pro ilustraci různých datových sad.
- Přidat [ověření toku dat](../log-analytics/log-analytics-view-designer-tiles.md) zpráva k zobrazením v řešení dáte pokyn, aby uživatel pro zdroje dat, které je potřeba nakonfigurovat pro požadovaná data, které se mají shromažďovat.
- Nakonfigurujte řešení, aby [obsahovat](operations-management-suite-solutions-solution-file.md#solution-resource) zobrazení proto to je odebrána, pokud je odebrán řešení.

## <a name="alerts"></a>Výstrahy
- Definujte seznam příjemců jako parametr v souboru řešení, uživatel je definovat při instalaci řešení.
- Nakonfigurujte řešení, aby [odkaz](operations-management-suite-solutions-solution-file.md#solution-resource) pravidla upozornění, že uživatel může změnit jejich konfigurace.  Budou chtít provést změny, jako je například úprava seznamu příjemců, změna prahovou hodnotu upozornění nebo zakázat pravidlo výstrahy. 


## <a name="next-steps"></a>Další postup
* Provede základní proces [navrhování a vytváření řešení pro správu](operations-management-suite-solutions-creating.md).
* Zjistěte, jak [vytvořte soubor řešení](operations-management-suite-solutions-solution-file.md).
* [Přidat uložená hledání a výstrahy](operations-management-suite-solutions-resources-searches-alerts.md) do řešení pro správu.
* [Přidání zobrazení](operations-management-suite-solutions-resources-views.md) do řešení pro správu.
* [Přidat runbooků služeb automatizace a dalším prostředkům](operations-management-suite-solutions-resources-automation.md) do řešení pro správu.

