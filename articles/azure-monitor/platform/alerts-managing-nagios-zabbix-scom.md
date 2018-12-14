---
title: Správa výstrah z jiné služby monitorování ve službě Azure Monitor
description: Správa výstrah Nagios a Zabbix SCOM ve službě Azure Monitor
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: bc597d42fe89c0e03c4af1db3a935031b9043a98
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53345989"
---
# <a name="manage-alerts-from-other-monitoring-services"></a>Správa upozornění z ostatních služeb monitorování

Teď si můžete zobrazit výstrahy od Nagios a Zabbix, System Center Operations Manager v [sjednocené prostředí upozornění](https://aka.ms/azure-alerts-overview). Tyto výstrahy pocházejí z integrace s servery Nagios a Zabbix nebo System Center Operations Manager do Log Analytics. 

## <a name="prerequisites"></a>Požadavky
Všechny záznamy v úložišti Log Analytics s typem upozornění bude importován do prostředí jednotné upozornění, je třeba provést konfiguraci, která je požadována ke shromažďování těchto záznamů.
1. Pro **Nagios** a **Zabbix** výstrahy, [konfiguraci těchto serverů](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) k odesílání upozornění Log Analytics.
1. Pro **System Center Operations Manager** výstrahy, [připojení skupiny pro správu nástroje Operations Manager do vašeho pracovního prostoru Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Všechny výstrahy vytvořené v nástroji System Center Operations Manager jsou importovány do Log Analytics.

## <a name="view-your-alert-instances"></a>Zobrazení výstrah instancí
Po nakonfigurování importu do Log Analytics můžete začít zobrazovat výstrahy instancí z těchto služeb v monitorování [sjednocené prostředí upozornění](https://aka.ms/azure-alerts-overview). Poté, co jsou k dispozici v prostředí jednotné upozornění, můžete [Spravovat výstrahy instance](https://aka.ms/managing-alert-instances), [spravovat inteligentní skupiny vytvořené v těchto výstrahách](https://aka.ms/managing-smart-groups) a [změnit stav upozornění a inteligentní skupiny](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  Výstrahy Nagios v prostředí jednotné upozornění nejsou stavové – například [monitorování stavu](https://aka.ms/azure-alerts-overview) výstrahy nebudou přejít z "Fired" na "Vyřešeno". Místo toho "Fired" i "Vyřešeno" se zobrazí jako samostatné výstrahy instance. 
