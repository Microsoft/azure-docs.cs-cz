---
title: Jak nakonfigurovat monitorování v Azure Digital revláken | Microsoft Docs
description: Jak nakonfigurovat monitorování v digitálních Vlákenách Azure.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/09/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: d0f5f9a1d488b6be8ca91fdd057880e351ae0b3f
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261032"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Jak nakonfigurovat monitorování v digitálních prozdvojeních Azure

Digitální vlákna Azure podporuje robustní protokolování, monitorování a analýzu. Vývojáři řešení můžou používat protokoly Azure Monitor, diagnostické protokoly, protokolování aktivit a další služby pro podporu složitých potřeb monitorování aplikace IoT. Možnosti protokolování lze kombinovat pro dotazování nebo zobrazení záznamů napříč několika službami a pro zajištění podrobného pokrytí protokolování pro mnoho služeb.

Tento článek shrnuje možnosti protokolování a monitorování a jejich kombinaci způsobem specifickým pro digitální vlákna Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>Kontrola protokolů aktivit

[Protokoly aktivit](../azure-monitor/platform/activity-logs-overview.md) Azure poskytují rychlé přehledy o akcích a historiích operací na úrovni předplatného pro každou instanci služby Azure.

Mezi události na úrovni předplatného patří:

* Vytvoření prostředku
* Odebrání prostředku
* Vytváření tajných klíčů aplikací
* Integrace s dalšími službami

Protokolování aktivit pro digitální vlákna Azure je ve výchozím nastavení povolené a dá se najít prostřednictvím Azure Portal:

1. Vybírá se instance digitálního vlákna Azure.
1. Výběr **protokolu aktivit** pro zobrazení panelu zobrazení:

    [![Protokol aktivit](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

Pro rozšířené protokolování aktivit:

1. Výběrem možnosti **protokoly** zobrazíte **Activity Log Analytics přehled**:

    [![Výběru](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. **Přehled Activity Log Analytics** shrnuje data základních protokolů aktivit:

    [![Přehled analýzy protokolů aktivit]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>**Protokoly aktivit** se používají pro rychlé přehledy událostí na úrovni předplatného.

## <a name="enable-customer-diagnostic-logs"></a>Povolit diagnostické protokoly zákazníků

[Nastavení diagnostiky](../azure-monitor/platform/resource-logs-overview.md) Azure je možné nastavit pro každou instanci Azure a doplnit protokolování aktivit. Protokoly aktivit se týkají událostí na úrovni předplatného a diagnostické protokolování nabízí přehledy o provozní historii samotných prostředků.

Mezi příklady protokolování diagnostiky patří:

* Doba provádění uživatelsky definované funkce
* Stavový kód odpovědi na úspěšnou žádost o rozhraní API
* Načtení klíče aplikace z trezoru

Povolení diagnostických protokolů pro instanci:

1. Uveďte prostředek v Azure Portal.
1. Vyberte **nastavení diagnostiky**:

    [![Nastavení diagnostiky 1](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Vyberte **zapnout diagnostiku** pro shromažďování dat (Pokud jste předtím nepovolili).
1. Vyplňte požadovaná pole a vyberte, jak a kam se budou data ukládat:

    [![Nastavení diagnostiky dvě](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    Diagnostické protokoly se často ukládají pomocí [Azure File Storage](../storage/files/storage-files-deployment-guide.md) a sdílí se s [protokoly Azure monitor](../azure-monitor/log-query/get-started-portal.md). Lze vybrat obě možnosti.

>[!TIP]
>Pomocí **diagnostických protokolů** můžete získat přehled o operacích prostředků.

## <a name="azure-monitor-and-log-analytics"></a>Azure monitor a Log Analytics

Aplikace IoT jednotkám jsou různorodé prostředky, zařízení, místa a data do jedné. Podrobné protokolování poskytuje podrobné informace o jednotlivých součástech, službě nebo komponentě celkové architektury aplikace, ale jednotný přehled je často nutný k údržbě a ladění.

Azure Monitor obsahuje výkonnou službu Log Analytics, která umožňuje zobrazení a analýzu zdrojů protokolování na jednom místě. Azure Monitor je proto vysoce užitečný pro analýzu protokolů v propracovaných aplikacích IoT.

Příklady použití zahrnují:

* Dotazování na několik historií protokolů diagnostiky
* Zobrazení protokolů pro několik uživatelsky definovaných funkcí
* Zobrazení protokolů pro dvě nebo více služeb v rámci určitého časového rámce

Úplné dotazování protokolu je k dispozici prostřednictvím [protokolů Azure monitor](../azure-monitor/log-query/log-query-overview.md). Nastavení těchto výkonných funkcí:

1. Vyhledejte **Log Analytics** v Azure Portal.
1. Zobrazí se vaše dostupné **Log Analytics instance pracovních prostorů** . Vyberte jednu z **protokolů** pro dotazování:

    [![Log Analytics](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Pokud ještě instanci **pracovního prostoru Log Analytics** nemáte, můžete si vytvořit pracovní prostor tak, že vyberete tlačítko **Přidat** :

    [![Vytvořit OMS](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

Po zřízení instance **pracovního prostoru Log Analytics** můžete použít výkonné dotazy pro hledání záznamů v protokolech více než v protokolu nebo hledání pomocí určitých kritérií pomocí **správy protokolů**:

   [![Správa protokolů](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

Další informace o výkonných operacích dotazů najdete v tématu [Začínáme s dotazy](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Při prvním posílání událostí do **Log Analytics pracovního prostoru** může docházet k prodlevě 5 minut.

Protokoly Azure Monitor také poskytují výkonné služby chyb a výstrah, které lze zobrazit výběrem možnosti **diagnostikovat a řešit problémy**:

   [![Upozornění a upozornění na chyby](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>Použijte **Log Analytics pracovní prostor** k dotazování na Historie protokolu pro více funkcí, předplatných nebo služeb aplikací.

## <a name="other-options"></a>Další možnosti

Digitální vlákna Azure také podporují protokolování a auditování zabezpečení specifické pro aplikace. Podrobný přehled všech možností protokolování Azure, které jsou dostupné pro vaši instanci digitálních vláken Azure, najdete v článku věnovaném [auditu protokolu Azure](../security/fundamentals/log-audit.md) .

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [protokolech aktivit](../azure-monitor/platform/activity-logs-overview.md)Azure.

- Podrobně hlubšího nastavení diagnostiky Azure tím, že si přečtete [Přehled diagnostických protokolů](../azure-monitor/platform/resource-logs-overview.md).

- Přečtěte si další informace o [protokolech Azure monitor](../azure-monitor/log-query/get-started-portal.md).
