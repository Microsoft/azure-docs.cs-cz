---
title: Přehled classic výstrah v Microsoft Azure a monitorování Azure
description: Výstrahy umožňují sledovat metriky prostředků Azure, události nebo protokoly a být upozorněni, když je splněna podmínka, které zadáte.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: 89c3975a1212aa991e42e0cce4fe5521b53bd373
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263666"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Jaké jsou classic výstrah v Microsoft Azure?

> [!NOTE]
> Tento článek popisuje postup vytvoření starší classic metriky výstrahy. Azure nyní podporuje monitorování [novější metriky výstrahy skoro v reálném čase a nové výstrahy prostředí](monitoring-overview-unified-alerts.md). 
>

Výstrahy umožňují nakonfigurovat podmínky nad daty a být upozorněni, když podmínky odpovídají data nejnovější sledování.


## <a name="alerts-on-azure-monitor-data"></a>Výstrahy na dat monitorování Azure
Existují dva typy classic výstrahy k dispozici – výstrahy protokolu aktivity a metriky výstrahy.

* **Classic metriky výstrahy** – Tato výstraha aktivuje, když hodnota zadané metriky překračuje prahovou hodnotu, která přiřadíte. Výstraha vygeneruje oznámení, pokud je výstraha "aktivován" (Pokud se překročí prahovou hodnotu a je splněna podmínka výstrahy) a také při se "nevyřeší" (Pokud je znovu překročí prahovou hodnotu a již není splněna podmínka). Pro novější metriky výstrahy najdete níže.

* **Výstrahy Classic aktivity protokolu** -streamování výstrahu protokolu, které spustí, když je generována událost protokol aktivit, že kritéria, které jste přiřadili filtru odpovídá. Tyto výstrahy mít pouze jeden stav "Aktivovali," vzhledem k tomu, že modul Výstrahy kritéria filtru, která jednoduše platí pro všechny nové události. Tyto výstrahy lze stát upozornění, když dojde k nový incident stav služby, nebo pokud uživatele nebo aplikace provede operaci v rámci vašeho předplatného, například "Odstranění virtuálního počítače."

Pro data protokolů diagnostiky k dispozici prostřednictvím Azure monitorování doporučujeme směrování dat do analýzy protokolů (OMS dříve) a pomocí dotazu upozornění na analýzy protokolů. Přihlaste se Analytics teď používá [nové výstrahy – metoda](monitoring-overview-unified-alerts.md) 

Následující diagram shrnuje zdroje dat v Azure monitorování a, koncepčně, jak můžete výstrahy z tato data.

![Vysvětlení výstrah](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="taxonomy-of-azure-monitor-alerts-classic"></a>Taxonomii výstrah monitorování Azure (klasický)
Azure používá k popisu classic výstrahy a jejich funkce následující podmínky:
* **Výstrahy** -definici kritéria (jeden nebo více pravidel nebo podmínky), která se změní na aktivovat, pokud splněny.
* **Aktivní** -stav, když je splněna kritéria definovaná classic výstraha.
* **Vyřešit** -stav, když po dříve splněné již není splněna kritéria definovaná classic výstraha.
* **Oznámení** – akce na základě z upozornění na classic, aby se aktivovala.
* **Akce** -konkrétní volání odeslaných k příjemce oznámení (například e-mailem na adresu nebo příspěvků na adresu URL webhooku). Oznámení můžete aktivovat obvykle více akcí.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Jak dostávat oznámení z výstrahy monitorování Azure classic
V minulosti Azure výstrahy z různých služeb používá vlastní metody předdefinovaných oznámení. 

Teď monitorování Azure nabízí opakovaně použitelné oznámení seskupování volané *skupiny akcí*. Zadejte skupiny akcí sadu příjemci pro oznámení a kdykoli se aktivuje výstraha odkazuje skupinu, akce, všichni příjemci dostávat oznámení. To vám umožní znova využít seskupení příjemci (například na pracovníka seznamu volání) napříč mnoho výstrah objektů. Akce skupiny podporují oznámení odesláním adresa URL webhooku kromě e-mailové adresy, čísla SMS a řadu dalších akcí.  Další informace najdete v tématu [skupiny akcí](monitoring-action-groups.md). 

Akce skupiny můžete použít starší classic výstrahy protokol aktivit.

Starší metriky výstrahy nepoužívejte skupiny akcí. Místo toho můžete nakonfigurovat následující akce: 
* Odesílání e-mailová oznámení pro správce služeb, spolusprávci nebo další e-mailové adresy, které zadáte.
* Volejte webhook, která umožňuje spustit další automatizace akce.

Webhooky umožňuje automatizace a nápravu, například pomocí:
    - Runbook Azure Automation
    - Funkce Azure Functions
    - Azure Logic Apps
    - službu třetí strany

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
* Další informace o [skupiny akcí](monitoring-action-groups.md)
* Konfigurace [novější výstrahy](monitor-alerts-unified-usage.md)
