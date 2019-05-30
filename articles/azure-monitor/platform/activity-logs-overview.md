---
title: Přehled protokolu aktivit Azure
description: Zjistěte, co je protokol aktivit Azure a jak ho použít k pochopení události, ke kterým dochází v rámci vašeho předplatného Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 34857108cf7f0580c380ffbd4bbcedb5cd5a807a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245028"
---
# <a name="overview-of-azure-activity-log"></a>Přehled protokolu aktivit Azure

**Protokolu aktivit Azure** poskytuje podrobné informace o události na úrovni předplatného, ke kterým došlo v Azure. To zahrnuje celou řadu dat z Azure Resource Manageru provozních dat k aktualizacím na události služby Service Health. Protokol aktivit se dřív označovalo jako _protokoly auditu_ nebo _operační protokoly_, protože administrativní kategorie sestavy událostí rovina řízení pro vaše předplatná. 

Použití protokolu aktivit k určení _co_, _kdo_, a _při_ veškerých operací (PUT, POST, DELETE) provedených ve prostředků ve vašem předplatném zápisu. Můžete také zjištění stavu operace a další relevantní vlastnosti. 

Protokol aktivit neobsahuje operace čtení (GET) ani operace pro prostředky, které používají model Classic/RDFE.

## <a name="comparison-to-diagnostic-logs"></a>Porovnání k diagnostickým protokolům
Existuje jeden protokol aktivit pro každé předplatné Azure. Poskytuje data o operacích v prostředku z vnějšku ("rovině řízení"). [Diagnostické protokoly](diagnostic-logs-overview.md) jakém množství se vysílají prostředek a poskytnout informace o fungování tohoto prostředku ("rovině dat"). Je nutné povolit nastavení diagnostiky pro každého prostředku.

![Protokoly aktivit ve srovnání s diagnostické protokoly](./media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)


> [!NOTE]
> Protokol aktivit Azure je primárně určen pro aktivity, ke kterým dochází v Azure Resource Manageru. Sledovat ugresources pomocí modelu Classic/RDFE. Některé typy prostředků Classic mají poskytovatele prostředků proxy serveru v Azure Resource Manageru (například Microsoft.ClassicCompute). Pokud pracujete s typem prostředku klasické prostřednictvím Azure Resource Manageru pomocí těchto poskytovatelů prostředků proxy serveru, operace se zobrazí v protokolu aktivit. Pokud pracujete s typem prostředku klasické mimo proxy služby Azure Resource Manageru, vaše akce se zaznamenávají jenom v operaci protokolu. Protokol operace můžete procházet v samostatných oddílech portálu.

## <a name="activity-log-retention"></a>Uchovávání protokolů aktivit
Události protokolu aktivit se ukládají po dobu 90 dnů. Pro ukládání těchto dat delší dobu [shromažďovat ve službě Azure Monitor](activity-log-collect.md) nebo [exportujte ho do úložiště nebo Center událostí](activity-log-export.md).

## <a name="view-the-activity-log"></a>Zobrazení protokolu aktivit
Zobrazení protokolu aktivit pro všechny prostředky z **monitorování** nabídky na webu Azure Portal. Zobrazení protokolu aktivit pro určitý prostředek z **protokolu aktivit** v nabídce tohoto prostředku. Můžete také načíst záznamy protokolu aktivit pomocí Powershellu, rozhraní příkazového řádku nebo rozhraní REST API.  Zobrazit [zobrazení a načtení Azure aktivity protokolu události](activity-log-view.md).

![Zobrazení protokolu aktivit](./media/activity-logs-overview/view-activity-log.png)

## <a name="collect-activity-log-in-azure-monitor"></a>Shromažďování protokolů aktivit ve službě Azure Monitor
Shromažďování protokolu aktivit do pracovního prostoru Log Analytics ve službě Azure Monitor k analýze s dalšími daty monitorování a uchovávání dat po dobu delší než 90 dní. Zobrazit [shromažďovat a analyzovat protokoly aktivit Azure do pracovního prostoru Log Analytics ve službě Azure Monitor](activity-log-collect.md).

![Dotaz protokolu aktivit](./media/activity-logs-overview/query-activity-log.png)

