---
title: Řešení pro správu v osvědčené postupy Azure | Dokumentace Microsoftu
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
ms.openlocfilehash: 726159f7450c29385a331b29191c38085a820876
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52957619"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Osvědčené postupy pro vytváření řešení pro správu v Azure (Preview)
> [!NOTE]
> Toto je předběžná dokumentace pro vytváření řešení pro správu v Azure, které jsou aktuálně ve verzi preview. Žádné schéma je popsáno níže se může změnit.  

Tento článek obsahuje osvědčené postupy pro [vytváření souboru řešení správy](solutions-solution-file.md) v Azure.  Tyto informace se aktualizují jako další doporučené postupy jsou označeny.

## <a name="data-sources"></a>Zdroje dat
- Zdroje dat mohou být [nakonfigurované pomocí šablony Resource Manageru](../../azure-monitor/platform/template-workspace-configuration.md), ale by neměly být obsažené v souboru řešení.  Důvodem je, že konfigurace zdroje dat není aktuálně idempotentní, což znamená, že vaše řešení může přepsat existující konfigurace v pracovním prostoru uživatele.<br><br>Vaše řešení například může vyžadovat upozornění a chybové události z protokolu událostí aplikace.  Pokud toto určíte jako zdroj dat ve vašem řešení, riskujete odebrání – informační události, pokud má uživatel nakonfigurovaný v jejich pracovním prostoru.  Pokud jste zahrnuli všechny události, pak vám může pokračovat ve shromažďování nadměrné – informační události v pracovním prostoru uživatele.

- Pokud vaše řešení vyžaduje data z jedné z standardních datových zdrojů, pak byste měli definovat to jako předpoklad.  V dokumentaci k stavu, že zákazník musí zdroj dat nakonfigurovat pro svoje vlastní.  
- Přidat [ověření toku dat](../../azure-monitor/platform/view-designer-tiles.md) zprávy k zobrazením v rámci vašeho řešení pro pokyn uživateli na zdroje dat, které je potřeba nakonfigurovat pro požadovaná data se mají shromažďovat.  Tato zpráva se zobrazí na dlaždici zobrazení, když nebyla nalezena požadovaná data.


## <a name="runbooks"></a>Runbooky
- Přidat [plán Automation](../../automation/automation-schedules.md) pro každou sadu runbook ve vašem řešení, které je potřeba spouštět podle plánu.
- Zahrnout [IngestionAPI modulu](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) ve vašem řešení pro zápis dat do úložiště Log Analytics sady runbook.  Nakonfigurujte řešení, aby [odkaz](solutions-solution-file.md#solution-resource) tento prostředek, takže se budou když toto řešení se odebere.  To umožňuje více řešení ke sdílení modulu.
- Použití [proměnné služeb automatizace](../../automation/automation-schedules.md) zadejte hodnoty do řešení, které mohou uživatelé chtít později změnit.  I když toto řešení je nakonfigurovaná tak, aby obsahovala proměnné, její hodnotu změnit půjdou.

## <a name="views"></a>Zobrazení
- Jedno zobrazení, které se zobrazuje uživatele portálu by měl obsahovat všechna řešení.  Zobrazení může obsahovat více [části vizualizace](../../azure-monitor/platform/view-designer-parts.md) pro ilustraci různých sad dat.
- Přidat [ověření toku dat](../../azure-monitor/platform/view-designer-tiles.md) zprávy k zobrazením v rámci vašeho řešení pro pokyn uživateli na zdroje dat, které je potřeba nakonfigurovat pro požadovaná data se mají shromažďovat.
- Nakonfigurujte řešení, aby [obsahovat](solutions-solution-file.md#solution-resource) zobrazení tak, že je odstranit, pokud se odebere řešení.

## <a name="alerts"></a>Výstrahy
- Definujte seznam příjemců jako parametr v souboru řešení, takže uživatel může definovat je při instalaci řešení.
- Nakonfigurujte řešení, aby [odkaz](solutions-solution-file.md#solution-resource) tak tento uživatel může změnit svou konfiguraci pravidla upozornění.  Mohou chtít provést změny, například kvůli úpravě seznamu příjemců, změna prahovou hodnotu upozornění nebo deaktivace pravidla upozornění. 


## <a name="next-steps"></a>Další postup
* Provede základní proces [návrh a řešení pro správu budov](solutions-creating.md).
* Zjistěte, jak [vytvořit soubor řešení](solutions-solution-file.md).
* [Přidat uložené výsledky hledání a výstrahy](solutions-resources-searches-alerts.md) do řešení pro správu.
* [Přidání zobrazení](solutions-resources-views.md) do řešení pro správu.
* [Přidání runbooků služeb automatizace a dalším prostředkům](solutions-resources-automation.md) do řešení pro správu.

