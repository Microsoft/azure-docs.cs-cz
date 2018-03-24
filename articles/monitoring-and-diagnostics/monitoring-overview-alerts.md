---
title: Přehled výstrah v Microsoft Azure a Azure monitorování | Microsoft Docs
description: Výstrahy umožňují sledovat metriky prostředků Azure, události nebo protokoly a být upozorněni, když je splněna podmínka, které zadáte.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: robb
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c64ca224705b7da57846e53bdc28d6d03eb28b06
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="what-are-alerts-in-microsoft-azure"></a>Co jsou výstrahy v Microsoft Azure?
Tento článek popisuje různé zdroje výstrah v Microsoft Azure, co se účely pro tyto výstrahy, jejich výhody a jak začít pracovat s jejich používání. Konkrétně platí pro monitorování Azure, ale poskytuje také výstrahy ukazatele k jiným službám. Výstrahy nabízejí metoda monitorování v Azure, který umožňuje nakonfigurovat podmínky nad daty a být upozorněni, když podmínky odpovídají data nejnovější sledování.


## <a name="taxonomy-of-azure-alerts"></a>Taxonomii Azure výstrah
Azure používá k popisu výstrahy a jejich funkce následující podmínky:
* **Výstrahy** -definici kritéria (jeden nebo více pravidel nebo podmínky), která se změní na aktivovat, pokud splněny.
* **Aktivní** -stav, když je splněna kritéria definovaná výstrahu.
* **Vyřešit** -stav, když po dříve splněné již není splněna kritéria definovaná výstrahu.
* **Oznámení** – akce na základě z výstrahu, aby se aktivovala.
* **Akce** -konkrétní volání odeslaných k příjemce oznámení (například e-mailem na adresu nebo příspěvků na adresu URL webhooku). Oznámení můžete aktivovat obvykle více akcí.


## <a name="alerts-in-different-azure-services"></a>Výstrahy v různých služeb Azure
Výstrahy jsou k dispozici v rámci několik Azure monitorování služeb. Informace o tom, a kdy použít tyto služby [najdete v článku](./monitoring-overview.md). Zde je výčet typů výstrah dostupné přes Azure:


| Služba | Typ výstrahy | Podporované služby | Popis |
|---|---|---|---|
| Azure Monitor | [Metriky výstrahy](./insights-alerts-portal.md) | [Podporované metriky z monitorování Azure](./monitoring-supported-metrics.md) | Přijímat oznámení v případě jakékoli úrovni platformy metrika splňuje určité podmínky (například % využití procesoru na virtuálním počítači je větší než 90 za posledních 5 minut). |
|Azure Monitor | [Téměř v reálném čase metriky výstrahy](./monitoring-near-real-time-metric-alerts.md)| [Podporované prostředky z monitorování Azure](./monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported) | Přijímat oznámení rychleji než metriky výstrahy v případě, že jeden nebo více metriky úrovně platformu splňují určitá kritéria (například je větší než 90 % využití procesoru na virtuálním počítači a umístění v síti je větší než 500 MB za posledních 5 minut). |
| Azure Monitor | [Aktivity protokolu výstrahy](./monitoring-activity-log-alerts.md) | Všechny typy prostředků, k dispozici ve službě Správce prostředků Azure | Příjem oznámení, když se všechny nové události v [protokol činnosti Azure](./monitoring-overview-activity-logs.md) odpovídá konkrétní podmínky (například kdy dochází k operaci "Odstranit virtuální počítač" v myProductionResourceGroup nebo pokud se zobrazí nové události stavu služby s "Aktivní", jako stav). |
| Application Insights | [Metriky výstrahy](../application-insights/app-insights-alerts.md) | Všechny aplikace instrumentovány k odesílání dat do služby Application Insights | Přijímat oznámení v případě jakékoli úrovni aplikace metrika splňuje určité podmínky (například doba odezvy serveru je větší než 2 sekund). |
| Application Insights | [Webový test výstrahy](../application-insights/app-insights-monitor-web-app-availability.md) | Všechny webu instrumentovány k odesílání dat do služby Application Insights | Přijímat oznámení v případě dostupnosti nebo odezvy webu je nižší než očekávání. |
| Log Analytics | [Upozornění analýzy protokolů](../log-analytics/log-analytics-alerts.md) | Všechny služby, konfigurované k odesílání dat do analýzy protokolů | Přijímat oznámení v případě vyhledávací dotaz analýzy protokolů přes data metriky a události splňuje určitá kritéria. |

## <a name="alerts-on-azure-monitor-data"></a>Výstrahy na dat monitorování Azure
Existují tři typy výstrah z dat z Azure sledování – metriky výstrahy, téměř v reálném čase metriky výstrahy a protokol aktivit výstrahy k dispozici.

