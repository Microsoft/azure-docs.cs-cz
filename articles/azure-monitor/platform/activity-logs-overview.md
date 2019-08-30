---
title: Přehled protokolu aktivit Azure
description: Přečtěte si, co je protokol aktivit Azure a jak ho můžete využít k porozumění událostem, ke kterým dochází v rámci vašeho předplatného Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: fa1737a8627fe9561a2a84e7f0ef69aefb6deb14
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70170623"
---
# <a name="overview-of-azure-activity-log"></a>Přehled protokolu aktivit Azure

**Protokol aktivit Azure** poskytuje přehled o událostech na úrovni předplatného, ke kterým došlo v Azure. To zahrnuje rozsah dat, od Azure Resource Manager provozních dat až po aktualizace Service Healthch událostí. Protokol aktivit byl dříve označován jako _protokoly auditu_ nebo _provozní protokoly_, protože kategorie správy oznamuje události na úrovni ovládacího prvku-roviny pro vaše předplatná. 

Pomocí protokolu aktivit můžete určit, _kdo_a _kdy_ se u prostředků ve vašem předplatném mají _dělat_operace zápisu (Put, post, DELETE). Můžete také zjištění stavu operace a další relevantní vlastnosti. 

Protokol aktivit nezahrnuje operace čtení (GET) nebo operací pro prostředky, které používají model Classic/RDFE.

## <a name="comparison-to-diagnostic-logs"></a>Porovnání s diagnostickými protokoly
Pro každé předplatné Azure existuje jeden protokol aktivit. Poskytuje data o operacích u prostředku z vnějšku ("Řídicí rovina"). [Diagnostické protokoly](diagnostic-logs-overview.md) vycházejí z prostředku a poskytují informace o operaci tohoto prostředku (rovina dat). U každého prostředku je nutné povolit nastavení diagnostiky.

![Protokoly aktivit v porovnání s diagnostickými protokoly](./media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)


> [!NOTE]
> Protokol aktivit Azure je primárně pro aktivity, ke kterým dochází v Azure Resource Manager. Nesleduje prostředky pomocí modelu Classic/RDFE. Některé typy klasických prostředků mají poskytovatele prostředků proxy v Azure Resource Manager (například Microsoft. ClassicCompute). Pokud pracujete s klasickým typem prostředku prostřednictvím Azure Resource Manager pomocí těchto zprostředkovatelů prostředků proxy, operace se zobrazí v protokolu aktivit. Pokud pracujete s klasickým typem prostředku mimo Azure Resource Manager proxy, vaše akce se zaznamenávají pouze do protokolu operací. Protokol operací se dá procházet v samostatné části portálu.

## <a name="activity-log-retention"></a>Uchování protokolu aktivit
Po vytvoření se položky protokolu aktivit nemění ani neodstraňují v systému. Také je nemůžete změnit v rozhraní nebo programově. Události protokolu aktivit jsou uloženy po dobu 90 dnů. Chcete-li uložit tato data po delší dobu, [Shromážděte je do Azure monitor](activity-log-collect.md) nebo [je exportujte do úložiště nebo Event Hubs](activity-log-export.md).

## <a name="view-the-activity-log"></a>Zobrazit protokol aktivit
Zobrazení protokolu aktivit pro všechny prostředky z nabídky **monitorování** v Azure Portal. Podívejte se na protokol aktivit pro konkrétní prostředek z možnosti **Protokol aktivit** v nabídce příslušného prostředku. Záznamy protokolu aktivit můžete také načíst pomocí PowerShellu, rozhraní příkazového řádku nebo REST API.  Podívejte [se na téma zobrazení a načtení událostí protokolu aktivit Azure](activity-log-view.md).

![Zobrazit protokol aktivit](./media/activity-logs-overview/view-activity-log.png)

## <a name="collect-activity-log-in-azure-monitor"></a>Shromažďovat protokol aktivit Azure Monitor
Shromážděte protokol aktivit do Log Analyticsho pracovního prostoru v Azure Monitor a analyzujte ho pomocí jiných dat monitorování a zachovejte data déle než 90 dní. Přečtěte si téma [shromáždění a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics v Azure monitor](activity-log-collect.md).

![Protokol aktivit dotazů](./media/activity-logs-overview/query-activity-log.png)

