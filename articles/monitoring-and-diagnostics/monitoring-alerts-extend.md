---
title: Rozšíření výstrahy (kopie) z portálu OMS do Azure – přehled | Microsoft Docs
description: Přehled procesu výstrah kopírování z portálu OMS do Azure výstrahy, podrobnosti kolem běžné otázky zákazníků.
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
ms.date: 04/06/2018
ms.author: vinagara
ms.openlocfilehash: 445adb7f57332a285494c744763f633806d2675e
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="extend-copy-alerts-from-oms-portal-into-azure"></a>Rozšíření výstrahy (kopie) z portálu OMS do Azure
Portál Operations Management Suite (OMS) zobrazí pouze analýzy protokolů výstrahy.  Nové prostředí výstrahy má teď integrované výstrahy prostředí mezi různé služby a částí v Microsoft Azure. Nové prostředí, které jsou k dispozici jako **výstrahy** v části monitorování Azure ve službě Azure portál obsahuje aktivitu protokolu výstrahy, metriky výstrahy a upozornění protokolu pro analýzy protokolů a Application Insights. 


Ale pro některé uživatele, použijte analýzy protokolů a allied funkcí, jako jsou výstrahy, byl prostřednictvím [portál Microsoft operaci Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). A proto a povolení jejich snadno spravovat jejich další prostředky Azure spolu s jejich používání analýzy protokolů - systematičtěji Microsoft má byla zajištění možnosti portálu OMS jsou také k dispozici na portálu Azure. V tomto ohledu Azure výstrahy již umožňuje uživatelům spravovat výstrahy na základě dotazu analýzy protokolů pro další informace najdete v tématu [protokolu výstrahy na Azure výstrahy](monitor-alerts-unified-log.md). V výstrahy pod Azure monitorování výstrahy vytvořeným na portálu OMS již uveden v části pracovní prostor analýzy příslušný protokol. Ale žádné úpravy nebo změnit na tyto výstrahy vytvořeným na portálu OMS, musí uživatel nechte Azure a používat portál OMS; pak se vraťte zpět do Azure potřeby ke správě jinou službu. Pokud chcete zkrátit tento obtížím vzniklým, Microsoft je teď uživatelům umožníte rozšířit jejich výstrahy z portálu OMS do Azure.

## <a name="benefits-of-extending-your-alerts"></a>Výhody rozšíření upozornění
Kromě benefit kumulovaných v nemusí přejděte mimo portál Azure existují další nejdůležitějšími výhody rozšíření výstrahy z portálu OMS do Azure

- Na rozdíl od na portálu OMS, kde může být pouze 250 výstrahy vytvořit a zobrazit; ve výstrahách Azure toto omezení není k dispozici
- Z Azure výstrah všechny typy výstrah lze spravovat, výčet a zobrazit; výstrahy nejen analýzy protokolů, jako je tomu u portálu OMS
- Řízení přístupu uživatelům, aby pouze sledování a výstrah pomocí [role Azure monitorování](monitoring-roles-permissions-security.md)
- Azure výstrahy využívat [skupiny akcí](monitoring-action-groups.md), které umožňují mít více než jednu akci pro každou výstrahu včetně SMS, hlasový hovor, sady Automation Runbook, Webhooku, ITSM konektoru a dalších. 

## <a name="process-of-extending-your-alerts"></a>Proces rozšíření upozornění
Proces rozšíření výstrahy z portálu OMS do Azure, nemá **není** zahrnuje změnu vaší definice upozornění, dotaz nebo konfigurace žádným způsobem. Jedinou změnou požadované je, že v Azure, všechny akce, jako je například e-mailové oznámení, webhooku hovoru, při spuštění sady automation runbook nebo připojení k nástroji ITSM hotovi prostřednictvím akce skupiny. Proto pokud příslušné akce skupiny jsou přidružené upozornění - jejich bude stát rozšířit do Azure.

