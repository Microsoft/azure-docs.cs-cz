---
title: Přehled klasických upozornění v Microsoft Azure a Azure Monitor
description: Výstrahy umožňují monitorovat metriky prostředků Azure, události nebo protokoly a upozornění, když je splněna podmínka, kterou zadáte.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: a0abcbdaa7e998413efb717be6e0addc5607ec5c
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114007"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Co jsou upozornění v Microsoft Azure classic?

> [!NOTE]
> Tento článek popisuje, jak vytvářet starší, klasické metriky upozornění. Azure teď podporuje monitorování [upozornění na metriku novější, téměř v reálném čase a nové prostředí upozornění](monitoring-overview-unified-alerts.md). 
>

Pomocí výstrah můžete nakonfigurovat podmínky nad daty a nechte se informovat podmínky odpovídají data nejnovější monitorování.


## <a name="alerts-on-azure-monitor-data"></a>Upozornění na data Azure Monitor
Nejsou k dispozici dva druhy klasických upozornění: upozornění na metriky a upozornění protokolu aktivit.

* **Klasické metriky upozornění**: Tato výstraha se aktivuje, když hodnota zadaného metrika překročí mezní hodnotu, která přiřadíte. Výstrahy generuje upozornění, když it společnosti "aktivovaný" (když se překročí prahovou hodnotu a je splněná podmínka výstrahy). Také vygeneruje výstrahu, když ho se "Vyřešeno" (Pokud je znovu překročí prahovou hodnotu a už není splněná podmínka). 

* **Upozornění protokolu aktivit Classic**: Tato výstraha streamování protokolů aktivuje, když událost protokolu aktivit se vygeneruje, že odpovídá filtru kritérií, které jste přiřadili. Tyto výstrahy mít pouze jeden stav "aktivovaná,", protože modul pro správu oznámení kritéria filtru, která jednoduše platí pro všechny nové události. Tyto výstrahy mohou vás upozorní, když dojde k nový incident Service Health nebo když uživatel nebo aplikace provádí operace ve vašem předplatném, jako "odstranit virtuální počítač."

Pokud chcete přijímat data diagnostického protokolu k dispozici, která je dostupná prostřednictvím služby Azure Monitor, směrovat data do Log Analytics (dříve Operations Management Suite) a používat upozornění dotazu Log Analytics. Protokolovat Analytics teď používá [nové výstrahy metoda](monitoring-overview-unified-alerts.md). 

Následující diagram obsahuje souhrn zdrojů dat ve službě Azure Monitor a navrhne, jak může upozornit vypnout tato data.

![Vysvětlení výstrah](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="taxonomy-of-azure-monitor-alerts-classic"></a>Taxonomie služby Azure Monitor upozornění (klasická)
Azure používá k popisu klasických upozornění a jejich funkce následující podmínky:
* **Upozornění**: definice kritéria (jeden nebo více pravidel nebo podmínky), který se stane při splnění aktivována.
* **Aktivní**: stav, ke které dojde při splnění kritéria, která je definována klasického upozornění.
* **Vyřešené**: stav, ke které dojde při kritéria, která je definována klasického upozornění už není splněná po dříve splněné.
* **Oznámení**: akce, která se provede na základě když classic výstraha se aktivuje.
* **Akce**: konkrétní volání, které je odesláno příjemce oznámení (například e-mailu nebo post na adresu URL webhooku). Oznámení obvykle může aktivovat více akcí.

## <a name="how-do-i-receive-notifications-from-an-azure-monitor-classic-alert"></a>Jak mohu dostávat oznámení z výstrahy monitorování Azure classic?
V minulosti upozornění z různých služeb Azure použít své vlastní metody předdefinovaných oznámení. 

Teď Azure Monitor nabízí, volá se opakovaně použitelné oznámení seskupení *skupiny akcí*. Skupiny akcí zadat sadu příjemců pro oznámení. Při výstraha se aktivuje, který odkazuje skupina akcí, všichni příjemci dostávat oznámení. Tato funkce umožňuje opakovaně používat seskupení příjemce (třeba seznamu na volání engineer) na velký počet výstrah objektů. Skupiny akcí oznámení podporovat prostřednictvím různých metod. Tyto metody mohou zahrnovat příspěvky na adresu URL webhooku, odesílání e-mailů, zpráv SMS a řadu dalších akcí. Další informace najdete v tématu [vytvořit a spravovat skupiny akcí na webu Azure Portal](monitoring-action-groups.md). 

Starší upozornění protokolu aktivit classic použití skupin akcí.

Ale nepoužívejte starší upozornění metrik skupiny akcí. Místo toho můžete nakonfigurovat následující akce: 
* Odeslat e-mailová oznámení pro správce služeb, spolusprávci, nebo další e-mailové adresy, které zadáte.
* Voláním webhooku, která umožňuje spustit další automatizace akcí.

Webhooky povolit automatizace a nápravy, například pomocí následující služby:
- Runbook Azure Automation
- Azure Functions
- Azure Logic App
- Služby třetích stran

## <a name="next-steps"></a>Další postup
Získání informací o pravidla upozornění a jak je nakonfigurovat pomocí následující dokumentaci:

* Další informace o [metriky](monitoring-overview-metrics.md)
* Konfigurace [klasického upozornění metrik s využitím webu Azure portal](insights-alerts-portal.md)
* Konfigurace [klasického upozornění metrik pomocí prostředí PowerShell](insights-alerts-powershell.md)
* Konfigurace [klasické metriky upozornění pomocí rozhraní příkazového řádku Azure](insights-alerts-command-line-interface.md)
* Konfigurace [klasického upozornění metrik pomocí REST API služby Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Další informace o [protokolů aktivit](monitoring-overview-activity-logs.md)
* Konfigurace [upozornění protokolu aktivit s využitím webu Azure portal](monitoring-activity-log-alerts.md)
* Konfigurace [upozornění protokolu aktivit s využitím Resource Manageru](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Zkontrolujte [schéma webhooku v upozornění protokolu aktivit](monitoring-activity-log-alerts-webhook.md)
* Další informace o [skupiny akcí](monitoring-action-groups.md)
* Konfigurace [novější upozornění](monitor-alerts-unified-usage.md)