* **Metriky výstrahy** – Tato výstraha aktivuje, když hodnota zadané metriky překračuje prahovou hodnotu, která přiřadíte. Výstraha vygeneruje oznámení, pokud je výstraha "aktivován" (Pokud se překročí prahovou hodnotu a je splněna podmínka výstrahy) a také při se "nevyřeší" (Pokud je znovu překročí prahovou hodnotu a již není splněna podmínka). Jedná se o starší metriky výstrahy. Pro novější metriky výstrahy najdete níže.

* **Téměř v reálném čase metriky výstrahy** – jedná se o novější generování metriky výstrahy s vylepšené funkce ve srovnání s předchozí metriky výstrahy. Tyto výstrahy mohou být spuštěny v frekvencí 1 min. Také podporují monitorování více (aktuálně dvě) metriky.  Výstraha vygeneruje oznámení, pokud je výstraha "aktivován" (Pokud se překročí mezní hodnoty pro jednotlivé metriky ve stejnou dobu a je splněna podmínka výstrahy) a také při se "nevyřeší" (pokud alespoň jedna metrika znovu protne prahovou hodnotu a je podmínka vyhodnocena jako žádné nebude splněna).

* **Aktivity protokolu výstrahy** -streamování výstrahu protokolu, které spustí, když je generována událost protokol aktivit, že kritéria, které jste přiřadili filtru odpovídá. Tyto výstrahy mít pouze jeden stav "Aktivovali," vzhledem k tomu, že modul Výstrahy kritéria filtru, která jednoduše platí pro všechny nové události. Tyto výstrahy lze stát upozornění, když dojde k nový incident stav služby, nebo pokud uživatele nebo aplikace provede operaci v rámci vašeho předplatného, například "Odstranění virtuálního počítače."

Pro data protokolů diagnostiky k dispozici prostřednictvím Azure monitorování doporučujeme směrování dat do analýzy protokolů a používání analýzy protokolů výstrahy. Následující diagram shrnuje zdroje dat v Azure monitorování a, koncepčně, jak můžete výstrahy z tato data.

![Vysvětlení výstrah](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="how-do-i-receive-a-notification-on-an-azure-monitor-alert"></a>Jak mohu dostávat oznámení na výstrahu monitorování Azure?
V minulosti Azure výstrahy z různých služeb používá vlastní metody předdefinovaných oznámení. Do budoucna, monitorování Azure nabízí opakovaně použitelné oznámení seskupení nazývají skupiny akce. Zadejte skupiny akcí sadu příjemci pro oznámení – všechny počet e-mailové adresy, telefonních čísel (pro SMS) nebo adresy URL webhooku – a kdykoli se aktivuje výstraha odkazuje skupinu, akce, všichni příjemci dostávat oznámení. To vám umožní znova využít seskupení příjemci (například na pracovníka seznamu volání) napříč mnoho výstrah objektů. V současné době pouze protokol aktivit výstrahy využívat skupin akce, ale několik dalších Azure typy výstrah pracují na také použití skupin akce.

Akce skupiny podporují oznámení odesláním adresa URL webhooku kromě e-mailové adresy a čísla SMS. To umožňuje automatizace a nápravu, například pomocí:
    - Runbook Azure Automation
    - Azure – funkce
    - Azure Logic Apps
    - službu třetí strany

Téměř v reálném čase metrika výstrahy a protokol aktivit výstrah pomocí akce skupin.

Starší metriky výstrahy, které jsou k dispozici v části výstrahy (klasické) nepoužívejte skupiny akcí. Na jednotlivé metriky výstrahy můžete nakonfigurovat oznámení:
* Odesílání e-mailová oznámení pro správce služeb, spolusprávci nebo další e-mailové adresy, které zadáte.
* Volejte webhook, která umožňuje spustit další automatizace akce.

## <a name="next-steps"></a>Další postup
Získání informací o pravidla výstrah a jejich konfigurací pomocí:

* Další informace o [metriky](monitoring-overview-metrics.md)
* Konfigurace [classic metrika výstrahy prostřednictvím portálu Azure](insights-alerts-portal.md)
* Konfigurace [classic PowerShell metrika výstrahy](insights-alerts-powershell.md)
* Konfigurace [klasické rozhraní metrika výstrahy příkazového řádku (CLI)](insights-alerts-command-line-interface.md)
* Konfigurace [classic metrika výstrahy monitorování REST API služby Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Další informace o [protokol aktivit](monitoring-overview-activity-logs.md)
* Konfigurace [aktivity protokolu výstrahy prostřednictvím portálu Azure](monitoring-activity-log-alerts.md)
* Konfigurace [aktivity protokolu výstrahy prostřednictvím Resource Manageru](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Zkontrolujte [schématu výstrahy webhooku protokolu aktivit](monitoring-activity-log-alerts-webhook.md)
* Další informace o [novější metrika výstrahy](monitoring-near-real-time-metric-alerts.md)
* Další informace o [oznámení o službách](monitoring-service-notifications.md)
* Další informace o [skupiny akcí](monitoring-action-groups.md)
* Konfigurace [výstrahy](monitor-alerts-unified-usage.md)
