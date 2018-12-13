---
title: Rozšíření upozornění (kopie) Log Analytics do upozornění Azure – přehled
description: Přehled procesu zkopírování upozornění z Log Analytics na portálu OMS do upozornění Azure s podrobně popisuje adresování běžné problémy zákazníků.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 7896a205cd281f0d11724f6f549b9aa247180bac
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285015"
---
# <a name="extend-log-analytics-alerts-to-azure-alerts"></a>Rozšíření upozornění Log Analytics do Azure Alerts

> [!NOTE]
> Microsoft už dokončili proces popsaný v tomto článku pro veřejné verze Azure. Nicméně stále platí pro verze pro státní správu USA.  

Dokud se nedávno, Azure Log Analytics zahrnuté vlastní výstrahy funkce, které by vás proaktivně upozorní podmínek založených na datech Log Analytics. Můžete spravovat pravidla výstrah ve službě portál Microsoft Operations Management Suite. Nové prostředí upozornění teď obsahuje integrované výstrahy napříč různými službami v Microsoft Azure. Tato možnost je dostupná jako **výstrahy** v části Azure Monitor na webu Azure Portal a podporuje upozornění z aktivity protokolů, metrik a protokolů v Log Analytics a Azure Application Insights. 

## <a name="benefits-of-extending-your-alerts"></a>Výhody rozšiřovat vaše upozornění
Existuje několik výhod, vytváření a Správa výstrah na webu Azure Portal, jako například:

- Na rozdíl od portálu Operations Management Suite, kde pouze 250 výstrahy může vytvořit a zobrazit, Azure Alerts nemá žádné takové omezení.
- Ve službě Azure Alerts můžete spravovat, výčet a zobrazit všechny typy výstrah. Dříve vám může tak učinit pouze pro upozornění Log Analytics.
- Můžete omezit přístup pouze k monitorování a upozorňování s využitím uživatelům [role Azure Monitor](../../monitoring-and-diagnostics/monitoring-roles-permissions-security.md).
- Ve výstrahách Azure, můžete použít [skupiny akcí](../../azure-monitor/platform/action-groups.md). To umožňuje mít více než jednu akci pro každou výstrahu. Zprávy SMS, můžete odeslat hlasový hovor, volání runbooku Azure Automation, vyvolat webhook a nakonfigurujete konektor správy služeb IT (ITSM). 

## <a name="process-of-extending-your-alerts"></a>Proces rozšiřovat vaše upozornění
Proces přechodu upozornění z Log Analytics do upozornění Azure nezahrnuje Změna definice upozornění, dotaz nebo konfiguraci jakýmkoli způsobem. Jediná změna, vyžaduje se, že v Azure, můžete provádět všechny akce s použitím skupiny akcí. Pokud skupiny akcí jsou už přidružené výstrahy, jsou zahrnuty, když se rozšíří do Azure.

Při plánování výstrahy v pracovním prostoru Log Analytics na rozšířit do Azure jsou dál fungovat a nejsou v, že některé způsobem ohrožení vaší konfigurace. Při plánování, upozornění možná nebude k dispozici pro úpravy dočasně, ale můžete pokračovat k vytvoření nové výstrahy Azure během této doby. Při pokusu upravit nebo vytvořit oznámení z portálu Operations Management Suite, máte možnost pokračovat ve vytváření z pracovního prostoru Log Analytics. Můžete také na webu Azure Portal je vytvořit ve službě Azure Alerts.

 ![Snímek obrazovky možnost vytvoření upozornění z Log Analytics nebo Azure Alerts](media/alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Rozšíření upozornění z Log Analytics do Azure není účtovat poplatky za ke svému účtu. Pro dotaz na základě upozornění Log Analytics pomocí Azure Alerts neúčtuje se při použití v rámci omezení a podmínky uvedené v [ceny zásad Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).  


### <a name="how-to-extend-your-alerts-voluntarily"></a>Jak odpojit rozšířit vaše upozornění
Můžete rozšířit svá upozornění do Azure Alerts, použijete průvodce k dispozici na portálu Operations Management Suite, nebo můžete provést sestavují programově pomocí rozhraní API. Další informace najdete v tématu [upozornění rozšíří do Azure pomocí rozhraní API a portálu Operations Management Suite](alerts-extend-tool.md).

## <a name="experience-after-extending-your-alerts"></a>Po skončení rozšiřovat vaše upozornění
Po upozornění jsou rozšířeny, aby Azure Alerts, nadále být k dispozici na portálu Operations Management Suite pro správu nejsou jinak než dřív.

![Snímek obrazovky s Operations Management Suite portál, pomocí výstrah uvedených](media/alerts-extend/PostExtendList.png)

Při pokusu upravit existující upozornění nebo vytvořit nové oznámení na portálu Operations Management Suite, budete automaticky přesměrováni na Azure Alerts.  

> [!NOTE]
> Ujistěte se, že oprávnění přiřazená uživateli osobám, které potřebujete k přidání nebo úpravě výstrahy jsou správně přiřazené v Azure. Chcete-li pochopit, jaká oprávnění je nutné udělit, přečtěte si téma [oprávnění pro pomocí Azure monitoru a výstrahy](../../monitoring-and-diagnostics/monitoring-roles-permissions-security.md).  
> 

Můžete pokračovat k vytvoření výstrahy [rozhraní API služby Log Analytics](../../azure-monitor/platform/api-alerts.md) a [šablony prostředku Log Analytics](../../azure-monitor/insights/solutions-resources-searches-alerts.md). Pokud tak učiníte, musíte zahrnout skupiny akcí.

## <a name="next-steps"></a>Další postup

* Další informace o nástrojích pro [zahájit rozšiřování upozornění z Log Analytics do Azure](alerts-extend-tool.md).
* Další informace o [prostředí Azure Alerts](../../monitoring-and-diagnostics/monitoring-overview-alerts.md).
* Zjistěte, jak vytvořit [upozornění protokolů ve službě Azure Alerts](alerts-unified-log.md).
