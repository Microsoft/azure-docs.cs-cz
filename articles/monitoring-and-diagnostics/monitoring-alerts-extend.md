---
title: Rozšíření výstrahy (kopie) analýzy protokolů do Azure upozornění – přehled
description: Přehled procesu zkopírujte výstrahy z Log Analytics na portálu OMS do Azure výstrahy s podrobnosti adresování běžné otázky zákazníků.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 6484142eafa8388117c1e96ab31eefeab188e488
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750268"
---
# <a name="extend-log-analytics-alerts-to-azure-alerts"></a>Rozšíření výstrahy analýzy protokolů Azure výstrah
Dokud nedávno Azure Log Analytics zahrnuty vlastní výstrah funkce, které může proaktivně oznámíme vám podmínek založených na protokolu analytická data. Můžete spravovat pravidla výstrah na portálu Microsoft Operations Management Suite. Nové prostředí výstrahy má teď integrované výstrahy mezi různé služby ve službě Microsoft Azure. Tato možnost je dostupná jako **výstrahy** v části monitorování Azure na portálu Azure a podporuje výstrahy z aktivity protokoly, metriky a protokoly od analýzy protokolů a Azure Application Insights. 

## <a name="benefits-of-extending-your-alerts"></a>Výhody rozšíření upozornění
Existuje několik výhod při vytváření a Správa výstrah v portálu Azure, jako například:

- Na rozdíl od Azure výstrahy má na portálu služby Operations Management Suite, kde pouze 250 výstrahy může vytvořit a zobrazit, žádné takové omezení.
- Z Azure výstrah můžete spravovat, výčet a zobrazit všechny typy výstrah. Dříve může pouze uděláte pro analýzy protokolů výstrahy.
- Můžete omezit přístup k uživatelům pouze monitorování a výstrah, pomocí [role Azure monitorování](monitoring-roles-permissions-security.md).
- Ve výstrahách Azure, můžete použít [skupiny akcí](monitoring-action-groups.md). Můžete mít více než jednu akci pro každou výstrahu. SMS, můžete odeslat telefonní hovor, vyvolání runbooku automatizace Azure, vyvolat webhook, jehož a nakonfigurujte konektor IT služby správy (ITSM). 

## <a name="process-of-extending-your-alerts"></a>Proces rozšíření upozornění
Proces přesunu výstrahy z analýzy protokolů do Azure výstrahy nezahrnuje změna vaše definice upozornění, dotaz nebo konfigurace žádným způsobem. Vyžadována pouze změna je, že v Azure, můžete provádět všechny akce pomocí skupinu akcí. Pokud se akce skupiny jsou již přidruženy upozornění, jsou zahrnuty při rozšířit do Azure.

> [!NOTE]
> Microsoft bude automaticky rozšířit výstrahy vytvořené v analýzy protokolů Azure výstrah na 14 může 2018 počínaje řadu opakovaných, dokud nebude dokončena. Pokud máte jakékoli problémy s vytvářením [skupiny akcí](monitoring-action-groups.md), použijte [tyto kroky nápravy](monitoring-alerts-extend-tool.md#troubleshooting) získat automaticky vytvořené skupiny akce. Tyto kroky můžete použít až 5 července 2018. 
> 

Pokud naplánujete výstrahy v pracovním prostoru analýzy protokolů potřeba rozšířit do Azure, budou dál fungovat a nechcete v, že všechny způsobem ohrozit konfiguraci. Při naplánované, upozornění může být k dispozici pro úpravy dočasně, ale můžete vytvořit nové výstrahy Azure během této doby. Pokud se pokusíte upravovat nebo vytvářet výstrahy z portálu služby Operations Management Suite, máte možnost pokračovat ve vytváření je z pracovního prostoru analýzy protokolů. Můžete také vytvořit je z Azure výstrah na portálu Azure.

 ![Snímek obrazovky možnost vytvoření výstrahy z Log Analytics nebo Azure výstrahy](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Rozšíření výstrahy z analýzy protokolů Azure není platit poplatky ke svému účtu. Pomocí Azure výstrahy pro dotaz na základě analýzy protokolů výstrahy není účtován při použití v rámci omezení a podmínky uvádí [Azure monitorování cenové zásady](https://azure.microsoft.com/pricing/details/monitor/).  


### <a name="how-to-extend-your-alerts-voluntarily"></a>Postup rozšíření upozornění odpojit
K rozšíření upozornění na výstrahy Azure, můžete použít průvodce k dispozici na portálu služby Operations Management Suite, nebo můžete provést sestavují programově pomocí rozhraní API. Další informace najdete v tématu [rozšíří výstrahy do Azure pomocí rozhraní API a portálu Operations Management Suite](monitoring-alerts-extend-tool.md).

## <a name="experience-after-extending-your-alerts"></a>Prostředí po rozšíření upozornění
Po upozornění jsou rozšířené Azure výstrahy, se nadále k dispozici na portálu služby Operations Management Suite pro správu již jinak než dřív.

![Snímek obrazovky Operations Management Suite portál, pomocí výstrah uvedených](./media/monitor-alerts-extend/PostExtendList.png)

Při pokusu upravit stávající výstrahu nebo vytvořit nové oznámení na portálu služby Operations Management Suite, budete automaticky přesměrováni na Azure výstrahy.  

> [!NOTE]
> Zkontrolujte, že jsou oprávnění přiřazená jednotlivcům, kteří potřebují můžete přidat nebo upravit výstrahy správně přiřazené v Azure. Chcete-li pochopit, jaká oprávnění, je potřeba udělit, přečtěte si téma [oprávnění pro použití Azure monitorování a výstrahy](monitoring-roles-permissions-security.md).  
> 

Můžete vytvářet výstrahy z [Log Analytics API](../log-analytics/log-analytics-api-alerts.md) a [Log Analytics prostředků šablony](../monitoring/monitoring-solutions-resources-searches-alerts.md). Pokud tak učiníte, musíte zahrnout skupiny akcí.

## <a name="next-steps"></a>Další postup

* Další informace o nástroje pro [inicializace rozšíření výstrahy z analýzy protokolů do Azure](monitoring-alerts-extend-tool.md).
* Další informace o [prostředí Azure výstrahy](monitoring-overview-unified-alerts.md).
* Naučte se vytvářet [protokolu výstrahy ve výstrahách Azure](monitor-alerts-unified-log.md).
