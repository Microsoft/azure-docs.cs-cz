---
title: Postup upgradu Azure Monitor pro agenta kontejnery (Preview) | Dokumentace Microsoftu
description: Tento článek popisuje, jak upgradovat agenta Log Analytics používá Azure Monitor pro kontejnery.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: 57bfa47d60ffd8aa7c4240ab77f788773e426bd8
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51715226"
---
# <a name="how-to-upgrade-the-azure-monitor-for-containers-preview-agent"></a>Postup upgradu Azure Monitor pro agenta kontejnery (Preview)
Azure Monitor pro kontejnery používá kontejnerizovaných verzi agenta Log Analytics pro Linux. Po vydání nové verze agenta, není ve vaší spravované clustery Kubernetes hostované ve službě Azure Kubernetes Service (AKS) automaticky upgraduje agenta.

Tento článek popisuje postup upgradu agenta.

## <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Upgrade agenta na sledovaných clusteru Kubernetes
Postup upgradu agenta zahrnuje dva kroky přímo vpřed. Prvním krokem je vypnout monitorování pro kontejnery pomocí Azure CLI s Azure Monitor.  Postupujte podle kroků popsaných v [zakázat monitorování](container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json#azure-cli) článku. Pomocí Azure CLI umožňuje odebrat agenta z uzlů v clusteru bez dopadu na řešení a odpovídajících dat, která je uložena v pracovním prostoru. 

>[!NOTE]
>Při provádění této aktivity údržby, uzly v clusteru nejsou předávání shromážděných dat a zobrazení výkonu nebudou zobrazovat data mezi časem odebrat agenta a nainstalujte novou verzi. 
>

Pokud chcete nainstalovat novou verzi agenta, postupujte podle kroků popsaných v [připojení monitorování](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-using-azure-cli) článku pomocí Azure CLI pro ukončení tohoto procesu.  

Po opětovné povolení monitorování, může trvat přibližně 15 minut, než uvidíte aktualizovaný stav metriky pro cluster. 

## <a name="next-steps"></a>Další postup
Pokud máte problémy při upgradu agenta, zkontrolujte [Průvodce odstraňováním potíží](container-insights-troubleshoot.md) pro podporu.