## <a name="export-activity-log"></a>Exportovat protokol aktivit
Export protokolu aktivit do služby Azure Storage pro archivaci nebo Streamovat do centra událostí za účelem ingestování datových služba třetí strany nebo vlastní analytické řešení. Zobrazit [exportovat protokol aktivit Azure](activity-log-export.md). Můžete také analyzovat události protokolu aktivit v Power BI pomocí [ **balíček obsahu Power BI**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

## <a name="alert-on-activity-log"></a>Upozornění v protokolu aktivit
Můžete vytvořit výstrahu při určité události v protokolu aktivit s [upozornění protokolu aktivit](activity-log-alerts.md). Můžete také vytvořit pomocí výstrah [dotaz protokolu](alerts-log-query.md) Pokud váš protokol aktivit je připojený k pracovnímu prostoru Log Analytics, ale je do protokolu dotazování výstrah. Se neúčtují žádné poplatky pro upozornění protokolu aktivit.

## <a name="categories-in-the-activity-log"></a>Kategorie v protokolu aktivit
Každá událost v protokolu aktivit se určité kategorie, které jsou popsány v následující tabulce. Všechny podrobnosti o schémat z těchto kategorií naleznete v tématu [schéma událostí protokolu aktivit Azure](activity-log-schema.md). 

| Category | Popis |
|:---|:---|
| Správa | Obsahuje záznam všech vytvoření, aktualizace, odstranění a akce operace provést prostřednictvím Resource Manageru. Příklady pro správu událostí _vytvořit virtuální počítač_ a _odstranit skupinu zabezpečení sítě_.<br><br>Všechny akce, které uživatele nebo aplikace pomocí Resource Manageru je modelovaná jako operace na konkrétní typ prostředku. Pokud je typ operace _zápisu_, _odstranit_, nebo _akce_, záznamy o zahájení a úspěchu nebo selhání této operace se zaznamenávají do administrativní kategorie. Události správy také obsahovat žádné změny k řízení přístupu na základě rolí v rámci předplatného. |
| Service Health | Obsahuje záznam všechny incidenty health service, ke kterým došlo v Azure. Příklad události Service Health _SQL Azure v oblasti východní USA má výpadek_. <br><br>Události služby Service Health dostaneme v pěti typy: _Požaduje se akce_, _s asistencí obnovení_, _Incident_, _údržby_, _informace_, nebo  _Zabezpečení_. Tyto události jsou vytvářeny, pouze pokud máte prostředek v rámci předplatného, který bude mít vliv na událost.
| Resource Health | Obsahuje záznam všechny události stavu prostředků, ke kterým došlo u vašich prostředků Azure. Příklad události Resource Health je _stav virtuálního počítače změnil na k dispozici_.<br><br>Události stavu prostředků může představovat jeden z čtyř stavů stavu: _K dispozici_, _není k dispozici_, _Degradovaný_, a _neznámý_. Kromě toho události stavu prostředků můžete zařadit _iniciovala platforma_ nebo _, kterou inicioval uživatel_. |
| Výstrahy | Obsahuje záznam aktivací pro upozornění v Azure. Je například událost výstrahy _% využití procesoru na myVM bylo za posledních 5 minut více než 80_.|
| Automatické škálování | Obsahuje záznam žádné události vztahující se k operaci modul automatického škálování podle libovolné nastavení automatického škálování, které jste definovali v rámci vašeho předplatného. Příklad události automatického škálování je _automaticky škálovat škálovací nahoru akce se nezdařilo_. |
| Doporučení | Obsahuje události doporučení Azure advisoru. |
| Zabezpečení | Obsahuje záznam všech výstrah generovaných v Azure Security Center. Příklad události zabezpečení je _podezřelou dvojitou příponou souboru provést_. |
| Zásada | Obsahuje záznamy všechny operace vliv akce prováděné zásadami Azure. Příklady události zásad _auditu_ a _Odepřít_. Každou akci provedenou na základě zásad je modelovaná jako operace pro prostředek. |


## <a name="next-steps"></a>Další kroky

* [Vytvořit profil protokolu, který chcete exportovat protokol aktivit Azure](activity-log-export.md)
* [Stream protokolů aktivit Azure do služby Event Hubs](activity-logs-stream-event-hubs.md)
* [Archivace protokolu aktivit Azure do služby storage](archive-activity-log.md)

