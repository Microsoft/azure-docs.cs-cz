---
title: Přehled classic výstrah v Microsoft Azure a Azure monitorování | Microsoft Docs
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
ms.date: 03/28/2018
ms.author: robb
ms.openlocfilehash: 06ba05f71cf1f696033099c04448526a0421ad42
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Jaké jsou classic výstrah v Microsoft Azure?

> [!NOTE]
> Tento článek popisuje postup vytvoření starší classic metriky výstrahy. Azure nyní podporuje monitorování [novější metriky výstrahy skoro v reálném čase](monitoring-overview-unified-alerts.md)
>

Tento článek popisuje různé zdroje výstrah v Microsoft Azure, co se účely pro tyto výstrahy, jejich výhody a jak začít pracovat s jejich používání. Konkrétně platí pro výstrahy monitorování Azure classic. Výstrahy nabízejí metoda monitorování v Azure, který umožňuje nakonfigurovat podmínky nad daty a být upozorněni, když podmínky odpovídají data nejnovější sledování.


## <a name="taxonomy-of-azure-monitor-classic-alerts"></a>Taxonomii výstrah monitorování Azure classic
Azure používá k popisu classic výstrahy a jejich funkce následující podmínky:
* **Výstrahy** -definici kritéria (jeden nebo více pravidel nebo podmínky), která se změní na aktivovat, pokud splněny.
* **Aktivní** -stav, když je splněna kritéria definovaná výstrahu classic.
* **Vyřešit** -stav, když po dříve splněné již není splněna kritéria definovaná výstrahu classic.
* **Oznámení** – akce na základě z výstrahu classic, aby se aktivovala.
* **Akce** -konkrétní volání odeslaných k příjemce oznámení (například e-mailem na adresu nebo příspěvků na adresu URL webhooku). Oznámení můžete aktivovat obvykle více akcí.


## <a name="alerts-on-azure-monitor-data"></a>Výstrahy na dat monitorování Azure
Existují tři typy výstrah z dat z Azure sledování – metriky výstrahy, téměř v reálném čase metriky výstrahy a protokol aktivit výstrahy k dispozici.

* **Classic metriky výstrahy** – Tato výstraha aktivuje, když hodnota zadané metriky překračuje prahovou hodnotu, která přiřadíte. Výstraha vygeneruje oznámení, pokud je výstraha "aktivován" (Pokud se překročí prahovou hodnotu a je splněna podmínka výstrahy) a také při se "nevyřeší" (Pokud je znovu překročí prahovou hodnotu a již není splněna podmínka). Jedná se o starší metriky výstrahy. Pro novější metriky výstrahy najdete níže.

* **Téměř v reálném čase metriky výstrahy** (novější výstrahy zkušenosti) – jedná se o novější generování metriky výstrahy s vylepšené funkce ve srovnání s předchozí metriky výstrahy. Tyto výstrahy mohou být spuštěny v frekvencí 1 min. Také podporují monitorování více (aktuálně dvě) metriky.  Výstraha vygeneruje oznámení, pokud je výstraha "aktivován" (Pokud se překročí mezní hodnoty pro jednotlivé metriky ve stejnou dobu a je splněna podmínka výstrahy) a také při se "nevyřeší" (pokud alespoň jedna metrika znovu protne prahovou hodnotu a je podmínka vyhodnocena jako žádné nebude splněna).

* **Výstrahy Classic aktivity protokolu** -streamování výstrahu protokolu, které spustí, když je generována událost protokol aktivit, že kritéria, které jste přiřadili filtru odpovídá. Tyto výstrahy mít pouze jeden stav "Aktivovali," vzhledem k tomu, že modul Výstrahy kritéria filtru, která jednoduše platí pro všechny nové události. Tyto výstrahy lze stát upozornění, když dojde k nový incident stav služby, nebo pokud uživatele nebo aplikace provede operaci v rámci vašeho předplatného, například "Odstranění virtuálního počítače."

Pro data protokolů diagnostiky k dispozici prostřednictvím Azure monitorování doporučujeme směrování dat do analýzy protokolů a používání analýzy protokolů výstrahy. Následující diagram shrnuje zdroje dat v Azure monitorování a, koncepčně, jak můžete výstrahy z tato data.

![Vysvětlení výstrah](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="how-do-i-receive-a-notification-on-an-azure-monitor-classic-alert"></a>Jak mohu dostávat oznámení na výstrahu monitorování Azure classic?
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