## <a name="export-activity-log"></a>Exportovat protokol aktivit
Exportujte protokol aktivit pro Azure Storage pro archivaci nebo streamování do centra událostí pro ingestování prostřednictvím služby třetí strany nebo řešení pro vlastní analýzu. Viz [Export protokolu aktivit Azure](activity-log-export.md). Můžete také analyzovat události protokolu aktivit v Power BI pomocí [**balíčku obsahu Power BI**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

## <a name="alert-on-activity-log"></a>Výstraha při protokolu aktivit
Můžete vytvořit výstrahu, když se v protokolu aktivit vytvoří konkrétní události s upozorněním [protokolu aktivit](activity-log-alerts.md). Můžete také vytvořit výstrahu pomocí [dotazu protokolu](alerts-log-query.md) , pokud je protokol aktivit připojen k pracovnímu prostoru Log Analytics, ale je k dispozici náklady na protokolování výstrah dotazů. Pro výstrahy protokolu aktivit se neúčtují žádné náklady.

## <a name="categories-in-the-activity-log"></a>Kategorie v protokolu aktivit
Každá událost v protokolu aktivit má konkrétní kategorii, která je popsána v následující tabulce. Všechny podrobnosti o schémat z těchto kategorií naleznete v tématu [schéma událostí protokolu aktivit Azure](activity-log-schema.md). 

| Kategorie | Popis |
|:---|:---|
| Administrativní | Obsahuje záznam všech operací vytvoření, aktualizace, odstranění a akcí provedených prostřednictvím Správce prostředků. Mezi příklady událostí správy patří _vytvořit virtuální počítač_ a _Odstranit skupinu zabezpečení sítě_.<br><br>Každá akce prováděná uživatelem nebo aplikací pomocí Správce prostředků je modelována jako operace pro konkrétní typ prostředku. Pokud je typ operace _zápis_, _odstranění_nebo _Akce_, záznamy o zahájení i úspěchu nebo selhání této operace se zaznamenávají do administrativní kategorie. Události správy také zahrnují všechny změny řízení přístupu na základě role v rámci předplatného. |
| Service Health | Obsahuje záznam o všech incidentech služby Health Service, ke kterým došlo v Azure. Příkladem události Service Health _SQL Azure v východní USA dochází_k výpadkům. <br><br>Service Health události přicházejí v šesti variantách: _Je vyžadována akce_, _pomocná obnovení_, _incident_, _Údržba_, _informace_nebo _zabezpečení_. Tyto události se vytvoří jenom v případě, že máte prostředek v předplatném, který by to ovlivnila událost.
| Stav prostředku | Obsahuje záznam o všech událostech stavu prostředku, ke kterým došlo u vašich prostředků Azure. Příkladem události Resource Health je _stav virtuálního počítače, který_není k dispozici.<br><br>Události Resource Health mohou představovat jeden ze čtyř stavů: _K dispozici_,nedostupné, degradované a _neznámé_. Resource Health události se navíc dají zařadit do kategorií jako iniciované platformou nebo _uživatelem iniciované_. |
| Výstrahy | Obsahuje záznam o aktivaci pro Azure Alerts. Příkladem události výstrahy je, že _procesor% v myVM byl za posledních 5 minut vyšší než 80_.|
| Automatické škálování | Obsahuje záznam všech událostí souvisejících s provozem modulu automatického škálování na základě všech nastavení automatického škálování, které jste definovali v předplatném. Příkladem události automatického škálování se nepovedlo provést _akci automatického škálování horizontálního_škálování. |
| Doporučení | Obsahuje události doporučení z Azure Advisor. |
| Zabezpečení | Obsahuje záznam všech výstrah vygenerovaných nástrojem Azure Security Center. Příkladem události zabezpečení je podezřelý _soubor_s dvojitou příponou. |
| Zásada | Obsahuje záznamy všech operací akce účinku prováděných Azure Policy. Příklady událostí zásad zahrnují _audit_ a _Deny_. Každá akce prováděná zásadami je modelovaná jako operace na prostředku. |


## <a name="next-steps"></a>Další kroky

* [Vytvořit profil protokolu pro export protokolu aktivit Azure](activity-log-export.md)
* [Streamování protokolu aktivit Azure do služby Event Hubs](activity-logs-stream-event-hubs.md)
* [Archivace protokolu aktivit Azure do úložiště](archive-activity-log.md)