Vzhledem k tomu, že proces rozšíření je nedestruktivní a není interruptive, společnost Microsoft rozšíří výstrahy vytvořeným na portálu OMS na Azure výstrahy automaticky – od **14 2018 může**. Z tohoto dne Microsoft zahájíte k plánování rozšíření výstrahy do Azure a postupně zkontrolujte všechny výstrahy přítomna na portálu OMS, spravovat z portálu Azure. 

Pokud výstrahy v pracovním prostoru analýzy protokolů získat naplánováno rozšíří do Azure, budou i nadále fungovat a bude **není** žádným způsobem ohrozit monitorování. Při plánování, upozornění pravděpodobně není k dispozici pro úpravy/úpravy dočasně; ale můžou dál nové výstrahy Azure vytvořit v této krátké doby. V tomto krátkém období žádné úpravy nebo vytvoření výstrahy se provádí z portálu OMS, budou mít uživatelé možnost pokračovat do Azure Log Analytics nebo Azure výstrahy.

 ![Během plánované doby akce uživatele na výstrahy přesměrován na Azure](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Rozšíření výstrahy z portálu OMS do Azure není účtován a využití Azure výstrahy pro dotaz na základě analýzy protokolů výstrahy nebude platit při použití v rámci omezení a podmínky uvedené v [Azure monitorování cenové zásady](https://azure.microsoft.com/en-us/pricing/details/monitor/)  

Uživatelé mohou využívat výhod rozšíření výstrahy před tímto datem; podle odpojit zvolit možnost, aby se daly spravovat v Azure výstrahy.

### <a name="how-to-voluntarily-extending-your-alerts"></a>Postup odpojit rozšíření upozornění
Umožníte uživatelům OMS snadno průchodu do Azure výstrahy, společnost Microsoft vytvořila nástroje umožňující rozšíření výstrahy. Zákazníci portálu Microsoft OMS můžete rozšířit jejich výstrahy do Azure buď pomocí Průvodce na portálu OMS (nebo) podle programový přístup pomocí nových rozhraní API. Další informace najdete v tématu [rozšíří výstrahy do Azure pomocí rozhraní API a portálu OMS](monitoring-alerts-extend-tool.md).


## <a name="usage-after-extending-your-alerts"></a>Po rozšíření upozornění využití
Jak jsme uvedli, výstrahách vytvořených v sadě Management operace Microsoft bude rozšířeno do Azure výstrah; Po, což je můžete spravovat z Azure. Výstrahy budou nadále uvedené na portálu OMS - část nastavení výstrah. Jak je uvedeno dále:

 ![Portál OMS výpis výstrahy po protažení do Azure](./media/monitor-alerts-extend/PostExtendList.png)

Pro žádnou operaci na výstrahy jako úprava nebo vytvoření udělat na portálu OMS se transparentně přesměruje uživatele na Azure výstrahy. 

> [!NOTE]
> Jak uživatelé se transparentně provedou do Azure, na všech přidání nebo úprava akce na výstrahu v OMS – Ujistěte se, uživatelé jsou správně mapována s příslušnou [oprávnění pro použití Azure monitorování a výstrahy](monitoring-roles-permissions-security.md)

Výstrahy, vytvoření bude pokračovat od existující [Log Analytics API](../log-analytics/log-analytics-api-alerts.md) jako starší, s pouze malé změny tom, že se po výstrahy jsou rozšířené do Azure - akce skupiny by bylo potřeba v plánu se proto přidružit.

## <a name="next-steps"></a>Další postup

* Další informace z nástrojů, který [inicializace rozšíření výstrahy od OMS do Azure](monitoring-alerts-extend-tool.md)
* Další informace o nové [prostředí Azure výstrahy](monitoring-overview-unified-alerts.md).
* Další informace o [protokolu výstrahy ve výstrahách Azure](monitor-alerts-unified-log.md).
