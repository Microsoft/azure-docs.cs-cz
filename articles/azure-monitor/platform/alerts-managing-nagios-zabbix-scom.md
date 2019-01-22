---
title: Správa výstrah z SCOM a ve službě Azure Monitor Nagios a Zabbix
description: Správa výstrah z SCOM a ve službě Azure Monitor Nagios a Zabbix
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: 48fb9d8eaf2003834a420b48d649c830c608fd6e
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54421211"
---
# <a name="manage-alerts-from-scom-zabbix-and-nagios-in-azure-monitor"></a>Správa výstrah z SCOM a ve službě Azure Monitor Nagios a Zabbix

Teď si můžete zobrazit výstrahy od Nagios a Zabbix, System Center Operations Manager v [Azure Monitor](https://aka.ms/azure-alerts-overview). Tyto výstrahy pocházejí z integrace s servery Nagios a Zabbix nebo System Center Operations Manager do Log Analytics. 

## <a name="prerequisites"></a>Požadavky
Všechny záznamy v úložišti Log Analytics s typem upozornění budou importovány do služby Azure Monitor, proto je třeba provést konfiguraci, který je potřeba shromáždit tyto záznamy.
1. Pro **Nagios** a **Zabbix** výstrahy, [konfiguraci těchto serverů](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) k [odesílání upozornění](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json) ke službě Log Analytics.
1. Pro **System Center Operations Manager** výstrahy, [připojení skupiny pro správu nástroje Operations Manager do vašeho pracovního prostoru Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Při tomto postupu nasazení [Správa výstrah](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution) řešení z řešení Azure marketplace. Až to bude hotové, všechny výstrahy vytvořené v nástroji System Center Operations Manager jsou importovány do Log Analytics.

## <a name="view-your-alert-instances"></a>Zobrazení výstrah instancí
Po nakonfigurování importu do Log Analytics můžete začít zobrazovat výstrahy instancí z těchto služeb v monitorování [Azure Monitor](https://aka.ms/azure-alerts-overview). Poté, co jsou k dispozici ve službě Azure Monitor, můžete [Spravovat výstrahy instance](https://aka.ms/managing-alert-instances), [spravovat inteligentní skupiny vytvořené v těchto výstrahách](https://aka.ms/managing-smart-groups) a [změnit stav výstrahy a inteligentní skupiny](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  1. Toto řešení umožňuje zobrazit SCOM/Zabbix/Nagios aktivovaných instancí upozornění ve službě Azure Monitor. Konfigurace pravidla výstrahy může být pouze prohlížet a upravovat v příslušných nástrojů pro monitorování. 
>  1. Všechny instance aktivovaná upozornění bude k dispozici v nástroji Azure Monitor a Azure Log Analytics. V současné době neexistuje žádný způsob, jak zvolit dvě nebo přijímat pouze konkrétní vyvolané výstrahy.
>  1. Všechny výstrahy z nástroje SCOM a Nagios a Zabbix mít typ signálu "Neznámá", protože základní typ telemetrických dat není k dispozici.
>  1. Výstrahy Nagios nejsou stavové – například [monitorování stavu](https://aka.ms/azure-alerts-overview) výstrahy nebudou přejít z "Fired" na "Vyřešeno". Místo toho "Fired" i "Vyřešeno" se zobrazí jako samostatné výstrahy instance. 

