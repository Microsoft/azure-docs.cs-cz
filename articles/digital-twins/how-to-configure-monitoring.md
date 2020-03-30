---
title: Konfigurace monitorování – Digitální dvojčata Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nakonfigurovat možnosti monitorování a protokolování pro Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: e35e18be20af3bd9f6fdc9541f9abfe857a6b87c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511854"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Konfigurace monitorování v Azure Digital Twins

Azure Digital Twins podporuje robustní protokolování, monitorování a analýzy. Vývojáři řešení můžou používat protokoly Azure Monitoru, diagnostické protokoly, protokolování aktivit a další služby k podpoře složitých potřeb monitorování aplikace IoT. Možnosti protokolování lze kombinovat pro dotazování nebo zobrazení záznamů v několika službách a poskytovat podrobné pokrytí protokolování pro mnoho služeb.

Tento článek shrnuje možnosti protokolování a monitorování a jak je kombinovat způsobem specifickým pro Azure Digital Twins.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>Kontrola protokolů aktivit

[Protokoly aktivit](../azure-monitor/platform/platform-logs-overview.md) Azure poskytují rychlý přehled o historii událostí a operací na úrovni předplatného pro každou instanci služby Azure.

Mezi události na úrovni předplatného patří:

* Vytvoření zdroje
* Odebrání prostředků
* Vytváření tajných kódů aplikace
* Integrace s dalšími službami

Protokolování aktivit pro Azure Digital Twins je ve výchozím nastavení povolené a na webu Azure Portal se našlo takto:

1. Výběr instance Azure Digital Twins.
1. Volba **protokolu aktivit** pro zobrazení panelu:

    [![Protokol aktivit](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

Pro protokolování pokročilých aktivit:

1. Výběrem možnosti **Protokoly** zobrazíte **přehled protokolu aktivit**:

    [![Výběr](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. **Přehled analýzy protokolu aktivit** shrnuje základní data protokolu aktivit:

    [![Přehled analýzy protokolu aktivit]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>Pomocí **protokolů aktivit** můžete získat rychlý přehled o událostech na úrovni předplatného.

## <a name="enable-customer-diagnostic-logs"></a>Povolení diagnostických protokolů zákazníků

[Nastavení diagnostiky](../azure-monitor/platform/platform-logs-overview.md) Azure lze nastavit pro každou instanci Azure k doplnění protokolování aktivit. Zatímco protokoly aktivit se vymýšlejí události na úrovni předplatného, protokolování diagnostiky poskytuje přehled o provozní historii samotných prostředků.

Příklady diagnostického protokolování:

* Doba spuštění uživatelem definované funkce
* Kód stavu odpovědi úspěšného požadavku rozhraní API
* Načítání klíče aplikace z trezoru

Povolení diagnostických protokolů pro instanci:

1. Zaregistrujte prostředek na webu Azure Portal.
1. Vyberte **Nastavení diagnostiky**:

    [![Diagnostická nastavení jedna](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Vyberte **Zapnout diagnostiku** pro shromažďování dat (pokud nebyla dříve povolena).
1. Vyplňte požadovaná pole a vyberte, jak a kde budou data uložena:

    [![Diagnostická nastavení dvě](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    Diagnostické protokoly se často ukládají pomocí [Azure File Storage](../storage/files/storage-files-deployment-guide.md) a sdílejí s [protokoly Azure Monitor .](../azure-monitor/log-query/get-started-portal.md) Lze vybrat obě možnosti.

>[!TIP]
>Pomocí **diagnostických protokolů** můžete získat přehled o operacích prostředků.

## <a name="azure-monitor-and-log-analytics"></a>Azure monitorování a analýzy protokolů

Aplikace IoT spojují různorodé prostředky, zařízení, umístění a data do jednoho. Jemně odstupňované protokolování poskytuje podrobné informace o jednotlivých částí, službě nebo součásti celkové architektury aplikace, ale pro údržbu a ladění je často vyžadován jednotný přehled.

Azure Monitor zahrnuje výkonnou službu analýzy protokolů, která umožňuje zobrazit a analyzovat zdroje protokolování na jednom místě. Azure Monitor je proto velmi užitečné pro analýzu protokolů v rámci sofistikované aplikace IoT.

Příklady použití:

* Dotazování na více historie diagnostických protokolů
* Zobrazení protokolů pro několik uživatelem definovaných funkcí
* Zobrazení protokolů pro dvě nebo více služeb v určitém časovém rámci

Úplné dotazování protokolu je k dispozici prostřednictvím [protokolů Azure Monitor](../azure-monitor/log-query/log-query-overview.md). Nastavení těchto výkonných funkcí:

1. Vyhledejte **analýzu protokolů** na webu Azure Portal.
1. Zobrazí se vaše dostupné instance **pracovního prostoru Log Analytics.** Vyberte jednu a vyberte **Protokoly** pro dotaz:

    [![Analýza protokolů](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Pokud ještě nemáte **instanci pracovního prostoru Analýzy protokolů,** můžete vytvořit pracovní prostor výběrem tlačítka **Přidat:**

    [![Vytvořit OMS](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

Po zřízení instance **pracovního prostoru Analýzy protokolů** můžete pomocí výkonných dotazů vyhledat položky v protokolech násobků nebo vyhledávat pomocí konkrétních kritérií pomocí **služby Správa protokolů**:

   [![Správa protokolů](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

Další informace o výkonných operacích dotazů [načtete, jak začít s dotazy](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Při prvním odesílání událostí do **pracovního prostoru Log Analytics** může dojít k 5minutovému zpoždění.

Protokoly Azure Monitor také poskytují výkonné služby upozornění na chyby a upozornění, které lze zobrazit výběrem **diagnostikovat a řešit problémy**:

   [![Upozornění a oznámení o chybách](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>Pomocí **pracovního prostoru Log Analytics** můžete dotazovat historii protokolu pro více funkcí aplikací, předplatných nebo služeb.

## <a name="other-options"></a>Další možnosti

Azure Digital Twins také podporuje protokolování specifické pro aplikaci a auditování zabezpečení. Podrobný přehled všech možností protokolování Azure, které jsou k dispozici pro vaši instanci Azure Digital Twins, najdete v článku [auditu protokolu Azure.](../security/fundamentals/log-audit.md)

## <a name="next-steps"></a>Další kroky

- Další informace o [protokolech aktivit](../azure-monitor/platform/platform-logs-overview.md)Azure .

- Podrobnější informace o diagnostických nastaveních Azure naleznete v [přehledu diagnostických protokolů](../azure-monitor/platform/platform-logs-overview.md).

- Přečtěte si další informace o [protokolech Azure Monitor .](../azure-monitor/log-query/get-started-portal.md)
