---
title: Rozšíření výstrahy (kopie) analýzy protokolů do Azure upozornění – přehled | Microsoft Docs
description: Přehled procesu zkopírujte výstrahy z Log Analytics na portálu OMS do Azure výstrahy s podrobnosti adresování běžné otázky zákazníků.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: vinagara
ms.openlocfilehash: 296bdc6939ef8ab67656e5805cd603b50bce211e
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763149"
---
# <a name="extend-log-analytics-alerts-to-azure-alerts"></a>Rozšíření výstrahy analýzy protokolů Azure výstrah
Dokud nedávno Azure Log Analytics zahrnuty vlastní výstrah funkce, které může proaktivně oznámíme vám podmínek založených na protokolu analytická data.  Správa pravidla výstrah se prováděly v [portál Microsoft operaci Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Nové prostředí výstrahy má teď integrované výstrahy prostředí mezi různé služby ve službě Microsoft Azure. Nové prostředí je k dispozici jako **výstrahy** v části monitorování Azure na portálu Azure a podporuje výstrahy z aktivity protokoly, metriky a protokoly od analýzy protokolů a Application Insights. 

## <a name="benefits-of-extending-your-alerts"></a>Výhody rozšíření upozornění
Existuje několik výhod při vytváření a Správa výstrah v Azure port, jako například:

- Na rozdíl od na portálu OMS, kde může být pouze 250 výstrahy vytvořit a zobrazit; ve výstrahách Azure toto omezení není k dispozici
- Z Azure výstrah všechny typy výstrah lze spravovat, výčet a zobrazit; nejen analýzy protokolů výstrahy stejně jako v případě dříve
- Řízení přístupu uživatelům, aby pouze sledování a výstrah pomocí [role Azure monitorování](monitoring-roles-permissions-security.md)
- Azure výstrahy využívat [skupiny akcí](monitoring-action-groups.md), což umožňuje mít více než jednu akci pro každou výstrahu včetně SMS, odeslání telefonní hovor, vyvolání Runbook služby automatizace, vyvolat Webhook, jehož, nakonfigurovat konektor ITSM a další. 

## <a name="process-of-extending-your-alerts"></a>Proces rozšíření upozornění
Proces přesunu výstrahy z analýzy protokolů do Azure výstrahy, nebude **není** zahrnuje změnu vaší definice upozornění, dotaz nebo konfigurace žádným způsobem. Vyžadována pouze změna je, že v Azure, všechny akce, jako je například e-mailové oznámení, volání webhooku systémem runbook služby automatizace nebo připojení k vaší ITSM nástroj se provádí pomocí skupinu akcí. Pokud akce skupiny jsou již přidruženy upozornění - budou zahrnuty při rozšířit do Azure.

> [!NOTE]
> Společnost Microsoft automaticky rozšíří výstrahy vytvořené v analýzy protokolů Azure výstrah, spouštění na **14 2018 může** v řadě opakování až do dokončení. Pokud dochází k potížím při vytváření [skupiny akcí](monitoring-action-groups.md), uživatel může použít [uvedených kroků nápravy](monitoring-alerts-extend-tool.md#troubleshooting) dokud **5 července 2018** získat automaticky vytvořené skupiny akce. 
> 

Pokud výstrahy v pracovním prostoru analýzy protokolů jsou naplánovány pro rozšíření do Azure, budou i nadále fungovat a bude **není** žádným způsobem ohrozit konfiguraci. Při plánování, upozornění pravděpodobně není k dispozici pro úpravy/úpravy dočasně; ale můžou dál nové výstrahy Azure vytvořit během této doby. Pokud se pokusíte upravovat nebo vytvářet výstrahy z portálu OMS, budete mít možnost pokračovat ve vytváření je z pracovního prostoru analýzy protokolů nebo z Azure výstrah na portálu Azure.

 ![Během plánované doby akce uživatele na výstrahy přesměrován na Azure](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> rozšíření výstrahy z analýzy protokolů Azure není platit poplatky pro váš účet a využití Azure výstrah pro dotaz na základě analýzy protokolů výstrahy nebude platit při použití v rámci omezení a podmínky uvádí [Azure monitorování cenové zásady](https://azure.microsoft.com/pricing/details/monitor/)  

Můžete využívat výhod rozšíření výstrahy před tímto datem podle odpojit vyjádření výslovného přesunout do Azure výstrahy.

### <a name="how-to-voluntarily-extend-your-alerts"></a>Postup odpojit rozšíření upozornění
K rozšíření upozornění na výstrahy Azure, jsme zahrnuli dvě metody pro dokončení této úlohy v pracovním prostoru.  To můžete udělat buď pomocí průvodce k dispozici v portálu OMS nebo programově pomocí nové rozhraní API.  Další informace najdete v tématu [rozšíří výstrahy do Azure pomocí rozhraní API a portálu OMS](monitoring-alerts-extend-tool.md).

## <a name="experience-after-extending-your-alerts"></a>Prostředí po rozšíření upozornění
Po upozornění jsou rozšířené výstrahy Azure, budou k dispozici na portálu OMS pro správu již jinak než dřív.<br><br> ![Výpis výstrahy po protažení na Azure portálu OMS](./media/monitor-alerts-extend/PostExtendList.png)

Při pokusu upravit stávající výstrahu nebo vytvořit nové oznámení na portálu OMS budete automaticky přesměrováni na Azure výstrahy.  

> [!NOTE]
> Je nutné zajistit, že se oprávnění přiřazená jednotlivce, kteří potřebují můžete přidat nebo upravit výstrahy přiřazené správně v Azure.  Zkontrolujte, [oprávnění pro použití Azure monitorování a výstrahy](monitoring-roles-permissions-security.md) pochopit, jaká oprávnění je potřeba udělit.  
> 

Výstrahy vytvoření budou nadále fungovat z [Log Analytics API](../log-analytics/log-analytics-api-alerts.md) a [Log Analytics prostředků šablony](../monitoring/monitoring-solutions-resources-searches-alerts.md), s menšími pouze jeden změnit, že je nutné nainstalovat – akce skupiny musí být zahrnuty.

## <a name="next-steps"></a>Další postup

* Další informace o nástroje pro [inicializace rozšíření výstrahy z analýzy protokolů do Azure](monitoring-alerts-extend-tool.md)
* Další informace o nové [prostředí Azure výstrahy](monitoring-overview-unified-alerts.md).
* Naučte se vytvářet [protokolu výstrahy ve výstrahách Azure](monitor-alerts-unified-log.md).
